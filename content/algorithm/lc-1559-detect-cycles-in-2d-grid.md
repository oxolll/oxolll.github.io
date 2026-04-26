---
title: "LeetCode 1559. Detect Cycles in 2D Grid"
date: 2026-04-26T10:40:00+08:00
lastmod: 2026-04-26T10:40:00+08:00
difficulty: 1837
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "bfs", "matrix"]
keywords: ["LC1559", "Medium", "1837", "Detect Cycles in 2D Grid", "BFS", "Cycle Detection"]
description: "LeetCode 第 1559 題：Detect Cycles in 2D Grid。難度評分：1837。探討如何在二維網格中尋找相同字元構成的環，並利用 BFS 的前線碰撞偵測 (Frontier Collision) 技巧，優雅取代傳統的父節點追蹤法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1559](https://leetcode.com/problems/detect-cycles-in-2d-grid/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1837)</span>  
> **核心主題**：`Graph` $\cdot$ `BFS` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個大小為 $m \times n$ 的二維字元網格 `grid`。
請判斷網格中是否存在一個由**相同小寫英文字母**構成的**環 (Cycle)**。
一個環是一條長度大於等於 4 的路徑，路徑的起點和終點是同一個格子，且路徑上的相鄰格子在網格中必須相鄰（上下左右四個方向）。路徑中同一個格子不能重複走過（除了起點與終點）。
如果存在這樣的環，回傳 `true`，否則回傳 `false`。

### 限制條件
- $1 \le m, n \le 500$
- `grid` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-26：初次提交 (BFS 前線碰撞偵測與座標壓縮)](#2026-04-26-初次提交)

---

## 💡 2026-04-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在無向圖中尋找環，最常見的方法是 DFS 或 BFS。本題的每個連通分塊 (Connected Component) 都是由相同的字母所組成。
常規的 BFS 找環通常需要記錄遍歷過程的 `parent` 節點，以避免「剛從 A 走到 B，又立刻從 B 看回 A」造成的誤判。

**創意解法：BFS 前線碰撞偵測 (Frontier Collision)**：
我們可以使用兩種狀態來標記節點：
1. `added`：代表已經被發現，並推入 Queue 中等待處理（前線節點）。
2. `visited`：代表已經從 Queue 中取出，並完成向外擴展（已結算節點）。

當我們從當前節點向四個方向擴展時，若遇到一個相鄰節點：
- 它不是 `visited` (代表不是我們剛走過的舊路或已結算的死胡同)。
- 它卻已經存在於 `added` 集合中。
這代表**有另外一條 BFS 路徑，跟我們同時 (或先一步) 抵達了這個節點**！在網格圖中，兩條不同路徑交會於同一個節點，就必定構成了一個環！

### 🛠️ 解題思路 (Approach)
1. **座標壓縮**：因為網格大小最大為 $500 \times 500$，我們可以使用 `x * 1000 + y` 將二維座標壓縮為一維整數，方便存入 Queue 與 Hash Set。
2. **全域走訪**：使用雙層迴圈走訪網格中的每一個格子，若該格子未被訪問 (`!visited[i][j]`)，則以此為起點發起 BFS。
3. **BFS 擴展**：
   - 將起點加入 `q` 與 `added` 集合。
   - 逐層擴展，取出節點時標記 `visited[x][y] = true`。
   - 往四個方向探索，過濾越界或字元不同的格子。
   - **找環核心邏輯**：如果相鄰節點未被 `visited`，且已經存在於 `added` 集合，立刻回傳 `true`。
   - 若未在 `added` 中且未被 `visited`，則將其加入 `added` 與 `q` 繼續擴展。
4. **回傳結果**：若走訪完所有連通分塊都沒有發現環，回傳 `false`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。最差情況下，網格中的每一個格子都只會被推入 Queue 並處理一次。Hash Set 的查詢為攤銷常數時間。
- **空間複雜度**: $\mathcal{O}(M \times N)$。`visited` 陣列、`added` 集合與 `q` 最大皆可能占用與網格大小等同的空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool containsCycle(vector<vector<char>>& grid) {
        int m = grid.size(), n = grid[0].size();

        vector<vector<bool>> visited(m, vector<bool>(n, false));
        queue<int> q;
        vector<pair<int, int>> dir = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        
        // 記錄已經進入過 Queue 的節點
        // 💡 效能優化建議：實務上可改用 vector<vector<bool>> 來取代 unordered_set 降低常數開銷
        unordered_set<int> added;
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // 如果已經結算過，跳過
                if(visited[i][j]) continue;
                
                // 發現新的連通分塊，開始 BFS
                q.push(i * 1000 + j);
                added.insert(i * 1000 + j);
                
                while(!q.empty()) {
                    int t = q.size();
                    while(t--) {
                        auto cur = q.front(); 
                        q.pop();
                        
                        int x = cur / 1000, y = cur % 1000;
                        visited[x][y] = true; // 正式標記為已結算
                        
                        for(int d = 0; d < 4; ++d) {
                            int nx = x + dir[d].first;
                            int ny = y + dir[d].second;
                            
                            // 越界或字元不同則跳過 (利用 grid[i][j] 代表該連通分塊的代表字元)
                            if(nx < 0 || nx >= m || ny < 0 || ny >= n || grid[i][j] != grid[nx][ny]) 
                                continue;
                                
                            // 🚀 前線碰撞偵測：還沒被結算，但已經在等待佇列中，代表兩條路徑交會，形成環！
                            if(!visited[nx][ny] && added.find(nx * 1000 + ny) != added.end()) 
                                return true;
                                
                            if(visited[nx][ny]) 
                                continue;
                                
                            // 標記為已加入，並推入 Queue 中
                            added.insert(nx * 1000 + ny);
                            q.push(nx * 1000 + ny);
                        }
                    }
                }
            }
        }

        return false;
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