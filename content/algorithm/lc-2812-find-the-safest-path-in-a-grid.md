---
title: "LeetCode 2812. Find the Safest Path in a Grid"
date: 2026-07-04T21:24:37+08:00
lastmod: 2026-07-04T21:24:37+08:00
difficulty: 2153
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "breadth-first-search", "binary-search", "graph"]
keywords: ["LC2812", "Medium", "2153", "Safest Path", "多源BFS", "二分搜尋答案", "最大化最小值"]
description: "LeetCode 第 2812 題：Find the Safest Path in a Grid。難度評分：2153。探討如何完美結合多源廣度優先搜尋 (Multi-source BFS) 與二分搜尋法 (Binary Search on Answer)，在 O(M * N * log D) 的時間內高效解決「最大化最小安全係數」的複雜網格路徑問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2812](https://leetcode.com/problems/find-the-safest-path-in-a-grid/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Medium (2153) - 實質為 Hard</span>  
> **核心主題**：`Breadth-First Search (BFS)` $\cdot$ `Binary Search` $\cdot$ `Array` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個大小為 $n \times n$ 的二維矩陣 `grid`，其中 `grid[r][c] = 1` 代表格子 `(r, c)` 有一個小偷，而 `grid[r][c] = 0` 代表該格子是空的。

你最初站在起點 `(0, 0)`，目標是走到終點 `(n - 1, n - 1)`。你可以向四個方向移動（上、下、左、右）。
路徑的 **安全係數 (Safeness Factor)** 定義為：該路徑上的任何格子到任意小偷的 **曼哈頓距離 (Manhattan Distance)** 的 **最小值**。
- 兩格 $(r_1, c_1)$ 和 $(r_2, c_2)$ 的曼哈頓距離為 $|r_1 - r_2| + |c_1 - c_2|$。

請你回傳所有從起點到終點的可能路徑中，**最大** 的安全係數。

### 範例
- **Input**: `grid = [[1,0,0],[0,0,0],[0,0,1]]`
- **Output**: `0`
- **Explanation**: 
  起點 `(0, 0)` 本身就有一個小偷，因此所有路徑的安全係數最高只能是 0。

- **Input**: `grid = [[0,0,0,1],[0,0,0,0],[0,0,0,0],[1,0,0,0]]`
- **Output**: `2`
- **Explanation**: 
  最佳路徑可以保持至少距離小偷 2 步的安全距離。

### 限制條件
- $1 \le grid.length == n \le 400$
- $grid[i][j]$ 為 0 或 1。
- `grid` 中至少有一個小偷。
</details>

---

