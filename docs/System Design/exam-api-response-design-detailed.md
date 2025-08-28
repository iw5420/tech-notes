
# 📦 考試系統 API 回傳值設計規範
*Version:* 2025-08-28 02:25  
*Owner:* Backend Team  
*Scope:* 全系統（學生端／監考端／後台管理）

---

## 0. 目標與原則
- **統一格式**：所有 API 回傳以 `ResponseResult<T>` 包裝，前端只需解析 `code / msg / data`。  
- **可擴充**：錯誤碼依「號段」規劃，新增不破壞既有行為。  
- **可讀性**：看數字就能 roughly 知道錯誤類型；命名具語意。  
- **跨模組一致**：學生端/監考端/管理端共用一套規則。

---

## 1. 回傳容器 `ResponseResult<T>`
```java
class ResponseResult<T> { 
  int code;     // 與 AppHttpCodeEnum 對應
  String msg;   // 人類可讀訊息（可本地化）
  T data;       // 真實資料，可為 null
}
```

### 1.1 回傳樣例
**成功（有資料）**
```json
{ "code": 200, "msg": "操作成功", "data": { "userId": 123, "name": "小明" } }
```

**成功（無資料）**
```json
{ "code": 200, "msg": "操作成功", "data": null }
```

**錯誤（查無資料）**
```json
{ "code": 1002, "msg": "查無題目", "data": null }
```

> 註：若前端自行決定提示語，可忽略 `msg`；若需要統一提示，後端提供 `msg` 以便直接顯示。

---

## 2. 錯誤碼總則（號段設計）
| 號段範圍       | 類別               | 說明與範例 |
|----------------|--------------------|------------|
| **200**        | ✅ 成功            | `SUCCESS` |
| **1–49**       | 🔐 登入驗證       | `NEED_LOGIN`, `LOGIN_EXPIRED` |
| **100–199**    | 📝 考試流程       | `EXAM_NOT_STARTED`, `EXAM_TIMEOUT` |
| **200–299**    | 📚 題目／答案     | `QUESTION_NOT_FOUND`, `ANSWER_INVALID` |
| **300–399**    | 🔒 權限與身分     | `NO_OPERATOR_AUTH`, `NEED_ADMIN` |
| **500–599**    | ⚠️ 系統級         | `PARAM_REQUIRE`, `SERVER_ERROR` |
| **1000–1999**  | 🔧 資料錯誤       | 使用者不存在、查無資料 等 |
| **2000–2999**  | 📦 第三方錯誤     | 外部 API 故障、支付失敗、雲儲存異常 |
| **9000–9999**  | 🧪 特殊用途       | 內部測試、診斷、灰度發布 |

### 2.1 延伸建議：分層 + 自訂區段
> 依團隊及產品演進，可在號段內再切子區段，避免混雜：

**1000–1999（資料錯誤）建議子區段：**
- **1000–1049**：使用者／帳號資料（例：`USER_NOT_EXIST(1000)`）  
- **1050–1099**：考生報名／資格資料（例：`CANDIDATE_NOT_ELIGIBLE(1050)` 保留）  
- **1100–1149**：題庫資料缺漏（例：`QUESTION_META_MISSING(1100)` 保留）  
- **1150–1199**：試卷／成績資料（例：`EXAM_RESULT_NOT_EXIST(1001)` 已用）  
- **1900–1999**：**通用資料錯誤**（例：`DATA_NOT_EXIST(1002)`、`DATA_CONFLICT(1901)` 保留）  

**2000–2999（第三方錯誤）建議子區段：**
- **2000–2099**：支付／金流（`PAYMENT_FAILED(2000)` 保留）  
- **2100–2199**：外部認證／OAuth（`OAUTH_TOKEN_INVALID(2100)` 保留）  
- **2200–2299**：雲儲存／物件存取（GCS／S3／MinIO；`OBJECT_UPLOAD_FAILED(2200)` 保留）  
- **2300–2399**：郵件／簡訊（`EMAIL_SEND_FAILED(2300)` 保留）  
- **2900–2999**：通用第三方錯誤（`THIRDPARTY_UNAVAILABLE(2900)` 保留）  

