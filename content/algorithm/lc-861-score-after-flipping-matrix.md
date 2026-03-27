---
title: "LeetCode 861. Score After Flipping Matrix"
date: 2026-03-27T12:35:00+08:00
lastmod: 2026-03-27T12:35:00+08:00
difficulty: 1818
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "matrix", "greedy", "bit manipulation"]
keywords: ["LC861", "1818", "Medium", "Score After Flipping", "Matrix", "Greedy"]
description: "LeetCode 第 861 題：Score After Flipping Matrix。難度評分：1818。探討如何依據二進位權重特性，利用貪婪策略決定行列翻轉，以 O(M * N) 時間複雜度最大化二維矩陣的整體二進位分數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 861](https://leetcode.com/problems/score-after-flipping-matrix/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1818)</span>  
> **核心主題**：`Array` $\cdot$ `Matrix` $\cdot$ `Greedy` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $m \times n$ 的二進位矩陣 `grid`。
在一次操作中，你可以選擇任何一列 (Row) 或任何一行 (Column)，並將其中的所有數值進行翻轉（0 變成 1，1 變成 0）。
每一列代表一個二進位數字。矩陣的「分數」是所有這些二進位數字的總和。
你可以執行任意次數的操作。請回傳可能獲得的最高分數。

### 限制條件
- $m == grid.length$
- $n == grid[i].length$
- $1 \le m, n \le 20$
- `grid[i][j]` 不是 0 就是 1。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (位元權重與行列獨立翻轉)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要最大化二進位總和，必須依賴二進位數值的權重特性。
對於一個長度為 $n$ 的二進位數字，最高位元 (Most Significant Bit) 所代表的值為 $2^{n-1}$，這大於其餘所有較低位元全部為 1 的總和 ($\sum_{k=0}^{n-2} 2^k = 2^{n-1} - 1$)。
因此，貪婪策略 (Greedy Strategy) 的第一原則是：**必須確保每一列的最左側元素 (第一行) 皆為 1**。若 `grid[i][0] == 0`，我們無條件翻轉該列。
在確保第一行皆為 1 之後，對於後續的每一行 $j$ (從 $j=1$ 到 $n-1$)，翻轉該行的目標是最大化該行中 1 的數量。若該行中 0 的數量多於 1 的數量，則翻轉該行。由於改變行不會影響同一列中第一行的狀態，這個操作能保證局部最佳解即為全域最佳解。

### 🛠️ 解題思路 (Approach)
1. **確保最高位元為 1 (列翻轉)**：
   - 宣告陣列 `colCnt` 記錄每一行中 1 的數量。
   - 走訪每一列 `i`，檢查該列的第一個元素 `grid[i][0]` 是否為 0。
   - 若為 0，將 `toggle = true`，對該列的所有元素進行 XOR (`^= 1`) 翻轉。
   - 翻轉過程中，順便將各行中值為 1 的數量累加至 `colCnt[j]`。
2. **最大化低位元 (行翻轉)**：
   - 從第 2 行 ($j = 1$) 開始遍歷至最後一行 ($j = n - 1$)。（第一行 `j = 0` 在上一步已確定全為 1）。
   - 檢查該行 1 的數量是否過半 (`colCnt[j] * 2 >= m`)。若過半，則不需翻轉，直接 `continue`。
   - 若未過半，代表 0 較多。進行該行的翻轉 (`grid[i][j] ^= 1`)，並更新 1 的數量為 `m - colCnt[j]`。*(註：實際上由於最終只依賴 `colCnt` 計算總和，可省略實際修改 `grid` 的動作，僅更新 `colCnt`)*。
3. **計算總分數**：
   - 宣告 `rt = 0`。
   - 由於每一行的權重是依序遞減的，可以利用位元左移操作 (`<< 1`) 計算。
   - 遍歷 `colCnt`，每次將 `rt` 左移一位，並加上當前行的 1 數量 `colCnt[j]`，逐步累加出最終分數。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。其中 $M$ 為列數，$N$ 為行數。執行了兩次常數級別的矩陣走訪操作。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了大小為 $N$ 的一維陣列 `colCnt` 用於統計每一行 1 的頻率。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int matrixScore(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<int> colCnt(n, 0);
        
        // 1. 列翻轉：確保每列的最高位元 (最左側) 均為 1
        for(int i = 0; i < m; ++i) {
            bool toggle = false;
            if(grid[i][0] == 0) toggle = true; // 首位為 0 則翻轉該列
            
            for(int j = 0; j < n; ++j) {
                if(toggle) {
                    grid[i][j] ^= 1;
                }
                // 順便統計各行 1 的數量
                if(grid[i][j] == 1) {
                    ++colCnt[j];
                }
            }
        }

        // 2. 行翻轉：確保其餘各行的 1 的數量達到最大
        for(int j = 0; j < n; ++j) {
            if(colCnt[j] * 2 >= m) continue; // 1 的數量已過半，無需翻轉
            
            for(int i = 0; i < m; ++i) {
                grid[i][j] ^= 1; // 進行翻轉 (可省略，僅修改 colCnt 即可)
            }
            // 更新翻轉後的 1 數量
            colCnt[j] = m - colCnt[j]; 
        }
        
        // 3. 計算最終分數
        int rt = 0;
        for(int j = 0; j < n; ++j) {
            rt <<= 1; // 等同於 rt = rt * 2，位元權重遞增
            rt += colCnt[j];
        }

        return rt;
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