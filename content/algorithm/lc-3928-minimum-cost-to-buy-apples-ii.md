---
title: "LeetCode 3928. Minimum Cost to Buy Apples II"
date: 2026-05-15T15:00:00+08:00
lastmod: 2026-05-15T15:00:00+08:00
difficulty: 2186
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "shortest-path", "dijkstra"]
keywords: ["LC3928", "Hard", "Dijkstra", "Minimum Cost", "Graph Theory"]
description: "LeetCode 第 3928 題：Minimum Cost to Buy Apples II。探討在路徑權重不對稱（去程與回程倍率不同）的場景下，如何利用多次 Dijkstra 演算法尋找最優採購路徑。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3928](https://leetcode.com/problems/minimum-cost-to-buy-apples-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2186)</span>  
> **核心主題**：`Graph` $\cdot$ `Dijkstra's Algorithm` $\cdot$ `Shortest Path`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個包含 $n$ 個城市和 $m$ 條無向道路的圖。每條道路連接城市 $u$ 和 $v$，其基礎成本為 $c$，且具有一個回程時間乘數 $t$。
在每個城市 $j$ 購買蘋果的價格為 $prices[j]$。

你需要計算從每個城市 $i$ 出發，前往任意城市 $j$ 購買一個蘋果並返回 $i$ 的**最小總成本**。
- **去程成本**：從 $i$ 到 $j$ 的每條道路成本為 $c$。
- **採購成本**：在 $j$ 購買蘋果的成本為 $prices[j]$。
- **回程成本**：從 $j$ 回到 $i$ 的每條道路成本變為 $c \times t$。

### 限制條件
- $1 \le n \le 1000$
- $1 \le roads.length \le 2000$
- $1 \le prices[i], c \le 10^9$
- $1 \le t \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-15：初次提交 (雙重權重 Dijkstra 解法)](#2026-05-15-初次提交)

---

## 💡 2026-05-15 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的核心在於處理「去」與「回」路徑權重的不對稱性。對於任意起始城市 $i$ 和採購城市 $j$，總成本 $TotalCost(i, j)$ 可以表示為：
$$TotalCost(i, j) = \text{Dist}_{go}(i, j) + Prices[j] + \text{Dist}_{return}(j, i)$$

由於圖是無向的，$\text{Dist}_{return}(j, i)$ 等同於在一個所有邊權都乘以 $t$ 的圖中從 $i$ 到 $j$ 的最短距離。

**核心破局點：**
對於每個起始點 $i$，我們需要知道它到所有其他點 $j$ 的兩類最短路徑：
1. **標準圖的最短路徑**（邊權為 $c$）。
2. **回程圖的最短路徑**（邊權為 $c \times t$）。

### 🛠️ 解題思路 (Approach)
1. **建模與預處理**：將道路儲存為鄰接串列，包含目標點 $v$、基礎成本 $c$ 與乘數 $t$。
2. **多源處理**：針對每一個城市 $i$ ($0 \le i < n$)：
   - 執行第一次 Dijkstra：計算 $i$ 到所有點 $j$ 的基礎最短距離 $d[j]$。
   - 執行第二次 Dijkstra：計算 $i$ 到所有點 $j$ 的回程最短距離 $rd[j]$（邊權套用 $c \times t$）。
   - 枚舉所有點 $j$，計算 $tmp = \min(d[j] + prices[j] + rd[j])$。
3. **結果收集**：將每個起始點的最佳 $tmp$ 加入結果陣列。

### 📊 複雜度分析
- **時間複雜度**: $O(N \cdot E \log V)$。
  - 我們對 $N$ 個節點中的每一個都執行了兩次 Dijkstra 演算法。
  - Dijkstra 在本題的複雜度為 $O(E \log V)$。
  - 總計：$1000 \times 2 \times 2000 \log 1000 \approx 4 \times 10^7$ 次運算，符合 Hard 題目的時限要求。
- **空間複雜度**: $O(N + E)$。主要開銷在於鄰接串列與距離陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long dist[1005];  // 儲存去程最短距離
    long long rdist[1005]; // 儲存回程最短距離

    // 通用的 Dijkstra 實作，透過 flag 決定是否套用時間乘數 t
    void get_dist(long long* d, int src, vector<vector<vector<int>>>& adj, bool flag) {
        int n = adj.size();
        // 初始化為無窮大
        for(int i = 0; i < n; ++i) d[i] = 1e18;
        
        d[src] = 0;
        priority_queue<pair<long long, int>, vector<pair<long long, int>>, greater<>> pq;
        pq.push({0, src});

        while(!pq.empty()) {
            auto [w, u] = pq.top(); pq.pop();
            if(w > d[u]) continue;
            
            for(auto& edge : adj[u]) {
                int v = edge[0];
                long long c = edge[1];
                long long t = edge[2];
                // 根據 flag 判斷是否為回程：回程權重為 c * t，去程為 c
                long long nw = w + c * (flag ? t : 1);
                
                if(nw < d[v]) {
                    d[v] = nw;
                    pq.push({nw, v});
                }
            }
        }
    }

    vector<int> minCost(int n, vector<int>& prices, vector<vector<int>>& roads) {
        // 鄰接串列：adj[u] = {v, cost, multiplier}
        vector<vector<vector<int>>> adj(n);
        for(auto& r : roads) {
            int u = r[0], v = r[1], c = r[2], t = r[3];
            adj[u].push_back({v, c, t});
            adj[v].push_back({u, c, t});
        }

        vector<int> rt;
        for(int i = 0; i < n; ++i) {
            long long min_total = 1e18;
            
            // 1. 計算從起始點 i 出發到各地的去程最短路徑
            get_dist(dist, i, adj, false);
            // 2. 計算從各地回到起始點 i 的回程最短路徑 (權重含 t)
            get_dist(rdist, i, adj, true);
            
            // 3. 枚舉所有可能的採購點 j，取總成本最小值
            for(int j = 0; j < n; ++j) {
                // 總成本 = 去程(i->j) + 價格(j) + 回程(j->i)
                min_total = min(min_total, dist[j] + prices[j] + rdist[j]);
            }

            rt.push_back((int)min_total);
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