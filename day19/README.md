[Day 19｜Web 換 IP 後，DB 的 SG 來源還匹配嗎？](https://hackmd.io/@hex-course/BJSyAgIcGe)

# Day 19｜Web 換 IP 後，DB 的 SG 來源還匹配嗎？

## 題目情境

Security Group（SG）的 Inbound 規則會用 Source 指定哪些來源可以連入。Source 可以寫成 IPv4 位址範圍，也可以引用一個 SG，依來源資源目前是否掛載該 SG 來辨認來源。

本題中 Web 與 DB 位於同一個 VPC，使用私有 IP 直接連線。若來源主機掛載了 DB 規則引用的 SG，且連線符合該規則的 Port，就符合這條規則的來源與 Port 條件。引用 SG 只用來辨認來源，不會將被引用 SG 的規則複製到接收端。

Web 主機要連向 DB 的 TCP 5432，之後 Web 被另一台新主機替換：

| 項目 | 替換前 | 替換後 |
| --- | --- | --- |
| Web 私有 IP | `10.0.1.10` | `10.0.1.30` |
| Web 掛載的 SG | `web-sg` | 同一個 `web-sg` |
| DB 入站規則（僅此一條） | TCP 5432，Source=`web-sg` | 未修改 |

另一台主機 `10.0.1.40` 與新 Web 位於同一個 Subnet，但沒有掛載 `web-sg`。

本題只判斷 DB 的入站規則；來源主機 SG 的 Outbound 規則、路由、NACL 與服務皆正常。

## 第 1 題－DB 規則引用的是什麼（單選）

DB 的 TCP 5432 入站規則將 Source 設為 `web-sg`。在本題同 VPC、私有 IP 直接連線的情況下，這個來源條件表示什麼？

A. 來源機器有掛 `web-sg` 就符合來源條件；port 仍看 DB 這條寫的 5432。  
B. DB 會沿用 `web-sg` 的入站規則，Web 開的 443 也對 DB 生效。  
C. 來源機器掛著 `web-sg`，而且 `web-sg` 自己的入站規則也要允許 DB，這條才算符合。  
D. 來源機器要掛 `web-sg`，而且得是建立這條 DB 規則時就已經掛著的那幾台。

### 答案

A

### 理由

DB 用 `web-sg` 判斷來源主機是否符合 Source 條件；連線也必須符合 DB 這條規則指定的 TCP 5432。這個引用不會把 `web-sg` 上其他規則套用到 DB。

## 第 2 題－替換 Web 後的來源匹配（複選，請選 2 項）

Web 替換後，以下哪兩項說法正確？

A. 新 Web 的 IP 已改變，因此必須把 DB 的 Source 改成 `10.0.1.30/32` 才會匹配。  
B. 新 Web 仍掛載被參照的同一個 SG，因此原 DB 規則仍能匹配它的 TCP 5432 連線。  
C. `10.0.1.40` 與新 Web 在同一 Subnet，位置相同，因此也符合 Source＝`web-sg`。  
D. `10.0.1.40` 沒掛載 `web-sg`，同 Subnet 不能代替這條來源條件。  
E. DB 引用了 `web-sg`，所以也會繼承 Web 對外開放的 HTTPS 入站規則。

### 答案

B、D

### 理由

新 Web 的 IP 雖然不同，但仍掛載 DB 規則引用的 `web-sg`，因此符合來源條件。`10.0.1.40` 沒有掛載該 SG；即使同在一個 Subnet，也不符合這條規則的 Source 條件。

## 第 3 題－Web 保留 IP、改掛 SG（單選）

這一題改用另一次變更：Web 的私有 IP 維持 `10.0.1.30`，但現在只掛載 `new-web-sg`，已移除 `web-sg`。DB 的 TCP 5432 入站規則仍只引用原本的 `web-sg`，其他條件皆正常。這條 DB 入站規則是否仍會匹配 Web？

A. 仍匹配，因為 DB 規則沒改，而且 Web 的私有 IP 也沒變。  
B. 不再匹配，因為 Web 已不再掛著 DB 規則引用的 `web-sg`。  
C. 仍匹配，因為 `new-web-sg` 接替了 `web-sg`，DB 規則的引用會跟著換過去。  
D. 不再匹配，除非把 `new-web-sg` 改名成 `web-sg`，DB 規則才會重新認得它。

### 答案

B

### 理由

DB 的 Source 條件仍指定 `web-sg`，而 Web 已移除這個 SG、改掛 `new-web-sg`，因此不再符合來源條件。私有 IP 沒變不會改變 SG 的掛載狀態。

### 這題要記住

引用 SG 作為 Source，是依來源主機是否掛載該 SG 來辨認，不是依 IP、Subnet 位置或名稱相似度判斷。被引用 SG 上的規則也不會因此複製到接收端。