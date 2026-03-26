---
title: "LeetCode 2697. Lexicographically Smallest Palindrome"
date: 2026-03-26T16:50:00+08:00
lastmod: 2026-03-26T16:50:00+08:00
difficulty: 1303
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "two pointers", "greedy"]
keywords: ["LC2697", "1303", "Easy", "Palindrome", "Lexicographically Smallest", "Two Pointers"]
description: "LeetCode 第 2697 題：Lexicographically Smallest Palindrome。難度評分：1303。探討如何利用雙指標由外向內比對，並運用貪婪策略以 ASCII 較小的字元取代較大字元，在 O(N) 時間複雜度內構造字典序最小的迴文字串。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2697](https://leetcode.com/problems/lexicographically-smallest-palindrome/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1303)</span>  
> **核心主題**：`String` $\cdot$ `Two Pointers` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個由小寫英文字母組成的字串 `s`。你可以透過執行操作將字串轉換為迴文 (Palindrome)。
在一次操作中，你可以將 `s` 中的任何一個字元替換為另一個小寫英文字母。
請找出一系列操作，使得操作次數最少的情況下，將 `s` 轉換為迴文。若存在多種最少操作次數的解，請回傳其中**字典序最小 (Lexicographically Smallest)** 的迴文字串。

### 限制條件
- $1 \le s.length \le 1000$
- `s` 僅由小寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (雙指標與字元極值判斷)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
迴文字串的定義為：對於長度為 $n$ 的字串，滿足 `s[i] == s[n - 1 - i]`。
要以最少的操作次數達成迴文，我們僅需針對不相等的對應字元對 `(s[i], s[n - 1 - i])` 進行替換，若字元已相等則保持不變。
題目進一步要求回傳「字典序最小」的結果。因此，當發生不相等時，貪婪策略 (Greedy Strategy) 是將這兩個字元皆替換為兩者之中 ASCII 碼較小的那個字元。

### 🛠️ 解題思路 (Approach)
1. 宣告變數 `n` 取得字串 `s` 的長度。
2. 使用雙指標概念，建立迴圈遍歷字串的前半部 (從 `i = 0` 到 `n / 2 - 1`)。
3. 對於每一對互相對應的字元 `s[i]` 與 `s[n - 1 - i]`，計算兩者的最小值：`char mm = min(s[i], s[n-1-i])`。
4. 將兩個對應位置的字元同時更新為該最小值：`s[i] = s[n-1-i] = mm`。
5. 走訪結束後，回傳修改完畢的字串 `s`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串的長度。迴圈執行 $N/2$ 次，每次進行常數時間的字元比對與替換操作。
- **空間複雜度**: $\mathcal{O}(1)$。直接在原字串 `s` 上進行原地替換，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string makeSmallestPalindrome(string s) {
        int n = s.size();
        
        // 走訪字串的前半部，與後半部對應字元進行比對
        for(int i = 0; i < n / 2; ++i) {
            // 取得兩對應字元中 ASCII 碼較小的字元
            char mm = min(s[i], s[n - 1 - i]);
            
            // 將兩側字元皆更新為較小的字元以確保字典序最小
            s[i] = s[n - 1 - i] = mm;
        }

        return s;
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