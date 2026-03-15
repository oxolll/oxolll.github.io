---
title: "LeetCode 121. Best Time to Buy and Sell Stock"
date: 2026-03-15T22:45:00+08:00
lastmod: 2026-03-15T22:45:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Dynamic Programming", "Greedy"]
keywords: ["LC121", "N/A", "Easy", "Stock", "Max Profit", "One Pass"]
description: "LeetCode 第 121 題：Best Time to Buy and Sell Stock。難度評分：N/A (經典面試題)。探討如何透過一次遍歷並動態維護歷史最低價，以 O(N) 的時間複雜度求得買賣股票的最大利潤。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 121](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(N/A)</span>    
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個陣列 `prices`，其中 `prices[i]` 代表給定股票在第 $i$ 天的價格。
你只能選擇**某一天**買入一股股票，並選擇在**未來的某一個不同的日子**賣出該股股票，以獲取最大利潤。

請回傳你能從這筆交易中獲得的**最大利潤**。如果你無法獲取任何利潤，請回傳 `0`。

### 範例
- **Input**: `prices = [7,1,5,3,6,4]`
  **Output**: `5`
  **Explanation**: 在第 2 天（價格 = 1）買入，並在第 5 天（價格 = 6）賣出，利潤 = $6 - 1 = 5$。
  注意，利潤不能是 $7 - 1 = 6$，因為賣出日期必須大於買入日期。

- **Input**: `prices = [7,6,4,3,1]`
  **Output**: `0`
  **Explanation**: 在這種情況下，沒有任何交易可以獲利，最大利潤為 0。

### 限制條件
- $1 \le prices.length \le 10^5$
- $0 \le prices[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (動態維護歷史最低價的一趟掃描法)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
要在股票市場中獲取最大利潤，核心原則就是「低買高賣」。
但我們不能預知未來，只能依序看過每一天的價格。如果在第 $i$ 天賣出股票，要讓這天的利潤最大化，我們唯一能做的就是「在第 $i$ 天之前的最低點買入」。
因此，我們不需要寫兩層迴圈去枚舉所有的買賣組合。我們只需要在遍歷每一天價格的同時，順手記錄下「到目前為止出現過的最低價格」，並假設自己就在今天把股票賣掉，計算能賺多少錢。不斷去刷新這個最大利潤，走到最後一天時，最佳解自然就浮現了。

### 🛠️ 解題思路 (Approach)
1. **初始化狀態**：
   - `mx = 0`：用來記錄全局的最大利潤，預設為 0（如果全盤皆輸就不交易）。
   - `lowest_prices = INT_MAX / 2`：用來記錄目前為止遇過的歷史最低價，初始設為一個極大值。
2. **一次遍歷 (One Pass)**：
   - 遍歷陣列中的每一個價格 `prices[i]`。
   - 假設今天賣出：當前的潛在利潤為 `prices[i] - lowest_prices`。若這個利潤大於歷史最大利潤 `mx`，則更新 `mx`。
   - 更新歷史最低價：比較當前價格 `prices[i]` 與 `lowest_prices`，若當前價格更低，則將歷史最低價更新為 `prices[i]`，作為未來日子賣出時的參考買點。
3. **回傳結果**：迴圈結束後，回傳紀錄到的最大利潤 `mx`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需對長度為 $N$ 的價格陣列進行一次線性掃描。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數級別的變數 `mx` 與 `lowest_prices` 來維護狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int mx = 0;
        int lowest_prices = INT_MAX / 2;
        
        for(int i = 0; i < n; ++i) {
            // 嘗試在今天賣出，結算利潤並挑戰全局最大值
            mx = max(mx, prices[i] - lowest_prices);
            
            // 隨時更新歷史最低價，作為往後天數的參考買點
            lowest_prices = min(lowest_prices, prices[i]);
        }

        return mx;
    }
};

// 數學/DP 視角：
// dp[i] := 在第 i 天賣出股票所能獲得的最大利潤
// dp[i] = max{ 0, prices[i] - min(prices[j]) } 其中 0 <= j < i
```

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