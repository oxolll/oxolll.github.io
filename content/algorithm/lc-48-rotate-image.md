---
title: "LeetCode 48. Rotate Image"
date: 2026-05-04T13:40:00+08:00
lastmod: 2026-05-04T13:40:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "matrix", "math"]
keywords: ["LC48", "Medium", "Rotate Image", "矩陣旋轉", "幾何變換", "In-place"]
description: "LeetCode 第 48 題：Rotate Image。難度評分：Medium。探討如何利用幾何變換的組合拳：「先上下翻轉，再沿主對角線轉置」，以極度優雅且簡潔的程式碼實現 O(1) 空間複雜度的原地順時針旋轉 90 度。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 48](https://leetcode.com/problems/rotate-image/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $n \times n$ 的二維矩陣 `matrix` 代表一張影像。請你將這張影像**順時針旋轉 90 度**。
你必須在**原地 (in-place)** 修改輸入的二維矩陣，這意味著你不能配置另一個新的二維矩陣來完成旋轉。

### 範例
- **Input**: `matrix = [[1,2,3],[4,5,6],[7,8,9]]`
- **Output**: `[[7,4,1],[8,5,2],[9,6,3]]`
- **Explanation**: 
  原矩陣：
  1 2 3
  4 5 6
  7 8 9
  旋轉後：
  7 4 1
  8 5 2
  9 6 3

### 限制條件
- $n == matrix.length == matrix[i].length$
- $1 \le n \le 20$
- $-1000 \le matrix[i][j] \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-04：初次提交 (上下翻轉 + 對角線轉置)](#2026-05-04-初次提交)

---

## 💡 2026-05-04 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要將一個矩陣順時針旋轉 90 度，最直觀的方法是找出座標的映射公式：$(i, j) \to (j, n-1-i)$。如果是在原地旋轉，需要同時處理四個對稱點的輪替（例如使用一個暫存變數 `tmp` 來進行四點交換），這在實作上需要仔細計算邊界，容易發生 Off-by-one error (差一錯誤)。

**更優雅的破局點：幾何變換拆解**
利用線性代數與幾何對稱的特性，我們可以將「順時針旋轉 90 度」拆解為兩個非常基礎且好寫的步驟：
1. **上下翻轉 (Horizontal Flip / Reverse upside down)**：將第一列與最後一列交換，第二列與倒數第二列交換...以此類推。
   - 映射關係：$(i, j) \to (n-1-i, j)$
2. **主對角線轉置 (Transpose)**：將矩陣沿著左上到右下的對角線進行翻轉交換。
   - 映射關係：$(i, j) \to (j, i)$

將這兩步組合起來，最終的座標映射恰好就會是 $(i, j) \to (j, n-1-i)$，完美達成順時針旋轉 90 度的效果！

*(補充說明：如果題目要求「逆時針」旋轉 90 度，則可以拆解為「先左右翻轉，再對角線轉置」，或是「先對角線轉置，再上下翻轉」)*。

### 🛠️ 解題思路 (Approach)
1. **上下翻轉**：
   - 使用一個外層迴圈遍歷列的「上半部」(`i` 從 $0$ 到 $n/2 - 1$)。
   - 內層迴圈遍歷所有的行 (`j` 從 $0$ 到 $n - 1$)。
   - 直接交換 `matrix[i][j]` 與對稱的 `matrix[n-1-i][j]`。
2. **對角線轉置**：
   - 外層迴圈遍歷所有列 (`i` 從 $0$ 到 $n - 1$)。
   - 內層迴圈只遍歷對角線「右上方」的元素 (`j` 從 `i + 1` 到 $n - 1$)。
   - 交換 `matrix[i][j]` 與 `matrix[j][i]`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。其中 $N$ 為矩陣的邊長。上下翻轉需走訪 $N^2/2$ 個元素，對角線轉置需走訪約 $N^2/2$ 個元素，總時間複雜度為線性 $\mathcal{O}(\text{總元素數量})$。
- **空間複雜度**: $\mathcal{O}(1)$。完全依靠指標交換 (`swap`) 原地修改，沒有使用任何額外的資料結構。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();

        // 步驟 1: 上下翻轉 (Reverse upside down)
        // 注意外層迴圈只需走到 n/2，否則會翻轉兩次變回原樣
        for(int i = 0; i < n / 2; ++i) {
            for(int j = 0; j < n; ++j) {
                swap(matrix[i][j], matrix[n - 1 - i][j]);
            }
        }
        
        // 步驟 2: 沿著主對角線轉置 (Transpose)
        // 內層迴圈從 i+1 開始，只交換對角線右上方的元素
        for(int i = 0; i < n; ++i) {
            for(int j = i + 1; j < n; ++j) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }

        return;
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