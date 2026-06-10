---
title: "LeetCode 3753. Total Waviness of Numbers in Range II"
date: 2026-06-10T15:00:00+08:00
lastmod: 2026-06-10T15:00:00+08:00
difficulty: 2296
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "digit-dp", "math"]
keywords: ["LC3753", "Hard", "2296", "Total Waviness", "Digit DP", "數位動態規劃", "貢獻法", "前綴和"]
description: "LeetCode 第 3753 題：Total Waviness of Numbers in Range II。難度評分：2296。探討如何面對天文數字級別的區間限制，利用數位動態規劃 (Digit DP) 結合貢獻計算法 (Contribution Method)，在 O(log N) 的極致時間內精算所有數字的波動總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3753](https://leetcode.com/problems/total-waviness-of-numbers-in-range-ii/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2296)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Digit DP` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
*(本題為 Range I 的極限進階版)*
給你兩個整數 `num1` 和 `num2`。
一個數字的 **Waviness (波動度)** 定義為其所有連續三個位數形成「波峰」或「波谷」的總次數：
- **波峰 (Peak)**：當連續三位數 $(d_1, d_2, d_3)$ 滿足 $d_1 < d_2$ 且 $d_2 > d_3$。
- **波谷 (Valley)**：當連續三位數 $(d_1, d_2, d_3)$ 滿足 $d_1 > d_2$ 且 $d_2 < d_3$。

請你計算在閉區間 `[num1, num2]` 內，所有數字的 Waviness 總和。

### 限制條件
- $1 \le num1 \le num2 \le 10^{15}$ (推測量級，強迫使用 $\mathcal{O}(\log_{10} N)$ 的解法)
</details>

---

