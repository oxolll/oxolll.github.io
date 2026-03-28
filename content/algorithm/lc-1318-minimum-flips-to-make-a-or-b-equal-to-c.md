---
title: "LeetCode 1318. Minimum Flips to Make a OR b Equal to c"
date: 2026-03-28T13:42:00+08:00
lastmod: 2026-03-28T13:42:00+08:00
difficulty: 1382
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "bit manipulation", "math"]
keywords: ["LC1318", "1382", "Medium", "Minimum Flips", "Bitwise OR", "Bit Manipulation"]
description: "LeetCode 第 1318 題：Minimum Flips to Make a OR b Equal to c。難度評分：1382。探討如何利用位元運算 (Bit Manipulation) 逐位檢查並計算使 `a OR b == c` 所需的最小翻轉次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1318](https://leetcode.com/problems/minimum-flips-to-make-a-or-b-equal-to-c/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1382)</span>  
> **核心主題**：`Bit Manipulation` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定三個正整數 `a`, `b` 和 `c`。
你可以選擇將 `a` 和 `b` 的某些位元翻轉（0 變成 1，1 變成 0）。
請回傳使 `(a OR b) == c` 成立所需的**最小翻轉次數**。

### 限制條件
- $1 \le a, b, c \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-28：初次提交 (逐位檢查與右移法)](#2026-03-28-初次提交)

---

## 💡 2026-03-28 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道標準的位元運算題。由於各個位元的操作是獨立的，我們只需要逐位 (Bit-by-Bit) 檢查 `a`, `b` 與 `c` 在對應位置的值，並根據目標值 `c` 決定翻轉邏輯：
對於給定的位元：
1. **目標 `c` 該位元為 1**：此時 `a` 或 `b` 至少需要有一個 1。若兩者皆為 0，則必須翻轉其中一個，翻轉次數 + 1。
2. **目標 `c` 該位元為 0**：此時 `a` 和 `b` 在該位元都必須是 0。若其中有任何 1，都必須翻轉為 0。因此翻轉次數即為 `a` 該位元的值加上 `b` 該位元的值。

透過右移運算 (`>> 1`) 與位元 AND 運算 (`& 1`)，我們可以從最低位一路檢查至最高位。

### 🛠️ 解題思路 (Approach)
1. 宣告 `rt = 0` 記錄翻轉次數。
2. **第一階段檢查** (當 `c` 尚未歸零時)：
   - 使用 `while(c)` 迴圈。
   - 提取當前 `a`, `b`, `c` 的最低位元 (`a&1`, `b&1`, `c&1`)。
   - 判斷邏輯 1：若 `c` 的位元為 1，但 `a` 與 `b` 皆為 0，則 `rt` 增加 1。
   - 判斷邏輯 2：若 `c` 的位元為 0，則 `rt` 增加 `(a&1) + (b&1)`（將所有 1 強制翻成 0）。
   - 將 `a`, `b`, `c` 皆向右位移一位 (`>>= 1`)。
3. **第二階段檢查** (處理 `a` 或 `b` 高於 `c` 的剩餘位元)：
   - 當 `c` 已歸零，但 `a` 或 `b` 還有剩餘位元時（代表 `c` 該位元視為 0）。
   - 使用 `while(a || b)` 迴圈，將任何剩餘的 1 皆視為需翻轉的位元：`rt += (a&1) + (b&1)`。
   - 將 `a` 與 `b` 向右位移一位。
4. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log(\max(a,b,c)))$。對於 32-bit 整數，最多執行 32 次迴圈操作，時間複雜度視為 $\mathcal{O}(1)$ 常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用常數個整數變數，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minFlips(int a, int b, int c) {
        int rt = 0;
        
        // 1. 逐位比較直到 c 處理完畢
        while(c) {
            // 若目標位元為 1，但 a 和 b 都是 0，必須翻轉其中一個
            if((c & 1) && (a & 1) == 0 && (b & 1) == 0) {
                ++rt;
            }
            // 若目標位元為 0，a 和 b 的 1 都必須翻轉成 0
            if((c & 1) == 0) {
                rt += (a & 1) + (b & 1);
            }

            c >>= 1;
            a >>= 1;
            b >>= 1;
        }

        // 2. 處理 c 已歸零，但 a 或 b 仍有殘留位元的情況
        // 這些高位元的目標值皆為 0，故只要有 1 就必須翻轉
        while(a || b) {
            rt += (a & 1) + (b & 1);
            a >>= 1;
            b >>= 1;
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