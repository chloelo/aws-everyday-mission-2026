[Day 09｜移出 Group 後，哪些權限會受到影響？](https://hackmd.io/@hex-course/HkMEdtPKze)

# Day 09｜移出 Group 後，哪些權限會受到影響？

## 題目情境

一個 IAM User 的權限可以來自不同來源。

使用者可以：

* 直接在 IAM User 上附加 Policy
* 透過加入 IAM Group，取得該 Group 所附加的 Policy

因此，將使用者移出某個 Group 後，只會失去**透過該 Group 取得的權限**，直接附加在 User 上的 Policy 不會因此被移除。

### 小芸職務調動：移出 Editors Group

調動前，小芸有以下兩份權限：

| 權限來源          | 附加位置                           | 允許的操作                  |
| ------------- | ------------------------------ | ---------------------- |
| Lookup Policy | 直接附加在小芸的 IAM User 上            | `iam:ListUsers`：列出使用者  |
| Edit Policy   | 附加在 Editors Group，小芸是 Group 成員 | `iam:UpdateUser`：修改使用者 |

這次調動**只把小芸移出 Editors Group**。

兩份 Policy 都沒有 `Deny`，也沒有其他授權或限制。

假設後台設定已經生效，不考慮系統同步延遲。

---

## 第 1 題－調動前的修改權限來自哪裡？

根據調動前的授權表，小芸的 `iam:UpdateUser` 權限來自哪一項關係或設定？

A. 小芸是 Editors Group 的成員，而這個 Group 附加了允許 UpdateUser 的 Edit Policy。
B. 小芸是 Editors Group 的成員，因為 Group 名稱中有 Editors，就代表她可以修改使用者。
C. Lookup Policy 直接附加在小芸的 IAM User 上，比 Group 的政策優先，因此也允許修改使用者。
D. 小芸已能登入，而且 UpdateUser 沒有被 Deny 拒絕，所以預設就能執行。

### 答案

**A**

### 理由

小芸的 `iam:UpdateUser` 權限是透過：

`IAM User → Editors Group → Edit Policy`

取得的。

Group 名稱本身不會產生權限，真正授予操作權限的是附加的 Policy。

### 這題要記住

**Group 負責集中管理 User，Policy 才是定義「可以做什麼」的地方。**

---

## 第 2 題－移出 Editors Group 後會發生什麼事？

將小芸移出 Editors Group，且變更已生效後，以下哪兩項說法正確？

A. 小芸仍能執行 `iam:ListUsers`，因為直接附加在 User 上的 Lookup Policy 沒有被移除。
B. 小芸的 IAM User 會隨著 Editors Group 成員資格一起被刪除。
C. 只要這次登入的工作階段還沒結束，小芸就仍能執行 `iam:UpdateUser`。
D. 小芸之後提出的 `iam:UpdateUser` 請求會被拒絕，因為已經沒有任何來源允許這項操作。
E. 小芸必須重新啟用 MFA，才能重新取得 Lookup Policy 提供的查詢權限。

### 答案

**A、D**

### 理由

移出 Editors Group 後，小芸仍保有直接附加在 User 上的 Lookup Policy，因此 `iam:ListUsers` 不受影響。

但原本的 `iam:UpdateUser` 只有透過 Editors Group 的 Edit Policy 取得。移出 Group 後，已經沒有其他來源授予這項權限，因此新的請求會被拒絕。

### 這題要記住

**移出 Group ≠ 刪除 User。**

移除的是：

> User 與 Group 的成員關係

因此只會影響透過該 Group 取得的權限。

---

## 第 3 題－User 上也有修改權限時

另一種情況：

除了原本的授權之外，小芸的 IAM User 上**原本還有直接附加另一份允許 `iam:UpdateUser` 的 Policy**。

這次同樣只把她移出 Editors Group，沒有 Deny 或其他限制。

變更生效後，以下哪一項正確？

A. UpdateUser 會被拒絕，因為離開 Group 時，User 上允許相同操作的直接授權也會一起失效。
B. UpdateUser 仍可執行，但範圍會縮小到只能修改 Editors Group 的成員。
C. UpdateUser 仍可執行，因為直接附加在 User 上的另一份 Policy 仍允許這項操作。
D. UpdateUser 會被拒絕，必須等管理員重新附加那份直接授權的 Policy，才會生效。

### 答案

**C**

### 理由

這次雖然移除了 Editors Group 提供的 `iam:UpdateUser` 權限，但 User 本身還有另一份直接附加的 Policy 允許相同操作。

因此，只要仍有適用的 Allow，`iam:UpdateUser` 就可以繼續執行。

### 這題要記住

**同一個權限可以有多個來源。**

移除其中一個來源，不代表其他來源的權限也會消失。

```text
小芸
├─ User 直接附加 Policy
│   └─ UpdateUser
│
└─ Editors Group
    └─ Edit Policy
        └─ UpdateUser
```

移出 Editors Group 後：

```text
小芸
└─ User 直接附加 Policy
    └─ UpdateUser  ← 仍然存在
```

所以 `UpdateUser` 仍然可以執行。
