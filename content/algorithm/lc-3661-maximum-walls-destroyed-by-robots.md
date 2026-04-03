---
title: "LeetCode 3661. Maximum Walls Destroyed by Robots"
date: 2026-04-03T16:15:00+08:00
lastmod: 2026-04-03T16:15:00+08:00
difficulty: 2525
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic-programming", "binary-search", "sorting"]
keywords: ["LC3661", "2525", "Hard", "Maximum Walls", "Robots", "Dynamic Programming", "Interval Overlap"]
description: "LeetCode 第 3661 題：Maximum Walls Destroyed by Robots。難度評分：2525。探討如何結合排序、二分搜尋 (lower_bound) 預處理區間牆壁數量，並透過狀態機動態規劃精準處理機器人破壞範圍的重疊，以 O(M log N + M log M) 時間複雜度求得最大破壞牆壁數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3661](https://leetcode.com/problems/maximum-walls-destroyed-by-robots/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2525)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Binary Search` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
在一個一維數線上，給定三個陣列 `robots`、`distance` 和 `walls`。
- `robots[i]` 代表第 $i$ 個機器人的位置。
- `distance[i]` 代表第 $i$ 個機器人的破壞半徑。
- `walls[j]` 代表第 $j$ 道牆壁的位置。

每個機器人可以選擇啟動並向左或向右發射破壞光束。
如果第 $i$ 個機器人選擇向左發射，它將摧毀範圍 `[robots[i] - distance[i], robots[i]]` 內的所有牆壁。
如果選擇向右發射，則摧毀範圍 `[robots[i], robots[i] + distance[i]]` 內的所有牆壁。

每個機器人最多只能選擇一個方向發射一次（或選擇不發射）。
請計算並回傳所有機器人啟動後，**最多可以摧毀多少道牆壁**。

*(注意：不同機器人的破壞範圍可能會重疊，同一道牆壁被多個機器人摧毀時，只能計算一次。)*

