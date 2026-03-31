---
title: "LeetCode 714. Best Time to Buy and Sell Stock with Transaction Fee"
date: 2026-04-01T15:32:00+08:00
lastmod: 2026-04-01T15:32:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic-programming", "greedy"]
keywords: ["LC714", "Medium", "N/A", "Stock", "Transaction Fee", "State Machine DP"]
description: "LeetCode 第 714 題：Best Time to Buy and Sell Stock with Transaction Fee。難度評分：Medium (N/A)。探討如何利用狀態機動態規劃 (State Machine DP) 處理股票買賣手續費，並利用滾動變數將空間複雜度優化至 O(1)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 714](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Greedy`

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (二維狀態機 DP)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題是股票買賣系列的經典變形。由於沒有交易次數限制，但每一筆完整的交易（買入後賣出）需要支付 `fee` 的手續費，這迫使我們不能隨意進行高頻微利的交易。
採用狀態機 DP，定義每一天結束時的兩種狀態：
- `dp[i][0]`：第 $i$ 天結束時，手中**沒有股票**的最大利潤。
- `dp[i][1]`：第 $i$ 天結束時，手中**持有股票**的最大利潤。

**狀態轉移：**
- 要在今天手中沒有股票 (`dp[i][0]`)：可能是昨天就沒有股票繼續保持，或是昨天有股票但在今天「賣出」（賣出時支付手續費 `fee`，獲取 `prices[i-1]`）。
- 要在今天手中持有股票 (`dp[i][1]`)：可能是昨天就有股票繼續保持，或是昨天沒有股票但在今天「買入」（支出 `prices[i-1]`）。

### 🛠️ 解題思路 (Approach)
1. 宣告二維陣列 `dp`，大小為 `(n+1) x 2`。
2. 初始化第 0 天（還沒開始交易）的狀態：`dp[0][0] = 0`，`dp[0][1] = INT_MIN / 2` (除以 2 是為了防止在後續減去股價時發生 Underflow)。
3. 使用迴圈從第 1 天遍歷至第 $n$ 天，依據轉移方程式更新狀態。
4. 迴圈結束後，回傳最後一天手中沒有股票的利潤 `dp[n][0]`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        vector<vector<int>> dp(n + 1, vector<int>(2, 0));
        
        // 邊界條件設定，使用 INT_MIN/2 避免整數溢位
        dp[0][0] = 0;
        dp[0][1] = INT_MIN / 2;
        
        for(int i = 1; i <= n; ++i) {
            // 今天結束時沒有股票：保持空倉 vs 今天賣出 (賣出時扣除手續費)
            dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i-1] - fee);
            
            // 今天結束時持有股票：保持持倉 vs 今天買入
            dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i-1]);
        }

        // 交易結束時，不持有股票的利潤必定大於持有股票
        return dp[n][0];
    }
};
```
- **時間複雜度**: $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(N)$。

---

### 💡 進階最佳化：$\mathcal{O}(1)$ 滾動變數 (Rolling Variables)
觀察狀態轉移方程式，第 `i` 天的狀態僅依賴於第 `i-1` 天的狀態。因此我們可以捨棄整個 `dp` 陣列，只使用兩個變數 `hold` 與 `free` 來記錄前一天的狀態。

<details>
<summary><b>點擊展開 O(1) 空間最佳化實作程式碼</b></summary>

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int free = 0; // 手中無股票的利潤
        int hold = -prices[0]; // 手中持有股票的利潤
        
        for(int i = 1; i < prices.size(); ++i) {
            int next_free = max(free, hold + prices[i] - fee);
            int next_hold = max(hold, free - prices[i]);
            
            free = next_free;
            hold = next_hold;
        }
        
        return free;
    }
};
```
</details>

<script>
    MathJax = {
      tex: {
        inlineMath: [['$', '$'], ['\\(', '\\)']],
        displayMath: [['$$','$$'], ['\\[', '\\]']],
        processEscapes: true
      },
      options: {
        skipHtmlTags: ['script', 'noscript', 'style', 'textarea', 'pre']
      }
    };
</script>
<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>