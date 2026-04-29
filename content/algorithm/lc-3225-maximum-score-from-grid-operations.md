---
title: "LeetCode 3225. Maximum Score From Grid Operations"
date: 2026-04-29T21:24:00+08:00
lastmod: 2026-04-29T21:24:00+08:00
difficulty: 3027
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "prefix-sum", "optimization"]
keywords: ["LC3225", "Hard", "3027", "Maximum Score", "Grid Operations", "DP Optimization"]
description: "LeetCode 第 3225 題：Maximum Score From Grid Operations。難度評分高達 3027。探討如何利用三維動態規劃 (3D DP) 解決網格著色得分問題，並透過精巧的代數移項與前綴最大值預處理，將 O(N^4) 的狀態轉移極限優化至 O(N^3)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3225](https://leetcode.com/problems/maximum-score-from-grid-operations/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (3027)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Prefix Sum` $\cdot$ `State Optimization`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個大小為 $n \times n$ 的二維整數矩陣 `grid`。
最初，矩陣中的所有儲存格都是白色的。在一次操作中，你可以選擇任何一個儲存格並將其塗成黑色。
在執行任意次數的操作後，我們將依據以下規則計算得分：
- 如果一個白色儲存格，其左邊或右邊**緊鄰**的儲存格是黑色的，則該白色儲存格的值將被加到總分中。
（註：一個白色儲存格最多只能被計算一次，即使它的左右都是黑色。）

此外，塗成黑色的格子必須滿足一個條件：**每一行的黑色格子必須是從最頂部開始連續往下塗的**（即每一行只能選擇塗頂部的 $k$ 個格子）。
請找出並回傳可以獲得的**最大總分**。

### 限制條件
- $1 \le n \le 100$
- $0 \le grid[i][j] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-29：初次提交 (3D DP 與狀態轉移 O(N³) 優化)](#2026-04-29-初次提交)

---

## 💡 2026-04-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
因為得分的條件僅依賴於「相鄰欄 (Column)」的狀態，這是一個標準的**列/欄狀態動態規劃 (Profile DP)** 問題。
由於每一行的黑格必須由上而下連續，我們可以將每一行的狀態簡化為一個數字：**「該行有幾個黑格」**。

**狀態定義**：
我們定義 `dp[j-1][i][k]` 為：考慮到第 $j$ 行（程式碼中對應 $j$ 與 $j-1$ 的互動），且第 $j$ 行有 $i$ 個黑格，第 $j-1$ 行有 $k$ 個黑格時，所能獲得的最大分數。

**狀態轉移與避免重複計算**：
要計算走到第 $j$ 行的分數，我們必須回溯第 $j-2$ 行的黑格數量 $L$。
1. **當 $i \le k$**：
   第 $j$ 行的黑格較少，因此第 $j$ 行從 $i$ 到 $k$ 之間的白格，會因為第 $j-1$ 行的黑格而獲得分數。
   增加的分數：`pre[j][k] - pre[j][i]`。
   *(此情況增加的分數與 $L$ 完全無關)*。
2. **當 $i > k$**：
   第 $j$ 行的黑格較多，因此第 $j-1$ 行從 $k$ 到 $i$ 之間的白格，會因為第 $j$ 行的黑格而獲得分數。
   **注意**：這段白格之中，從 $k$ 到 $L$ 的部分，可能**已經**被第 $j-2$ 行的黑格給算過分數了！為了避免重複計算，我們只能加上從 $\max(k, L)$ 到 $i$ 的白格分數。
   增加的分數：`pre[j-1][i] - pre[j-1][max(k, L)]`。

### 🚀 極限優化 (O(N⁴) -> O(N³))
如果直接依照上述邏輯枚舉 $j, i, k, L$，複雜度將是 $\mathcal{O}(N^4)$，對於 $N=100$ 來說必將 TLE。
**代數拆解與預處理**：
對於固定的 $j$ 與固定的 $k$，我們可以預先遍歷 $L$，找出最佳的歷史狀態：
- 對於第一種情況 (`i <= k`)，我們只需要歷史 `dp` 值的最大值：
  `mmxl[k] = max(dp[j-2][k][L])`
- 對於第二種情況 (`i > k`)，我們將得分公式結合歷史狀態：`dp[j-2][k][L] + pre[j-1][i] - pre[j-1][max(k, L)]`。因為 `pre[j-1][i]` 也是獨立的，我們可以將其提出，只對剩下的部分取最大值：
  `mxl[k] = max(dp[j-2][k][L] - pre[j-1][max(k, L)])`

透過將 $L$ 的枚舉抽離，我們成功在進入 `i` 與 `k` 的迴圈前，以 $\mathcal{O}(N^2)$ 的時間完成了預處理。後續的 DP 轉移降階為 $\mathcal{O}(N^3)$！

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^3)$。$j$ 迴圈執行 $N$ 次，內部有預處理 $K, L$ 迴圈 $\mathcal{O}(N^2)$，以及狀態轉移 $i, k$ 迴圈 $\mathcal{O}(N^2)$。整體時間複雜度為 $\mathcal{O}(N^3)$，在 $N=100$ 下約為 $10^6$ 次運算，極其快速。
- **空間複雜度**: $\mathcal{O}(N^3)$。宣告了大小為 $105 \times 105 \times 105$ 的 3D DP 陣列，佔用約 $9 \text{ MB}$ 的記憶體，完全在限制範圍內。

