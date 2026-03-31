---
title: "LeetCode 72. Edit Distance"
date: 2026-04-01T15:35:00+08:00
lastmod: 2026-04-01T15:35:00+08:00
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
keywords: ["LC72", "Medium", "N/A", "Edit Distance", "Levenshtein Distance", "Dynamic Programming"]
description: "LeetCode 第 72 題：Edit Distance。難度評分：Medium (N/A)。探討如何利用二維動態規劃 (2D DP) 計算兩個字串間的萊文斯坦距離 (Levenshtein Distance)，處理字元的插入、刪除與替換操作。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 72](https://leetcode.com/problems/edit-distance/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Dynamic Programming`

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (二維動態規劃)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求計算大名鼎鼎的「萊文斯坦距離 (Levenshtein Distance)」。
我們定義 `dp[i][j]` 為將 `word1` 的前 `i` 個字元轉換為 `word2` 的前 `j` 個字元所需的最少操作次數。

**狀態轉移推導：**
1. 如果 `word1[i-1] == word2[j-1]`：不需要進行任何操作，成本直接從左上角繼承，即 `dp[i][j] = dp[i-1][j-1]`。
2. 如果字元不相等，我們面臨三種操作決策，必須取其最小值並加上 1：
   - **刪除 (Delete)**：刪除 `word1[i-1]`，等價於看 `word1` 前 `i-1` 個字元轉換到 `word2` 前 `j` 個字元的成本，即 `dp[i-1][j] + 1`。
   - **插入 (Insert)**：在 `word1` 尾端插入 `word2[j-1]`，等價於看 `word1` 前 `i` 個字元轉換到 `word2` 前 `j-1` 個字元的成本，即 `dp[i][j-1] + 1`。
   - **替換 (Replace)**：將 `word1[i-1]` 替換為 `word2[j-1]`，等價於看兩個字串扣除最後一個字元的成本，即 `dp[i-1][j-1] + 1`。

### 🛠️ 解題思路 (Approach)
1. 處理空字串的防呆條件：若任一字串為空，直接回傳另一字串的長度。
2. 宣告大小為 `(m+1) x (n+1)` 的 DP 陣列，初始化為 `INT_MAX/2` 避免加法溢位。
3. **初始化邊界條件**：
   - `dp[i][0] = i`：將長度 `i` 的字串變成空字串，需要 `i` 次刪除。
   - `dp[0][j] = j`：將空字串變成長度 `j` 的字串，需要 `j` 次插入。
4. 使用雙層迴圈遍歷，根據字元是否相等進行狀態轉移。
5. 迴圈結束後，回傳 `dp[m][n]`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。其中 $M$ 和 $N$ 分別為兩字串的長度。
- **空間複雜度**: $\mathcal{O}(M \times N)$。宣告了一個二維 DP 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        if(word1.empty()) return word2.size();
        if(word2.empty()) return word1.size();
        
        int m = word1.size(), n = word2.size();
        // 使用 INT_MAX / 2 防止後續加 1 運算產生 Overflow
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, INT_MAX / 2));
        
        // 初始化邊界
        for(int i = 0; i <= m; ++i) dp[i][0] = i; // 全刪除
        for(int i = 0; i <= n; ++i) dp[0][i] = i; // 全插入
        
        for(int i = 1; i <= m; ++i) {
            for(int j = 1; j <= n; ++j) {
                if(word1[i-1] == word2[j-1]) {
                    dp[i][j] = dp[i-1][j-1]; // 免費過關
                } else {
                    // 取 刪除、插入、替換 三者的最小值 + 1
                    dp[i][j] = min({dp[i-1][j] + 1, 
                                    dp[i][j-1] + 1, 
                                    dp[i-1][j-1] + 1});
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