---
title: "LeetCode 3130. Find All Possible Stable Binary Arrays II"
date: 2026-03-11T05:39:00+08:00
lastmod: 2026-03-11T05:39:00+08:00
difficulty: 2824
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Dynamic Programming", "Math", "Prefix Sum"]
keywords: ["LC3130", "2824", "Hard", "State Transition", "Mathematical Derivation"]
description: "LeetCode 第 3130 題：Find All Possible Stable Binary Arrays II。難度評分：2824。探討如何將 3129 題的狀態壓縮至 3D，並透過數學代數推導，將狀態轉移優化為 O(1) 時間複雜度。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3130](https://leetcode.com/problems/find-all-possible-stable-binary-arrays-ii/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(2824)</span>   
> **核心主題**：`Dynamic Programming` $\cdot$ `Math` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
此題為 [LeetCode 3129. Find All Possible Stable Binary Arrays I](../LC3129/) 的資料規模放大版。
給定三個正整數 `zero`, `one`, 和 `limit`。要求找出滿足條件的二進位陣列總數：
1. 陣列中恰好包含 `zero` 個 0 以及 `one` 個 1。
2. 陣列中沒有任何長度超過 `limit` 的連續 0 或連續 1 的子陣列。

請回傳所有可能的穩定二進位陣列總數，並對 $10^9 + 7$ 取同餘。

### 限制條件
- $1 \le zero, one, limit \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-11：初次提交 (3D DP 前綴和優化與數學推導 O(1) 轉移)](#2026-03-11-初次提交)

---

## 💡 2026-03-11 初次提交

### 🎯 直覺 (Intuition)
在上一題中，我們將連續次數作為一個維度記錄下來。但當資料規模擴大至 1000 時，如果繼續維護連續次數，時間複雜度將會達到 $\mathcal{O}(zero \times one \times limit)$，必然導致 Time Limit Exceeded (TLE)。
我們必須改變狀態定義：直接計算「結尾為 `0`」或「結尾為 `1`」的總數，並透過「一次追加一段符合長度限制的連續區塊」來進行轉移。

### 🛠️ 解題思路 (Approach)

#### 方法一：3D DP 搭配前綴和優化 (Prefix Sum)
1. **狀態定義**：
   定義 $dp[i][j][0]$ 為使用 $i$ 個 0、$j$ 個 1，且結尾為 `0` 的合法組合數。
   $dp[i][j][1]$ 為結尾為 `1` 的合法組合數。
2. **轉移邏輯**：
   要構成結尾為 `0` 的組合，必然是在一個「結尾為 `1`」的合法字串後面，追加長度為 $1 \sim limit$ 的連續 `0`。
   因此：$dp[i][j][0] = \sum_{k=1}^{limit} dp[i-k][j][1]$。
3. **前綴和優化**：
   為了避免這層 $\mathcal{O}(limit)$ 的累加，我們額外維護一個前綴和陣列 $pre[i][j][1]$，使得 $\sum_{k=1}^{limit} dp[i-k][j][1]$ 可以透過 $pre[i-1][j][1] - pre[i-limit-1][j][1]$ 在 $\mathcal{O}(1)$ 時間內計算完成。

#### 方法二：純數學代數推導 $\mathcal{O}(1)$ 轉移 (最佳解)
我們可以直接對原始的狀態方程進行數學展開與相減，完全免除維護額外 $pre$ 陣列的需求。
根據定義：
$$dp[i][j][0] = \sum_{k=1}^{limit} dp[i-k][j][1]$$

我們考慮狀態 $(i-1)$ 與 $j$ 時的總和（代表在所有長度為 $i+j-1$ 的合法序列後強制加上一個 `0`）：
$$dp[i-1][j][0] + dp[i-1][j][1] = \sum_{k=0}^{limit} dp[i-1-k][j][1]$$

將上述等式展開，會發現其累加範圍從 $k=0$ 到 $k=limit$。我們將 $k=limit$ 的那一項 $dp[i-1-limit][j][1]$ 移項相減：
$$dp[i-1][j][0] + dp[i-1][j][1] - dp[i-1-limit][j][1] = \sum_{k=0}^{limit-1} dp[i-1-k][j][1]$$

觀察右半部等式，這正好與 $dp[i][j][0]$ 的定義完全相同！
因此，狀態轉移方程可以直接化簡為：
$$dp[i][j][0] = dp[i-1][j][0] + dp[i-1][j][1] - dp[i-1-limit][j][1]$$
同理：
$$dp[i][j][1] = dp[i][j-1][1] + dp[i][j-1][0] - dp[i][j-1-limit][0]$$

這是一個嚴謹且完美的 $\mathcal{O}(1)$ 轉移公式。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(zero \times one)$。無論是使用前綴和或是數學推導，內部迴圈的轉移時間皆為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(zero \times one)$。僅需儲存 $dp[i][j][k]$ 的狀態表。

### 💻 程式碼實作 (C++)

#### 版本一：數學代數推導版 (免前綴和陣列)
```cpp
long long dp[1005][1005][2];
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int numberOfStableArrays(int zero, int one, int limit) {
        memset(dp, 0, sizeof(dp));
        
        // 初始化邊界條件
        for(int i = 1; i <= min(zero, limit); ++i) dp[i][0][0] = 1;
        for(int j = 1; j <= min(one, limit); ++j) dp[0][j][1] = 1;
        
        for(int i = 1; i <= zero; ++i) {
            for(int j = 1; j <= one; ++j) {
                // 根據推導公式進行 O(1) 轉移
                dp[i][j][0] = (dp[i-1][j][0] + dp[i-1][j][1] - (i - 1 - limit >= 0 ? dp[i-1-limit][j][1] : 0) + MOD) % MOD;
                dp[i][j][1] = (dp[i][j-1][1] + dp[i][j-1][0] - (j - 1 - limit >= 0 ? dp[i][j-1-limit][0] : 0) + MOD) % MOD;
            }
        }

        return (dp[zero][one][0] + dp[zero][one][1]) % MOD;
    }
};
```

<details>
<summary><b>點擊展開：版本二 (3D DP 搭配前綴和優化)</b></summary>

```cpp
long long dp[1005][1005][2];
long long pre[1005][1005][2];
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int numberOfStableArrays(int zero, int one, int limit) {
        memset(dp, 0, sizeof(dp));
        memset(pre, 0, sizeof(pre));
        dp[0][0][0] = dp[0][0][1] = 1;
        
        for(int i = 0; i <= zero; ++i) {
            for(int j = 0; j <= one; ++j) {
                if(i > 0) {
                    dp[i][j][0] += (pre[i-1][j][1] - (i - limit >= 1 ? pre[i-limit-1][j][1] : 0) + MOD) % MOD;
                    dp[i][j][0] %= MOD;
                }

                if(j > 0) {
                    dp[i][j][1] += (pre[i][j-1][0] - (j - limit >= 1 ? pre[i][j-limit-1][0] : 0) + MOD) % MOD;
                    dp[i][j][1] %= MOD;
                }

                // 維護前綴和
                pre[i][j][0] += (j > 0 ? pre[i][j-1][0] : 0) + dp[i][j][0];
                pre[i][j][0] %= MOD;

                pre[i][j][1] += (i > 0 ? pre[i-1][j][1] : 0) + dp[i][j][1];
                pre[i][j][1] %= MOD;
            }
        }

        return (dp[zero][one][0] + dp[zero][one][1]) % MOD;
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