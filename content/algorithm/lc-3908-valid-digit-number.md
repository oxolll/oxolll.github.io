---
title: "LeetCode 3908. Valid Digit Number"
date: 2026-04-26T01:10:00+08:00
lastmod: 2026-04-26T01:10:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "simulation"]
keywords: ["LC3908", "Easy", "Valid Digit Number", "Digit Extraction", "Math"]
description: "LeetCode 第 3908 題：Valid Digit Number。探討如何利用數學取模與除法拆解數字，並巧妙設定迴圈邊界條件以隔離最高位數字 (Most Significant Digit)，實作精巧的條件驗證。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3908](https://leetcode.com/problems/valid-digit-number/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個整數 `n` 與 `x`（$0 \le x \le 9$）。
請判斷數字 `n` 是否為一個「有效的數字」。有效數字的定義為：
1. 數字 `x` 必須出現在 `n` 的位數中。
2. 數字 `x` **不可以**是 `n` 的最高位數字（Most Significant Digit）。

如果符合上述兩個條件，請回傳 `true`，否則回傳 `false`。

### 限制條件
- `n` 為正整數。
- $0 \le x \le 9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-26：初次提交 (數學拆解與最高位隔離法)](#2026-04-26-初次提交)

---

## 💡 2026-04-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題測驗的是基礎的 **數字拆解 (Digit Extraction)**。
一般在拆解數字時，我們習慣使用 `while(n > 0)` 搭配 `n % 10` 將每一位數剝離。但在這題中，我們需要對「最高位數字」進行特殊對待。

**巧妙的迴圈邊界控制**：
如果我們將迴圈條件設定為 `while(n >= 10)`，那麼在迴圈執行的過程中，只會處理到「個位數」一路到「次高位數」。
當迴圈終止時，變數 `n` 裡面存放的數值，恰巧就會是剝離剩下的「最高位數字」。
利用這個特性，我們可以完美分離兩階段的判斷邏輯：在迴圈內尋找 $x$，在迴圈外驗證最高位是否不等於 $x$。

### 🛠️ 解題思路 (Approach)
1. **初始化標記**：宣告布林變數 `has = false`，用來記錄 $x$ 是否出現在非最高位的位數中。
2. **拆解非最高位數字**：
   - 設立條件 `while(n >= 10)`。
   - 利用 `n % 10` 取得當前最低位，若等於 $x$，則將 `has` 設為 `true`。
   - 利用 `n /= 10` 將數字向右推移，捨棄已檢查的位數。
3. **終止檢查**：
   - 迴圈結束後，此時的 `n` 即為最高位數字。
   - 檢查 `n != x` (最高位不可為 $x$) 且 `has` 為 `true` (其他位數必須包含 $x$)，並將這個邏輯運算的結果回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_{10} N)$。迴圈執行的次數取決於數字 `n` 的總位數，因此時間複雜度與數字的位數成正比，為對數時間。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個布林變數儲存狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool validDigit(int n, int x) {
        bool has = false;
        
        // 迴圈條件 n >= 10 確保最高位數字不會在迴圈內被處理
        while(n >= 10) {
            // 檢查當前位數是否為目標數字 x
            if((n % 10) == x) {
                has = true;
            }
            n /= 10;
        }
        
        // 迴圈結束時，n 剛好剩下最高位數字。
        // 條件 1: n != x (最高位不能是 x)
        // 條件 2: has (其他位數必須包含 x)
        return (n != x) && has;
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