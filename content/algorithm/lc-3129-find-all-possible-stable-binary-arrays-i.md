---
title: "LeetCode 3129. Find All Possible Stable Binary Arrays I"
date: 2026-03-10T05:39:00+08:00
lastmod: 2026-03-10T05:39:00+08:00
difficulty: 2200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Dynamic Programming", "Combinatorics"]
keywords: ["LC3129", "2200", "Medium", "Stable Binary Arrays", "4D DP", "Space Optimization"]
description: "LeetCode 第 3129 題：Find All Possible Stable Binary Arrays I。難度評分：2200。探討如何利用 4D 動態規劃記錄連續字元狀態，並透過滾動陣列將空間複雜度降維。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3129](https://leetcode.com/problems/find-all-possible-stable-binary-arrays-i/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(2200)</span>   
> **核心主題**：`Dynamic Programming`  
> 💡 **進階挑戰**：若已徹底理解此題，強烈建議挑戰進階版 [LeetCode 3130. Find All Possible Stable Binary Arrays II](../lc-3130-find-all-possible-stable-binary-arrays-ii/)，探討如何利用數學推導將維度與時間複雜度進一步壓縮。

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定三個正整數 `zero`, `one`, 和 `limit`。
一個二進位陣列如果滿足以下條件，我們稱之為 **穩定 (Stable)**：
1. 陣列中恰好包含 `zero` 個 0 以及 `one` 個 1。
2. 陣列中沒有任何長度超過 `limit` 的連續 0 或連續 1 的子陣列。

請回傳所有可能的穩定二進位陣列總數。由於答案可能很大，請對 $10^9 + 7$ 取同餘。

