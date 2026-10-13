[Day 13｜現有設定能證明 public-orders 是 Public Subnet 嗎？](https://hackmd.io/@hex-course/BJ-Icxz9fx)

# Day 13｜現有設定能證明 public-orders 是 Public Subnet 嗎？

## 題目情境

同一個 VPC 可能有多張路由表，不同 Subnet 也可能使用不同的路由表。判斷某個 Subnet 是否具有 Public Subnet 的路由特徵，必須先確認它實際使用哪張路由表，再檢查該表是否有直接通往 Internet Gateway（IGW）的路由。

同事提供了名為 `public-orders` 的 Subnet 設定摘錄：

```text
Subnet 名稱：public-orders
自動指派公有 IPv4：開啟
所屬 VPC：livefit-vpc
IGW：已連接到 livefit-vpc
public-orders 使用的路由表：這張摘錄沒有顯示
該表的路由內容：這張摘錄沒有顯示
```

這些資訊尚未指出 `public-orders` 使用哪張路由表，也沒有提供該路由表的內容。以下各題只根據題目提供的設定判斷路由特徵，不判斷 Subnet 內的主機能否實際連上 Internet。

## 第 1 題－找到一張有 IGW 路由的表（單選）

同事後來多找到一張名為 `rt-public` 的路由表，裡面有 `0.0.0.0/0 → IGW`，但還沒查到 `public-orders` 是否使用這張表。這份證據目前能支持哪一項說法？

A. 已找到一張有 IGW 路由的表，但仍需確認 `public-orders` 是否實際使用它。  
B. 這張表有 IGW 路由，同一個 VPC 的其他 Subnet 也都能沿用這條路由。  
C. `rt-public` 的名稱與 `public-orders` 對得上，這已經算是兩者互相關聯的證據。  
D. 即使 `public-orders` 沒有明確指定路由表，它應該也會預設使用 `rt-public`。

### 答案

A

### 理由

`rt-public` 有 IGW 路由，只能說明這張路由表的內容。還需要確認 `public-orders` 是否使用它；如果沒有明確指定路由表，則要確認 Subnet 使用的 main route table。

## 第 2 題－還需要哪些設定資訊（複選，請選 2 項）

回到前面的設定摘錄，只根據其中提供的資料，要判斷 `public-orders` 是否具有 Public Subnet 的路由特徵，還需要哪兩項資訊？

A. 這個 Subnet 實際使用哪張路由表，包含未明確關聯時使用的 main route table。  
B. 這個 Subnet 裡是否至少有一台主機已拿到公有 IPv4，有的話大致可視為 Public。  
C. 這個 Subnet 的 NACL 是否已放行進出流量。  
D. 這個 VPC 的 CIDR 範圍比 Subnet 大多少。  
E. 這個 Subnet 實際使用的路由表有哪些路由項目，並確認其中是否有路由直接指向已連接的 IGW。

### 答案

A、E

### 理由

先找出 `public-orders` 實際使用的路由表，包括可能使用的 main route table；接著檢查該表是否有直接指向 IGW 的路由。其他資訊不能取代這兩項路由設定的確認。

## 第 3 題－補齊關聯後重新判斷（單選）

前面的設定摘錄沒有顯示 `public-orders` 使用哪張路由表，這一題補上以下資料：

- `public-orders` 確定沒有明確指定路由表
- 它所屬 VPC 的 main route table 是 `rt-main`，這張表的完整內容只有 local
- 先前找到的 `rt-public` 雖有 IGW 路由，卻沒有與 `public-orders` 關聯

根據以上資料，目前能得出什麼結論？

A. 自動指派已開啟，通常就代表它仍有一條直接 IGW 路由可用。  
B. 必須把 `rt-main` 與 `rt-public` 的路由合併起來看，才知道它的路由。  
C. VPC 已連接 IGW，所以即使它使用 `rt-main`，也已經有直接對外的路。  
D. 它目前使用 `rt-main`，而這張表裡沒有直接通往 IGW 的路由。

### 答案

D

### 理由

`public-orders` 沒有明確關聯其他路由表，因此使用 VPC 的 main route table，也就是 `rt-main`。這張表只有 local，沒有直接通往 IGW 的路由；`rt-public` 的內容不會套用到沒有與它關聯的 Subnet。

### 這題要記住

判斷 Subnet 的路由特徵，要看它實際使用的路由表。Subnet 名稱、自動指派公有 IPv4，以及 VPC 有沒有連接 IGW，都不能代替這項確認。