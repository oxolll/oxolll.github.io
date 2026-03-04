---
title: "LeetCode 1824. Minimum Sideway Jumps"
date: 2026-03-04T20:44:00+08:00
lastmod: 2026-03-04T20:44:00+08:00
difficulty: 1778
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Dynamic Programming", "Greedy", "Array"]
keywords: ["LC1824", "1778", "Medium", "DP Space Optimization", "State Transition"]
description: "LeetCode 第 1824 題：Minimum Sideway Jumps。難度評分：1778。探討如何利用一維滾動陣列進行動態規劃，並精準推演側跳的狀態轉移邏輯來求得最小側跳次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1824](https://leetcode.com/problems/minimum-sideway-jumps/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1778)</span>    
> **核心主題**：`Dynamic Programming` $\cdot$ `Greedy` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
有一隻青蛙位於長度為 `n` 的三車道跑道上，起點為點 `0` 的第 2 車道。
給定一個長度為 `n + 1` 的陣列 `obstacles`，其中 `obstacles[i]` 描述了在點 `i` 上的障礙物：
- 如果 `obstacles[i] == 0`，代表點 `i` 沒有任何障礙物。
- 如果 `obstacles[i] == 1, 2 或 3`，代表點 `i` 的對應車道上有一個障礙物。

青蛙可以從點 `i` 往前跳到點 `i + 1`（在同一車道上），前提是點 `i + 1` 的該車道沒有障礙物。
青蛙也可以在同一個點 `i` 上進行**側跳 (Sideway Jump)**，跳到另一個沒有障礙物的車道。側跳計為 1 次跳躍。

請回傳青蛙到達點 `n` 所需的**最小側跳次數**。
*(註：起點和終點保證沒有障礙物)*

### 範例
- **Input**: `obstacles = [0,1,2,3,0]` → **Output**: `2`
- **Explanation**: 
  最優路徑如下：
  - 起點在點 0 的車道 2。
  - 往前走到點 1 的車道 2。
  - 因為點 2 的車道 2 有障礙物，在點 1 側跳至車道 3 (側跳 1 次)。
  - 往前走到點 2 的車道 3。
  - 因為點 3 的車道 3 有障礙物，在點 2 側跳至車道 1 (側跳 1 次)。
  - 往前走至終點 4 的車道 1。
  總共需要 2 次側跳。

### 限制條件
- `obstacles.length == n + 1`
- `1 <= n <= 5 * 10^5`
- `0 <= obstacles[i] <= 3`
- `obstacles[0] == obstacles[n] == 0`
</details>

---

## 📝 歷次打卡與更新
- [2026-03-04：初次提交 (一維 DP 空間優化與軌跡推演)](#2026-03-04-初次提交)

---

## 💡 2026-03-04 初次提交

### 🎯 直覺 (Intuition)
這是一道尋找極值的路徑規劃問題，最直覺的想法是定義二維動態規劃 `dp[i][j]`：表示到達第 `i` 個位置、第 `j` 條車道所需的最小側跳次數。
仔細觀察後會發現，青蛙在第 `i` 個位置的狀態，**完全只取決於第 `i-1` 個位置的狀態**以及當前位置的障礙物。因此，我們可以將空間複雜度從 $\mathcal{O}(N)$ 壓縮到 $\mathcal{O}(1)$，只維護一個長度為 3 的一維陣列 `dp`，記錄青蛙在當前三個車道上的最小跳躍數。

### 🛠️ 解題思路 (Approach)
1. **初始化狀態**：
   因為青蛙一開始在點 0 的車道 2（0-indexed 為 `1`），所以初始 `dp` 陣列設為 `{INT_MAX/2, 0, INT_MAX/2}`，代表起點就在車道 2（跳躍數 0），其他車道目前不可達（使用 `INT_MAX/2` 避免後續加法溢位）。

2. **迴圈推演與狀態壓縮**：
   遍歷所有位置的障礙物 `x`，並宣告一個 `tmp` 陣列來記錄跳到當前位置的三個車道的最小代價。
   - **過濾不可能的終點**：如果當前車道 `i` 有障礙物 (`x - 1 == i`)，該車道絕對不可達，直接跳過。
   - **從上一層轉移**：枚舉上一層的所有車道 `j`：
     - 若上一層的車道 `j` 有障礙物 (`pre_ob - 1 == j`)，代表上一層不可能從那裡出發，跳過。
     - **直行不跳躍 (`i == j`)**：不需要額外代價，`tmp[i] = min(tmp[i], dp[j])`。
     - **跨車道跳躍 (`i != j`)**：這裡有兩種細部情況：
       1. 若上一層出發的車道 `j` 在當前位置**沒有**障礙物 (`j != x - 1`)：代表青蛙可以安全直行到當前位置，然後在當前位置進行 1 次側跳到達目標車道 `i`。代價為 `dp[j] + 1`。
       2. 若上一層出發的車道 `j` 在當前位置**有**障礙物 (`j == x - 1`)：代表青蛙不能直行走過來。牠必須在上一層就先側跳到某個空車道，往前走，然後在當前層再側跳到目標車道 `i`。這意味著總共需要 2 次側跳！代價為 `dp[j] + 2`。

3. **滾動更新**：
   每一輪結束後，將當前位置的障礙物更新為 `pre_ob`，並使用 `std::move(tmp)` 將新狀態轉移給 `dp`，繼續計算下一層。
   最終回傳 `dp` 陣列中的最小值即為解答。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。對於 `obstacles` 陣列中的每個元素，我們只執行了一個 $3 \times 3$ 的常數級別雙層迴圈。整體時間與 `n` 呈線性關係。
- **空間複雜度**: $\mathcal{O}(1)$。透過滾動陣列優化，我們僅維持了長度為 3 的 `dp` 與 `tmp` 陣列，完全去除了對 `n` 的空間依賴。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minSideJumps(vector<int>& obstacles) {
        // 初始化點 0 的狀態 (0-indexed, 車道 2 的 index 為 1)
        vector<int> dp = {INT_MAX / 2, 0, INT_MAX / 2};
        int pre_ob = 0;
        
        for(auto x : obstacles) {
            vector<int> tmp(3, INT_MAX / 2);
            
            for(int i = 0; i < 3; ++i) { // i: 當前目標車道
                if(x - 1 == i) // 當前目標車道有障礙物，不可能到達
                    continue;

                for(int j = 0; j < 3; ++j) { // j: 上一層的車道
                    if(pre_ob - 1 == j) // 上一層車道有障礙物，不可能從這出發
                        continue;
                    
                    // 情況 1：不換車道直行
                    if(i == j) {
                        tmp[i] = min(tmp[i], dp[j]);
                        continue;
                    }

                    // 情況 2：換車道
                    if(j != x - 1) {
                        // j 車道在當前方格無障礙，直行後側跳 1 次
                        tmp[i] = min(tmp[i], dp[j] + 1);
                    } else {
                        // j 車道在當前方格有障礙，必須在上一格先跳 1 次，這格再跳 1 次，共 2 次
                        tmp[i] = min(tmp[i], dp[j] + 2);
                    }
                }
            }
            pre_ob = x;
            dp = std::move(tmp); // 滾動更新狀態
        }

        return *min_element(dp.begin(), dp.end());
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