## 📝 歷次打卡與更新
- [2026-06-10：初次提交 (Digit DP 與貢獻法)](#2026-06-10-初次提交)

---

## 💡 2026-06-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
當測資範圍達到 $10^{15}$ 或更高時，任何 $\mathcal{O}(N)$ 的迴圈枚舉都會直接超時。針對這種「在給定範圍 $[L, R]$ 內統計符合特定條件的數字/特徵」，標準的最優解法是 **數位動態規劃 (Digit DP)**，利用 $Cal(R) - Cal(L-1)$ 的前綴和思維來大幅降維。

**核心破局點 1：狀態定義 (State Design)**
要判斷波峰或波谷，我們需要知道「當前填入的數字 $i$」與「前兩個數字 $fi, se$」的關係。
因此 DP 狀態定義為 `dp[pos][fi][se]`：
- `pos`：當前處理到第幾位數。
- `fi`：上上個位數 (first)。
- `se`：上個位數 (second)。
為了處理 **前置零 (Leading Zeros)** (例如數字 `00123` 中的 `0` 不能拿來算波動)，我們引入狀態 `10` 代表「該位置尚未填入有效數字」。

**核心破局點 2：貢獻計算法 (Contribution Method)**
這題最大的難點在於：我們不是算「符合條件的數字有幾個」，而是算「所有數字包含的波動**總和**」。
當我們在 `pos` 位置發現了一個波動 (`w = 1`)，這個波動會被重複計算幾次？這取決於**後方還有多少種合法的填法**！
1. **如果不受上限限制 (`!isLimit`)**：後面的 `remaining` 個位數可以隨便填 $0 \sim 9$，因此這個波動會出現在 $10^{\text{remaining}}$ 個數字中。貢獻度為 $1 \times 10^{\text{remaining}}$ (即 `base[s.size() - pos - 1]`)。
2. **如果受到上限限制 (`isLimit`)**：後面的位數不能超過原字串的後綴，因此合法的填法共有 `後綴數字 + 1` 種 (包含全 0)。貢獻度為 `stoll(suffix) + 1`。

### 🛠️ 解題思路 (Approach)
1. **預處理 10 的冪次**：建構 `base` 陣列，`base[i]` 代表 $10^i$，加速不受限時的貢獻度計算。
2. **拆解為前綴和查詢**：定義 `totalWaviness(num1, num2) = cal(num2) - cal(num1-1)`。
3. **Digit DP 實作 (`dfs`)**：
   - **終止條件**：`pos == s.size()` 回傳 0。
   - **記憶化**：若不受限 (`!isLimit`) 且 `dp` 有值，直接回傳。
   - **枚舉當前數字**：從 0 枚舉到 `up` (受限則為字串對應位數，否則為 9)。
   - **前置零處理**：若 `se == 10` 且 `i == 0`，代表目前還在前置零階段，繼續傳遞 `10, 10`。
   - **波動計算與狀態轉移**：
     - 若 `fi != 10` (已有連續三個有效數字)，判斷是否形成波谷或波峰，是則 `w = 1`。
     - 累加後續遞迴的結果 `dfs(...)`。
     - 累加當前波動的「貢獻度」：`w * (合法的後綴數量)`。
   - **寫入記憶體**：若不受限，將結果記錄進 `dp[pos][fi][se]` 中。
4. **回傳結果**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_{10}(N) \cdot 11 \cdot 11 \cdot 10)$。其中 $N$ 是傳入的數字大小。狀態數為 $17 \times 11 \times 11$，每個狀態內部迴圈 10 次。實際運算次數極小，屬於嚴格的 $\mathcal{O}(\log N)$ 級別，完美輾壓所有極限測資。
- **空間複雜度**: $\mathcal{O}(\log_{10}(N) \cdot 11 \cdot 11)$。DP 記憶化陣列所需的大小，空間消耗微乎其微。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // dp[pos][fi][se]：記憶化陣列
    // pos: 處理到的位數
    // fi: 上上個有效數字 (10 代表前置零/空)
    // se: 上個有效數字 (10 代表前置零/空)
    long long dp[17][11][11];
    long long base[17]; // 預建 10 的冪次陣列
    string s;

    long long dfs(int pos, bool isLimit, int fi, int se) {
        // 終止條件：已經處理完所有位數
        if(pos == s.size()) return 0;
        
        // 記憶化查詢：只有在「不受限」的情況下才能套用快取
        if(!isLimit && dp[pos][fi][se] != -1) return dp[pos][fi][se];
        
        // 決定當前位數的枚舉上限
        int up = (isLimit ? s[pos] - '0' : 9);
        long long rt = 0;
        
        for(int i = 0; i <= up; ++i) {
            // 處理前置零：如果前面都還是空 (se == 10) 且現在又填 0，繼續視為空
            if(se == 10 && i == 0) {
                rt += dfs(pos + 1, isLimit && (i == up), 10, 10);
            } 
            else {
                long long w = 0;
                // 判斷是否構成波動 (必須要有連續的三個有效數字)
                if(fi != 10) {
                    if(fi > se && se < i) ++w; // 波谷
                    if(fi < se && se > i) ++w; // 波峰
                }
                
                // 核心貢獻法：總和 = 後續產生的波動和 + 當前波動產生的貢獻
                rt += dfs(pos + 1, isLimit && (i == up), se, i) + 
                      w * (isLimit && (i == up) ? 
                          (pos + 1 == s.size() ? 1 : stoll(s.substr(pos + 1)) + 1) : 
                          base[s.size() - pos - 1]);
            }
        }
        
        // 記錄不受限的狀態
        if(!isLimit) {
            dp[pos][fi][se] = rt;
        }
        return rt;
    }

    long long cal(long long x) {
        s = to_string(x);
        
        // 初始化 DP 表為 -1
        for(int i = 0; i < 17; ++i)
            for(int j = 0; j < 11; ++j)
                for(int k = 0; k < 11; ++k) {
                    dp[i][j][k] = -1;
                }

        // 從第 0 位開始，初始受限，前置狀態皆設為 10
        return dfs(0, true, 10, 10);
    }

    long long totalWaviness(long long num1, long long num2) {
        // 預處理 10 的冪次
        base[0] = 1;
        for(int i = 1; i < 17; ++i) {
            base[i] = base[i - 1] * 10;
        }
        
        // 前綴和相減
        return cal(num2) - cal(num1 - 1);
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