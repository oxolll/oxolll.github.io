---
title: "LeetCode 1689. Partitioning Into Minimum Number Of Deci-Binary Numbers"
date: 2026-03-26T15:40:00+08:00
lastmod: 2026-03-26T15:40:00+08:00
difficulty: 1355
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "math", "greedy"]
keywords: ["LC1689", "1355", "Medium", "Deci-Binary", "Greedy"]
description: "LeetCode 第 1689 題：Partitioning Into Minimum Number Of Deci-Binary Numbers。難度評分：1355。探討 Deci-Binary 數值的加法性質，將組合問題轉化為尋找字串中最大字元的線性時間解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1689](https://leetcode.com/problems/partitioning-into-minimum-number-of-deci-binary-numbers/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1355)</span>  
> **核心主題**：`String` $\cdot$ `Math` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
一個十進位數字如果不包含任何前導零，且其所有位數都是 0 或 1，則我們稱其為「Deci-Binary (十-二進位)」數字。例如 101 和 1100 是 Deci-Binary 數字，而 112 和 3001 不是。
給定一個表示正整數的字串 `n`，請回傳需要多少個 Deci-Binary 數字相加才能得到 `n`，並要求所使用的 Deci-Binary 數字數量最少。

### 限制條件
- $1 \le n.length \le 10^5$
- `n` 僅由數字組成。
- `n` 不包含任何前導零，且代表一個正整數。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (貪婪策略與字元尋找最大值)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道基於數學性質的構造題。Deci-Binary 數字的每一位只能是 0 或 1。
這意味著，在執行多個 Deci-Binary 數字相加時，每一個 Deci-Binary 數字對特定位數（個位、十位、百位等）的數值貢獻，最多只能是 1。
要構造出目標數字 `n`，對於 `n` 中的任何一個位數 $d$，我們最少需要 $d$ 個 Deci-Binary 數字在該位置上提供 1。
因此，整個數字 `n` 所需要的最少 Deci-Binary 數字總數，直接取決於 `n` 之中數值最大的那一個位數。

### 🛠️ 解題思路 (Approach)
1. 宣告整數 `mx` 記錄當前找到的最大位數，初始值設為 0。
2. 將字串 `n` 視為字元陣列進行線性走訪。
3. 將字元轉換為整數數值 (`c - '0'`)，並更新 `mx = max(mx, c - '0')`。
4. 走訪結束後，回傳 `mx` 即為最少需要的 Deci-Binary 數字數量。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(L)$。其中 $L$ 是字串 `n` 的長度。只需走訪字串一次。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用單一整數變數 `mx` 進行狀態維護。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minPartitions(string n) {
        int mx = 0;
        
        // 尋找字串中的最大數字字元
        for(auto c : n) {
            mx = max(mx, c - '0');
        }
        
        return mx;
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