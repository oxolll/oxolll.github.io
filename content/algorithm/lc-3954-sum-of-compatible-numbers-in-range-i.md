---
title: "LeetCode 3954. Sum of Compatible Numbers in Range I"
date: 2026-06-09T14:15:00+08:00
lastmod: 2026-06-09T14:15:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "bit-manipulation", "simulation"]
keywords: ["LC3954", "Easy", "Sum of Compatible Numbers", "位元運算", "互斥", "區間掃描"]
description: "LeetCode 第 3954 題：Sum of Compatible Numbers in Range I。探討如何利用位元運算 AND (n & i == 0) 判斷數字的相容性，並透過邊界防護進行 O(K) 的區間線性掃描以求得總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3954](https://leetcode.com/problems/sum-of-compatible-numbers-in-range-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Bit Manipulation` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個正整數 `n` 和 `k`。
我們定義一個正整數 `i` 與 `n` 是 **Compatible (相容的)**，若且唯若它們在二進位表示中，沒有任何同一個位元皆為 1。在程式語言中，這等價於位元運算 `(n & i) == 0`。

請你計算並回傳在閉區間 `[max(1, n - k), n + k]` 中，所有與 `n` 相容的正整數 `i` 的總和。

### 限制條件
*(依據 Range I 特性推斷)*
- $1 \le n \le 10^5$
- $1 \le k \le 10^5$
- 區間長度 $2k$ 允許線性時間複雜度的掃描。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (位元運算與區間掃描)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的模擬題。
目標是尋找特定區間內滿足「相容性」的數字總和。
- **搜索區間**：根據題意，我們只需檢查中心點為 `n`，向左右延伸 `k` 的視窗。同時因為必須是正整數，左邊界需使用 `max(1, n - k)` 來防禦負數或零。
- **核心判定 (Bitwise AND)**：`i` 與 `n` 互斥 (相容)，代表它們的二進位完全錯開，沒有交集。這可以利用 $\mathcal{O}(1)$ 的位元運算 `(n & i) == 0` 來瞬間判定。

### 🛠️ 解題思路 (Approach)
1. 宣告總和變數 `rt = 0`。
2. 使用 `for` 迴圈掃描指定區間：起點為 `max(1, n - k)`，終點為 `n + k`。
3. 對於區間內的每一個數字 `i`，檢查 `(n & i) == 0`。
4. 若條件成立，則將 `i` 加進總和 `rt`。
5. 回傳結果。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(K)$。迴圈執行的次數為區間長度，即 $(n + k) - \max(1, n - k) + 1$，數量級大致為 $2k$。內部判定為 $\mathcal{O}(1)$ 位元運算，整體時間為嚴格線性。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個整數變數，無需額外記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int sumOfGoodIntegers(int n, int k) {
        int rt = 0;
        
        // 掃描區間：從 max(1, n-k) 到 n+k
        for(int i = max(1, n - k); i <= n + k; ++i) {
            // 位元運算 AND 檢查相容性：若二進位沒有共同的 1，結果即為 0
            if((n & i) == 0) {
                rt += i;
            }
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