**9000–9999（特殊）建議子區段：**
- **9000–9099**：A/B 測試／灰度控制碼  
- **9900–9999**：內部診斷、臨時開關（禁止上線長期使用）

> **新增錯誤碼規則**：先選主號段 → 再挑子區段 → 採 遞增 編碼；避免修改既有值。

---

## 3. AppHttpCodeEnum（現況 + 建議保留位）
> 下表包含目前已實作項目，並在關聯號段下方標註「建議保留位」供未來擴充。

### 3.1 既有定義
| Code | 名稱                      | 訊息                     |
|-----:|---------------------------|--------------------------|
| 200  | SUCCESS                   | 操作成功                 |
| 1    | NEED_LOGIN                | 需要登入後操作           |
| 2    | LOGIN_PASSWORD_ERROR      | 帳號或密碼錯誤           |
| 3    | LOGIN_EXPIRED             | 登入已過期，請重新登入   |
| 100  | EXAM_NOT_STARTED          | 考試尚未開始             |
| 101  | EXAM_ALREADY_ENDED        | 考試已經結束             |
| 102  | EXAM_TIMEOUT              | 考試時間已到             |
| 103  | EXAM_SUBMIT_DUPLICATE     | 試卷已提交，不能重複提交 |
| 200  | QUESTION_NOT_FOUND        | 考題不存在               |
| 201  | ANSWER_INVALID            | 答案格式有誤             |
| 202  | ANSWER_MISMATCH           | 答案與題目不符           |
| 203  | QUESTION_BANK_EMPTY       | 題庫為空，無法生成試卷   |
| 300  | NO_OPERATOR_AUTH          | 無操作權限               |
| 301  | NEED_ADMIN                | 需要管理員權限           |
| 302  | NEED_PROCTOR              | 需要監考員權限           |
| 500  | PARAM_REQUIRE             | 缺少必要參數             |
| 501  | PARAM_INVALID             | 無效的參數               |
| 503  | SERVER_ERROR              | 伺服器內部錯誤           |
| 504  | DATABASE_ERROR            | 資料庫存取錯誤           |
| 505  | FILE_UPLOAD_ERROR         | 檔案上傳失敗             |
| 1000 | USER_NOT_EXIST            | 使用者不存在             |
| 1001 | EXAM_RESULT_NOT_EXIST     | 考試成績不存在           |
| 1002 | DATA_NOT_EXIST            | 數據不存在               |

### 3.2 建議保留位（未來可擴充）
> 以下為 預留名稱，尚未實作；若需採用請加入 Enum 並補訊息文案。

**資料錯誤 1000–1999**
- `CANDIDATE_NOT_ELIGIBLE(1050)`：考生資格不符  
- `QUESTION_META_MISSING(1100)`：題目元資料缺漏  
- `PAPER_VERSION_CONFLICT(1151)`：試卷版本衝突  
- `DATA_CONFLICT(1901)`：資料狀態衝突（併發寫入）  

**第三方錯誤 2000–2999**
- `PAYMENT_FAILED(2000)`：支付失敗  
- `OAUTH_TOKEN_INVALID(2100)`：第三方 Token 無效  
- `OBJECT_UPLOAD_FAILED(2200)`：物件上傳失敗（GCS/S3/MinIO）  
- `EMAIL_SEND_FAILED(2300)`：郵件寄送失敗  
- `THIRDPARTY_UNAVAILABLE(2900)`：第三方服務不可用  

**特殊用途 9000–9999**
- `AB_TEST_BUCKET_MISMATCH(9001)`：A/B 組桶錯誤（僅測試環境）  
- `INTERNAL_DIAGNOSTIC(9900)`：內部診斷碼（臨時）  

---

