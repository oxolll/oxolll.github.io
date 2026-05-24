---
title: "LeetCode 1340. Jump Game V"
date: 2026-05-24T21:40:00+08:00
lastmod: 2026-05-24T21:40:00+08:00
difficulty: 1866
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "sorting", "graph"]
keywords: ["LC1340", "Hard", "1866", "Jump Game V", "動態規劃", "拓撲排序", "DAG"]
description: "LeetCode 第 1340 題：Jump Game V。難度評分：1866。探討如何利用高度遞減的物理特性作為拓撲排序，結合 Forward DP (主動推送狀態) 高效求解圖論中的最長路徑問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1340](https://leetcode.com/problems/jump-game-v/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (1866)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `arr`，代表一排建築物的高度。同時給你一個整數 `d`。
你可以從任何一棟建築物開始跳躍。如果你當前在索引 `i`，你可以跳到索引 `j`，前提是必須滿足以下所有條件：
- $i - d \le j \le i + d$ （不能跳超過距離 `d`）
- 在索引 `i` 和索引 `j` 之間的所有建築物，其高度都必須 **嚴格小於** `arr[i]`。也就是說，你不能跳過任何高度大於或等於 `arr[i]` 的建築物。
- 目標建築物的高度也必須嚴格小於起點建築物：`arr[i] > arr[j]`。

請你計算並回傳你可以訪問的 **最多索引數量**（包含起點）。

### 範例
- **Input**: `arr = [6,4,14,6,8,13,9,7,10,6,12], d = 2`
- **Output**: `4`
- **Explanation**: 
  你可以從索引 10 (高度 12) 開始。
  - 跳到索引 8 (高度 10)
  - 跳到索引 6 (高度 9)
  - 跳到索引 7 (高度 7)
  總共訪問了 4 個索引。

- **Input**: `arr = [3,3,3,3,3], d = 3`
- **Output**: `1`
- **Explanation**: 你可以從任意起點開始，但不能跳到任何地方，因為沒有嚴格較低的相鄰建築物。

### 限制條件
- $1 \le arr.length \le 1000$
- $1 \le arr[i] \le 10^5$
- $1 \le d \le arr.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-24：初次提交 (降序拓撲排序與 Forward DP)](#2026-05-24-初次提交)

---

## 💡 2026-05-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在一般的動態規劃中，我們常常煩惱於「狀態的計算順序」，深怕某個狀態在被使用前還沒計算完成。這題的物理限制給了我們最完美的解答：**我們只能從高處跳往低處**。

這意味著，所有的跳躍路徑構成了一個有向無環圖 (DAG)。如果我們將建築物**由高到低**排序，這恰好就是該圖的「拓撲順序 (Topological Order)」。

**核心破局點：Forward DP (推送式 DP)**
1. **狀態定義**：`mx[i]` 代表「以索引 `i` 作為終點」所能累積的最多跳躍次數。初始值皆為 1（自己單獨一個點）。
2. **拓撲排序**：將所有建築物依高度**降序**排列。當我們處理到某棟建築物 `idx` 時，所有比它高的建築物都已經處理完了。這保證了此時的 `mx[idx]` 已經累積了來自高處的最優解。
3. **主動推送 (Push)**：從當前的 `idx` 出發，向左和向右模擬跳躍（距離不超過 `d`）。一旦遇到比自己高或等高的建築物，視線被阻擋，立即中斷該方向的搜索 (`flag1 = false`, `flag2 = false`)。若能跳到，則主動更新對方的狀態：`mx[idx+i] = max(mx[idx+i], mx[idx] + 1)`。

---

### 🛠️ 解題思路 (Approach)
1. **建立索引映射與排序**：
   將 `(高度, 索引)` 打包成 `pair` 存入陣列 `tmp`，並利用 `sort(tmp.rbegin(), tmp.rend())` 進行降序排列。
2. **初始化 DP 陣列**：
   `vector<int> mx(n, 1)`，每棟建築物預設最多能走 1 步。
3. **依序處理 (由高至低)**：
   - 遍歷 `tmp` 取出高度 `val` 與索引 `idx`。
   - 更新全局答案 `rt = max(rt, mx[idx])`。
   - 使用 `flag1` 和 `flag2` 雙向探測，限制範圍為 `d` 步以內。
   - **向右探測**：若未越界且 `arr[idx+i] < val`，則將自身的跳躍次數加 1 推送給目標 `mx[idx+i]`；否則觸發阻擋，`flag1 = false`。
   - **向左探測**：若未越界且 `arr[idx-i] < val`，則將自身的跳躍次數加 1 推送給目標 `mx[idx-i]`；否則觸發阻擋，`flag2 = false`。
4. **回傳結果**：回傳全局最大值 `rt`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N + N \cdot d)$。
  - 排序階段耗時 $\mathcal{O}(N \log N)$。
  - DP 狀態轉移階段，每個節點最多向左右各探索 $d$ 步，耗時 $\mathcal{O}(N \cdot d)$。
  - 在本題 $N \le 1000$ 的限制下，時間綽綽有餘。
- **空間複雜度**: $\mathcal{O}(N)$。使用了 `mx` 陣列儲存 DP 狀態，以及 `tmp` 陣列來儲存排序用的高度與索引對映。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxJumps(vector<int>& arr, int d) {
        int n = arr.size();
        // mx[i] 記錄以索引 i 結尾的最長路徑長度，初始皆為 1
        vector<int> mx(n, 1);
        
        // 1. 建立 [高度, 索引] 陣列並依高度降序排序 (拓撲排序)
        vector<pair<int, int>> tmp; 
        tmp.reserve(n);
        for(int i = 0; i < n; ++i) {
            tmp.push_back({arr[i], i});
        }        
        // 使用 rbegin() 和 rend() 達成降序排序
        sort(tmp.rbegin(), tmp.rend());

        int rt = 0;
        
        // 2. 依照高度從高到低的順序，將狀態「往下推 (Push)」
        for(int i = 0; i < n; ++i) {
            int val = tmp[i].first;
            int idx = tmp[i].second;

            // 因為是嚴格降序處理，此時的 mx[idx] 已是最終最優解
            rt = max(rt, mx[idx]);

            bool flag1 = true, flag2 = true;
            // 3. 向左右兩側最多探測 d 步
            for(int step = 1; step <= d && (flag1 || flag2); ++step) {
                // 處理向右跳躍
                if(flag1 && (idx + step >= n || val <= arr[idx + step])) {
                    flag1 = false; // 遇到邊界或較高的建築物，視線被阻擋
                }
                if(flag1) {
                    mx[idx + step] = max(mx[idx + step], mx[idx] + 1);
                }

                // 處理向左跳躍
                if(flag2 && (idx - step < 0 || val <= arr[idx - step])) {
                    flag2 = false; // 遇到邊界或較高的建築物，視線被阻擋
                }
                if(flag2) {
                    mx[idx - step] = max(mx[idx - step], mx[idx] + 1);
                }
            }
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