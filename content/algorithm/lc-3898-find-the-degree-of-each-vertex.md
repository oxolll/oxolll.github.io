---
title: "LeetCode 3898. Find the Degree of Each Vertex"
date: 2026-04-13T14:00:00+08:00
lastmod: 2026-04-13T14:00:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "matrix", "array"]
keywords: ["LC3898", "Easy", "N/A", "Degree of Each Vertex", "Adjacency Matrix"]
description: "LeetCode 第 3898 題：Find the Degree of Each Vertex。難度評分：Easy (N/A)。探討如何利用無向圖鄰接矩陣的對稱性，僅走訪矩陣右上半部以 O(N^2) 的最佳時間複雜度精準計算每個頂點的度數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3898](https://leetcode.com/problems/find-the-degree-of-each-vertex/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Graph` $\cdot$ `Matrix` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $n \times n$ 的二維陣列 `matrix`，代表一個包含 $n$ 個頂點的**無向圖 (Undirected Graph)** 的鄰接矩陣 (Adjacency Matrix)。
其中，若 `matrix[i][j] == 1` 代表頂點 $i$ 與頂點 $j$ 之間存在一條邊；若為 `0` 則代表不存在邊。
在無向圖中，一個頂點的「度數 (Degree)」是指與該頂點相連的邊的總數。

請回傳一個長度為 $n$ 的整數陣列 `answer`，其中 `answer[i]` 代表第 $i$ 個頂點的度數。

### 限制條件
- $n == matrix.length == matrix[i].length$
- $2 \le n \le 1000$
- `matrix[i][j]` 僅為 `0` 或 `1`。
- `matrix[i][i] == 0` (圖中沒有自環)。
- `matrix[i][j] == matrix[j][i]` (這是一個無向圖)。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-13：初次提交 (對稱矩陣優化遍歷)](#2026-04-13-初次提交)

---

## 💡 2026-04-13 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求計算無向圖中每個頂點的度數。
最直觀的做法是遍歷整個 $N \times N$ 的鄰接矩陣，對於每個 `matrix[i][j] == 1` 的情況，將頂點 $i$ 的度數加一。
然而，由於輸入的是「無向圖」，鄰接矩陣必定沿著對角線對稱 (`matrix[i][j] == matrix[j][i]`)，且對角線本身全為 0。如果遍歷整個矩陣，每一條邊會被重複檢查兩次。
為了達到極致的效能優化，我們只需遍歷矩陣的**右上半部 (Upper Triangular Matrix)**。當發現 `matrix[i][j] == 1` 時，代表有一條邊連接頂點 $i$ 與 $j$，我們可以直接將 $i$ 和 $j$ 的度數同時加一。這樣不僅避免了重複計算，還將執行時間減半。

### 🛠️ 解題思路 (Approach)
1. **初始化**：取得頂點數量 `n = matrix.size()`。宣告一個長度為 `n` 且初始值皆為 0 的陣列 `cnt`，用來儲存每個頂點的度數。
2. **優化遍歷**：
   - 外層迴圈 `i` 從 `0` 遍歷至 `n-1`。
   - 內層迴圈 `j` 從 `i+1` 遍歷至 `n-1`。這確保了我們只檢查主對角線右上方的元素，避開了自環檢查與重複邊的檢查。
3. **更新度數**：若 `matrix[i][j] == 1`，代表頂點 $i$ 和頂點 $j$ 之間有連線，則同時執行 `++cnt[i]` 與 `++cnt[j]`。
4. **回傳結果**：迴圈結束後，`cnt` 陣列即為各頂點的正確度數，直接回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。確切來說是執行了 $\frac{N(N-1)}{2}$ 次檢查。由於必須讀取整個圖的結構資訊，這已經是讀取鄰接矩陣的理論最優時間複雜度。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了長度為 $N$ 的結果陣列 `cnt`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> findDegrees(vector<vector<int>>& matrix) {
        int n = matrix.size();
        vector<int> cnt(n, 0); // 記錄各頂點的度數
        
        for(int i = 0; i < n; ++i) {
            // 利用無向圖的對稱性，僅遍歷矩陣的右上半部 (j 從 i+1 開始)
            for(int j = i + 1; j < n; ++j) {
                if(matrix[i][j] == 1) {
                    // 找到一條邊，同時增加兩端頂點的度數
                    ++cnt[i];
                    ++cnt[j];
                }
            }
        }
        
        return cnt;
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