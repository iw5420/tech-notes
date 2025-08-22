---
title: 為什麼要用 DDD？
tags:
  - Java
  - DDD
---
# 為什麼要用 DDD？— 從 Service 邏輯到聚合根的思維轉換（完整版）

> 目的：補齊「Repository 應該在哪裡用？」與「範例：考生提交答案」等關鍵細節，作為可直接落地的技術筆記。

---

## 一、常見疑問
第一次接觸 DDD（Domain-Driven Design）時，常見疑問：

> 「那不就是把 Service 的邏輯搬到 Entity 裡嗎？幹嘛那麼麻煩？」

DDD 的價值不在於「程式碼搬到哪一層」，而在於 **長期治理大型系統**：邊界清晰、一致性集中、測試容易、語言與模型對齊。

---

## 二、傳統 Service-heavy 的問題
典型三層（Controller → Service → Repository）的常見痛點：
- **胖 Service**：方法動輒數百行、到處 if/else。
- **貧血模型**：Entity 只有 getter/setter。
- **耦合擴散**：規則散在多個 Service；修改要動很多地方。
- **測試困難**：多靠整合測試，單元測試價值不高。

---

## 三、DDD Aggregate 的好處（重點）
1. **一致性集中**：所有規則進入 **Aggregate Root**（例如 `Exam`），外部只能透過 Root 操作子實體（`Answer`、`ExamKind`）。  
2. **邊界清晰**：跨聚合互動走應用服務 / API，而非直接改對方資料。  
3. **瘦 Service**：Service 做用例編排、交易控制，不寫複雜規則。  
4. **易測試**：規則在 Domain，**無 DB** 就能寫單元測試。  
5. **語言對齊**：`exam.submitAnswer()`、`exam.finish()` 這類 API 可讀性更高。

---

## 四、豐血模型 vs 常見誤用

### ❌ 把業務邏輯塞進 JPA `@Entity`（誤用示例）
```java
@Entity
@Table(name = "answer")
public class AnswerEntity {
  @Id Long id;
  String questionNumber;
  String answer;

  public void validate() {
    if (answer == null || answer.isBlank()) throw new IllegalStateException("答案不可為空");
  }
}
```
問題：把**持久化**（ORM 約束）與**業務規則**混在同一類別，耦合大、測試難。

### ✅ 較佳做法：Domain Model 與 Persistence Entity 分離
**Domain Model（純業務，不依賴 JPA）**
```java
public class Answer {
  private final String questionNumber;
  private String answer;

  public Answer(String questionNumber, String answer) {
    changeAnswerInternal(answer);
    this.questionNumber = questionNumber;
  }

  public void changeAnswer(String newAnswer) {
    changeAnswerInternal(newAnswer);
  }

  private void changeAnswerInternal(String v) {
    if (v == null || v.isBlank()) throw new IllegalArgumentException("答案不可為空");
    this.answer = v;
  }

  public String getQuestionNumber() { return questionNumber; }
  public String getAnswer() { return answer; }
}
```

**Persistence Entity（資料庫映射）**
```java
@Entity
@Table(name = "answer")
public class AnswerEntity {
  @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
  Long id;

  @Column(name="question_number") String questionNumber;
  @Column(name="answer") String answer;

  protected AnswerEntity() {} // for JPA

  public static AnswerEntity fromDomain(Answer d) {
    AnswerEntity e = new AnswerEntity();
    e.questionNumber = d.getQuestionNumber();
    e.answer = d.getAnswer();
    return e;
  }

  public Answer toDomain() {
    return new Answer(questionNumber, answer);
  }
}
```

---

## 五、Repository 應該在哪裡用？（關鍵補充）

### ✅ 應用服務（Application Service）使用 Repository
- 從 Repository **載入聚合根** → 呼叫聚合方法執行規則 → **保存聚合根**。
- Application Service 負責用例編排、交易邊界（`@Transactional`）、權限、整合外部系統。

### ❌ Domain Model（聚合/實體）不要使用 Repository
- Domain 專注規則，不關心資料存哪裡。
- 若 Domain 直接注入 Repository，會讓規則與持久化耦合，測試也會變慢、變難。

> 一句話：**Domain 管規則、Service 管協調、Repository 管存取**。

---

## 六、範例：考生提交答案（完整落地）

