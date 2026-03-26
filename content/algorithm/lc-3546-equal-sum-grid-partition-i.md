---
title: "LeetCode 3546. Equal Sum Grid Partition I"
date: 2026-03-26T16:25:00+08:00
lastmod: 2026-03-26T16:25:00+08:00
difficulty: 1411
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "matrix", "prefix sum"]
keywords: ["LC3546", "1411", "Medium", "Grid Partition", "Prefix Sum"]
description: "LeetCode 第 3546 題：Equal Sum Grid Partition I。難度評分：1411。探討如何利用二維前綴和 (2D Prefix Sum) 以 O(M * N) 時間複雜度快速驗證網格是否能透過單一水平或垂直切割分為兩個總和相等的區塊。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3546](https://leetcode.com/problems/equal-sum-grid-partition-i/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1411)</span>  
> **核心主題**：`Matrix` $\cdot$ `Prefix Sum`

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (二維前綴和驗證)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求判斷是否能透過「橫切一刀」或「縱切一刀」，將網格分割成兩個數值總和完全相等的區塊。
要快速取得區塊的總和，二維前綴和 (2D Prefix Sum) 是最標準的工具。完成預處理後：
- 全局總和即為矩陣右下角的元素 `mat[m-1][n-1]`。
- **橫切驗證**：切在第 `i` 列之下，上半部的總和即為第 `i` 列最右側的前綴和 `mat[i][n-1]`。
- **縱切驗證**：切在第 `i` 行之右，左半部的總和即為第 `i` 行最下方的前綴和 `mat[m-1][i]`。
若上半部（或左半部）的總和恰好等於全局總和的一半，即代表成功分割。若全局總和為奇數，則無論如何切割皆無法平分。

### 🛠️ 解題思路 (Approach)
1. **建立 2D 前綴和陣列**：
   - 宣告與 `grid` 同等大小的二維陣列 `mat`，型別使用 `long long` 防止數值溢位。
   - 使用標準轉移方程：`mat[i][j] = grid[i][j] + 上方和 + 左方和 - 左上方重複和`，計算每一個座標的二維前綴和。
2. **奇偶性初步篩選**：
   - 若全局總和 `mat[m-1][n-1]` 為奇數，必定無法分為兩個相等的整數總和，直接回傳 `false`。
3. **驗證水平分割 (橫切)**：
   - 走訪列索引 `i` 從 `0` 到 `m-2`（保留至少一列給下半部）。
   - 若發現 `mat[m-1][n-1] == 2 * mat[i][n-1]`，代表上半部總和為全部的一半，回傳 `true`。
4. **驗證垂直分割 (縱切)**：
   - 走訪行索引 `i` 從 `0` 到 `n-2`（保留至少一行給右半部）。
   - 若發現 `mat[m-1][n-1] == 2 * mat[m-1][i]`，代表左半部總和為全部的一半，回傳 `true`。
5. **最終回傳**：若所有切法皆不符合條件，回傳 `false`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。建立二維前綴和需要遍歷整個矩陣一次。後續的水平與垂直分割驗證分別需 $\mathcal{O}(M)$ 與 $\mathcal{O}(N)$，總時間複雜度由預處理步驟主導。
- **空間複雜度**: $\mathcal{O}(M \times N)$。宣告了一個大小相同的二維陣列 `mat` 來儲存前綴和狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canPartitionGrid(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        
        // 使用 long long 防止大數值溢位
        vector<vector<long long>> mat(m, vector<long long>(n, 0));
        
        // 1. 建立二維前綴和
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                mat[i][j] = grid[i][j];
                mat[i][j] += (i > 0 ? mat[i-1][j] : 0) + 
                             (j > 0 ? mat[i][j-1] : 0) - 
                             (i > 0 && j > 0 ? mat[i-1][j-1] : 0);
            }
        }
        
        // 2. 總和為奇數必定無法平分
        if(mat[m-1][n-1] % 2 == 1) return false;
        
        // 3. 測試所有的水平切割 (檢查前 i 列的總和是否為一半)
        for(int i = 0; i < m - 1; ++i) {
            if(mat[m-1][n-1] == 2 * mat[i][n-1]) return true;
        }

        // 4. 測試所有的垂直切割 (檢查前 i 行的總和是否為一半)
        for(int i = 0; i < n - 1; ++i) {
            if(mat[m-1][n-1] == 2 * mat[m-1][i]) return true;
        }

        return false; 
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