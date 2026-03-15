---
title: "LeetCode 3871. Count Commas in Range II"
date: 2026-03-15T15:51:00+08:00
lastmod: 2026-03-15T15:51:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Math"]
keywords: ["LC3871", "N/A", "Medium", "Commas", "Math", "O(1)", "Cumulative Contribution"]
description: "LeetCode 第 3871 題：Count Commas in Range II。難度評分：N/A。探討如何利用「貢獻度疊加」的數學思想，將巨量資料規模的字串逗號計算問題化簡為 O(1) 的極速公式解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3871](https://leetcode.com/problems/count-commas-in-range-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>    
> **核心主題**：`Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
此題為 [Count Commas in Range](../LC3870/) 的進階版。
給定一個極大的整數 $n$。當你將從 $1$ 到 $n$ 的所有整數以標準格式寫出（也就是每三位數使用一個逗號分隔，例如 `1,000`, `1,000,000`）時，請計算總共會使用到多少個逗號。

### 限制條件
- $1 \le n \le 10^{15}$ *(依據解法推斷)*
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (O(1) 貢獻度疊加數學解)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
當資料規模 $n$ 來到 $10^{15}$，任何 $\mathcal{O}(N)$ 的遍歷做法都會立刻收到 Time Limit Exceeded (TLE)。我們必須延續上一題的 $\mathcal{O}(1)$ 數學思維。
遇到多個區間的計算時，常見的痛點是去計算「嚴格包含 $k$ 個逗號的區間長度」，這往往會引入繁瑣的邊界加減運算。
更聰明的數學直覺是使用 **「貢獻度疊加 (Cumulative Contribution)」**：
- 所有 $\ge 10^3$ 的數字，都至少有 1 個逗號，所以它們共同提供了底層的 1 個逗號。
- 所有 $\ge 10^6$ 的數字，除了剛才底層的 1 個逗號，它們還會提供第 2 個逗號。
- 所有 $\ge 10^9$ 的數字，會再提供第 3 個逗號。
我們只需要把這每一層的「有效數量」全部相加，就能完美得到總逗號數，且邏輯極度清晰！

### 🛠️ 解題思路 (Approach)
1. **界定門檻值**：逗號的產生門檻分別為 $10^3, 10^6, 10^9, 10^{12}, 10^{15}$。
2. **條件展開 (Loop Unrolling)**：
   為了追求極致的效能與避免浮點數次方 `pow()` 帶來的潛在精度誤差，直接使用 `if` 條件式將 $n$ 的範圍進行攔截。
   - 若 $n < 1000$，毫無貢獻，回傳 `0`。
   - 若 $n < 10^6$，只有第一層門檻有貢獻，回傳 `n - 1000 + 1`。
   - 若 $n < 10^9$，前兩層都有貢獻，回傳 `(n - 1000 + 1) + (n - 1000000 + 1)`。
   - 以此類推，將每一層超出門檻的數量不斷疊加並回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。沒有迴圈，只有單純的條件分支與基礎加減法，運算時間為常數級別。
- **空間複雜度**: $\mathcal{O}(1)$。無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long countCommas(long long n) {
        // 利用貢獻度疊加 (Cumulative Contribution) 計算
        if(n < 1000LL) 
            return 0;
        if(n < 1000000LL) 
            return n - 1000 + 1;
        if(n < 1000000000LL) 
            return n - 1000 + 1 + n - 1000000 + 1;
        if(n < 1000000000000LL) 
            return n - 1000 + 1 + n - 1000000 + 1 + n - 1000000000 + 1;
        if(n < 1000000000000000LL) 
            return n - 1000 + 1 + n - 1000000 + 1 + n - 1000000000 + 1 + n - 1000000000000 + 1;
            
        return n - 1000 + 1 + n - 1000000 + 1 + n - 1000000000 + 1 + n - 1000000000000 + 1 + n - 1000000000000000 + 1;
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