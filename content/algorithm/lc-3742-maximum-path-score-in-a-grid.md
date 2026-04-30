---
title: "LeetCode 3742. Maximum Path Score in a Grid"
date: 2026-04-30T13:21:00+08:00
lastmod: 2026-04-30T13:21:00+08:00
difficulty: 1804
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "matrix"]
keywords: ["LC3742", "Medium", "1804", "Maximum Path Score", "Grid", "Dynamic Programming"]
description: "LeetCode 第 3742 題：Maximum Path Score in a Grid。難度評分：1804。探討如何解決帶有成本限制的網格路徑 DP 問題，並透過滾動陣列 (Rolling Array) 與 C++ std::move 技巧進行空間複雜度的極致優化。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3742](https://leetcode.com/problems/maximum-path-score-in-a-grid/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1804)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個大小為 $m \times n$ 的二維整數網格 `grid` 和一個整數 `k`。
你從左上角 $(0, 0)$ 出發，目標是走到右下角 $(m-1, n-1)$。每次你只能向右或向下移動一格。
網格中的格子分為兩種：
- **分數格 (`grid[i][j] > 0` 或 `< 0`)**：如果你走進這個格子並選擇「獲取」，你可以將它的值加入總分，但這會消耗 $1$ 點成本 (`cost`)。
- **空白格 (`grid[i][j] == 0`)**：走進這個格子不需要成本，也不會獲得分數。

你最多只能消耗 `k` 點成本。
請回傳在總成本不超過 `k` 的情況下，從起點走到終點所能獲得的**最大總分**。如果無法到達終點（例如必經之路成本超過限制），則回傳 `-1`。

### 限制條件
- $1 \le m, n \le 50$
- $0 \le k \le m + n - 1$
- $-100 \le grid[i][j] \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-30：初次提交 (3D DP 降維與 std::move 滾動優化)](#2026-04-30-初次提交)

---

## 💡 2026-04-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一個標準的「帶有狀態限制」的網格 DP。
我們定義狀態 `dp[i][j][K]` 為：走到座標 $(i, j)$，且總共**恰好消耗了** (或最多消耗了) `K` 點成本時，所能獲得的最大分數。

**狀態轉移方程式**：
對於任意一個格子 $(i, j)$，我們考慮如何從左邊 $(i, j-1)$ 或上面 $(i-1, j)$ 走過來：
1. **當 `grid[i][j] != 0` 時**：
   我們必須消耗 1 點成本。這意味著我們當前的成本狀態 `K`，是由上一步成本為 `K-1` 的狀態轉移而來的。
   $$dp[i][j][K] = \max(dp[i-1][j][K-1], dp[i][j-1][K-1]) + grid[i][j]$$
2. **當 `grid[i][j] == 0` 時**：
   我們不需要消耗成本。這意味著我們當前的成本狀態 `K`，是直接由上一步同樣成本為 `K` 的狀態平移而來。
   $$dp[i][j][K] = \max(dp[i-1][j][K], dp[i][j-1][K])$$

**降維優化 (Space Optimization)**：
觀察轉移方程式可以發現，計算成本為 `K` 的狀態 `next_dp` 時，我們只需要用到成本為 `K-1` 的歷史狀態 `dp`（遇到有分數的格子）或是成本為 `K` 自身已經計算好的左上狀態（遇到空白格）。
因此，我們完全可以省略第三維度，使用兩個二維陣列交替滾動計算，將空間複雜度大幅降低！

### 🛠️ 解題思路 (Approach)
1. **初始化**：
   - 宣告兩個二維陣列 `dp` 和 `next_dp`，大小為 $M \times N$，初始值設為極小值 `INT_MIN / 2` 避免溢位。
   - `next_dp[0][0] = 0` 作為起點，代表消耗成本為 $0$ 時的初始狀態。
2. **成本迴圈 (外層 `K`)**：
   - 從 $K = 0$ 枚舉到 $K = k$。
3. **網格走訪 (內層 `i`, `j`)**：
   - 首先繼承上一個成本狀態的結果，因為「最多消耗 K」也包含了「剛好消耗 K-1」。`next_dp[i][j] = max(next_dp[i][j], dp[i][j])`。
   - **遇到有分數的格子 (`!= 0`)**：只能從歷史成本 (`dp`) 的左方或上方走來，並加上當前分數。
   - **遇到空白格 (`== 0`)**：可以無痛從當前成本 (`next_dp`) 的左方或上方走來。
4. **滾動交換**：
   - 一個成本維度計算完畢後，使用 `dp = std::move(next_dp)` 高效地將結果轉移給歷史狀態陣列。
   - 重新分配一個全新的極小值陣列給 `next_dp`，供下一輪使用。
5. **回傳結果**：
   - 檢查 `dp[m-1][n-1]`，如果依然是極小值 (或小於 0，視題目規定)，代表無解回傳 `-1`，否則回傳最大分數。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N \times K)$。外層成本迴圈執行 $K$ 次，內層走訪 $M \times N$ 的網格，總計約為常數極小的三層迴圈。
- **空間複雜度**: $\mathcal{O}(M \times N)$。使用了兩個二維陣列進行滾動，完美消除了三維陣列帶來的空間負擔。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxPathScore(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();

        // 宣告滾動陣列：dp 代表成本為 K-1 的歷史狀態，next_dp 代表成本為 K 的當前狀態
        vector<vector<int>> dp(m, vector<int>(n, INT_MIN / 2));
        vector<vector<int>> next_dp(m, vector<int>(n, INT_MIN / 2));
        
        // 初始狀態：在起點，成本為 0，分數為 0
        next_dp[0][0] = 0;
        
        // 外層迴圈枚舉允許消耗的成本 K
        for(int K = 0; K <= k; ++K) {
            for(int i = 0; i < m; ++i) {
                for(int j = 0; j < n; ++j) {
                    
                    // 繼承：消耗 K 點成本的最佳解，絕對不會比消耗 K-1 點更差
                    next_dp[i][j] = max(next_dp[i][j], dp[i][j]);
                    
                    if(grid[i][j] != 0) {
                        // 有分數的格子：必須花費 1 點成本。
                        // 因此必須從歷史狀態 (dp) 的左邊或上面轉移過來
                        int from_top = (i >= 1) ? dp[i-1][j] : INT_MIN / 2;
                        int from_left = (j >= 1) ? dp[i][j-1] : INT_MIN / 2;
                        
                        next_dp[i][j] = max(next_dp[i][j], max(from_top, from_left) + grid[i][j]);
                    } else {
                        // 空白格子：不花費成本。
                        // 直接從當前狀態 (next_dp) 的左邊或上面轉移過來
                        int from_top = (i >= 1) ? next_dp[i-1][j] : INT_MIN / 2;
                        int from_left = (j >= 1) ? next_dp[i][j-1] : INT_MIN / 2;
                        
                        next_dp[i][j] = max(next_dp[i][j], max(from_top, from_left));
                    }
                }
            }
            
            // 滾動更新：使用 std::move 避免 vector 拷貝開銷，提升效能
            dp = std::move(next_dp);
            next_dp = vector<vector<int>>(m, vector<int>(n, INT_MIN / 2));
        }

        // 若最終終點仍為負數極小值，代表無法抵達
        return (dp[m-1][n-1] < 0 ? -1 : dp[m-1][n-1]);
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