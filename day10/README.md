[Day 10｜能扮演 Role，為什麼還是不能刪除報表？](https://hackmd.io/@hex-course/Hk8KuKDFfg)

# Day 10｜能扮演 Role，為什麼還是不能刪除報表？

## 情境

IAM Role 是一種暫時性的身分，不一定直接綁定某一位使用者。

如果使用者被授權可以扮演某個 Role，就可以切換成該 Role，並取得有使用期限的臨時憑證。

今天有一位新人：

* 成功扮演 `ReportReader` Role
* 取得臨時憑證
* 可以成功查詢報表
* 但刪除報表時收到 `AccessDenied`
* `ReportReader` 的操作權限只允許查詢報表，沒有刪除報表的權限
* 沒有其他額外的 Deny 或限制

題目中的圖示包含：

* Trust Policy
* Permissions Policy
* 臨時憑證的有效期限

---

## 第 1 題

Trust Policy 與 Permissions Policy 分別負責什麼？

A. Trust Policy 決定 Role 可以執行哪些操作，Permissions Policy 決定誰可以扮演 Role。

B. Trust Policy 決定誰可以扮演 Role，Permissions Policy 決定扮演後可以執行哪些操作。

C. Trust Policy 與 Permissions Policy 都是用來決定 Role 可以執行哪些操作。

D. Trust Policy 與 Permissions Policy 都是用來管理 IAM User。

### 答案

B

### 理由

Trust Policy 負責判斷「誰可以扮演這個 Role」；Permissions Policy 則負責判斷「扮演 Role 後可以做什麼」。

---

## 第 2 題

新人已經成功扮演 `ReportReader`，為什麼還是不能刪除報表？

A. 因為 Role 的憑證是暫時的，所以只能查詢，不能刪除。

B. 因為 Role 必須等待一段時間，才會取得完整權限。

C. 因為 `ReportReader` 的 Permissions Policy 只允許查詢報表，沒有授予刪除權限。

D. 因為刪除權限是由 Trust Policy 決定的。

### 答案

C

### 理由

成功扮演 Role 只代表取得這個 Role 的身分與臨時憑證，不代表所有操作都可以執行。實際能做哪些事情，要看 Role 的 Permissions Policy。

### 這題要記住

「能不能扮演 Role」和「扮演後能做什麼」是兩件不同的事：

* Trust Policy → 誰可以扮演
* Permissions Policy → 扮演後可以做什麼

---

## 第 3 題

後來臨時憑證已經過期，但新人仍然有權限再次扮演 `ReportReader`；新人自己的 IAM User 沒有任何報表權限。

此時哪些敘述正確？

A. 把過期的臨時憑證複製到另一台電腦，就可以繼續使用。

B. 改回使用自己的 IAM User，就可以繼續查詢報表。

C. 重新取得有效的臨時憑證後，可以再次使用 `ReportReader` 原本被授予的查詢權限。

D. 如果仍然沒有刪除權限，重新扮演 `ReportReader` 後也不會自動取得刪除權限。

E. 重新扮演 Role 後，Role 的 Permissions Policy 會自動增加刪除權限。

### 答案

C、D

### 理由

臨時憑證過期後，需要重新取得有效憑證才能使用 Role 的權限；但重新取得憑證不會改變 Role 原本的 Permissions Policy。

因此：

* `ReportReader` 原本有查詢權限 → 重新扮演後仍可查詢
* `ReportReader` 原本沒有刪除權限 → 重新扮演後仍然不能刪除

### 這題要記住

Role 的「臨時性」主要是指取得的憑證有有效期限，不代表 Role 本身的權限會隨時間改變。

**重新扮演 Role = 重新取得臨時憑證，不是重新設定權限。**