### 限制條件
- $1 \le zero, one, limit \le 200$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-11：初次提交 (4D DP 狀態轉移與一維空間壓縮)](#2026-03-11-初次提交)

---

## 💡 2026-03-11 初次提交

### 🎯 直覺 (Intuition)
為了確保連續的 `0` 或 `1` 不超過 `limit`，我們在動態規劃的狀態中，除了需要知道「目前用了幾個 0」與「目前用了幾個 1」，還必須確切記錄「當前結尾的字元是什麼」以及「該字元在結尾已經連續出現了幾次」。
因此，最直觀的解法是建立一個四維的動態規劃狀態表。

### 🛠️ 解題思路 (Approach)

#### 方法一：基礎 4D DP
1. **狀態定義**：
   $dp[i][j][k][l]$ 代表：使用了 $i$ 個 0、$j$ 個 1，且結尾字元為 $k$ ($k \in \{0, 1\}$)，並且該結尾字元連續出現了 $l$ 次的合法陣列總數。
2. **轉移方程**：
   - 當我們在結尾加上 `0` (即 $k=0$) 時：
     - 若前一個字元是 `1`，則加上 `0` 後，`0` 的連續次數變為 1：
       $dp[i][j][0][1] = \sum_{cons=1}^{limit} dp[i-1][j][1][cons]$
     - 若前一個字元也是 `0`，則連續次數 $l$ 會遞增：
       $dp[i][j][0][l] = dp[i-1][j][0][l-1]$ (其中 $2 \le l \le limit$)
   - 當我們在結尾加上 `1` (即 $k=1$) 時，邏輯對稱：
     - $dp[i][j][1][1] = \sum_{cons=1}^{limit} dp[i][j-1][0][cons]$
     - $dp[i][j][1][l] = dp[i][j-1][1][l-1]$ (其中 $2 \le l \le limit$)

#### 方法二：降維與空間優化 (Rolling Array)
觀察 4D DP 的轉移方程可以發現，當我們在計算使用了 $i$ 個 0 的狀態 $dp[i][\dots]$ 時，資料來源**只依賴於**：
- $i-1$ 個 0 的狀態（當追加 `0` 時）
- $i$ 個 0 的當前狀態（當追加 `1` 時，因為 `j` 變動，但 `i` 不變）

這意味著我們不需要把 $0 \sim zero$ 的所有 $i$ 狀態都儲存在記憶體中。我們只需要保留「當前列 (`curr`)」與「上一列 (`prev`)」。
透過計算 $curr = i \pmod 2$ 與 $prev = 1 - curr$，可以將第一維的大小從 $205$ 壓縮至 $2$，大幅減少記憶體佔用。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(zero \times one \times limit)$。共有三層主要迴圈，內部對連續長度 $limit$ 進行累加與轉移。
- **空間複雜度**: 
  - 基礎版：$\mathcal{O}(zero \times one \times 2 \times limit)$。
  - 降維版：$\mathcal{O}(2 \times one \times 2 \times limit) = \mathcal{O}(one \times limit)$。

### 💻 程式碼實作 (C++)

#### 版本一：降維空間優化版 (滾動陣列)
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int numberOfStableArrays(int zero, int one, int limit) {
        long long dp[2][205][2][205]; 
        
        // 初始化陣列
        for(int i = 0; i < 2; ++i)
            for(int j = 0; j <= one; ++j)
                for(int end = 0; end < 2; ++end)
                    for(int cons = 0; cons <= limit; ++cons)
                        dp[i][j][end][cons] = 0;

        dp[0][0][0][0] = dp[0][0][1][0] = 1;
        
        for(int i = 0; i <= zero; ++i) {
            int curr = i % 2;
            int prev = 1 - curr;
            
            for(int j = 0; j <= one; ++j) {
                if(i == 0 && j == 0) continue;

                for(int end = 0; end < 2; ++end) {
                    // 每次計算前清空當前狀態
                    memset(dp[curr][j][end], 0, sizeof(dp[curr][j][end]));

                    if(end == 0) {
                        // 加上 0，且前一個字元是 1 (連續數重置為 1)
                        for(int cons = 0; cons <= min(i + j, limit); ++cons) {
                            dp[curr][j][0][1] += (i > 0 ? dp[prev][j][1][cons] : 0);
                            if(dp[curr][j][0][1] >= MOD) dp[curr][j][0][1] -= MOD;
                        }
                        // 加上 0，且前一個字元也是 0 (連續數遞增)
                        for(int cons = 2; cons <= min(i + j, limit); ++cons) {
                            dp[curr][j][0][cons] += (i > 0 ? dp[prev][j][0][cons - 1] : 0);
                            if(dp[curr][j][0][cons] >= MOD) dp[curr][j][0][cons] -= MOD;
                        }
                    } 
                    else {
                        // 加上 1，且前一個字元是 0 (連續數重置為 1)
                        for(int cons = 0; cons <= min(i + j, limit); ++cons) {
                            dp[curr][j][1][1] += (j > 0 ? dp[curr][j - 1][0][cons] : 0);
                            if(dp[curr][j][1][1] >= MOD) dp[curr][j][1][1] -= MOD;
                        }
                        // 加上 1，且前一個字元也是 1 (連續數遞增)
                        for(int cons = 2; cons <= min(i + j, limit); ++cons) {
                            dp[curr][j][1][cons] += (j > 0 ? dp[curr][j - 1][1][cons - 1] : 0);
                            if(dp[curr][j][1][cons] >= MOD) dp[curr][j][1][cons] -= MOD;
                        }
                    }
                }
            }
        }

        long long rt = 0;
        for(int end = 0; end < 2; ++end) {
            for(int cons = 1; cons <= limit; ++cons) {
                rt += dp[zero % 2][one][end][cons];
                if(rt >= MOD) rt -= MOD;
            }
        }

        return rt;
    }
};
```

<details>
<summary><b>點擊展開：版本二 (基礎 4D DP 初始版)</b></summary>

```cpp
long long dp[205][205][2][205]; 
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int numberOfStableArrays(int zero, int one, int limit) {
        memset(dp, 0, sizeof(dp));
        dp[0][0][0][0] = dp[0][0][1][0] = 1;
        dp[1][0][0][1] = dp[0][1][1][1] = 1;
        
        for(int i = 0; i <= zero; ++i) {
            for(int j = 0; j <= one; ++j) {
                for(int end = 0; end < 2; ++end) {
                    if(i == 0 && j == 0) continue;

                    for(int cons = 1; cons <= min(i + j, limit); ++cons) {
                        if(end == 0) {
                            dp[i][j][end][1] += (i > 0 ? dp[i - 1][j][end ^ 1][cons] : 0);
                            if(dp[i][j][end][1] >= MOD) dp[i][j][end][1] -= MOD;
                        } else {
                            dp[i][j][end][1] += (j > 0 ? dp[i][j - 1][end ^ 1][cons] : 0);
                            if(dp[i][j][end][1] >= MOD) dp[i][j][end][1] -= MOD;
                        }
                    }

                    for(int cons = 2; cons <= min(i + j, limit); ++cons) {
                        if(end == 0) {
                            dp[i][j][end][cons] += (i > 0 ? dp[i - 1][j][end][cons - 1] : 0);
                            if(dp[i][j][end][cons] >= MOD) dp[i][j][end][cons] -= MOD;
                        } else {
                            dp[i][j][end][cons] += (j > 0 ? dp[i][j - 1][end][cons - 1] : 0);
                            if(dp[i][j][end][cons] >= MOD) dp[i][j][end][cons] -= MOD;
                        }
                    }
                }
            }
        }

        long long rt = 0;
        for(int end = 0; end < 2; ++end) {
            for(int cons = 1; cons <= limit; ++cons) {
                rt += dp[zero][one][end][cons];
                if(rt >= MOD) rt -= MOD;
            }
        }

        return rt;
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