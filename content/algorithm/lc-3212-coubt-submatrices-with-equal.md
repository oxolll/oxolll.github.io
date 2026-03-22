---
title: "LeetCode 3212. Count Submatrices With Equal Frequency of X and Y"
date: 2026-03-20T04:21:00+08:00
lastmod: 2026-03-20T04:21:00+08:00
difficulty: 1672
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Matrix", "Prefix Sum", "Bit Manipulation"]
keywords: ["LC3212", "1672", "Medium", "2D Prefix Sum", "State Compression", "Bitmask"]
description: "LeetCode 第 3212 題：Count Submatrices With Equal Frequency of X and Y。難度評分：1672。探討如何利用二維前綴和計算子矩陣字元頻率，並透過位元狀態壓縮 (State Compression) 在單一 64-bit 整數中同時維護兩個計數器以優化常數時間。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3212](https://leetcode.com/problems/count-submatrices-with-equal-frequency-of-x-and-y/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1672)</span>    
> **核心主題**：`Matrix` $\cdot$ `Prefix Sum` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個二維字元矩陣 `grid`，其中 `grid[i][j]` 可能是 `'X'`, `'Y'`, 或 `'.'`。
請回傳滿足以下條件的子矩陣數量：
1. 子矩陣包含 `grid[0][0]`（即左上角座標必須為 `(0, 0)`）。
2. 子矩陣中 `'X'` 的數量與 `'Y'` 的數量**嚴格相等**。
3. 子矩陣中**至少包含一個** `'X'`。

### 限制條件
- $1 \le grid.length, grid[i].length \le 1000$
- `grid[i][j]` 為 `'X'`, `'Y'`, 或 `'.'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-20：初次提交 (二維前綴和與位元狀態壓縮)](#2026-03-20-初次提交)

---

## 💡 2026-03-20 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
題目要求計算左上角固定為 `(0, 0)` 的子矩陣數量，這完全符合**二維前綴和 (2D Prefix Sum)** 的定義。對於矩陣中的每一個座標 `(i, j)`，我們需要計算從 `(0, 0)` 到 `(i, j)` 的區間內 `'X'` 和 `'Y'` 的總數。

常規做法是建立兩個獨立的二維前綴和陣列（或一個儲存 `pair<int, int>` 的陣列）。為了追求極致的常數時間優化，可採用**狀態壓縮 (State Compression)**：
給定最大測資範圍 $1000 \times 1000 = 10^6$，任一字元的最大出現次數不會超過 $10^6$。由於 $2^{20} = 1048576 > 10^6$，我們可以分配 20 個位元來獨立儲存一個計數器。
將 `'X'` 的計數左移 20 位儲存於高位元，`'Y'` 的計數儲存於低 20 位元。如此一來，即可使用單一一個 64-bit 的 `long long` 變數，配合加減運算同時完成兩個數值的前綴和轉移，避免進位造成的數值污染。

### 🛠️ 解題思路 (Approach)
1. **常數定義**：
   - `shift = 1024 * 1024` ($2^{20}$)，用於將 `'X'` 的計數推至高位元。
   - `mask = 1024 * 1024 - 1`，用於遮罩萃取出低位元的 `'Y'` 計數。
2. **建構前綴和矩陣**：
   - 宣告 `acc` 二維矩陣儲存 `long long` 型態的狀態。
   - 轉移方程：`acc[i][j] = acc[i-1][j] + acc[i][j-1] - acc[i-1][j-1]`。
   - 處理當前字元：若為 `'X'`，則加上 `shift`；若為 `'Y'`，則加上 `1`。
3. **條件驗證與計數**：
   - 遍歷完成後的 `acc` 矩陣，每一格即代表一個以 `(0, 0)` 為左上角、`(i, j)` 為右下角的子矩陣。
   - 利用位元右移 `>> 20` 提取 `'X'` 數量，利用 `& mask` 提取 `'Y'` 數量。
   - 判斷條件：`acc[i][j] != 0` 且 `(acc[i][j] >> 20) == (acc[i][j] & mask)`。因兩者數量相等且總和不為零，推導出 `'X'` 的數量必然大於 0，符合題目要求。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。執行兩次矩陣的完整遍歷。前綴和的轉移皆為 $\mathcal{O}(1)$ 的位元與加減運算。
- **空間複雜度**: $\mathcal{O}(M \times N)$。使用了一個大小與 `grid` 相同的二維矩陣 `acc`，儲存型態為 64-bit 整數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numberOfSubmatrices(vector<vector<char>>& grid) {
        int m = grid.size(), n = grid[0].size();
        
        // 分配 20 個位元 (2^20 = 1048576 > 10^6)，確保極端測資下計數不會溢位互補
        long long shift = 1024 * 1024; 
        long long mask = 1024 * 1024 - 1;
        
        // acc 儲存壓縮狀態：高位元為 X 計數，低位元為 Y 計數
        vector<vector<long long>> acc(m, vector<long long>(n, 0));
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // 標準二維前綴和轉移方程
                acc[i][j] = (i > 0 ? acc[i-1][j] : 0) + 
                            (j > 0 ? acc[i][j-1] : 0) - 
                            (i > 0 && j > 0 ? acc[i-1][j-1] : 0);
                            
                // 狀態更新
                if(grid[i][j] == 'X')
                    acc[i][j] += shift;
                else if(grid[i][j] == 'Y')
                    ++acc[i][j];
            }
        }

        int rt = 0;
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // acc[i][j] != 0 且數量相等，保證了 X 和 Y 的數量皆大於 0
                if(acc[i][j] != 0 && (acc[i][j] >> 20) == (acc[i][j] & mask)) {
                    ++rt;
                }
            }
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