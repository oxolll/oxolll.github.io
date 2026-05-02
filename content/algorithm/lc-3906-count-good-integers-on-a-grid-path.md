---
title: "LeetCode 3906. Count Good Integers on a Grid Path"
date: 2026-04-30T21:10:00+08:00
lastmod: 2026-04-30T21:10:00+08:00
difficulty: 2160
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "digit-dp", "math"]
keywords: ["LC3906", "Hard", "2160", "Count Good Integers", "Digit DP", "Grid Path"]
description: "LeetCode 第 3906 題：Count Good Integers on a Grid Path。難度評分：2160。探討如何將網格路徑轉化為一維狀態，並利用數位動態規劃 (Digit DP) 配合前導零補齊技巧，優雅且高效地計算符合條件的數字總數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3906](https://leetcode.com/problems/count-good-integers-on-a-grid-path/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2160)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Digit DP` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個範圍 `[l, r]` 以及一個長度為 6 的字串 `directions`（僅包含 'R' 代表向右，'D' 代表向下）。
我們有一個 $4 \times 4$ 的網格（共有 16 個格子），`directions` 定義了一條從左上角 $(0, 0)$ 到右下角 $(3, 3)$ 的路徑。
我們說一個整數是「好的 (Good)」，如果滿足以下條件：
1. 將該整數補齊為 16 位數（長度不足則在前面補前導零）。
2. 將這 16 個數字由左至右、由上至下依序填入 $4 \times 4$ 的網格中。
3. 沿著 `directions` 所指示的路徑走訪，路徑上經過的數字必須是 **非嚴格遞增 (Non-decreasing)** 的。

請回傳範圍 `[l, r]` 中有多少個好整數。

### 限制條件
- $0 \le l \le r \le 10^{15}$ (推測值，確保能放入 16 位數網格)
- `directions` 長度為 6，且包含恰好 3 個 'R' 與 3 個 'D'。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-30：初次提交 (路徑映射與 Digit DP 狀態壓縮)](#2026-04-30-初次提交)

---

## 💡 2026-04-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的本質是 **「帶有特定位數限制條件的區間計數」**，毫無疑問需要使用 **數位動態規劃 (Digit DP)**。
常見的 Digit DP 限制通常是針對相鄰位數（如不能有連續的 1），而本題的限制則是建構在一條「二維網格的路徑」上。
為了套用常規的 DP 模板，我們需要將這條「二維路徑」**降維 / 映射 (Mapping)** 回一維的索引序列。

**核心破局點：**
1. **一維化網格**：$4 \times 4$ 的網格可以攤平為 $0 \sim 15$ 的一維陣列。向右走 ('R') 相當於索引 `+1`，向下走 ('D') 相當於索引 `+4`。透過預先模擬，我們可以記錄下路徑經過了哪些索引。
2. **前導零的巧妙轉換**：一般的 Digit DP 最怕前導零影響數值特性。但這題規定長度不足要補零填入網格，因此一個數字 `123` 實際上就是 `0000000000000123`。由於 `0 <= 0 <= ... <= 1 <= 2 <= 3` 完美符合非嚴格遞增條件，我們直接將字串補齊 16 位，**徹底免去 `is_lead` (前導零) 的狀態判斷！**

### 🛠️ 解題思路 (Approach)
1. **路徑預處理 (`is_path`)**：
   - 宣告大小為 16 的 boolean 陣列 `is_path`。
   - 從起點 0 開始，依據 `directions` 模擬行走，將經過的索引設為 `true`。
2. **Digit DP 設計 (`dfs`)**：
   - 狀態定義：`dfs(pos, last, haslimit)`。
     - `pos`：當前填到第幾個數字 (0 ~ 15)。
     - `last`：**在路徑上**出現的最後一個數字。
     - `haslimit`：當前是否受到上界字串 `s` 的限制。
   - 轉移限制：
     - 下界 (`low`)：如果當前格子在路徑上 (`is_path[pos]`)，則填入的數字必須 $\ge last$；否則沒有限制，從 $0$ 開始。
     - 上界 (`up`)：如果受限於 `haslimit`，最高只能填到 `s[pos] - '0'`；否則可以填到 $9$。
   - 記憶化：對於不受限的狀態 (`!haslimit`)，記錄到 `dp[pos][last]` 中。
3. **區間相減求解**：
   - 答案即為 `cal(r) - cal(l - 1)`。
   - 💡 **效能優化細節**：因為 `is_path` 的規則對於 $r$ 和 $l-1$ 是相同的，所以 `dp` 記憶化陣列可以宣告在全域或主函式中，讓兩次 `cal` 共用同一個 `dp` 狀態，避免重複計算！

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\text{Length} \times 10 \times 10)$。其中 $\text{Length} = 16$ 是最大位數。狀態總數為 $16 \times 10 = 160$ 種，每個狀態最多執行 10 次轉移迴圈。時間複雜度極小，視為 $\mathcal{O}(1)$ 常數時間。
- **空間複雜度**: $\mathcal{O}(\text{Length} \times 10)$。DP 記憶化表格的大小為 $16 \times 10$，空間消耗極低，為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<bool> is_path;
    vector<vector<long long>> dp;
    string s;
    
    // Digit DP：枚舉位數，記錄路徑上最後一個數字，以及是否受限於上界
    long long dfs(int pos, int last, bool haslimit) {
        // 16 位數字全部填完，找到一種合法組合
        if(pos == 16) return 1;

        // 記憶化：若不受限且已計算過，直接回傳
        if(!haslimit && dp[pos][last] != -1) return dp[pos][last];

        long long rt = 0;
        
        // 如果當前格子在路徑上，必須符合非嚴格遞增 (>= last)；否則無限制 (>= 0)
        int low = (is_path[pos] ? last : 0);
        // 上界限制判斷
        int up = (haslimit ? s[pos] - '0' : 9);

        // 枚舉當前位數可以填入的數字
        for(int nextd = low; nextd <= up; ++nextd) {
            // 如果當前格子在路徑上，更新 last 為 nextd，否則 last 保持不變往下傳
            rt += dfs(pos + 1, (is_path[pos] ? nextd : last), haslimit && (nextd == up));
        }

        if(!haslimit) dp[pos][last] = rt;
        return rt;
    }

    // 計算 0 ~ x 之間的好整數數量
    long long cal(long long x) {
        s = to_string(x);
        // 💡 核心亮點：強制補齊 16 位數，完美規避前導零 (Leading Zeros) 的特殊處理！
        s = string(16 - s.size(), '0') + s;
        return dfs(0, 0, true);
    }

    long long countGoodIntegersOnPath(long long l, long long r, string directions) {
        is_path = vector<bool>(16, false);
        // 💡 效能優化：dp 陣列在兩次 cal 計算中可以共用，避免重複運算
        dp = vector<vector<long long>>(16, vector<long long>(10, -1));
        
        // 模擬路徑，標記經過的網格索引 (攤平為 1D)
        for(int i = 0, j = 0; i < 16; ++j) {
            is_path[i] = true;
            if(j < directions.size()) {
                if(directions[j] == 'D') i += 4; // 向下走，索引 +4
                else ++i;                        // 向右走，索引 +1
            }
        }

        // 區間相減：[l, r] = [0, r] - [0, l-1]
        return cal(r) - cal(l - 1);
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