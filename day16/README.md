[Day 16｜從 Web 與 DB 看連線方向](https://hackmd.io/@hex-course/rJN_dkBqfl)

# Day 16｜從 Web 與 DB 看連線方向

## 題目情境

判斷一條新連線是 Inbound 還是 Outbound，要先確認是相對於哪個資源來看：其他裝置或資源連進來，對接收端是 Inbound；由該資源向外發起，對發起端是 Outbound。同一條連線在發起端和接收端看來，方向名稱會不同。

服務 port 是接收端提供服務的入口。本題中的 Web HTTPS 使用 TCP 443，DB PostgreSQL 使用 TCP 5432。

會員預約時有兩條分別發起的新連線：

| 連線 | 發起端 → 接收端 | 接收端的服務 port |
| --- | --- | --- |
| ① | 會員瀏覽器 → Web | Web 的 HTTPS TCP 443 |
| ② | Web → DB | DB 的 PostgreSQL TCP 5432 |

箭頭只表示連線發起方向，沒有畫出回應。本題只需確認接收端的服務 port，並判斷連線對 Web 或 DB 是 Inbound 還是 Outbound；沒有提供完整安全規則，因此不判斷連線能否成功。

## 第 1 題－連線②的服務 port（單選）

連線②是 Web 向 DB 發起的 PostgreSQL 連線。這條連線的接收端服務 port 是哪個？

A. DB 的 TCP 5432，因為連線②的接收端是 DB。  
B. Web 的 TCP 443，因為連線①已經使用這個 port。  
C. Web 的 TCP 5432，因為連線②是由 Web 主動發起的。  
D. 會員瀏覽器的 TCP 443，因為預約從瀏覽器開始。

### 答案

A

### 理由

連線②的接收端是 DB，而 DB 提供 PostgreSQL 服務，使用 TCP 5432。Web 是發起端，不能因此把 DB 的服務 port 算到 Web 上。

## 第 2 題－從 Web 與 DB 判斷方向（複選，請選 2 項）

根據圖中的連線①、②，以下哪兩項 Inbound／Outbound 判斷正確？

A. ① 對 Web 是 Outbound，因為請求是瀏覽器主動發出的。  
B. ① 對 Web 是 Inbound，因為連線是從瀏覽器進入 Web。  
C. ② 對 Web 是 Inbound，因為 DB 的 5432 是接收服務的 port。  
D. ② 對 DB 是 Outbound，因為 Web 會向 DB 取回資料。  
E. ② 對 DB 是 Inbound，因為這條新連線從 Web 進入 DB。

### 答案

B、E

### 理由

① 從瀏覽器進入 Web，所以對 Web 是 Inbound。② 從 Web 進入 DB，所以對 DB 是 Inbound。判斷方向時，要看連線相對於哪個資源，而不是只看服務 port 或預期取得的資料。

## 第 3 題－Web 另連到金流服務（單選）

這一題新增連線③：結帳時，Web 主動連到外部金流服務的 TCP 443（Web → 金流）。連線①仍是會員瀏覽器 → Web。只看①和③這兩條新連線的發起方向：若要檢查 **Web 掛載的 Security Group（SG）**，它們分別應對照 Inbound rules 還是 Outbound rules？

A. ①和③都對照 Web 的 Inbound rules，因為兩條連線的接收端都使用 TCP 443。  
B. ①和③都對照 Web 的 Outbound rules，因為 Web 在這兩條連線裡都有送資料出去。  
C. ①對照 Web 的 Inbound rules；③只查金流端的 Inbound rules，Web 端不需對照。  
D. ①對照 Web 的 Inbound rules；③對照 Web 的 Outbound rules。

### 答案

D

### 理由

① 是瀏覽器連進 Web，對 Web 是 Inbound；③ 是 Web 主動連到金流服務，對 Web 是 Outbound。兩條連線即使都使用 TCP 443，方向仍依發起端和接收端判斷。

### 這題要記住

Inbound 和 Outbound 是相對於某個資源的方向。新連線從瀏覽器進入 Web，就要從 Web 的 Inbound 方向看；新連線由 Web 發起，就要從 Web 的 Outbound 方向看。接收端的服務 port 則依該接收端提供的服務判斷。