---
title: "LeetCode 3922. Minimum Flips to Make Binary String Coherent"
date: 2026-05-10T19:30:00+08:00
lastmod: 2026-05-10T19:30:00+08:00
difficulty: 1759
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy", "counting"]
keywords: ["LC3922", "Medium", "Binary String", "Coherent", "Greedy", "貪心"]
description: "LeetCode 第 3922 題：Minimum Flips to Make Binary String Coherent。探討如何透過統計 '1' 的分佈，利用枚舉目標狀態的貪心策略，以 O(N) 時間複雜度解決二進位字串的最小翻轉問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3922](https://leetcode.com/problems/minimum-flips-to-make-binary-string-coherent/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1759)</span>  
> **核心主題**：`String` $\cdot$ `Greedy` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個僅由 '0' 和 '1' 組成的字串 `s`。
如果一個字串滿足以下條件之一，則稱其為「協調的 (Coherent)」：
1. 字串中 '1' 的個數少於 2。
2. 字串中所有的 '1' 都位於邊界（即首位或末位，或者兩者都是）。
3. 字串全部由 '1' 組成。

請計算將 `s` 轉化為「協調字串」所需的最小翻轉次數。

### 限制條件
- $1 \le s.length \le 10^5$
- `s` 僅包含 '0' 和 '1'。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-10：初次提交 (枚舉目標狀態之貪心法)](#2026-05-10-初次提交)

---

## 💡 2026-05-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這道題目的定義雖然特殊，但目標狀態非常明確且數量有限。要讓字串變得「協調」，我們不需要考慮複雜的子序列，只需要考慮幾種特定的目標佈局：

1. **基礎情況 (Base Case)**：
   如果原始字串中 '1' 的數量 $t_1 < 2$，根據定義它已經是「協調的」，翻轉次數為 $0$。

2. **目標狀態枚舉**：
   - **目標一：全 1 陣列 (All Ones)**
     成本為將所有 '0' 翻轉為 '1'：$n - t_1$。
   - **目標二：孤狼模式 (Single One)**
     成本為只保留一個 '1'，其餘全部翻轉為 '0'：$t_1 - 1$。
   - **目標三：邊界守衛 (Boundary Guards)**
     成本為移除所有「內部」的 '1'，只保留索引 $0$ 和 $n-1$ 位置上的 '1'（若該位置原本不是 '1'，則需補上，但根據貪心，我們只需移除內部的 '1'）。
     公式為：$t_1 - (s[0] == '1') - (s.back() == '1')$。

### 🛠️ 解題思路 (Approach)
1. **統計總量**：先遍歷一次字串，統計出 '1' 的總數 `t1`。
2. **條件攔截**：若 `t1 < 2`，直接回傳 `0`。
3. **成本取極小值**：
   - 計算 `n - t1` (變全 1)。
   - 計算 `t1 - 1` (變單 1)。
   - 計算 `t1 - (開頭是否為1) - (結尾是否為1)` (變純邊界)。
4. 取以上三者的最小值即為答案。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。僅需遍歷一次字串統計 '1' 的個數，後續判斷與比較皆為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個整數變數，不需要額外的資料結構。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minFlips(string s) {
        int n = s.size();
        int t1 = 0;
        
        // 1. 統計字串中 '1' 的總數
        for(int i = 0; i < n; ++i) {
            if(s[i] == '1') ++t1;
        }

        // 2. 根據題目定義，'1' 的數量少於 2 則自動協調
        if(t1 < 2) return 0;

        // 3. 貪心枚舉三種目標狀態並取最小值：
        // (1) n - t1: 將所有 0 翻轉為 1 (變為全 1 字串)
        // (2) t1 - 1: 將所有 1 翻轉為 0，僅保留其中一個 (變為單 1 字串)
        // (3) t1 - (首位是否1) - (末位是否1): 僅保留邊界的 1，翻轉所有內部的 1
        int option1 = n - t1;
        int option2 = t1 - 1;
        int option3 = t1 + (s[0] == '1' ? -1 : 0) + (s.back() == '1' ? -1 : 0);

        return min({option1, option2, option3});
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