## 4. 前後端對接建議
- **前端判斷建議**：  
  - `code == 200` → 成功  
  - `1–49` → 觸發登入流程（轉登入頁／刷新 Token）  
  - `300–399` → 權限提示／導回首頁  
  - `500–599` → 以「系統異常」呈現並上報  
  - `1000–1999` → 顯示「資料相關」提示（查無資料／狀態衝突）  
  - `2000–2999` → 顯示「第三方服務」提示（可重試）  
- **i18n**：若前端需要本地化，`msg` 做為 fallback，另傳一個 `msgKey` 可再擴充。  
- **HTTP Status**：如無強需求，建議 HTTP 統一 `200`，以 `code` 作業務狀態；如需符合傳統 REST，可在極端錯誤改 4xx/5xx。

---

## 5. Controller 使用樣例
```java
@PostMapping("/candidate")
public ResponseResult<UserProfileRs> getCandidate(@RequestBody EmailRq req) {
    if (req == null || req.getEmail() == null || req.getEmail().isBlank()) {
        return ResponseResult.error(AppHttpCodeEnum.PARAM_REQUIRE, "email 不能為空");
    }
    return examService.findByEmail(req.getEmail())
            .map(u -> ResponseResult.ok(UserProfileRs.from(u)))
            .orElseGet(() -> ResponseResult.error(AppHttpCodeEnum.USER_NOT_EXIST, "查無使用者"));
}
```

---

## 6. 新增錯誤碼流程
1) 決定主號段（登入／流程／資料／第三方／特殊）  
2) 選擇子區段（如 1000–1049 使用者）  
3) 挑選下一個 未使用 編號（遞增）  
4) 於 `AppHttpCodeEnum` 加入 `CODE_NAME(number, "訊息")`  
5) 在 PR 說明中附上使用情境與對應頁面行為

---

## 7. FAQ
- **Q: 為什麼成功也要 `msg`？**  
  A: 可做為統一提示的來源（例如「提交完成」）；若前端自定提示，可忽略。  
- **Q: 為什麼用號段而不是 HTTP Status？**  
  A: 以 `code` 做業務狀態更穩定；HTTP 僅反映通訊層是否成功。  
- **Q: 可以只用 `ResponseEntity` 嗎？**  
  A: 不建議；會導致格式分裂、前端判斷複雜。`ResponseResult` 能統一行為。

---

**Appendix – 完整 `AppHttpCodeEnum`（現況）**  
> 如需擴充請依 *3.2 建議保留位* 原則新增。
```java
public enum AppHttpCodeEnum {
    SUCCESS(200, "操作成功"),
    NEED_LOGIN(1, "需要登入後操作"),
    LOGIN_PASSWORD_ERROR(2, "帳號或密碼錯誤"),
    LOGIN_EXPIRED(3, "登入已過期，請重新登入"),
    EXAM_NOT_STARTED(100, "考試尚未開始"),
    EXAM_ALREADY_ENDED(101, "考試已經結束"),
    EXAM_TIMEOUT(102, "考試時間已到"),
    EXAM_SUBMIT_DUPLICATE(103, "試卷已提交，不能重複提交"),
    QUESTION_NOT_FOUND(200, "考題不存在"),
    ANSWER_INVALID(201, "答案格式有誤"),
    ANSWER_MISMATCH(202, "答案與題目不符"),
    QUESTION_BANK_EMPTY(203, "題庫為空，無法生成試卷"),
    NO_OPERATOR_AUTH(300, "無操作權限"),
    NEED_ADMIN(301, "需要管理員權限"),
    NEED_PROCTOR(302, "需要監考員權限"),
    PARAM_REQUIRE(500, "缺少必要參數"),
    PARAM_INVALID(501, "無效的參數"),
    SERVER_ERROR(503, "伺服器內部錯誤"),
    DATABASE_ERROR(504, "資料庫存取錯誤"),
    FILE_UPLOAD_ERROR(505, "檔案上傳失敗"),
    USER_NOT_EXIST(1000, "使用者不存在"),
    EXAM_RESULT_NOT_EXIST(1001, "考試成績不存在"),
    DATA_NOT_EXIST(1002, "數據不存在");
}
```
