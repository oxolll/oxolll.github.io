---
title: "LeetCode 2410. Maximum Matching of Players With Trainers"
date: 2026-04-05T15:00:00+08:00
lastmod: 2026-04-05T15:00:00+08:00
difficulty: 1381
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "two pointers", "greedy", "sorting"]
keywords: ["LC2410", "1381", "Medium", "Maximum Matching", "Two Pointers", "Greedy"]
description: "LeetCode 第 2410 題：Maximum Matching of Players With Trainers。難度評分：1381。探討如何利用貪婪策略與雙指標 (Two Pointers)，在陣列排序後以 O(M log M + N log N) 時間複雜度求得玩家與教練的最大匹配數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2410](https://leetcode.com/problems/maximum-matching-of-players-with-trainers/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1381)</span>  
> **核心主題**：`Array` $\cdot$ `Two Pointers` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個以 0 為起始索引的整數陣列 `players`，其中 `players[i]` 代表第 `i` 名玩家的能力值。另給你一個以 0 為起始索引的整數陣列 `trainers`，其中 `trainers[j]` 代表第 `j` 名教練的訓練能力。
如果第 `j` 名教練的能力大於或等於第 `i` 名玩家的能力值（即 `trainers[j] >= players[i]`），這名玩家就可以和該教練進行配對。
每名玩家最多只能和一名教練配對，且每名教練最多只能訓練一名玩家。
請回傳玩家與教練能夠形成的**最大配對數**。

### 限制條件
- $1 \le players.length, trainers.length \le 10^5$
- $1 \le players[i], trainers[j] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-05：初次提交 (雙指標貪婪配對法)](#2026-04-05-初次提交)

---

## 💡 2026-04-05 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題目標為求最大配對數量。依據貪婪策略 (Greedy Strategy)：
為了讓配對數最大化，我們應該讓「能力需求最低的玩家」優先配對到「能力剛好能滿足他的教練」。這樣可以將能力較高的教練保留給後續需求更高的玩家。
如果在比對過程中，當前教練的能力無法滿足當前需求最低的玩家，那麼這位教練也絕對無法滿足後續需求更高的玩家，因此我們可以直接捨棄這位教練並尋找下一位。

為了實作上述策略，我們必須先將 `players` 與 `trainers` 兩個陣列進行遞增排序，接著使用雙指標 (Two Pointers) 由小到大進行配對。

### 🛠️ 解題思路 (Approach)
1. **排序**：對 `players` 和 `trainers` 陣列分別呼叫 `sort` 進行遞增排序。
2. **初始化指標**：
   - 取得陣列長度 `m = players.size()` 與 `n = trainers.size()`。
   - 宣告雙指標 `i = 0` (指向當前玩家) 與 `j = 0` (指向當前教練)。
   - 宣告變數 `rt = 0` 記錄成功的配對數量。
3. **雙指標掃描**：
   - 使用 `while(i < m && j < n)` 迴圈，確保指標不越界。
   - 在迴圈內部，使用另一個 `while(j < n && players[i] > trainers[j])`，不斷將教練指標 `j` 向右移動，直到找到一位能力足以指導當前玩家 `i` 的教練。
   - 若 `j == n`，代表剩餘的教練中已無人能滿足當前玩家，直接跳出主迴圈 (`break`)。
   - 若找到配對，將玩家指標 `i` 與教練指標 `j` 同時遞增 1，並將配對數 `rt` 加 1。
4. **回傳結果**：回傳記錄的配對數 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \log M + N \log N)$。其中 $M$ 是玩家數量，$N$ 是教練數量。主要的效能瓶頸在於對兩個陣列進行排序。後續的雙指標走訪過程中，每個玩家與教練最多只會被存取一次，耗時 $\mathcal{O}(M + N)$。
- **空間複雜度**: $\mathcal{O}(\log M + \log N)$。取決於 C++ `std::sort` 內部遞迴呼叫堆疊的深度（通常為 IntroSort）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int matchPlayersAndTrainers(vector<int>& players, vector<int>& trainers) {
        // 1. 對玩家與教練的能力值進行遞增排序
        sort(players.begin(), players.end());
        sort(trainers.begin(), trainers.end());
        
        int m = players.size(), n = trainers.size();
        int i = 0, j = 0;
        int rt = 0;
        
        // 2. 雙指標貪婪配對
        while(i < m && j < n) {
            // 尋找第一個能力值大於等於當前玩家的教練
            while(j < n && players[i] > trainers[j]) {
                ++j;
            }
            
            // 若所有教練都無法滿足當前玩家，提前結束比對
            if(j == n) break;
            
            // 配對成功，指標與計數器更新
            ++i;
            ++j;
            ++rt;
        }

        return rt;
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