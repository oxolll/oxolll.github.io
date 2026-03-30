---
title: "LeetCode 746. Min Cost Climbing Stairs"
date: 2026-03-31T10:20:00+08:00
lastmod: 2026-03-31T10:20:00+08:00
difficulty: 1358
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic programming"]
keywords: ["LC746", "1358", "Easy", "Min Cost", "Climbing Stairs", "Dynamic Programming"]
description: "LeetCode 第 746 題：Min Cost Climbing Stairs。難度評分：1358。探討如何定義動態規劃 (DP) 狀態以計算到達樓層頂部的最小花費，並解析空間優化至 O(1) 的實作技巧。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 746](https://leetcode.com/problems/min-cost-climbing-stairs/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1358)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `cost`，其中 `cost[i]` 是從樓梯第 `i` 個階梯向上爬的花費。一旦支付了該花費，你可以選擇向上爬一階或兩階。
你可以選擇從索引 `0` 或是索引 `1` 的階梯作為起點。
請回傳到達樓梯頂部（即跨過陣列最後一個元素）所需的最小總花費。

### 限制條件
- $2 \le cost.length \le 1000$
- $0 \le cost[i] \le 999$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-31：初次提交 (一維陣列 DP 狀態轉移)](#2026-03-31-初次提交)

---

## 💡 2026-03-31 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題是非常經典的線性動態規劃 (1D DP)。
我們定義 `dp[i]` 為：「到達第 $i$ 階樓梯（且尚未支付該階梯花費）所需的最小累積花費」。
因為我們只能從前一階爬一步上來，或是從前兩階爬兩步上來，所以要到達第 $i$ 階，我們必定是經歷了以下兩種路徑之一，並取其成本較小者：
1. 從第 $i-1$ 階爬上來：成本為 `dp[i-1] + cost[i-1]`。
2. 從第 $i-2$ 階爬上來：成本為 `dp[i-2] + cost[i-2]`。

根據題意，我們可以免費選擇從第 0 階或第 1 階起步，因此初始狀態為 `dp[0] = 0` 與 `dp[1] = 0`。頂層的位置為索引 $n$（陣列的最後一個元素之後），因此最終答案即為 `dp[n]`。

### 🛠️ 解題思路 (Approach)
1. 取得階梯數量 `n = cost.size()`。
2. 宣告長度為 $n + 1$ 的 DP 陣列，全初始化為 0。這自動滿足了基礎狀態 `dp[0] = 0` 與 `dp[1] = 0`。
3. 使用 `for` 迴圈從 $i = 2$ 遍歷至 $n$。
4. 執行狀態轉移方程式：`dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])`。
5. 迴圈結束後，回傳 `dp.back()` (即 `dp[n]`)。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int n = cost.size(); 
        vector<int> dp(n + 1, 0); // dp[i] 表示抵達第 i 階的最小總花費
        
        for(int i = 2; i <= n; ++i) {
            // 抵達第 i 階的最小花費，取決於前一階與前兩階的較優決策
            dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2]);
        }

        return dp.back();
    }
};
```
- **時間複雜度**: $\mathcal{O}(N)$。遍歷一次長度為 $N$ 的陣列。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了長度為 $N+1$ 的 `dp` 陣列。

---

### 💡 進階最佳化：$\mathcal{O}(1)$ 滾動變數 (Rolling Variables)
與費氏數列的優化原理相同，既然 `dp[i]` 永遠只依賴於前兩個狀態 `dp[i-1]` 和 `dp[i-2]`，我們可以使用兩個變數 `prev1` 和 `prev2` 來取代整條 `dp` 陣列，將空間複雜度極致壓縮至 $\mathcal{O}(1)$。

<details>
<summary><b>點擊展開 O(1) 空間最佳化實作程式碼</b></summary>

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int n = cost.size();
        int prev2 = 0; // 對應 dp[0]
        int prev1 = 0; // 對應 dp[1]
        
        for(int i = 2; i <= n; ++i) {
            int current = min(prev1 + cost[i-1], prev2 + cost[i-2]);
            prev2 = prev1;
            prev1 = current;
        }

        return prev1;
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