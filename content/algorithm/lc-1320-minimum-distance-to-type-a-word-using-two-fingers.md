---
title: "LeetCode 1320. Minimum Distance to Type a Word Using Two Fingers"
date: 2026-04-19T23:50:00+08:00
lastmod: 2026-04-19T23:50:00+08:00
difficulty: 2027
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "bit-manipulation", "hash-table"]
keywords: ["LC1320", "Hard", "2027", "Minimum Distance to Type a Word", "Bitmask DP"]
description: "LeetCode 第 1320 題：Minimum Distance to Type a Word Using Two Fingers。難度評分：2027。探討如何揚棄傳統三維 DP，改用 26-bit 狀態壓縮 (Bitmask) 來記錄雙指位置，結合 lowbit 運算與查表法，達成極致優雅的狀態轉移。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1320](https://leetcode.com/problems/minimum-distance-to-type-a-word-using-two-fingers/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2027)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Bit Manipulation` $\cdot$ `Hash Table`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
你有一個標準的英文大寫鍵盤配置，共有 26 個字母排成 5 列，每列 6 個字母（除了最後一列只有 2 個）。
給你一個字串 `word`，請你計算使用**兩根手指**在鍵盤上打出這個字串所需的**最小移動總距離**。
兩根手指初始時可以放在鍵盤上的**任意位置**（初始放置不計入移動距離）。
從座標 $(x_1, y_1)$ 移動到 $(x_2, y_2)$ 的距離定義為曼哈頓距離：$|x_1 - x_2| + |y_1 - y_2|$。

