---
title: "LeetCode 1143. Longest Common Subsequence"
date: 2026-04-01T10:05:00+08:00
lastmod: 2026-04-01T10:05:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "dynamic-programming"]
keywords: ["LC1143", "Medium", "N/A", "LCS", "Longest Common Subsequence", "Dynamic Programming"]
description: "LeetCode 第 1143 題：Longest Common Subsequence。難度評分：Medium (N/A)。探討如何定義二維 DP 狀態並利用字元匹配條件進行轉移，以 O(M * N) 時間複雜度求解兩字串的最長共同子序列長度。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1143](https://leetcode.com/problems/longest-common-subsequence/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Dynamic Programming`

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (二維 LCS 標準實作)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
最長共同子序列 (LCS) 是字串比對的經典動態規劃題型。
定義狀態 `dp[i][j]` 表示字串 `text1` 的前 `i` 個字元與字串 `text2` 的前 `j` 個字元的 LCS 長度。
狀態轉移分為兩種情況：
1. **字元相等** (`text1[i-1] == text2[j-1]`)：這兩個字元必定屬於 LCS 的一部分，因此長度等於這兩個字元抵銷前的狀態加上 1，即 `dp[i-1][j-1] + 1`。
2. **字元不相等** (`text1[i-1] != text2[j-1]`)：這兩個字元不可能同時存在於 LCS 中，因此我們必須捨棄其中一個，並取兩種情況的最大值，即 `max(dp[i-1][j], dp[i][j-1])`。

### 🛠️ 解題思路 (Approach)
1. 取得兩字串長度 `m` 與 `n`。
2. 宣告大小為 $(m + 1) \times (n + 1)$ 的二維陣列 `dp`，並初始化為 0（索引 0 作為空字串的邊界條件）。
3. 使用雙層迴圈，`i` 從 1 到 `m`，`j` 從 1 到 `n`。
4. 依據上述轉移方程式進行計算與更新。
5. 迴圈結束後，回傳右下角的值 `dp[m][n]`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。需遍歷長度分別為 $M$ 與 $N$ 的字串所構成的矩陣。
- **空間複雜度**: $\mathcal{O}(M \times N)$。宣告了一個二維 DP 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int m = text1.size(), n = text2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
        
        for(int i = 1; i <= m; ++i) {
            for(int j = 1; j <= n; ++j) {
                // 字元相等，延續左上角狀態並加 1
                if(text1[i-1] == text2[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } 
                // 字元不等，取左方或上方的最大值
                else {
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
                }
            }
        }

        return dp[m][n];
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