---
title: "🧩 LeetCode 799. Champagne Tower"
date: 2026-02-14T16:00:00+08:00
lastmod: 2026-02-14T16:05:00+08:00
draft: false
hidden: false
ShowToc: true
TocOpen: false
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Dynamic Programming", "Simulation"]
keywords: ["LC799", "1855", "香檳塔", "Champagne Tower"]
description: "LeetCode 第 799 題：Champagne Tower。難度評分：1855。透過模擬流體溢出的過程，從二維 DP 優化至一維空間的解法探討。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 799](https://leetcode.com/problems/champagne-tower/)  
> **難度評分**：<span style="color: #feb019; font-weight: bold;">1855</span> (Medium)
> **核心主題**：`Dynamic Programming` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
每個杯子容量為 1。倒入 `poured` 杯後，溢出的部分會均分給正下方左右兩個杯子。

### 限制條件
- $0 \le \text{poured} \le 10^9$
- $0 \le \text{query\_glass} \le \text{query\_row} < 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-02-14：初次提交 (2D Simulation)](#2026-02-14-初次提交)
- [2026-02-14：優化提交 (1D Space Optimization)](#2026-02-14-優化提交)

---

## 💡 2026-02-14 初次提交

### 直覺 (Intuition)
本題要求我們模擬香檳的流動過程。物理規則如下：
* 一個杯子最多只能盛裝 1 單位的液體。
* 任何多餘的液體 $(\text{total} - 1)$ 會平均溢流到其下方的兩個杯子（左下方與右下方）。
* 由於重力向下流動，我們可以逐行模擬此過程，以找出特定杯子的最終狀態。

### 解題思路 (Approach)
#### 1. 通用解法 (2D 矩陣)
最直觀的方法是使用 2D 矩陣 `dp[r][c]`：
* `dp[r][c]` 代表流入第 $r$ 行、第 $c$ 列杯子的液體總量。
* 若 `dp[r][c] > 1`，則溢流量為 `(dp[r][c] - 1) / 2.0`。
* 我們將此溢流量分別累加至 `dp[r+1][c]` 與 `dp[r+1][c+1]`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    double champagneTower(int poured, int query_row, int query_glass) {
        vector<vector<double>> dp(101, vector<double>(101, 0.0));
        dp[0][0] = poured;
        
        for(int r = 0; r <= query_row; ++r)
            for(int c = 0; c <= r; ++c) {
                double overflow = (dp[r][c] - 1) / 2.0;
                if(overflow > 0) 
                {
                    dp[r+1][c] += overflow;
                    dp[r+1][c+1] += overflow;
                }
            }
        
        return min(1.0, dp[query_row][query_glass]);
    }
};
```

---

## 💡 2026-02-14 優化提交

### 核心改進 (Approach)
#### 2. 空間優化 (1D 陣列)
由於第 $i+1$ 行的狀態僅取決於第 $i$ 行，我們可以將空間優化至 $O(N)$。

**關鍵技巧：逆向迭代 (Backward Iteration)**
* 使用 1D 陣列時，我們從後往前迭代（$j$ 從 $i$ 遞減至 $0$）。
* `dp[j]` 目前儲存的是當前行的值。
* 計算溢流量後，將其加入 `dp[j+1]`（成為下一行的右側子節點）。
* 接著將 `dp[j]` 覆蓋為溢流量（成為下一行的左側子節點）。
* 逆向迭代確保我們在利用 `dp[j]` 計算 `dp[j+1]` 的溢流之前，不會先覆蓋掉 `dp[j]` 的值。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    double champagneTower(int poured, int query_row, int query_glass) {
        vector<double> dp(101, 0);
        dp[0] = poured;
        
        for(int i = 0; i < query_row; ++i) 
            for(int j = i; j >= 0; --j) 
            {
                double overflow = (dp[j] - 1) / 2.0;
                if(overflow > 0) 
                {
                    dp[j+1] += overflow;
                    dp[j] = overflow;
                }
                else {
                    dp[j] = 0;
                }
            }
        
        return min(1.0, dp[query_glass]);
    }
};
```

---

### 📊 複雜度分析
* **時間複雜度**: $O(N^2)$。我們逐行模擬流動，處理的杯子總數約為 $1+2+...+N \approx N^2/2$。
* **空間複雜度**: $O(N)$。優化後的解法使用大小為 `query_row + 1` 的 1D 陣列。（通用解法使用 $O(N^2)$ 空間）。

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