---
title: "LeetCode 2492. Minimum Score of a Path Between Two Cities"
date: 2026-07-04T21:20:00+08:00
lastmod: 2026-07-04T21:20:00+08:00
difficulty: 1679
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "breadth-first-search", "connected-components"]
keywords: ["LC2492", "Medium", "1679", "Minimum Score", "Path Between Two Cities", "圖論", "廣度優先搜尋", "連通分量"]
description: "LeetCode 第 2492 題：Minimum Score of a Path Between Two Cities。難度評分：1679。探討如何利用廣度優先搜尋 (BFS) 完整遍歷圖的連通分量，並精準捕捉所有邊的權重，找出路徑中的最低分數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2492](https://leetcode.com/problems/minimum-score-of-a-path-between-two-cities/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1679)</span>  
> **核心主題**：`Graph` $\cdot$ `Breadth-First Search (BFS)` $\cdot$ `Depth-First Search (DFS)` $\cdot$ `Union Find`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個正整數 `n`，代表有 `n` 座城市，編號從 `1` 到 `n`。同時給你一個二維陣列 `roads`，其中 $roads[i] = [a_i, b_i, distance_i]$ 代表城市 $a_i$ 和 $b_i$ 之間有一條雙向道路，距離為 $distance_i$。城市圖中可能包含多重邊 (Multiple edges)。

一條路徑的 **分數 (Score)** 定義為該路徑上所有道路之中的 **最小距離**。
請你回傳從城市 `1` 到城市 `n` 的所有可能路徑中的 **最小分數**。

**注意**：
- 路徑可以多次包含同一條道路或同一個城市。
- 測試測資保證城市 `1` 和城市 `n` 之間至少存在一條路徑。

### 範例
- **Input**: `n = 4, roads = [[1,2,9],[2,3,6],[2,4,5],[1,4,7]]`
- **Output**: `5`
- **Explanation**: 
  從城市 1 到城市 4 的路徑中，分數最低的走法可以是：1 -> 2 -> 4。這條路徑上的道路距離分別為 9 和 5，最小距離為 5。

- **Input**: `n = 4, roads = [[1,2,2],[1,3,4],[3,4,7]]`
- **Output**: `2`
- **Explanation**: 
  路徑可以是 1 -> 2 -> 1 -> 3 -> 4。道路距離分別為 2, 2, 4, 7，最小距離為 2。
  *(註：因為可以走回頭路，所以只要是跟 1 連通的邊，都可以被納入考量)*

### 限制條件
- $2 \le n \le 10^5$
- $1 \le roads.length \le 10^5$
- $roads[i].length == 3$
- $1 \le a_i, b_i \le n$
- $a_i \neq b_i$
- $1 \le distance_i \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-07-04：初次提交 (BFS 遍歷連通分量)](#2026-07-04-初次提交)

---

## 💡 2026-07-04 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的敘述有一個非常重要的核心精神：**「路徑可以多次包含同一條道路或同一個城市」**。
這意味著，只要是與城市 1 處於同一個 **連通分量 (Connected Component)** 的任何一條道路，我們都可以走過去，然後再原路退回來。
因此，這題實際上並不是要求我們找一條特定的「最短路徑 (Shortest Path)」，而是要求我們 **「找出城市 1 所在的連通分量中，權重最小的那一條邊」**。

**核心破局點：精確的邊界判定**
我們可以使用廣度優先搜尋 (BFS) 或深度優先搜尋 (DFS) 從節點 1 開始展開。
在遍歷的過程中，最大的陷阱是：**一條邊的兩個端點可能都已經被 `visited` 標記過了**（例如圖中有環）。
如果我們在探測相鄰節點時，先檢查 `if(visited[next]) continue;` 再去更新最小分數，就會漏掉這條邊！
正確的作法是：**只要看到邊，就立刻更新最小分數 `sc = min(sc, d);`，然後才判斷是否要將對面節點推入 Queue 中。**

### 🛠️ 解題思路 (Approach)
1. **建構鄰接表 (Adjacency List)**：利用 `vector<vector<pair<int, int>>>` 將 `roads` 轉換為無向圖的鄰接表結構 `adj`，同時儲存目標節點與邊的距離。
2. **初始化 BFS 狀態**：
   - 宣告 `visited` 陣列防止無限迴圈。
   - 準備 Queue `q`，將起點 `1` 推入，並標記為已造訪。
   - 宣告答案變數 `sc = INT_MAX` 準備捕捉最小值。
3. **執行 BFS 遍歷**：
   - 取出隊列首的節點 `cur`。
   - 遍歷所有與 `cur` 相連的邊 `[next, d]`。
   - **關鍵步驟**：無論 `next` 是否走過，無條件執行 `sc = min(sc, d)` 捕捉邊的權重。
   - 若 `next` 尚未走過，則標記為已造訪並推入 `q` 中。
4. **回傳結果**：迴圈結束後，`sc` 即為整個連通分量中的最小邊權重。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(V + E)$。其中 $V$ 是節點數 ($n$)，$E$ 是邊數 (`roads.length`)。我們需要 $\mathcal{O}(E)$ 的時間建構圖，並花費 $\mathcal{O}(V + E)$ 的時間完成 BFS 遍歷，每個節點與每條邊最多只被處理常數次。
- **空間複雜度**: $\mathcal{O}(V + E)$。鄰接表需要儲存所有節點與邊的資訊，同時需要 $\mathcal{O}(V)$ 的空間來維護 `visited` 陣列與 Queue。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    using pii = pair<int, int>;
    
    int minScore(int n, vector<vector<int>>& roads) {
        // 1. 建構無向圖的鄰接表
        // 為了防範 1-based 索引，大小開 n + 1
        vector<vector<pii>> adj(n + 1, vector<pii>());

        for(auto& x : roads) {
            int u = x[0], v = x[1], dist = x[2];
            adj[u].push_back({v, dist});
            adj[v].push_back({u, dist}); // 雙向邊
        }

        // 2. 初始化 BFS
        vector<bool> visited(n + 1, false);
        queue<int> q;
        
        q.push(1);
        visited[1] = true;
        int sc = INT_MAX; // 紀錄連通分量中的最小分數
        
        // 3. 展開 BFS 遍歷
        while(!q.empty()) {
            auto cur = q.front(); 
            q.pop();
            
            for(auto [next, d] : adj[cur]) {
                // ⚠️ 核心細節：無論對方是否造訪過，只要看到邊就先更新最小距離
                sc = min(sc, d);
                
                // 如果相鄰節點已經走過，就不需要重複加入 Queue
                if(visited[next]) continue;
                
                visited[next] = true;
                q.push(next);
            }
        }

        return sc;
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