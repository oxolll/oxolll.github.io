---
title: "LeetCode 338. Counting Bits"
date: 2026-04-01T10:10:00+08:00
lastmod: 2026-04-01T10:10:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "bit-manipulation"]
keywords: ["LC338", "Easy", "N/A", "Counting Bits", "Brian Kernighan's Algorithm"]
description: "LeetCode 第 338 題：Counting Bits。難度評分：Easy (N/A)。探討如何利用 Brian Kernighan 演算法逐位消除最低位元的 1 以計算二進位 1 的數量，並補充 O(N) 動態規劃的最佳化解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 338](https://leetcode.com/problems/counting-bits/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Bit Manipulation`

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (Brian Kernighan 演算法)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
對於計算單一數字二進位中 `1` 的數量，可以使用 **Brian Kernighan 演算法**：利用位元運算 `j & -j` 取得最低位的 `1` (LSB)，然後利用 `j -= (j & -j)` 將其消除（或直接使用等價的 `j &= (j - 1)`）。每次消除都能讓計數器加一，直到數字歸零，這樣的運算次數完全等同於 `1` 的數量。

### 🛠️ 解題思路 (Approach)
1. 宣告結果陣列 `rt`。
2. 建立迴圈，`i` 從 0 遍歷至 `n`。
3. 使用內部 `while` 迴圈，針對每一個 `i` 執行位元消除操作，並累加計數 `s`。
4. 結算後將 `s` 推入 `rt` 陣列，最終回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。對 $N$ 個數字各執行一次迴圈，最差情況下每次迴圈執行次數為二進位總長度 $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(1)$。除回傳陣列外無額外空間開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> rt;
        for(int i = 0; i <= n; ++i) {
            int j = i;
            int s = 0;
            // Brian Kernighan 演算法：每次消除最低位的 1
            while(j) {
                j -= (j & -j); // 等價於 j &= (j - 1)
                ++s;
            }
            rt.push_back(s);
        }      
        return rt;
    }
};
```

---

### 💡 進階最佳化：$\mathcal{O}(N)$ 動態規劃 (Dynamic Programming)
題目進階要求在 $\mathcal{O}(N)$ 時間內解出。我們可以利用已知的狀態進行推導：
任何數字 `i` 二進位中 `1` 的數量，必定等於 `i` 向右位移一位 (`i >> 1`，即 `i / 2`) 的 `1` 數量，加上 `i` 的最低位元是否為 `1` (`i & 1`)。
狀態轉移方程式：`dp[i] = dp[i >> 1] + (i & 1)`。

<details>
<summary><b>點擊展開 O(N) 動態規劃實作程式碼</b></summary>

```cpp
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> dp(n + 1, 0);
        for(int i = 1; i <= n; ++i) {
            dp[i] = dp[i >> 1] + (i & 1);
        }
        return dp;
    }
};
```
</details>

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