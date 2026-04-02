---
title: "LeetCode 3418. Maximum Amount of Money Robot Can Earn"
date: 2026-04-02T14:20:00+08:00
lastmod: 2026-04-02T14:20:00+08:00
difficulty: 1798
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic-programming", "matrix"]
keywords: ["LC3418", "1798", "Medium", "Maximum Amount", "Robot", "Dynamic Programming", "3D DP"]
description: "LeetCode 第 3418 題：Maximum Amount of Money Robot Can Earn。難度評分：1798。探討如何利用三維動態規劃 (3D DP) 處理帶有次數限制的特殊技能，以 O(M * N) 時間複雜度在二維網格中求得最大路徑和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3418](https://leetcode.com/problems/maximum-amount-of-money-robot-can-earn/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1798)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $m \times n$ 的網格 `coins`，一個機器人從左上角 `(0, 0)` 出發，目標是走到右下角 `(m - 1, n - 1)`。
機器人每次只能向右或向下移動一步。
網格中的數字代表該位置的金幣數量：
- 如果 `coins[i][j] >= 0`，機器人將獲得等量的金幣。
- 如果 `coins[i][j] < 0`，代表遇到了強盜，機器人將失去等量的金幣。

機器人擁有一個特殊能力：它可以**最多 2 次**阻止強盜搶走金幣（即將該負數格子的影響視為 0）。
請回傳機器人到達右下角時，能夠獲得的**最大金幣數量**。

### 限制條件
- $m == coins.length$
- $n == coins[i].length$
- $1 \le m, n \le 500$
- $-1000 \le coins[i][j] \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-02：初次提交 (三維動態規劃與狀態機轉移)](#2026-04-02-初次提交)

---

## 💡 2026-04-02 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的網格路徑 DP 加上一個「技能使用次數」的額外維度。
我們定義三維 DP 狀態 `dp[i][j][k]`：表示機器人走到網格 `(i-1, j-1)` 位置時，**剩餘 `k` 次免除技能**所能獲得的最大金幣數。
根據網格數值的正負，轉移邏輯如下：
1. **格子為非負數 (`coins >= 0`)**：
   - 技能無法使用也無須使用。直接從上方或左方的相同 `k` 狀態轉移，並加上該格子的金幣。
2. **格子為負數 (`coins < 0`)**：
   - **保留技能 (硬扛扣款)**：從上方或左方的 `k` 狀態轉移，並扣除該格子的金幣。
   - **使用技能 (免除扣款)**：前提是當前狀態的剩餘技能數 $k < 2$（代表我們剛好在這一格消耗了一次技能，因此前一個狀態必然擁有 $k+1$ 次技能）。從上方或左方的 $k+1$ 狀態轉移，且不扣除金幣（加 0）。
   - 取上述兩種策略的極大值。*(註：若 $k = 2$ 代表技能從未使用，因此只能選擇硬扛扣款)*。

### 🛠️ 解題思路 (Approach)
1. 取得網格的列數 `m` 與行數 `n`。
2. 宣告大小為 `(m+1) x (n+1) x 3` 的三維陣列 `dp`，並將所有值初始化為 `INT_MIN / 2`（除以 2 是為了防止在轉移過程中加上負數導致整數下溢位 Underflow）。
3. **邊界初始化**：為了讓起點 `(1, 1)` 能夠正確獲取數值，設立虛擬起點狀態 `dp[0][1][2] = 0` 與 `dp[1][0][2] = 0`。
4. 使用三層迴圈遍歷 `i` (1 到 m)、`j` (1 到 n) 與 `k` (0 到 2)：
   - 若 `coins[i-1][j-1] >= 0`：`dp[i][j][k] = max(上方, 左方) + coins`。
   - 若 `coins[i-1][j-1] < 0`：
     - 若 `k < 2`：`dp[i][j][k] = max(使用技能免除, 硬扛扣款)`。
     - 若 `k == 2`：`dp[i][j][k] = max(上方, 左方) + coins`（只能硬扛）。
5. 迴圈結束後，右下角 `(m, n)` 必定存在三種剩餘技能次數的狀態 (`k = 0, 1, 2`)，取其中的最大值回傳即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N \times 3)$。需要走訪整個三維陣列，每個狀態的轉移皆為 $\mathcal{O}(1)$ 常數時間。
- **空間複雜度**: $\mathcal{O}(M \times N \times 3)$。配置了大小為 $(M+1) \times (N+1) \times 3$ 的三維 DP 陣列。*(註：可利用滾動陣列將空間優化至 $\mathcal{O}(N \times 3)$)*。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximumAmount(vector<vector<int>>& coins) {
        int m = coins.size(), n = coins[0].size();
        
        // dp[i][j][k] := 抵達 (i-1, j-1) 且剩餘 k 次免除技能的最大利潤
        // 初始化為 INT_MIN / 2 防止負數運算造成 Underflow
        vector<vector<vector<int>>> dp(m + 1, vector<vector<int>>(n + 1, vector<int>(3, INT_MIN / 2)));
        
        // 設定虛擬邊界，讓起點 (1, 1) 的狀態能正確轉移
        dp[0][1][2] = 0;
        dp[1][0][2] = 0;
        
        for(int i = 1; i <= m; ++i) {
            for(int j = 1; j <= n; ++j) {
                for(int k = 0; k <= 2; ++k) {
                    
                    // 情況 1：格子為正數或零，不觸發技能
                    if(coins[i-1][j-1] >= 0) {
                        dp[i][j][k] = max(dp[i-1][j][k], dp[i][j-1][k]) + coins[i-1][j-1];
                    } 
                    // 情況 2：格子為負數，且剩餘技能 < 2 (代表可選擇在當前格子消耗一次技能)
                    else if(k < 2) {
                        // 決策：消耗技能 (從 k+1 轉移，加 0) vs 不消耗技能 (從 k 轉移，扣錢)
                        int use_skill = max(dp[i-1][j][k+1], dp[i][j-1][k+1]);
                        int no_skill = max(dp[i-1][j][k], dp[i][j-1][k]) + coins[i-1][j-1];
                        dp[i][j][k] = max(use_skill, no_skill);
                    } 
                    // 情況 3：格子為負數，且剩餘技能 == 2 (代表未曾消耗過技能，只能硬扛)
                    else {
                        dp[i][j][k] = max(dp[i-1][j][k], dp[i][j-1][k]) + coins[i-1][j-1];
                    }
                }
            }
        }

        // 取抵達終點時，剩餘 0, 1, 2 次技能狀態的最大值
        return max({dp[m][n][0], dp[m][n][1], dp[m][n][2]});
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