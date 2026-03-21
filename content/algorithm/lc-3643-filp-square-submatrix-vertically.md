---
title: "LeetCode 3643. Flip Square Submatrix Vertically"
date: 2026-03-21T17:15:00+08:00
lastmod: 2026-03-21T17:15:00+08:00
difficulty: 1234
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Matrix", "Simulation"]
keywords: ["LC3643", "1234", "Easy", "Submatrix", "Vertically Flip", "In-place"]
description: "LeetCode 第 3643 題：Flip Square Submatrix Vertically。難度評分：1234。探討如何利用索引推導與 In-place 原地交換，以 O(k^2) 時間複雜度與 O(1) 額外空間，垂直翻轉指定的正方形子矩陣。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3643](https://leetcode.com/problems/flip-square-submatrix-vertically/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1234)</span>    
> **核心主題**：`Matrix` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $m \times n$ 的二維整數矩陣 `grid`。
同時給定三個整數 `x`, `y` 和 `k`，代表矩陣中的一個子矩陣：
- 子矩陣的左上角座標為 `(x, y)`。
- 子矩陣的大小為 $k \times k$（一個邊長為 $k$ 的正方形）。

請將這個特定的子矩陣進行**垂直翻轉 (Flip Vertically)**。
垂直翻轉的意思是：子矩陣內的每一行 (Column)，其最上方元素與最下方元素交換，次上方元素與次下方元素交換，依此類推。
請回傳執行翻轉操作後的 `grid` 矩陣。

### 限制條件
- $m == grid.length$
- $n == grid[i].length$
- $1 \le m, n \le 1000$
- $1 \le k \le \min(m, n)$
- $0 \le x \le m - k$
- $0 \le y \le n - k$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (In-place 局部區塊交換)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求對指定的子矩陣進行垂直翻轉，本質上是對該子矩陣範圍內的每一「行 (Column)」單獨執行陣列反轉 (Reverse)。
為了達到最佳效能，我們不需要取出子矩陣的資料或配置新的陣列。只要推導出正確的二維索引，就能直接利用 `swap` 進行 **In-place (原地)** 置換。
針對起始座標 `(x, y)` 與邊長 `k`：
- 行的範圍：從 $y$ 到 $y + k - 1$。
- 列的範圍：對於每一行，我們只需要交換上半部與下半部，因此只需一個遞增偏移量 $j$ 從 $0$ 走到 $k/2 - 1$。
- 交換的對應位置：索引 $x + j$ 與索引 $x + k - 1 - j$。

### 🛠️ 解題思路 (Approach)
1. **行遍歷**：建立外層迴圈 `i`，代表當前正在處理的行索引。範圍設定為 `y` 到 `y + k - 1`。
2. **列交換 (雙指標概念)**：建立內層迴圈 `j`，代表相對起點 `x` 的列偏移量。範圍設定為 `0` 到 `k/2 - 1`。
3. **原地置換**：
   - 位於上半部的元素座標為 `grid[x + j][i]`。
   - 位於下半部的對應元素座標為 `grid[x + k - 1 - j][i]`。
   - 呼叫 `swap` 將兩者數值交換。
4. **回傳結果**：完成子矩陣內所有行的置換後，直接回傳修改後的 `grid`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(k^2)$。我們只針對邊長為 $k$ 的子矩陣進行處理，總共執行了 $k \times (k/2)$ 次的 `swap` 操作。若 $k$ 最大等於 $\min(m, n)$，則最差時間複雜度為 $\mathcal{O}(M \times N)$。
- **空間複雜度**: $\mathcal{O}(1)$。直接在原矩陣 `grid` 上進行操作，僅使用常數級別的變數來儲存迴圈索引。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> reverseSubmatrix(vector<vector<int>>& grid, int x, int y, int k) {
        int m = grid.size(), n = grid[0].size();

        // 遍歷子矩陣的每一行 (Column)
        for(int i = y; i < y + k; ++i) {
            // 只需遍歷到 k 的一半，進行上下對稱交換
            for(int j = 0; j < k / 2; ++j) {
                // grid[x + j][i]         : 子矩陣當前行的上半部元素
                // grid[x + k - 1 - j][i] : 子矩陣當前行的下半部對應元素
                swap(grid[x + j][i], grid[x + k - 1 - j][i]);
            }
        }

        return grid;
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