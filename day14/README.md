[Day 14｜如何兼顧 App 與 Audit 的路由需求變更？](https://hackmd.io/@hex-course/HyuWOWGcfg)

# Day 14｜如何兼顧 App 與 Audit 的路由需求變更？

## 題目情境

每個 Subnet 同一時間會使用一張路由表。若沒有明確指定路由表，就會使用 VPC 的 main route table；同一張路由表也可以供多個 Subnet 使用。

App 與 Audit 目前的設定如下：

| 項目 | 目前設定 |
| --- | --- |
| main route table | `rt-main`，只有 `10.0.0.0/16 → local` |
| `rt-public` | 有 `10.0.0.0/16 → local` 與 `0.0.0.0/0 → IGW` |
| App Subnet | 未明確指定路由表，使用 `rt-main` |
| Audit Subnet | 未明確指定路由表，使用 `rt-main` |

這次的變更需求是：

- App Subnet 要使用現成的 `rt-public`，取得直接通往 IGW 的路由。
- Audit Subnet 必須繼續只有 VPC 內部的 local 路由。
- IGW 已連接到這個 VPC，兩張路由表的內容也都已正確設定。

## 第 1 題－目前的路由表關聯與影響範圍（複選，請選 2 項）

根據目前的設定，以下哪兩項說法正確？

A. App 還沒有明確指定，所以目前應該沒有路由表在用。  
B. 兩者目前都使用 `rt-main` 這張 main route table。  
C. 每張 Route Table 最多只能關聯一個 Subnet。  
D. `rt-public` 已存在於同一個 VPC，App 目前應該也能用到它的 IGW 路由。  
E. 此時修改 `rt-main` 的路由會改變兩者使用的路由內容。

### 答案

B、E

### 理由

App 和 Audit 都沒有明確指定路由表，因此目前都使用 VPC 的 main route table `rt-main`。兩個 Subnet 共用同一張表，所以修改 `rt-main` 的路由內容會影響兩者。

## 第 2 題－選擇符合兩個需求的操作（單選）

以下每個選項各是一項設定操作。哪一項可以同時滿足 App 與 Audit 的需求？

A. 將 VPC 的 main route table 從 `rt-main` 改為 `rt-public`；Audit 未明確指定，通常不受影響。  
B. 將 App Subnet 明確關聯到現成的 `rt-public`。  
C. 在 `rt-main` 新增 `0.0.0.0/0 → IGW`。  
D. 把 App Subnet 改名為 `public-app`，讓它對應 `rt-public`。

### 答案

B

### 理由

將 App 明確關聯到 `rt-public` 後，App 就會使用含有 IGW 預設路由的路由表；Audit 沒有變更，仍使用只有 local 路由的 `rt-main`。

## 第 3 題－取消剛建立的明確關聯（單選）

假設已將 App 明確關聯到 `rt-public`，但另一位同事又把 App 改回使用 main。`rt-main` 仍是 main，兩張表的路由內容也都沒有改變。這項變更會造成什麼結果？

A. App 改回 main 之後，先前經由 `rt-public` 取得的 IGW 路由多半仍會跟著它保留。  
B. App 回到 `rt-main` 的 local 路由；Audit 仍使用 `rt-main`。  
C. `rt-public` 不再被 Subnet 使用後就會被清掉，之後要用得再建一張。  
D. Audit 會接著改用 `rt-public`，補上 App 空出的關聯位置。

### 答案

B

### 理由

App 改回使用 main 後，就會使用 `rt-main` 現有的路由內容；這張表只有 local。Audit 的設定沒有變更，因此仍使用 `rt-main`。

### 這題要記住

Subnet 使用哪張路由表，取決於它的明確關聯或 VPC 的 main route table。多個 Subnet 可以共用一張表，因此修改共用表的路由會影響所有使用它的 Subnet。Subnet 名稱不會改變路由表關聯。