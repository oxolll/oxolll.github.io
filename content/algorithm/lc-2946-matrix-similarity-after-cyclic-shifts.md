---
title: "LeetCode 2946. Matrix Similarity After Cyclic Shifts"
date: 2026-03-27T13:20:00+08:00
lastmod: 2026-03-27T13:20:00+08:00
difficulty: 1405
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "matrix", "math"]
keywords: ["LC2946", "1405", "Easy", "Matrix Similarity", "Cyclic Shifts", "Modulo"]
description: "LeetCode 第 2946 題：Matrix Similarity After Cyclic Shifts。難度評分：1405。探討如何利用模運算 (Modulo) 代替實體的陣列旋轉操作，以 O(1) 額外空間複雜度驗證矩陣經循環位移後的相似性。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2946](https://leetcode.com/problems/matrix-similarity-after-cyclic-shifts/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1405)</span>  
> **核心主題**：`Array` $\cdot$ `Matrix` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個大小為 $m \times n$ 的整數矩陣 `mat` 和一個整數 `k`。請執行以下操作：
- 將偶數列 (索引為 0, 2, 4...) 的元素向左循環移動 `k` 步。
- 將奇數列 (索引為 1, 3, 5...) 的元素向右循環移動 `k` 步。

如果在執行所有操作後，初始矩陣和最終矩陣完全相同，則回傳 `true`，否則回傳 `false`。

### 限制條件
- $1 \le mat.length \le 25$
- $1 \le mat[i].length \le 25$
- $1 \le mat[i][j] \le 25$
- $1 \le k \le 50$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (模運算索引對齊法)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理循環位移 (Cyclic Shift) 的最佳方式是避免實際修改或複製陣列，而是計算位移後元素的相對索引位置。
對於陣列長度 $n$，移動 $k$ 步等同於移動 $k \bmod n$ 步。
當矩陣的列必須判斷移動方向時：
- **向左移動 $k$ 步**：對於當前索引 $j$，原本位於該處的元素將來自右側，對應的原始索引為 $(j + k) \bmod n$。
- **向右移動 $k$ 步**：向右移動 $k$ 步等價於向左移動 $n - (k \bmod n)$ 步。

因此，我們可以利用模運算將兩種方向的位移轉換為統一的「向左位移」邏輯，直接比對原座標與理論位移座標上的數值是否相等。

### 🛠️ 解題思路 (Approach)
1. 取得矩陣的列數 `m` 與行數 `n`。
2. 建立雙層迴圈走訪矩陣。對於每一列 `i`，先計算其等價的向左位移步數 `l`：
   - 若為偶數列 (`i % 2 == 0`)，等價向左位移步數為 `n - (k % n)` *(這在數學上對應右移，因相對位置改變，原實作邏輯將右移轉化)*。
   - 若為奇數列，等價向左位移步數為 `k`。
3. 走訪該列的每個元素 `j`，檢查 `mat[i][j]` 是否等於預期位移後的來源座標 `mat[i][(l + j) % n]`。
4. 若有任何一個元素不相等，立即回傳 `false`。
5. 若全部檢查完畢皆無衝突，回傳 `true`。

*(備註：因向左或向右移動的相對週期特性，向左移 $k$ 與向右移 $n - k$ 等價。原解法的實作細節中 `l = (i % 2 == 0 ? n - (k % n) : k)` 是建立在特定的來源對位上，均利用了 $(l + j) \bmod n$ 作為映射函數)*。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。其中 $M$ 為列數，$N$ 為行數。需要遍歷矩陣中的每一個元素一次，每次索引計算與比對皆為常數時間操作。
- **空間複雜度**: $\mathcal{O}(1)$。沒有建立任何新的陣列或矩陣，所有驗證皆在原地透過索引推導完成。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool areSimilar(vector<vector<int>>& mat, int k) {
        int m = mat.size(), n = mat[0].size();
        
        for(int i = 0; i < m; ++i) {
            // 計算每一列等效的位移基數 l
            // 偶數列與奇數列依據移動方向，轉換為對應的模數偏移量
            int l = (i % 2 == 0 ? n - (k % n) : k);
            
            for(int j = 0; j < n; ++j) {
                // 檢查原始位置是否與位移後對應來源位置的元素相等
                if(mat[i][j] != mat[i][(l + j) % n]) {
                    return false;
                }
            }
        }
        
        return true;
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