## 📝 歷次打卡與更新
- [2026-07-04：初次提交 (多源 BFS + 二分搜尋驗證)](#2026-07-04-初次提交)

---

## 💡 2026-07-04 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標是「找出所有路徑中，最小值的最大化 (Maximize the minimum)」。這類問題的終極武器通常是 **二分搜尋法 (Binary Search on Answer)**。

**第一階段：多源 BFS (Multi-source BFS)**
我們需要知道網格中「每一個點」到最近小偷的安全距離。
如果對每個格子分別計算會耗時極大。正確的作法是將所有小偷的座標作為「超級源點」同時放入 Queue 中。透過 BFS 像水波一樣向外擴散，我們可以在 $\mathcal{O}(M \times N)$ 的時間內，一次性算出整個矩陣的 `dist` (安全距離) 映射表。

**第二階段：二分搜尋與路徑驗證**
在求得 `dist` 矩陣後，問題轉化為：**「給定一個安全閾值 `tar`，能否在網格中找到一條從 `(0,0)` 到 `(n-1, n-1)` 的路徑，且路徑上每個格子的 `dist[x][y]` 都 $\ge tar$？」**
- 答案具有單調性：如果 `tar = 3` 可以走到，那 `tar = 2` 必定可以；如果 `tar = 5` 走不到，那 `tar = 6` 絕對走不到。
- 利用二分法猜測 `tar` 的值，並配合一個簡單的 BFS (`isOK` 函數) 驗證連通性。
- **優化邊界**：路徑的起點和終點是必經之路，因此整條路徑的安全係數上限絕不可能超過 `dist[0][0]` 和 `dist[n-1][n-1]` 的較小值。這為我們的二分搜尋提供了極為精準的上界 `r = min(dist[0][0], dist[m-1][n-1])`。

### 🛠️ 解題思路 (Approach)
1. **收集小偷並初始化距離矩陣**：宣告 `dist` 矩陣並初始化為極大值。將所有 `grid[i][j] == 1` 的座標放入 `q`，設 `dist[i][j] = 0`。
2. **展開多源 BFS**：逐層向外擴張，若遇到相鄰格子且其 `dist` 仍為極大值，則 `dist[nx][ny] = dist[x][y] + 1` 並推入 `q`。
3. **二分搜尋答案**：
   - 搜尋下界 `l = 0`。
   - 搜尋上界 `r = min(dist[0][0], dist[m-1][n-1])`。
   - 執行二分迴圈：計算 `mid = r - (r - l) / 2` (注意向上取整以防無窮迴圈)。
   - 呼叫 `isOK(dist, mid)`：
     - 若 `true`，代表 `mid` 安全係數可行，嘗試更大的值：`l = mid`。
     - 若 `false`，代表 `mid` 太高走不通，縮減上界：`r = mid - 1`。
4. **回傳結果**：二分結束後回傳 `l`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(V + V \log D)$。其中 $V = M \times N$ 為網格格子總數，$D$ 為二分搜尋的上界（最大可能距離）。多源 BFS 需 $\mathcal{O}(V)$；二分搜尋需執行 $\log D$ 次，每次驗證呼叫 `isOK` 需 $\mathcal{O}(V)$ 的 BFS 遍歷時間。整體極為高效。
- **空間複雜度**: $\mathcal{O}(V)$。`dist` 矩陣、BFS 的 Queue，以及驗證函數中的 `visited` 陣列皆消耗 $\mathcal{O}(M \times N)$ 空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    using pii = pair<int, int>;
    vector<pii> dir = {{0,1}, {1,0}, {0,-1}, {-1,0}};
    
    // 驗證函數：判斷能否在最低安全閾值 tar 下走到終點
    bool isOK(vector<vector<int>>& dist, int tar) {
        int m = dist.size(), n = dist[0].size();
        
        // 邊界防禦：若起點本身就不符合閾值，直接回傳 false
        if(dist[0][0] < tar) return false; 

        vector<vector<bool>> visited(m, vector<bool>(n, false));
        queue<pii> q;
        
        visited[0][0] = true;
        q.push({0, 0});
        
        while(!q.empty()) {
            auto [x, y] = q.front(); 
            q.pop();
            
            // 提早終止：抵達終點
            if (x == m - 1 && y == n - 1) return true;
            
            for(auto [dx, dy] : dir) {
                int nx = x + dx, ny = y + dy;
                // 越界、已走過，或該格子安全距離未達標，皆跳過
                if(nx < 0 || nx >= m || ny < 0 || ny >= n || visited[nx][ny] || dist[nx][ny] < tar) continue;
                
                visited[nx][ny] = true;
                q.push({nx, ny});
            }
        }

        return visited[m-1][n-1];
    }
    
    int maximumSafenessFactor(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        queue<pii> q;
        vector<vector<int>> dist(m, vector<int>(n, INT_MAX / 2));
        
        // 1. 初始化多源 BFS (超級源點)
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(grid[i][j] == 1) {
                    q.push({i, j});
                    dist[i][j] = 0;
                }
            }
        }

        // 2. 執行多源 BFS，計算每個格子到最近小偷的曼哈頓距離
        while(!q.empty()) {
            int t = q.size();
            while(t--) {
                auto [x, y] = q.front(); q.pop();

                for(auto [dx, dy] : dir) {
                    int nx = x + dx, ny = y + dy;
                    // 若尚未被更新過，代表找到了該格子的最短安全距離
                    if(nx < 0 || nx >= m || ny < 0 || ny >= n || dist[nx][ny] != INT_MAX / 2) continue;
                    
                    dist[nx][ny] = dist[x][y] + 1;
                    q.push({nx, ny});
                }
            }
        }
        
        // 3. 二分搜尋最大化最小值
        int l = 0;
        // 極致優化：整條路徑的安全係數不可能超過起點或終點的上限
        int r = min(dist[0][0], dist[m-1][n-1]);
        
        while(l < r) {
            // 注意向上取整 (mid = l + (r-l+1)/2 的等價寫法)，防止死迴圈
            int mid = r - (r - l) / 2;
            
            if(isOK(dist, mid)) {
                l = mid; // 可以滿足，嘗試更大的安全係數
            } else {
                r = mid - 1; // 無法滿足，下修上界
            }
        }
        
        return l;
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