### 💻 程式碼實作 (C++)
```cpp
long long dp[105][105][105];
long long pre[105][105];

class Solution {
public:
    long long maximumScore(vector<vector<int>>& grid) {
        memset(dp, 0, sizeof(dp));
        memset(pre, 0, sizeof(pre));
        int n = grid.size();
        if(n <= 1) return 0;
        
        // 1. 計算每一行的前綴和，方便 O(1) 取得區間分數
        for(int j = 0; j < n; ++j) {
            for(int i = 1; i <= n; ++i) {
                pre[j][i] = pre[j][i-1] + grid[i-1][j];
            }
        }

        // 2. 狀態轉移 (Profile DP)
        for(int j = 1; j < n; ++j) {
            // 預處理陣列，用於消去內層的 L 迴圈 (將 O(N^4) 降為 O(N^3))
            vector<long long> mxl(n + 1, 0);
            vector<long long> mmxl(n + 1, 0);
            
            for(int K = 0; K <= n; ++K) {
                for(int L = 0; L <= n; ++L) {
                    // 用於 i > k 的情況：預先扣除可能被第 j-2 行重複計算的白格區間
                    mxl[K] = max(mxl[K], (j >= 2 ? dp[j-2][K][L] - pre[j-1][max(K, L)] : 0));
                    // 用於 i <= k 的情況：直接取歷史狀態最大值
                    mmxl[K] = max(mmxl[K], (j >= 2 ? dp[j-2][K][L] : 0));
                }
            }

            // 進行降維後的 O(N^2) 狀態轉移
            for(int i = 0; i <= n; ++i) {
                for(int k = 0; k <= n; ++k) {
                    if(i <= k) {
                        // 第 j 行黑格較少，第 j 行的白格吃第 j-1 行的黑格分數
                        dp[j-1][i][k] = max(dp[j-1][i][k], mmxl[k] + pre[j][k] - pre[j][i]);
                    } else {
                        // 第 j 行黑格較多，第 j-1 行的白格吃第 j 行的黑格分數 (結合 mxl 避開重複計算)
                        dp[j-1][i][k] = max(dp[j-1][i][k], mxl[k] + pre[j-1][i]);
                    }
                }
            }
        }

        // 3. 尋找全域最大值
        long long mx = 0;
        for(int i = 0; i <= n; ++i) {
            for(int k = 0; k <= n; ++k) {
                mx = max(mx, dp[n-2][i][k]);
            }
        }
        
        return mx;
    }
};

/*
User Comments & Notes:
dp[j][i][k] := maximum score that can be achieved that 
color black all the cells of the j-th column starting from the top row down to the i-th row 
and (j-1)-th column starting from the top row down to the k-th row 

dp[j-1][i][k] = max{ dp[j-2][k][l] + 
                     sum[i .. k]  + //i <= k   
                     sum[max(l, k) .. i] } //i >= l 
                     //pre[j-1][i] - pre[j-1][max(l, k)]
*/
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