---
title: "LeetCode 1582. Special Positions in a Binary Matrix"
date: 2026-03-04T18:16:00+08:00
lastmod: 2026-03-04T18:16:00+08:00
difficulty: 1321
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Matrix", "Counting"]
keywords: ["LC1582", "1321", "Easy", "Special Position", "Precomputation"]
description: "LeetCode 第 1582 題：Special Positions in a Binary Matrix。難度評分：1321。探討如何利用行列預處理計數，將時間複雜度優化至 O(M * N) 來尋找特殊位置。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1582](https://leetcode.com/problems/special-positions-in-a-binary-matrix/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1321)</span>    
> **核心主題**：`Array` $\cdot$ `Matrix` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $m \times n$ 的二進位矩陣 `mat`，請回傳矩陣中 **特殊位置 (Special Positions)** 的數量。

如果 `mat[i][j] == 1`，且第 `i` 行和第 `j` 列中的所有其他元素都是 `0`（也就是說，該行與該列的 1 的數量皆為 1），則稱位置 `(i, j)` 為特殊位置。

### 範例
- **Input**: `mat = [[1,0,0],[0,0,1],[1,0,0]]` → **Output**: `1`
- **Explanation**: 
  - (1, 2) 是一個特殊位置，因為 `mat[1][2] == 1` 且第 1 行和第 2 列的所有其他元素都是 0。
  - (0, 0) 和 (2, 0) 不是，因為第 0 列有兩個 1。

### 限制條件
- $m == mat.length$
- $n == mat[i].length$
- $1 \le m, n \le 100$
- `mat[i][j]` 必定為 0 或 1。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-04：初次提交 (行列計數預處理)](#2026-03-04-初次提交)

---

## 💡 2026-03-04 初次提交

### 🎯 直覺 (Intuition)
尋找「特殊位置」的條件非常明確：該點必須是 1，且它所在的行與列加總起來剛好也只能有 1 個 1。
如果我們採用暴力解，每次遇到 `1` 都去重新遍歷該行與該列，會浪費大量時間進行重複計算。更好的思維是「先拍平統計」：花一次遍歷的時間，把每一行、每一列總共有幾個 1 記錄下來，之後再重新遍歷一次矩陣進行查表比對即可。

### 🛠️ 解題思路 (Approach)
1. **宣告計數陣列**：
   準備兩個大小分別為 $m$ 與 $n$ 的一維陣列 `row_cnt` 和 `col_cnt`，用來記錄每行與每列的 1 的總數。
2. **第一階段：預處理計數 (Precomputation)**：
   遍歷整個矩陣，只要遇到 `mat[i][j] == 1`，就將對應的 `row_cnt[i]` 與 `col_cnt[j]` 各加 1。
3. **第二階段：判定與結算**：
   再次遍歷矩陣。這次的判定條件變得很簡單：
   只要 `mat[i][j] == 1`，並且查表發現 `row_cnt[i] == 1` 且 `col_cnt[j] == 1`，這就代表該點是整行整列唯一的 1，將答案 `rt` 加一。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。我們總共遍歷了二維矩陣兩次，時間開銷與矩陣大小成線性正比。
- **空間複雜度**: $\mathcal{O}(M + N)$。使用了兩個額外的一維陣列來儲存行與列的計數狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numSpecial(vector<vector<int>>& mat) {
        int m = mat.size(), n = mat[0].size();
        
        // 1. 預處理：紀錄每行、每列 1 的總數
        vector<int> row_cnt(m, 0);
        vector<int> col_cnt(n, 0);
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(mat[i][j]) {
                    ++row_cnt[i];
                    ++col_cnt[j];
                }   
            }
        }

        int rt = 0;
        
        // 2. 判定：尋找符合條件的特殊位置
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(mat[i][j] && row_cnt[i] == 1 && col_cnt[j] == 1) {
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