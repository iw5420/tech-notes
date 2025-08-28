
# 📚 Java 工具類設計與 Lombok `@UtilityClass` 使用說明

> 本文件說明 Java 工具類（Utility Class）的設計原則，為什麼應避免被 `new`，以及如何透過 Lombok `@UtilityClass` 簡化撰寫。

---

## 🔧 工具類（Utility Class）是什麼？

工具類是一種只包含靜態方法（`static`）的類別，常見用途包含：

- 字串處理（`StringUtils`）
- 時間格式化（`DateUtil`）
- 驗證邏輯（`ValidationUtil`）
- 加密、解密工具（`JwtUtil`）

工具類的特點是：

- 不需儲存狀態（stateless）
- 不應該被實體化（不需要 `new`）
- 方法應為 `static`，可直接呼叫

---

## ❌ 為什麼工具類不應被 `new`？

### ✅ 理由一：工具類沒有狀態

```java
ValidationUtil.isNullOrEmpty("abc");       // ✅ 正常呼叫
new ValidationUtil().isNullOrEmpty("abc"); // ❌ 沒有意義
```

### ✅ 理由二：避免不必要的物件產生

```java
for (int i = 0; i < 1000; i++) {
    new ValidationUtil().isNullOrEmpty("abc"); // ❌ 浪費記憶體
}
```

### ✅ 理由三：語意清楚

工具類的設計目的是「提供方法」而非「建立物件」，避免誤用。

---

## 🛡 傳統做法：使用 private constructor

若不加 constructor，Java 會自動產生 `public` constructor，導致可以被 new。

**正確做法如下：**

```java
public class ValidationUtil {

    private ValidationUtil() {} // ✅ 禁止被實體化

    public static boolean isNullOrEmpty(String str) {
        return str == null || str.trim().isEmpty() || "string".equals(str);
    }
}
```

---

## 💡 使用 Lombok `@UtilityClass` 簡化工具類撰寫

### ✅ 語法

```java
import lombok.experimental.UtilityClass;

@UtilityClass
public class ValidationUtil {

    public boolean isNullOrEmpty(String str) {
        return str == null || str.trim().isEmpty() || "string".equals(str);
    }
}
```

### ✅ 功能特點

| 功能                         | 效果                           |
|------------------------------|----------------------------------|
| 自動加上 private constructor | 無法被 `new`                    |
| 所有方法自動變為 static      | 可省略 `static` 關鍵字           |
| 使用方式統一                 | `ValidationUtil.isNullOrEmpty()` |

### ✅ 使用範例

```java
if (ValidationUtil.isNullOrEmpty(email)) {
    // 處理空值邏輯
}
```

---

## ✅ 小結

| 設計原則                     | 建議                         |
|------------------------------|------------------------------|
| 工具類禁止被 new             | 加上 private constructor 或 `@UtilityClass` |
| 工具類方法應為 static        | `@UtilityClass` 自動處理      |
| 工具類不應保存任何狀態       | 所有欄位與方法皆為 static     |
| 可搭配 IDE、Lombok 簡化撰寫 | 增加一致性與可維護性         |

---

## 📦 建議命名與分類

| 工具類名稱        | 功能範圍                |
|-------------------|-------------------------|
| `StringUtil`      | 字串轉換、裁切、替換等     |
| `DateUtil`        | 時間解析、格式化          |
| `ValidationUtil`  | 欄位檢查、格式驗證、email驗證 |
| `JwtUtil`         | JWT 建立與解析            |
| `FileUtil`        | 檔案路徑、擴展名處理        |

---

**推薦使用方式：** 如果你專案已使用 Lombok，`@UtilityClass` 是乾淨簡潔又安全的工具類寫法首選 ✅