### 1) Domain：`Exam` 聚合（聚合根管理狀態與規則）
```java
public enum ExamStatus { NOT_STARTED, IN_PROGRESS, FINISHED }

public class Exam {
  private ExamStatus status = ExamStatus.NOT_STARTED;
  private final List<Answer> answers = new ArrayList<>();

  public void start() {
    if (status != ExamStatus.NOT_STARTED) throw new IllegalStateException("不能重複開始");
    status = ExamStatus.IN_PROGRESS;
  }

  public void submitAnswer(Answer answer) {
    if (status != ExamStatus.IN_PROGRESS) throw new IllegalStateException("未在考試中，不能提交");
    // 可加規則：同題覆蓋、計分策略、題號驗證...
    this.answers.add(answer);
  }

  public void finish() {
    if (status != ExamStatus.IN_PROGRESS) throw new IllegalStateException("非進行中，無法結束");
    status = ExamStatus.FINISHED;
  }

  public ExamStatus getStatus() { return status; }
  public List<Answer> getAnswers() { return Collections.unmodifiableList(answers); }
}
```

### 2) Repository 介面（以聚合為單位存取）
```java
public interface ExamRepository {
  Exam findById(String examId);
  void save(Exam exam);
}
```

> 實作可用 JPA、MyBatis 或 Event Sourcing，**不影響 Domain**。

### 3) Application Service（協調用例）
```java
@Service
public class ExamApplicationService {
  private final ExamRepository examRepository;

  public ExamApplicationService(ExamRepository examRepository) {
    this.examRepository = examRepository;
  }

  @Transactional
  public void submitAnswer(String examId, Answer dto) {
    Exam exam = examRepository.findById(examId); // 1. 載入聚合根
    exam.submitAnswer(dto);                       // 2. 執行業務規則（由聚合負責）
    examRepository.save(exam);                    // 3. 保存聚合根
  }

  @Transactional
  public void startExam(String examId) {
    Exam exam = examRepository.findById(examId);
    exam.start();
    examRepository.save(exam);
  }

  @Transactional
  public void finishExam(String examId) {
    Exam exam = examRepository.findById(examId);
    exam.finish();
    examRepository.save(exam);
  }
}
```

### 4) 單元測試（無需 DB）
```java
class ExamDomainTest {
  @Test
  void submit_answer_only_when_in_progress() {
    Exam exam = new Exam();
    exam.start();
    exam.submitAnswer(new Answer("Q1", "A"));
    assertEquals(1, exam.getAnswers().size());
  }

  @Test
  void cannot_submit_when_not_started_or_finished() {
    Exam notStarted = new Exam();
    assertThrows(IllegalStateException.class,
        () -> notStarted.submitAnswer(new Answer("Q1", "A")));

    Exam finished = new Exam();
    finished.start();
    finished.finish();
    assertThrows(IllegalStateException.class,
        () -> finished.submitAnswer(new Answer("Q1", "A")));
  }
}
```

---

## 七、反模式清單（避免掉坑）
- **Domain 直接注入 Repository**（邏輯與持久化耦合）。  
- **跨聚合直接改資料**（應透過應用服務或領域事件協作）。  
- **巨型聚合**：把不需要強一致的資料硬塞同一聚合，導致鎖衝突/效能差。  
- **胖 Service**：規則寫在 Service，而非聚合。  

---

## 八、落地建議（從單體到微服務）
1. **現在**：在單體內先把 Controller / Service / Domain / Repository 邊界拉清、補齊單元測試。  
2. **之後**：當子域流量/責任成長，**再**拆成微服務；Application Service 與 Domain 幾乎可原封不動抽離。  
3. **資料庫策略**：現階段共用同一 DB；File 走 GCS（僅存 metadata 於主 DB）。

---

## 九、總結
- DDD 不是「把 Service 搬到 Entity」，而是 **規則回歸聚合根**，讓：  
  - Service 更瘦（用例編排）  
  - 規則集中（一致性更好）  
  - 測試更簡單（純 Domain 測試）  
- **Repository 用在 Application Service**；**Domain 不碰 Repository**。  
- 以「考生提交答案」為例：**載入聚合 → 呼叫聚合方法 → 儲存聚合**，交易邊界由應用服務掌握。

