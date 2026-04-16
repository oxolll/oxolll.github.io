---
title: "LeetCode 464. Can I Win"
date: 2026-04-16T17:00:00+08:00
lastmod: 2026-04-16T17:00:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "dynamic-programming", "bit-manipulation", "game-theory", "memoization"]
keywords: ["LC464", "Medium", "Can I Win", "Minimax", "Bitmask DP", "Game Theory"]
description: "LeetCode 第 464 題：Can I Win。探討如何利用賽局理論 (Minimax) 的必勝態推導，結合狀態壓縮 (Bitmask) 與記憶化搜尋 (Memoization)，以 O(2^M * M) 的時間複雜度完美破解數字挑選遊戲。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 464](https://leetcode.com/problems/can-i-win/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Dynamic Programming` $\cdot$ `Bit Manipulation` $\cdot$ `Game Theory` $\cdot$ `Memoization`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
在「100 game」這個遊戲中，兩位玩家輪流從 1 到 10 的整數中挑選數字。將雙方挑選的數字累加起來，誰先讓累加總和達到或超過 100，誰就獲勝。
每次挑選的數字都不能重複使用。

現在，我們改變遊戲規則，給定兩個整數 `maxChoosableInteger` (可選的最大整數，即數字池為 1 到 `maxChoosableInteger`) 和 `desiredTotal` (目標總和)。
假設兩位玩家都採取**最佳策略 (Optimal Play)**，請判斷「先手玩家」是否一定能獲勝。

### 限制條件
- $1 \le maxChoosableInteger \le 20$
- $0 \le desiredTotal \le 300$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-16：初次提交 (Bitmask DP 與賽局理論)](#2026-04-16-初次提交)

---

## 💡 2026-04-16 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的 **零和賽局 (Zero-sum Game)**，雙方都採取最佳策略，這意味著我們可以使用 **Minimax** 的概念配合 DFS 來求解。
賽局核心法則：
1. **必勝態**：只要當前玩家存在**至少一種**選擇，能夠直接達到目標，或是讓下一個狀態對手陷入「必敗態」，那麼當前狀態就是必勝態。
2. **必敗態**：當前玩家嘗試了**所有**可能的選擇，發現無論怎麼選，都會讓對手進入「必勝態」，那麼當前狀態就是必敗態。

**狀態壓縮 (Bitmask)**：
由於 $maxChoosableInteger \le 20$，數字池很小。我們可以使用一個 32 位元整數 `state` 來記錄哪些數字已經被挑選過。第 `i-1` 個位元為 1 代表數字 `i` 已被使用。這樣可以將複雜的集合狀態壓縮成一個整數，並宣告大小為 $2^{20}$ (約 104 萬) 的一維陣列 `memo` 來進行 $\mathcal{O}(1)$ 的狀態快取。

### 🛠️ 解題思路 (Approach)
1. **邊界防呆與剪枝**：
   - 若 `desiredTotal <= maxChoosableInteger`，先手直接挑選該數字即可獲勝，回傳 `true`。
   - 計算數字池的總和 $\frac{M \times (M+1)}{2}$。若總和仍小於 `desiredTotal`，代表不管怎麼玩雙方都達不到目標，沒人能贏，直接回傳 `false`。
2. **初始化快取**：使用 `memset` 將 `memo` 陣列初始化為 `-1` (代表未計算)。
3. **DFS 記憶化搜尋 (`dfs(state, mx, tar)`)**：
   - 若 `memo[state] != -1`，直接查表回傳。
   - 遍歷所有可選的數字 $i \in [1, mx]$：
     - 利用位元運算 `state & (1 << (i - 1))` 檢查數字 $i$ 是否已被選過，若已選過則跳過。
     - 檢查必勝條件：如果選了 $i$ 之後剩餘目標 $\le 0$ (`tar - i <= 0`)，或是該步能讓對手面臨必敗態 (`!dfs(state | bit, mx, tar - i)`)，則當前玩家必勝！將 `memo[state]` 設為 1 並回傳。
   - 若遍歷完所有可用數字都無法滿足必勝條件，代表當前為必敗態，將 `memo[state]` 設為 0 並回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(2^M \times M)$。其中 $M$ 為 `maxChoosableInteger`。總共有 $2^M$ 種不同的數字挑選狀態，每個狀態最多會用迴圈嘗試 $M$ 種數字。
- **空間複雜度**: $\mathcal{O}(2^M)$。快取陣列 `memo` 的大小為 $2^{20}$。DFS 遞迴深度最多為 $M$ 層，這與陣列空間相比可忽略。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 狀態壓縮快取，1 << 20 大約為 104 萬，完全可以容納 maxChoosableInteger <= 20 的所有狀態
    int memo[1 << 20];
    
    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        // 1. 特判：如果目標小於等於最大可選數字，先手直接拿目標數字即可獲勝
        if(desiredTotal <= maxChoosableInteger) return true;

        // 2. 數學剪枝：如果全部數字加起來都達不到目標，代表永遠無法獲勝
        int sum = 0;
        for(int i = 1; i <= maxChoosableInteger; ++i) sum += i;
        if(sum < desiredTotal) return false;

        // 3. 初始化快取陣列為 -1 (未計算)
        memset(memo, -1, sizeof(memo));

        // 4. 開始遞迴搜尋
        return dfs(0, maxChoosableInteger, desiredTotal);
    }
    
private:
    bool dfs(int state, int mx, int tar) {
        // 若該狀態已經計算過，直接回傳結果
        if(memo[state] != -1) return memo[state];

        // 遍歷所有可選數字
        for(int i = 1; i <= mx; ++i) {
            int bit = (1 << (i - 1));
            // 如果該數字已經被選過，跳過
            if(state & bit) continue;
            
            // Minimax 核心判斷：
            // 1. 如果選了這個數字直接達到目標 (tar - i <= 0)
            // 2. 或者選了這個數字之後，下一個狀態對手必敗 (!dfs(...))
            // 只要滿足其一，當前玩家就必勝！
            if(tar - i <= 0 || !dfs(state | bit, mx, tar - i)) {
                return memo[state] = 1;
            }
        }

        // 所有選擇都無法必勝，代表當前狀態為必敗態
        return memo[state] = 0;
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