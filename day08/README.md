[Day 08｜把 Allow 移到最後，操作就會被允許嗎？](https://hackmd.io/@hex-course/HydaPFvKMg)

# Day 08｜把 Allow 移到最後，操作就會被允許嗎？

## 情境

IAM Policy 中的 `Allow` 與 `Deny` 會共同影響使用者是否能執行某個操作。

一般來說：

* 有適用的 Explicit Deny → 操作會被拒絕
* 沒有適用的 Explicit Deny，但有適用的 Allow → 操作可以執行
* 沒有適用的 Allow → 預設為 Implicit Deny

這次要比較的是：Policy 裡面的規則順序，是否會影響最終結果。

---

## 第 1 題

目前有以下兩條 IAM Policy：

* `Allow`：`iam:ListUsers`
* `Deny`：`iam:UpdateUser`
* `Resource: *`

現在執行 `iam:ListUsers`，哪個說法正確？

A. 因為 `Resource: *`，所以所有 IAM 操作都會被拒絕
B. `iam:UpdateUser` 的 Deny 會影響 `iam:ListUsers`
C. 不同 IAM Action 的規則會互相影響
D. `iam:ListUsers` 可以執行

### 答案

**D**

### 理由

`Deny` 只針對它指定的 `iam:UpdateUser`，不會連帶拒絕 `iam:ListUsers`。

### 這題要記住

`Resource: *` 代表資源範圍較廣，**不代表所有 IAM Action 都會套用這條規則**。

---

## 第 2 題

有兩個版本的 Policy：

### 版本 A

```text
Allow：iam:ListUsers
Deny：iam:ListUsers
```

### 版本 B

```text
Deny：iam:ListUsers
Allow：iam:ListUsers
```

兩個版本的 `Resource` 都是 `*`，沒有 Condition，只有規則順序不同。

同事認為：「把 Allow 放到最後，就可以覆蓋前面的 Deny。」

哪個說法正確？

A. 版本 A 可以執行，因為 Allow 在前面
B. 版本 B 可以執行，因為 Allow 在最後
C. 只有 Deny 在 Allow 前面時才會拒絕
D. 兩個版本都會被拒絕

### 答案

**D**

### 理由

IAM Policy 評估不是依照規則排列順序決定結果。

只要存在適用的 **Explicit Deny**，就會優先於 Allow，因此兩個版本都會拒絕 `iam:ListUsers`。

### 這題要記住

**Explicit Deny 優先於 Allow，Policy statement 的順序不會改變結果。**

---

## 第 3 題

從版本 A 開始：

```text
Allow：iam:ListUsers
Deny：iam:ListUsers
```

以下每個選項都獨立處理，哪兩個做法可以讓 `iam:ListUsers` 執行？

A. 刪除 Allow，只保留 Deny
B. 刪除 Deny，只保留 Allow
C. 同時刪除 Allow 與 Deny
D. 只修改 Deny 的 `Sid`
E. 再增加一條 Allow，但保留原本的 Deny

### 答案

**B、C**

### 理由

依照題目設定，B、C 是可以讓 `iam:ListUsers` 執行的做法。

### 這題要記住

**Explicit Deny 會優先於 Allow；調整 Policy 中的規則後，要看題目設定下是否還存在會影響該操作的拒絕規則。**

`Sid` 只是 statement 的識別名稱，單純修改 `Sid` 不會改變原本的授權效果。
