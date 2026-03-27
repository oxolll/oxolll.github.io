---
title: "LeetCode 1605. Find Valid Matrix Given Row and Column Sums"
date: 2026-03-27T12:32:00+08:00
lastmod: 2026-03-27T12:32:00+08:00
difficulty: 1867
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "matrix", "greedy"]
keywords: ["LC1605", "1867", "Medium", "Valid Matrix", "Row Sum", "Column Sum", "Greedy"]
description: "LeetCode 第 1605 題：Find Valid Matrix Given Row and Column Sums。難度評分：1867。探討如何利用貪婪策略，每次取列與行的需求總和之最小值進行填充，以 O(M * N) 時間複雜度構造出符合限制條件的二維矩陣。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1605](https://leetcode.com/problems/find-valid-matrix-given-row-and-column-sums/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1867)</span>  
> **核心主題**：`Array` $\cdot$ `Matrix` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個非負整數陣列 `rowSum` 和 `colSum`，分別代表一個二維矩陣中每一列 (Row) 的元素總和以及每一行 (Column) 的元素總和。
請構造並回傳一個滿足上述總和條件的二維非負整數矩陣。
題目保證 `rowSum` 的總和等於 `colSum` 的總和，且至少存在一個合法的矩陣解答。

### 限制條件
- $1 \le rowSum.length, colSum.length \le 500$
- $0 \le rowSum[i], colSum[i] \le 10^8$
- `sum(rowSum) == sum(colSum)`
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (取極小值的貪婪填充法)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道矩陣構造題，核心解法為貪婪策略 (Greedy Strategy)。
對於矩陣中的每一個座標 `(i, j)`，我們希望填入盡可能大的數值以快速滿足 `rowSum[i]` 或 `colSum[j]` 的需求。然而，填入的數值不能違反限制，因此該座標的最大合法數值必然是當前列的剩餘需求與當前行的剩餘需求兩者之中的最小值，即 $\min(rowSum[i], colSum[j])$。
填入該值後，將其從 `rowSum[i]` 與 `colSum[j]` 中扣除。此操作保證每次至少有一個行或列的剩餘需求會歸零，從而逐步將所有需求削減至 0。

### 🛠️ 解題思路 (Approach)
1. **初始化矩陣**：取得列數 `m` 與行數 `n`。宣告一個大小為 $m \times n$ 的二維陣列 `mat`，初始值為 0。
2. **雙層迴圈遍歷**：遍歷每一個座標 `(i, j)`。
3. **貪婪賦值與狀態更新**：
   - 比較 `rowSum[i]` 與 `colSum[j]` 的大小。
   - 若 `rowSum[i] >= colSum[j]`：將 `mat[i][j]` 設為 `colSum[j]`。將 `rowSum[i]` 扣除 `colSum[j]` 的值，並將 `colSum[j]` 直接歸零。
   - 若 `rowSum[i] < colSum[j]`：將 `mat[i][j]` 設為 `rowSum[i]`。將 `colSum[j]` 扣除 `rowSum[i]` 的值，並將 `rowSum[i]` 直接歸零。
4. **回傳結果**：當所有座標走訪完畢，回傳構造好的矩陣 `mat`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。其中 $M$ 和 $N$ 分別為矩陣的列數與行數。需要走訪矩陣中的每個單元格一次，每次操作為常數時間。
- **空間複雜度**: $\mathcal{O}(M \times N)$。宣告了一個大小為 $M \times N$ 的二維陣列 `mat` 作為結果回傳（若不計入回傳結果的空間，則為 $\mathcal{O}(1)$）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> restoreMatrix(vector<int>& rowSum, vector<int>& colSum) {
        int m = rowSum.size(), n = colSum.size();
        vector<vector<int>> mat(m, vector<int>(n, 0));

        // 貪婪填充每一個座標
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // 取列需求與行需求之中的最小值填入
                if(rowSum[i] >= colSum[j]) {
                    mat[i][j] = colSum[j];
                    rowSum[i] -= colSum[j];
                    colSum[j] = 0;
                } else {
                    mat[i][j] = rowSum[i];
                    colSum[j] -= rowSum[i];
                    rowSum[i] = 0;
                }
            }
        }

        return mat;
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