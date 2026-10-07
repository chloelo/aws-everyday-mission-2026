[Day 07｜如何用 User、Group 與 Policy 管理同事的權限？](https://hackmd.io/@hex-course/B1PSvKvtfx)

# Day 07｜如何用 User、Group 與 Policy 管理同事的權限？

## 情境

IAM User 是使用者的身分，Group 用來集中管理使用者，Policy 則是一份描述權限的文件。

Policy 會使用：

* `Effect`：允許或拒絕
* `Action`：允許或拒絕的操作
* `Resource`：適用的資源

將 Policy 附加到 Group 後，Group 的成員就能取得該 Policy 授予的權限。

團隊有三位要做相同查詢工作的同事，希望將他們放在同一個集合中集中管理權限。每位同事仍然使用自己的登入身分。

圖中以小芸為例：

* 小芸是 IAM User。
* `ReadOnlyLookup` 是一份 Policy。
* Policy 內容為 `Effect: Allow`、`Action: iam:ListUsers`、`Resource: *`。
* 目前沒有其他權限來源或限制。

---

## 第 1 題－ReadOnlyLookup 是什麼？

根據交接圖中列出的內容，以下哪一項能正確說明 `ReadOnlyLookup` 是什麼？

A. 三位同事共用來登入 Console 的帳號。
B. 名稱標示為唯讀、目前只有小芸一名成員的使用者集合。
C. 記錄允許或拒絕哪些操作的 Policy 文件。
D. 查詢時暫時扮演、附有到期時間的唯讀身分。

### 答案

**C**

### 理由

`ReadOnlyLookup` 的內容包含 `Effect`、`Action` 與 `Resource`，這些都是用來描述權限規則的內容，因此它是 **Policy 文件**。

### 這題要記住

**Policy = 描述「可以做什麼」的權限文件。**

---

## 第 2 題－如何補上交接圖中的關係？

為了符合團隊的管理需求，交接圖中的甲與乙應分別填入什麼？

A. 甲是每位同事各自的 IAM User；乙是把 Policy 分別直接附加到三個 User，不另外建立集合。
B. 甲是 IAM Group；乙是把 Policy 附加到這個 Group，讓每位成員取得相應的權限。
C. 甲是 IAM Role；乙是把三位同事的 IAM User 加入 Role，讓 Role 成為使用者集合。
D. 甲是 Policy；乙是把兩份 Policy 合併，讓小芸成為 Policy 的成員。

### 答案

**B**

### 理由

IAM Group 可以集中管理多個 IAM User。將 Policy 附加到 Group 後，Group 成員就能取得該 Policy 授予的權限。

這樣之後有人加入或離開，只需要調整 Group 的成員，不需要逐一修改每個 User 的權限。

### 這題要記住

三者可以先這樣記：

**User → 加入 Group → Group 附加 Policy**

* User：使用者身分
* Group：集中管理 User
* Policy：定義權限

---

## 第 3 題－小明加入相同 Group 後

小明加入相同的 Group，並使用自己的 IAM User。

這個 Group 附加的政策允許查詢，沒有其他政策或限制。

以下哪兩項說法正確？（複選）

A. 小明可以透過這個 Group 取得相應的查詢權限。
B. 小明必須使用小芸的密碼，才能取得這個 Group 的權限。
C. 還需要在 ReadOnlyLookup 裡列出小明的名字，他的查詢權限才會生效。
D. 小明仍使用自己的 IAM User 操作，Group 的成員管理不會取代他自己的登入身分。
E. 小明加入這個 Group 後，也會一併取得扮演帳號內既有 Role 的資格。

### 答案

**A、D**

### 理由

小明加入 Group 後，就能取得附加在 Group 上的 Policy 所授予的權限。

但 Group 不會取代使用者自己的 IAM User，因此小明仍然使用自己的身分登入與操作。

加入這個 Group 也不代表會自動取得其他 Role 的扮演資格。

### 這題要記住

**Group 管理成員，Policy 管理權限，User 仍然是使用者自己的登入身分。**

加入 Group 不等於變成 Group，也不會因此自動取得其他 Role 的權限。
