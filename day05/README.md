[Day 05｜收到預估超支通知，已累計成本就超過預算了嗎？](https://hackmd.io/@hex-course/BkmrA3XYfe)

# Day 05｜收到預估超支通知，已累計成本就超過預算了嗎？

## 情境

AWS Budgets 可以設定每月預算，並透過 Actual Cost 與 Forecasted Cost 來觀察目前成本與預估月底成本。

* **Actual**：目前這個月已經累計的實際成本。
* **Forecasted**：依照目前的使用情況，預估月底整個月的累計成本。
* 比較成本時，要使用一致的成本計算範圍。
* 本題設定的月預算為 **US$30**。
* 只設定 **Email 通知**，沒有設定 **Budget Action**。

上午收到通知時：

* Actual：**US$12**
* Forecasted：**US$45**

之後調整使用方式：

* Actual：**US$12**
* Forecasted：**US$25**

後續的 Forecasted US$25 代表該次檢查時記錄的預估值，不代表調整使用方式後立即重新計算出的結果。

---

## 第 1 題

上午收到通知時，哪一個金額代表目前已經累計的成本？

A. US$12
B. US$30
C. US$45
D. US$57

### 答案

**A**

### 理由

Actual 代表目前已經累計的實際成本，因此上午的累計成本是 **US$12**。

US$30 是月預算，US$45 是 Forecasted，代表預估月底的總成本，不能把 Actual 與 Forecasted 再相加。

### 這題要記住

**Actual = 目前已經發生的成本**
**Forecasted = 預估月底的總成本**

兩者不是兩筆不同的費用。

---

## 第 2 題

根據上述紀錄，以下哪些說法正確？（複選）

A. 上午已經花費 US$45。
B. Forecasted 從 US$45 降到 US$25，代表已經退回 US$20 的費用。
C. Forecasted US$25 雖然低於 US$30 的月預算，月底仍可能超支。
D. 後續沒有再收到 Email，代表 AWS 已經自動關閉資源。
E. 兩次紀錄的 Actual 都是 US$12，因此不能把 US$25 再加到 US$12 上當成實際帳單。

### 答案

**C、E**

### 理由

Forecasted US$25 低於目前 US$30 的預算，但它只是預估值，之後使用量增加仍可能造成月底超支。

另外，Forecasted 是整個月的預估總成本，不是 Actual 之外另外產生的費用，因此不能將 US$25 與 US$12 相加。

---

## 第 3 題

後續出現新的成本紀錄：

* 月預算：**US$30**
* Actual：**US$32**
* Forecasted：**US$35**
* 只有 Email 通知
* 沒有設定 Budget Action

此時應如何判斷？

A. Actual 超過預算，因此 AWS 會自動暫停新的收費資源。
B. Forecasted 已經從 US$45 降到 US$35，因此月底不會超支。
C. 月預算是 US$30，因此費用超過 US$30 後就不會繼續增加。
D. Actual 已超過預算，應先檢查費用來源與用量，再決定後續處理方式。

### 答案

**D**

### 理由

Actual US$32 已經超過 US$30 的月預算，但預算本身不是費用上限。

而且這份預算只有 Email 通知，沒有設定 Budget Action，因此 AWS 不會因為超過預算就自動暫停資源。此時應先確認是哪一些資源產生了費用，以及實際使用量，再決定後續處理方式。

### 這題要記住

**AWS Budgets 的預算不是硬性的費用上限。**

設定 Email 通知和設定 Budget Action 是不同的事情：

* Email：提醒你成本可能超支。
* Budget Action：符合條件時，才可能執行預先設定的動作。