### 限制條件
- $2 \le word.length \le 300$
- `word` 僅包含大寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-19：初次提交 (Bitmask DP 狀態壓縮與 Lowbit 解析)](#2026-04-19-初次提交)

---

## 💡 2026-04-19 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的常規解法是 `dp[i][finger1][finger2]`，但實際上兩根手指的順序並不影響結果（即 `(A, B)` 和 `(B, A)` 狀態等價）。
**降維打擊 (Bitmask DP)**：
我們可以使用一個 26 位元的整數 `state` 來表示兩根手指的位置。由於兩根手指佔據兩個字母，我們只需找出所有「恰好有 2 個位元為 1」的二進位狀態。這樣的合法狀態總數為 $\binom{26}{2} = 325$ 種，狀態空間極小。

**狀態解析技巧 (Bitwise Tricks)**：
給定一個含有兩個 1 的 `state`，如何快速還原出這兩個手指所在的字母索引？
- **手指一**：利用 `state & -state` (Lowbit) 取得最低位的 1。
- **手指二**：利用 `state & (state - 1)` 消去最低位的 1 後，剩下的就是另一個 1。
接著，我們預先建構一個 Hash Map (`mp`)，用來將這些 $2^k$ 的數值 $\mathcal{O}(1)$ 反查回對應的字母索引 $k$。

### 🛠️ 解題思路 (Approach)
1. **鍵盤距離計算**：實作 `cal(a, b)` 函數，利用 `/ 6` 計算列數 (x)，`% 6` 計算行數 (y)，並回傳曼哈頓距離。
2. **初始化狀態與反查表**：
   - 雙迴圈走訪所有字母組合，建構包含所有 325 種雙指初始位置的 `status` 陣列。
   - 使用 Lambda 表達式初始化 `mp`，將 $2^i$ 映射回 $i$。
3. **DP 狀態定義與轉移**：
   - 定義 `dp[i][state]` 為打完前 `i` 個字母，且手指停留在 `state` 的最小成本。
   - 初始化 `dp[0][state] = 0`，代表雙指可以自由選擇初始位置而不產生花費。
   - 對於 `word` 中的每個字母 `j`，遍歷上一層的所有有效 `state`：
     - **情況一 (免移動)**：如果 `j` 已經是 `state` 中的某個位元（雙指之一已在目標鍵上），則成本不變，直接將狀態繼承至 `dp[i+1][state]`。
     - **情況二 (需移動)**：利用 Bitwise 技巧拆解出目前雙指的索引 `idx` 與 `idx2`。
       - 嘗試移動手指一：新狀態為 `next1`，成本加上 `cal(idx, j)`。
       - 嘗試移動手指二：新狀態為 `next2`，成本加上 `cal(idx2, j)`。
       - 取兩者的最小值存入下一層 DP 狀態。
4. **回傳結果**：在最後一層 `dp[n]` 中尋找所有可能狀態中的最小成本 `mm` 並回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times \binom{26}{2})$。其中 $N$ 是 `word` 的長度。合法狀態數僅 325，每個狀態轉移只需常數時間的位元運算與查表，整體時間約為 $325 \times 300 \approx 10^5$ 次運算，極度高效。
- **空間複雜度**: $\mathcal{O}(N \times \binom{26}{2})$。使用了一個大小為 $N+1$ 的陣列，內部存放最多裝有 325 個元素的 Hash Map。*(可透過滾動陣列優化至常數空間)*

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 計算 6-width 鍵盤上的曼哈頓距離
    int cal(int a, int b) {
        int x1 = a / 6, y1 = a % 6;
        int x2 = b / 6, y2 = b % 6;
        return abs(x1 - x2) + abs(y1 - y2);
    }
    
    int minimumDistance(string word) {
        // 1. 預先建構所有可能的手指狀態 (C(26, 2) = 325 種)
        vector<int> status;
        for(int i = 0; i < 26; ++i) {
            for(int j = i + 1; j < 26; ++j) {
                status.push_back((1 << i) | (1 << j));
            }
        }

        // 2. 建構反查表：將 2^i 對應回索引 i
        unordered_map<int, int> mp;
        int func = [&mp]() {
            for(int i = 0; i < 26; ++i) mp[1 << i] = i;
            return 0;
        }();

        int n = word.size();
        // dp[i] 代表打完第 i 個字元時，各個狀態的最小成本
        vector<unordered_map<int, int>> dp(n + 1, unordered_map<int, int>());
        
        // 初始狀態：雙指可以自由放置在任意組合，成本為 0
        for(auto state : status) dp[0][state] = 0;
        
        for(int i = 0; i < n; ++i) {
            int j = word[i] - 'A'; // 當前要打的目標按鍵
            
            for(auto state : status) {
                if(dp[i].find(state) == dp[i].end()) continue;
                
                // 情況一：目標鍵已經被其中一根手指按住
                if((state >> j) & 1) {
                    if(dp[i+1].find(state) == dp[i+1].end())
                        dp[i+1][state] = dp[i][state];
                    else
                        dp[i+1][state] = min(dp[i+1][state], dp[i][state]);
                }
                // 情況二：必須移動其中一根手指過去
                else {
                    // 解析手指一的位置 (利用 lowbit 取出最低位的 1)
                    int idx = mp[state & -state];
                    // 新狀態：手指一移開，並按下 j
                    int next1 = (1 << j) | (state ^ (1 << idx));
                    
                    // 解析手指二的位置 (消去最低位的 1 後，剩下的就是另一個)
                    int idx2 = mp[state & (state - 1)];
                    // 新狀態：手指二移開，並按下 j
                    int next2 = (1 << j) | (state ^ (1 << idx2));
                    
                    // 嘗試轉移：移動手指一
                    if(dp[i+1].find(next1) == dp[i+1].end())
                        dp[i+1][next1] = dp[i][state] + cal(idx, j);
                    else
                        dp[i+1][next1] = min(dp[i+1][next1], dp[i][state] + cal(idx, j));

                    // 嘗試轉移：移動手指二
                    if(dp[i+1].find(next2) == dp[i+1].end())
                        dp[i+1][next2] = dp[i][state] + cal(idx2, j);
                    else
                        dp[i+1][next2] = min(dp[i+1][next2], dp[i][state] + cal(idx2, j));
                }
            }
        }

        // 3. 在最終層找出最小成本
        int mm = INT_MAX / 2;
        for(auto [states, v] : dp[n]) {
            mm = min(mm, v);
        }

        return mm;
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