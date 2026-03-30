---
title: "LeetCode 790. Domino and Tromino Tiling"
date: 2026-03-30T23:45:00+08:00
lastmod: 2026-03-30T23:45:00+08:00
difficulty: 1830
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic programming", "math"]
keywords: ["LC790", "1830", "Medium", "Domino and Tromino Tiling", "Dynamic Programming", "State Machine"]
description: "LeetCode 第 790 題：Domino and Tromino Tiling。難度評分：1830。探討如何利用多維動態規劃 (Dynamic Programming) 建立狀態機，處理平整與不平整的邊界轉移，以 O(N) 時間複雜度計算所有可能的拼圖組合。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 790](https://leetcode.com/problems/domino-and-tromino-tiling/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1830)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
你有兩種類型的拼圖：
1. `2 x 1` 的骨牌 (Domino)
2. `L` 形的三格骨牌 (Tromino)

你可以旋轉這些拼圖。給定一個整數 `n`，請回傳填滿 `2 x n` 棋盤的方法數。由於答案可能很大，請回傳對 $10^9 + 7$ 取餘數後的結果。

在平鋪棋盤時，棋盤上的每一個方格都必須被拼圖覆蓋。若兩種平鋪方式中，有任何一個方格被不同形狀或不同方向的拼圖覆蓋，則視為不同的平鋪方式。

### 限制條件
- $1 \le n \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-30：初次提交 (二維狀態機 DP)](#2026-03-30-初次提交)

---

## 💡 2026-03-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在填滿 `2 x n` 棋盤的過程中，棋盤的右側邊界可能會出現兩種狀態：
1. **完全平整**：上下兩列皆被填滿。
2. **不平整 (突出一格)**：上一列比下一列多一格，或下一列比上一列多一格。由於這兩種突出的情況在後續的轉移上是對稱的，可以將其合併視為單一狀態進行數量統計。

定義動態規劃狀態陣列 `dp[i][j]`：
- `dp[i][0]`：表示填滿至第 $i$ 行，且第 $i$ 行**完全平整**的方法數。
- `dp[i][1]`：表示填滿至第 $i$ 行，且第 $i$ 行**突出一格**（包含上方突出與下方突出兩種情形的總和）的方法數。

**狀態轉移推導**：
1. **如何達成 `dp[i][0]` (平整)**：
   - 從 `dp[i-1][0]` 加上 1 個垂直骨牌。
   - 從 `dp[i-2][0]` 加上 2 個水平骨牌。
   - 從 `dp[i-1][1]` (突出一格的狀態) 加上 1 個 L 形 Tromino 剛好填平。（因 `dp[i-1][1]` 已包含上下兩種突出，各有唯一一種 L 形能填平，直接相加即可）。
   - 推導公式：`dp[i][0] = dp[i-1][0] + dp[i-2][0] + dp[i-1][1]`
2. **如何達成 `dp[i][1]` (突出一格)**：
   - 從 `dp[i-2][0]` (平整) 加上 1 個 L 形 Tromino。因為可以選擇朝上或朝下，所以產生 $2 \times dp[i-2][0]$ 種方法。
   - 從 `dp[i-1][1]` (已突出) 加上 1 個水平骨牌，使突出的部分向右延伸。
   - 推導公式：`dp[i][1] = 2 * dp[i-2][0] + dp[i-1][1]`

### 🛠️ 解題思路 (Approach)
1. 宣告常數 `MOD = 1e9 + 7`。
2. **邊界處理**：若 `n == 1`，只有 1 種平整的方法，直接回傳 `1`。
3. **初始化 DP 陣列**：宣告大小為 `(n + 1) x 2` 的二維陣列 `dp`，並初始化為 0。
   - `dp[0][0] = 1`：長度為 0 的平整棋盤視為 1 種方法（空集合）。
   - `dp[1][0] = 1`：長度為 1 只能放一個垂直骨牌。
4. **迴圈轉移**：從 $i = 2$ 遍歷至 $n$。
   - 依據推導公式更新 `dp[i][0]` 與 `dp[i][1]`。
   - 每次加法運算後，皆對 `MOD` 取餘數以防止整數溢位。
5. **回傳結果**：迴圈結束後，回傳 `dp[n][0]`（要求最終棋盤必須是平整的）。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需遍歷長度為 $N$ 的迴圈一次，每次轉移為常數時間。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了大小為 $(N + 1) \times 2$ 的二維 DP 陣列。*(註：因每次轉移僅依賴前兩項狀態 $i-1$ 與 $i-2$，此空間可進一步優化為 $\mathcal{O}(1)$ 的滾動變數)*。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const int MOD = 1e9 + 7;
    
    int numTilings(int n) {
        if(n == 1) return 1;
        
        // dp[i][0]: 長度 i 且平整結尾的方法數
        // dp[i][1]: 長度 i 且突出一格的方法數 (已包含上下對稱兩種)
        vector<vector<int>> dp(n + 1, vector<int>(2, 0));
        
        dp[0][0] = 1;
        dp[1][0] = 1;
        
        for(int i = 2; i <= n; ++i) {
            // dp[i][0] = dp[i-1][0] + dp[i-2][0] + dp[i-1][1]
            dp[i][0] = ((dp[i-1][0] + dp[i-1][1]) % MOD + dp[i-2][0]) % MOD;
            
            // dp[i][1] = dp[i-1][1] + 2 * dp[i-2][0]
            dp[i][1] = (dp[i-1][1] + (2LL * dp[i-2][0]) % MOD) % MOD;
        }

        return dp[n][0];
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