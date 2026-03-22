---
title: "LeetCode 1886. Determine Whether Matrix Can Be Obtained By Rotation"
date: 2026-03-22T15:15:00+08:00
lastmod: 2026-03-22T15:15:00+08:00
difficulty: 1407
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "matrix", "simulation"]
keywords: ["LC1886", "1407", "Easy", "Matrix Rotation", "Transpose", "In-place"]
description: "LeetCode 第 1886 題：Determine Whether Matrix Can Be Obtained By Rotation。難度評分：1407。探討如何利用矩陣轉置與列反轉的經典技巧，以 O(1) 額外空間在原地將矩陣旋轉 90 度，並驗證是否能與目標矩陣匹配。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1886](https://leetcode.com/problems/determine-whether-matrix-can-be-obtained-by-rotation/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1407)</span>  
> **核心主題**：`Array` $\cdot$ `Matrix` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個大小為 $n \times n$ 的二進位矩陣 `mat` 和 `target`。
請判斷是否能透過將 `mat` 順時針旋轉 90 度的倍數（0 度、90 度、180 度或 270 度），使其與 `target` 完全相同。如果可以，請回傳 `true`；否則回傳 `false`。

### 限制條件
- $n == mat.length == target.length$
- $n == mat[i].length == target[i].length$
- $1 \le n \le 10$
- `mat[i][j]` 與 `target[i][j]` 不是 0 就是 1。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-22：初次提交 (原地轉置與水平翻轉法)](#2026-03-22-初次提交)

---

## 💡 2026-03-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標是檢查 `mat` 在經過 0、1、2、3 次的 90 度順時針旋轉後，是否能與 `target` 匹配。
最直觀的做法是宣告一個新的二維陣列來存放旋轉後的結果，但這樣會消耗 $\mathcal{O}(N^2)$ 的額外空間。
在競技程式設計與面試中，將一個正方形矩陣「原地 (In-place) 順時針旋轉 90 度」有一個非常經典的兩步數學轉換技巧：
1. **轉置 (Transpose)**：沿著主對角線（左上到右下）進行鏡像交換，即 `mat[i][j]` 與 `mat[j][i]` 互換。
2. **水平翻轉 (Reverse Rows)**：將矩陣中的每一列 (Row) 進行左右反轉。

只要重複這個步驟，我們就能在不配置新陣列的情況下，輕鬆得出旋轉 90、180 與 270 度後的結果。

### 🛠️ 解題思路 (Approach)
1. **設定最多 4 次的驗證迴圈**：因為旋轉 4 次 (360 度) 就會回到原點，所以我們只需要測試 4 個狀態。
2. **比對狀態**：在每次旋轉前（包含初始 0 度的狀態），先檢查 `mat == target`。若相同，直接回傳 `true`。C++ 的 `vector<vector<int>>` 支援直接使用 `==` 進行深度值比對，非常方便。
3. **執行順時針旋轉 90 度**：
   - **轉置矩陣**：利用雙層迴圈，內層迴圈 `j` 從 `i` 開始，執行 `swap(mat[i][j], mat[j][i])`。
   - **列反轉**：遍歷每一列 `i`，使用 `reverse(mat[i].begin(), mat[i].end())` 將整列左右對調。
4. **結束迴圈**：如果轉了 4 個方向（包含原本的狀態）都無法匹配，則回傳 `false`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。矩陣的大小為 $N \times N$。比對矩陣、轉置與反轉皆需要遍歷整個矩陣。由於最多只執行 4 次，常數時間雖然較大，但整體的漸進時間複雜度依然為 $\mathcal{O}(N^2)$。
- **空間複雜度**: $\mathcal{O}(1)$。完全在原矩陣 `mat` 上進行原地交換與翻轉，不需依賴額外的資料結構空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool findRotation(vector<vector<int>>& mat, vector<vector<int>>& target) {
        int n = mat.size();
        
        // 最多旋轉 4 次 (0度, 90度, 180度, 270度)
        for(int k = 0; k < 4; ++k) {
            // 1. 檢查當前狀態是否匹配
            if(mat == target) {
                return true;
            }
            
            // 2. 原地順時針旋轉 90 度：先轉置 (Transpose)
            for(int i = 0; i < n; ++i) {
                for(int j = i; j < n; ++j) {
                    swap(mat[i][j], mat[j][i]);
                }
            }
            
            // 3. 原地順時針旋轉 90 度：再將每一列左右翻轉 (Reverse)
            for(int i = 0; i < n; ++i) {
                reverse(mat[i].begin(), mat[i].end());
            }
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