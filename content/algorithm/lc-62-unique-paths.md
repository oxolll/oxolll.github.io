---
title: "LeetCode 62. Unique Paths"
date: 2026-04-01T10:00:00+08:00
lastmod: 2026-04-01T10:00:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "dynamic-programming", "combinatorics", "matrix"]
keywords: ["LC62", "Medium", "N/A", "Unique Paths", "Dynamic Programming"]
description: "LeetCode 第 62 題：Unique Paths。難度評分：Medium (N/A)。探討如何利用二維動態規劃 (Dynamic Programming) 計算網格中從起點走到終點的所有可能路徑數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 62](https://leetcode.com/problems/unique-paths/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Dynamic Programming` $\cdot$ `Combinatorics`

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (二維動態規劃)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道經典的二維動態規劃 (2D DP) 基礎題。
因為機器人每次只能向右或向下移動，所以抵達座標 `(i, j)` 的路徑數量，必定等於「抵達上方座標 `(i-1, j)` 的路徑數」加上「抵達左方座標 `(i, j-1)` 的路徑數」。
定義狀態 `dp[i][j]` 為抵達 `(i, j)` 的總方法數，狀態轉移方程式即為：`dp[i][j] = dp[i-1][j] + dp[i][j-1]`。

### 🛠️ 解題思路 (Approach)
1. 宣告大小為 $m \times n$ 的二維陣列 `dp`，初始值為 0。
2. 設定起點狀態 `dp[0][0] = 1`。
3. 利用雙層迴圈遍歷整個網格。
4. 若 `i == 0` 且 `j == 0`，跳過此次迴圈。
5. 計算狀態轉移：利用三元運算子處理邊界條件（若 `i > 0` 則取上方值，否則為 0；若 `j > 0` 則取左方值，否則為 0）。
6. 迴圈結束後，回傳網格右下角的值 `dp[m-1][n-1]`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。需遍歷整個 $M \times N$ 的網格一次。
- **空間複雜度**: $\mathcal{O}(M \times N)$。宣告了一個大小為 $M \times N$ 的二維陣列。*(註：由於每次狀態更新只依賴上一列與當前列，可利用滾動陣列將空間壓縮至 $\mathcal{O}(N)$)*。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>> dp(m, vector<int>(n, 0));
        dp[0][0] = 1;
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(i == 0 && j == 0) continue;
                
                // 狀態轉移：上方路徑數 + 左方路徑數
                dp[i][j] = (i > 0 ? dp[i-1][j] : 0) + (j > 0 ? dp[i][j-1] : 0);
            }
        }

        return dp[m-1][n-1];
    }
};
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