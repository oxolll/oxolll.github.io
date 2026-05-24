---
title: "LeetCode 3924. Minimum Threshold Path With Limited Heavy Edges"
date: 2026-05-14T20:00:00+08:00
lastmod: 2026-05-14T20:00:00+08:00
difficulty: 2079
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "binary-search", "graph", "shortest-path", "0-1-bfs"]
keywords: ["LC3924", "Hard", "Minimum Threshold", "0-1 BFS", "Dijkstra", "二分搜尋"]
description: "LeetCode 第 3924 題：Minimum Threshold Path With Limited Heavy Edges。探討如何在限制「重邊」數量的條件下，利用二分搜尋與 0-1 BFS 尋找最小門檻值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3924](https://leetcode.com/problems/minimum-threshold-path-with-limited-heavy-edges/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2079)</span>  
> **核心主題**：`Binary Search` $\cdot$ `Shortest Path` $\cdot$ `0-1 BFS`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個包含 $n$ 個節點的無向圖，每條邊都有一個權重 $w$。給定起始點 `source`、終點 `target` 和一個限制常數 $k$。
我們需要定義一個門檻值 $thr$：
- 如果一條邊的權重 $w > thr$，則稱其為 **重邊 (Heavy Edge)**。
- 如果一條邊的權重 $w \le thr$，則稱其為 **輕邊 (Light Edge)**。

請找到最小的 $thr$，使得從 `source` 到 `target` 存在一條路徑，其中包含的 **重邊數量不超過 $k$**。如果無法抵達，回傳 $-1$。

### 限制條件
- $2 \le n \le 10^5$
- $1 \le edges.length \le 2 \times 10^5$
- $0 \le k < n$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-14：初次提交 (二分搜尋答案 + Dijkstra/0-1 BFS 驗證)](#2026-05-14-初次提交)

---

## 💡 2026-05-14 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題具備典型的「最小化最大值」或「尋找門檻值」特徵。隨著門檻 $thr$ 的增加，原本的「重邊」會變成「輕邊」，使得滿足條件的路徑更容易被找到。這種**單調性**讓我們可以使用 **二分搜尋 (Binary Search)** 答案。

**核心破局點：如何高效驗證一個 $thr$ 是否可行？**
給定 $thr$ 後，我們將圖中邊權大於 $thr$ 的邊設為 $1$，其餘設為 $0$。問題轉化為：**「從 source 到 target 的最短路徑權重是否 $\le k$？」**

1.  **Dijkstra (方法一)**：邊權為 $0$ 或 $1$ 時可以運作，時間複雜度 $O(E \log V)$。
2.  **0-1 BFS (方法二)**：這是在邊權僅有 $\{0, 1\}$ 時的最優做法。利用 `std::deque`：
    - 遇到權重為 $0$ 的邊，加入隊首 (`push_front`)。
    - 遇到權重為 $1$ 的邊，加入隊尾 (`push_back`)。
    - 這樣能保證隊列的單調性，時間複雜度優化至 $O(E + V)$。

---

### 🛠️ 方法一：二分搜尋 + Dijkstra

```cpp
class Solution {
public:
    bool bfs(int n, int src, int tar, int thr, vector<vector<pair<int, int>>>& adj, int k) {
        vector<int> dist(n, 1e9);
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
        dist[src] = 0;
        pq.push({0, src});
        
        while(!pq.empty()) {
            auto [d, cur] = pq.top(); pq.pop();
            if(d > dist[cur]) continue;
            if(cur == tar) return d <= k;

            for(auto [nx, w] : adj[cur]) {
                int cost = (w > thr ? 1 : 0);
                if(dist[cur] + cost < dist[nx]) {
                    dist[nx] = dist[cur] + cost;
                    pq.push({dist[nx], nx});
                }
            }
        }
        return dist[tar] <= k;
    }

    int minimumThreshold(int n, vector<vector<int>>& edges, int source, int target, int k) {
        if(source == target) return 0;
        vector<vector<pair<int, int>>> adj(n);
        int r = 0;
        for(auto& e : edges) {
            r = max(r, e[2]);
            adj[e[0]].push_back({e[1], e[2]});
            adj[e[1]].push_back({e[0], e[2]});
        }

        int l = 0, ans = -1;
        while(l <= r) {
            int mid = l + (r - l) / 2;
            if(bfs(n, source, target, mid, adj, k)) {
                ans = mid;
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }
        return ans;
    }
};
```

---

### 🛠️ 方法二：二分搜尋 + 0-1 BFS (最優解)

```cpp
class Solution {
public:
    bool bfs(int n, int src, int tar, int thr, vector<vector<pair<int, int>>>& adj, int k) {
        vector<int> dist(n, 1e9);
        dist[src] = 0;
        deque<int> dq;
        dq.push_back(src);

        while(!dq.empty()) {
            int cur = dq.front(); dq.pop_front();
            if(cur == tar) return dist[cur] <= k;

            for(auto [next, w] : adj[cur]) {
                int cost = (w > thr ? 1 : 0);
                if(dist[cur] + cost < dist[next]) {
                    dist[next] = dist[cur] + cost;
                    if(cost == 0) dq.push_front(next);
                    else dq.push_back(next);
                }
            }
        }
        return dist[tar] <= k;
    }

    int minimumThreshold(int n, vector<vector<int>>& edges, int source, int target, int k) {
        if(source == target) return 0;
        vector<vector<pair<int, int>>> adj(n);
        int r = 0;
        for(auto& e : edges) {
            r = max(r, e[2]);
            adj[e[0]].push_back({e[1], e[2]});
            adj[e[1]].push_back({e[0], e[2]});
        }

        int l = 0, ans = -1;
        while(l <= r) {
            int mid = l + (r - l) / 2;
            if(bfs(n, source, target, mid, adj, k)) {
                ans = mid;
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        }
        return ans;
    }
};
```

---

### 📊 複雜度總結

| 方法 | 時間複雜度 | 空間複雜度 | 適用場景 |
| :--- | :--- | :--- | :--- |
| **Dijkstra 版** | $O(\log(\text{max\_w}) \cdot E \log V)$ | $O(V + E)$ | 通用最短路徑，適合邊權非負但多變的場景。 |
| **0-1 BFS 版** | $O(\log(\text{max\_w}) \cdot (V + E))$ | $O(V + E)$ | **最優解**，專門處理邊權僅有 $\{0, 1\}$ 的情況。 |

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