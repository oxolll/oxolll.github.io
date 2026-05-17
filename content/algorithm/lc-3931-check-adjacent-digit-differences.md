---
title: "LeetCode 3931. Check Adjacent Digit Differences"
date: 2026-05-18T01:20:00+08:00
lastmod: 2026-05-18T01:20:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "simulation"]
keywords: ["LC3931", "Easy", "Adjacent Differences", "字串處理", "相鄰差值"]
description: "LeetCode 第 3931 題：Check Adjacent Digit Differences。難度評分：Easy。探討如何利用字元 ASCII 碼的連續性，在單次線性掃描中高效檢查相鄰數字的差值是否符合限制。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3931](https://leetcode.com/problems/check-adjacent-digit-differences/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個僅由數字字元（'0'-'9'）組成的字串 `s`。
請檢查字串中所有 **相鄰數字** 之間的絕對差值是否 **至多為 2**。

也就是說，對於所有滿足 $0 \le i < s.length - 1$ 的索引 $i$，都必須滿足：
$$|s[i] - s[i+1]| \le 2$$

如果滿足條件，回傳 `true`；否則回傳 `false`。

### 範例
- **Input**: `s = "2435"`
- **Output**: `true`
- **Explanation**: 
  - $|2 - 4| = 2 \le 2$
  - $|4 - 3| = 1 \le 2$
  - $|3 - 5| = 2 \le 2$
  全部符合條件。

- **Input**: `s = "142"`
- **Output**: `false`
- **Explanation**: $|1 - 4| = 3 > 2$，不符合條件。

### 限制條件
- $2 \le s.length \le 100$
- `s` 僅包含數字字元。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-18：初次提交 (線性掃描與 ASCII 差值法)](#2026-05-18-初次提交)

---

## 💡 2026-05-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道標準的字串相鄰元素檢查題目，非常適合用來練習基礎的迴圈控制與字元運算。

**核心破局點：**
1. **ASCII 直減特性**：在 C++ 中，`char` 類型參與數學運算時會自動提升為它的 ASCII 整數值。由於 `'0'` 到 `'9'` 的 ASCII 碼分別是 $48$ 到 $57$（具有連續性），因此 `abs(s[i+1] - s[i])` 計算出的結果，與將它們轉成整數後再相減的結果完全相同。
2. **早停優化 (Early Exit)**：只要在遍歷過程中發現任意一組相鄰字元的絕對差大於 2，就可以立即判定此字串不合法並回傳 `false`，不需浪費時間檢查後續字元。

### 🛠️ 解題思路 (Approach)
1. 使用單層迴圈，從索引 `0` 遍歷到 `s.size() - 2`（即倒數第二個字元）。
2. 在每次迭代中，計算 `s[i]` 與 `s[i+1]` 的絕對差。
3. 若 `abs(s[i+1] - s[i]) > 2`，立即回傳 `false`。
4. 若順利結束迴圈，代表所有相鄰點均通過驗證，回傳 `true`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串 `s` 的長度。在最壞情況下，我們需要將整個字串掃描一遍，執行 $N-1$ 次比較。
- **空間複雜度**: $\mathcal{O}(1)$。演算法只使用了常數個指標與暫存變數，沒有向系統申請額外的記憶體空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool isAdjacentDiffAtMostTwo(string s) {
        // 建立邊界掃描，迴圈終點設在 s.size() - 1 之前
        // 註：題目限制 s.length >= 2，故 s.size() - 1 不會發生無號整數溢位 (Underflow)
        for(int i = 0; i < s.size() - 1; ++i) {
            // 利用字元 ASCII 碼的連續性直接計算絕對差
            if(abs(s[i+1] - s[i]) > 2) {
                return false; // 捕捉到不合法的跳躍，立即中斷
            }
        }
        return true; // 順利通過全域檢查
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