### 限制條件
- $1 \le robots.length \le 10^5$
- $robots.length == distance.length$
- $1 \le walls.length \le 10^5$
- $-10^9 \le robots[i], walls[j] \le 10^9$
- $0 \le distance[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-03：初次提交 (區間重疊處理與狀態機 DP)](#2026-04-03-初次提交)

---

## 💡 2026-04-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的困難點在於機器人的選擇（向左或向右）會互相影響，尤其是當相鄰機器人的破壞範圍發生重疊時，盲目加總會導致重複計算。
為了解決這個問題，我們可以採用 **動態規劃 (Dynamic Programming)**，並依據空間順序由左至右處理每個機器人。

1. **排序與映射**：先將機器人與牆壁依據位置排序，確保後續處理的單調性。
2. **預處理有效範圍**：為了避免重複計算，我們限制每個機器人的影響範圍不會跨越相鄰的機器人。
   - 向左的有效起點 `left` 為 `max(curp - dis, 機器人 i-1 的位置 + 1)`。
   - 向右的有效終點 `right` 為 `min(curp + dis, 機器人 i+1 的位置 - 1)`。
3. **二分搜尋計數**：利用 `std::lower_bound` 快速計算出該機器人向左 (`lwallcnt`) 與向右 (`rwallcnt`) 有效範圍內的牆壁數量。
4. **狀態機 DP**：
   - 定義 `dp[i][0]` 為第 $i$ 個機器人向左破壞時，前 $i$ 個機器人能造成的最大累積破壞數。
   - 定義 `dp[i][1]` 為第 $i$ 個機器人向右破壞時的最大累積破壞數。
   - **重疊處理 (Overlap)**：當轉移狀態時，若前一個機器人的向右範圍 (`limit[i-1].second`) 與當前機器人的向左範圍 (`limit[i].first`) 發生交集，則在計算 `dp[i][0]` 時，必須從累積和中扣除這段重疊區域內的牆壁數 (`ovlp`)，以保證正確計數。

### 🛠️ 解題思路 (Approach)
1. **資料對齊與排序**：使用 Hash Map `mp` 記住每個機器人原始對應的 `distance`。將 `robots` 與 `walls` 排序。
2. **區間與牆數預處理**：
   - 走訪排序後的機器人，計算受限的 `left` 與 `right` 邊界。
   - 利用 `lower_bound` 取得 `[left, curp]` 之間的牆數 `lwallcnt`，以及 `[curp, right]` 之間的牆數 `rwallcnt`。
   - 將邊界與牆數分別存入 `limit` 與 `wallcnt` 陣列中（為了對齊 DP 索引，從 1 開始儲存，索引 0 放入虛擬邊界）。
3. **動態規劃轉移**：
   - 建立 `dp[m+1][2]` 陣列。
   - 走訪機器人 $i=1 \dots m$：
     - 若無重疊 (`limit[i-1].second < limit[i].first`)：
       - `dp[i][0] = max(dp[i-1][0], dp[i-1][1]) + wallcnt[i].first`
       - `dp[i][1] = max(dp[i-1][0], dp[i-1][1]) + wallcnt[i].second`
     - 若發生重疊：
       - 計算重疊區間 `[limit[i].first, limit[i-1].second]` 內的牆數 `ovlp`。
       - `dp[i][0] = max(dp[i-1][0] + wallcnt[i].first, dp[i-1][1] + wallcnt[i].first - ovlp)`（若前一個向右，需扣除重疊）。
       - `dp[i][1] = max(dp[i-1][0], dp[i-1][1]) + wallcnt[i].second`（向右不會與前一個重疊）。
4. **回傳結果**：取最後一個機器人的兩種狀態最大值 `max(dp[m][0], dp[m][1])`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \log M + M \log N + N \log N)$。其中 $M$ 是機器人數量，$N$ 是牆壁數量。排序耗時 $\mathcal{O}(M \log M + N \log N)$。預處理與 DP 轉移中呼叫 `lower_bound`，總共耗時約 $\mathcal{O}(M \log N)$。
- **空間複雜度**: $\mathcal{O}(M)$。配置了 Hash Map 以及長度為 $M$ 的 `limit`、`wallcnt` 與 `dp` 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxWalls(vector<int>& robots, vector<int>& distance, vector<int>& walls) {
        int m = robots.size(), n = walls.size();
        unordered_map<int, int> mp; // 記錄原始機器人對應的破壞距離
        
        for(int i = 0; i < m; ++i) {
            mp[robots[i]] = distance[i];
        }

        // 確保由左至右處理
        sort(robots.begin(), robots.end());
        sort(walls.begin(), walls.end());
        
        vector<pair<int, int>> wallcnt; 
        wallcnt.push_back({0, 0}); // 虛擬起點對齊 DP 索引 1
        
        vector<pair<int, int>> limit; 
        limit.push_back({INT_MIN, INT_MIN});
        
        // 1. 預處理有效破壞邊界與對應的牆壁數量
        for(int i = 0; i < m; ++i) {
            int curp = robots[i], dis = mp[robots[i]];
            // 限制破壞範圍不能越過相鄰機器人
            int left = (i == 0 ? curp - dis : max(curp - dis, robots[i-1] + 1));
            int right = (i + 1 == m ? curp + dis : min(curp + dis, robots[i+1] - 1));
            limit.push_back({left, right});

            // 計算向左有效範圍內的牆壁數
            int k = lower_bound(walls.begin(), walls.end(), left) - walls.begin();
            int kk = lower_bound(walls.begin(), walls.end(), curp + 1) - walls.begin();
            int lwallcnt = (k == n || walls[k] > curp ? 0 : kk - k);
            
            // 計算向右有效範圍內的牆壁數
            k = lower_bound(walls.begin(), walls.end(), curp) - walls.begin();
            kk = lower_bound(walls.begin(), walls.end(), right + 1) - walls.begin();
            int rwallcnt = (k == n || walls[k] > right ? 0 : kk - k);

            wallcnt.push_back({lwallcnt, rwallcnt});
        }

        // 2. 狀態機 DP
        // dp[i][0]: 第 i 個機器人向左破壞的最大累積牆數
        // dp[i][1]: 第 i 個機器人向右破壞的最大累積牆數
        vector<vector<int>> dp(m + 1, vector<int>(2, 0));
        
        for(int i = 1; i <= m; ++i) {
            // 無重疊情況
            if(limit[i-1].second < limit[i].first) {
                dp[i][0] = max(dp[i-1][0], dp[i-1][1]) + wallcnt[i].first;
                dp[i][1] = max(dp[i-1][0], dp[i-1][1]) + wallcnt[i].second;
            } 
            // 發生重疊，需扣除交集部分的重複計算
            else {
                int ovlp = lower_bound(walls.begin(), walls.end(), limit[i-1].second + 1) - 
                           lower_bound(walls.begin(), walls.end(), limit[i].first);
                           
                dp[i][0] = max(dp[i-1][0] + wallcnt[i].first, dp[i-1][1] + wallcnt[i].first - ovlp);
                dp[i][1] = max(dp[i-1][0], dp[i-1][1]) + wallcnt[i].second;
            }
        }
        
        return max(dp[m][0], dp[m][1]);
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