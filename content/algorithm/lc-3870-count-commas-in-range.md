---
title: "LeetCode 3870. Count Commas in Range"
date: 2026-03-15T15:50:00+08:00
lastmod: 2026-03-15T15:50:00+08:00
difficulty: 1149
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Math"]
keywords: ["LC3870", "1149", "Easy", "Commas", "Math", "O(1)"]
description: "LeetCode 第 3870 題：Count Commas in Range。難度評分：1149。探討如何利用數字範圍特性與數學公式，將字串運算降維成 O(1) 的極速解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3870](https://leetcode.com/problems/count-commas-in-range/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1149)</span>   
> **核心主題**：`Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數 $n$。當你將從 $1$ 到 $n$ 的所有整數以標準格式寫出（也就是每三位數使用一個逗號分隔，例如 `1,000`, `100,000`）時，請計算總共會使用到多少個逗號。

### 限制條件
- $1 \le n \le 10^5$ *(依據解法與註解推斷)*
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (O(1) 數學公式計算)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
這題如果使用迴圈搭配字串轉換，雖然直覺但效率極差。
仔細觀察數字格式的規律：
- $1$ 到 $999$：沒有任何逗號 (0 個)。
- $1,000$ 到 $999,999$：每個數字恰好包含 1 個逗號。
- $1,000,000$ 以上：每個數字會包含 2 個以上的逗號。

根據題目的資料範圍（$n$ 最大約為 100,000），我們永遠不會遇到需要 2 個逗號的數字（即一百萬）。因此，這題直接轉化為一個簡單的數學問題：「在 $1$ 到 $n$ 之間，有幾個大於等於 1000 的數字？」

### 🛠️ 解題思路 (Approach)
1. **邊界判斷**：如果 $n < 1000$，代表完全不需要逗號，結果為 0。
2. **區間計算**：如果 $n \ge 1000$，則範圍 $[1000, n]$ 內的每一個數字都會貢獻 1 個逗號。根據區間長度公式，總數量為 $n - 1000 + 1$。
3. **一行化簡**：利用三元運算子 `(n - 1000 < 0 ? 0 : n - 1000 + 1)` 即可在單行內完美處理所有情況。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。只進行了基礎的算術運算與條件判斷。
- **空間複雜度**: $\mathcal{O}(1)$。沒有使用任何額外的記憶體空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int countCommas(int n) {
        // 如果 n 小於 1000，逗號數為 0
        // 否則，每個大於等於 1000 的數字都貢獻 1 個逗號
        return (n - 1000 < 0 ? 0 : n - 1000 + 1);
    }
};

// 註解：測資上限約為 100,000，不會超過 1,000,000，故最多只有一個逗號
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