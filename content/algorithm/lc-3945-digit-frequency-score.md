---
title: "LeetCode 3945. Digit Frequency Score"
date: 2026-06-02T14:30:00+08:00
lastmod: 2026-06-02T14:30:00+08:00
difficulty: 1201
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math"]
keywords: ["LC3945", "Easy", "Digit Frequency Score", "數學", "位數和"]
description: "LeetCode 第 3945 題：Digit Frequency Score。探討如何避免字串轉換，直接利用模除與除法等數學操作，以 O(1) 空間與最佳時間複雜度高效計算整數的位數和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3945](https://leetcode.com/problems/digit-frequency-score/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `n`。請你計算並回傳該整數的 **Digit Frequency Score (數位頻率分數)**。
根據定義，一個整數的數位頻率分數等於其 **各個位數的總和 (Sum of Digits)**。

### 範例
- **Input**: `n = 256`
- **Output**: `13`
- **Explanation**: 2 + 5 + 6 = 13。

- **Input**: `n = 1000`
- **Output**: `1`
- **Explanation**: 1 + 0 + 0 + 0 = 1。

### 限制條件
- $0 \le n \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-02：初次提交 (純數學位數拆解法)](#2026-06-02-初次提交)

---

## 💡 2026-06-02 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道基礎的數學題。最直觀但不夠優雅的作法是將整數轉換為字串（如 `to_string(n)`），然後遍歷字元進行相加。然而，字串轉換會涉及動態記憶體分配，帶來不必要的常數時間開銷與空間耗費。

**核心破局點：數學拆解 (Mathematical Extraction)**
處理數字位數最有效率的標準作法，是利用基本的數學運算：
1. **取餘數 (`n % 10`)**：精準萃取出該數字的「最後一個位數」(個位數)。
2. **整數除法 (`n /= 10`)**：將數字向右推移，捨棄剛剛萃取出的個位數。
透過 `while(n > 0)` 迴圈反覆執行這兩個動作，我們就能在完全不依賴字串操作的情況下，原地 (In-place) 計算出總和。

### 🛠️ 解題思路 (Approach)
1. **初始化總和**：宣告一個整數變數 `sum = 0`。
2. **迴圈拆解**：當 `n` 大於 0 時（在 C++ 中可以直接寫 `while(n)`），進入迴圈：
   - 將 `n` 除以 10 的餘數加進 `sum` 中 (`sum += n % 10`)。
   - 將 `n` 除以 10 (`n /= 10`) 以捨棄當前的個位數。
3. **回傳結果**：迴圈結束後，`sum` 即為位數總和。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_{10}(N))$
  其中 $N$ 是輸入的數字 `n`。`while` 迴圈的執行次數取決於數字的位數長度，即 $\approx \log_{10}(N)$。在整數極限內，操作次數最多不超過 10 次，時間開銷微乎其微，可視為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(1)$
  演算法完全就地執行 (In-place)。我們僅配置了一個基本整數變數 (`sum`)，記憶體足跡極小且為常數級別。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int digitFrequencyScore(int n) {
        int sum = 0;
        
        // 透過數學運算逐一剝離最後一個位數
        while(n) {
            sum += n % 10;  // 取出個位數並累加
            n /= 10;        // 捨棄個位數
        }
        
        return sum;
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