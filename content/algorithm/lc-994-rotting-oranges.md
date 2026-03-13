---
title: "LeetCode 994. Rotting Oranges"
date: 2026-03-13T23:58:00+08:00
lastmod: 2026-03-13T23:58:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Breadth-First Search", "Array", "Matrix"]
keywords: ["LC994", "N/A", "Medium", "Rotting Oranges", "Multi-source BFS"]
description: "LeetCode 第 994 題：Rotting Oranges。難度評分：Medium。探討如何利用多源廣度優先搜尋 (Multi-source BFS) 模擬腐爛擴散的過程，並計算感染所有新鮮橘子所需的最短時間。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 994](https://leetcode.com/problems/rotting-oranges/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>  
> **核心主題**：`Breadth-First Search` $\cdot$ `Array` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $m \times n$ 的網格 `grid`，其中每個儲存格有以下三種值之一：
- `0` 代表空儲存格。
- `1` 代表新鮮橘子。
- `2` 代表腐爛的橘子。

每一分鐘，任何與腐爛橘子**四個方向相鄰**的新鮮橘子都會變成腐爛的。
請回傳直到沒有任何新鮮橘子為止所必須經過的最小分鐘數。如果不可能讓所有新鮮橘子都腐爛，則回傳 `-1`。

### 範例
- **Input**: `grid = [[2,1,1],[1,1,0],[0,1,1]]`
  **Output**: `4`

- **Input**: `grid = [[2,1,1],[0,1,1],[1,0,1]]`
  **Output**: `-1`
  **Explanation**: 左下角的橘子永遠不會腐爛，因為它沒有與其他腐爛的橘子相連。

- **Input**: `grid = [[0,2]]`
  **Output**: `0`
  **Explanation**: 第 0 分鐘時已經沒有新鮮橘子了。

### 限制條件
- $m == grid.length$
- $n == grid[i].length$
- $1 \le m, n \le 10$
- `grid[i][j]` 是 0, 1 或 2。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-13：初次提交 (Multi-source BFS 與 1D 座標壓縮)](#2026-03-13-初次提交)

---

## 💡 2026-03-13 初次提交

### 🎯 直覺 (Intuition)
這題的擴散模式非常經典：腐爛的橘子會「同時」向四周感染。這意味著我們不能單純用 DFS 尋找路徑，而是必須使用 **多源廣度優先搜尋 (Multi-source BFS)**。
我們可以把一開始所有腐爛的橘子當作「第 0 分鐘」的起點，全部丟進 Queue 裡。接著，每一分鐘我們就把 Queue 裡現有的橘子全部拿出來，向四周擴散，把新感染的橘子放進下一輪的 Queue 中。當所有新鮮橘子都被感染（計數器歸零）時，經過的分鐘數就是答案。

### 🛠️ 解題思路 (Approach)
1. **初始化與統計**：
   - 遍歷整個網格。如果遇到新鮮橘子 (`1`)，將 `fresh` 計數器加一。
   - 如果遇到腐爛橘子 (`2`)，將其座標轉換為一維數字（例如 `i * 100 + j`）並推入 Queue 中作為初始擴散源。
2. **提早結束 (Early Exit)**：
   - 如果一開始就沒有新鮮橘子 (`fresh == 0`)，直接回傳 `0`。
   - 如果有新鮮橘子但沒有任何腐爛橘子 (`q.empty()`)，絕對無法感染，回傳 `-1`。
3. **逐層 BFS (Level-by-Level)**：
   - 進入 BFS 迴圈。每次迴圈代表一分鐘的流逝。
   - 從 Queue 中取出當前分鐘的所有腐爛橘子，向上下左右四個方向檢查。
   - 如果遇到新鮮橘子，將其狀態改為腐爛 (`grid[nx][ny] = 2`)，並將其座標推入 Queue 中。同時將 `fresh` 數量減一。
   - 如果在擴散過程中 `fresh` 歸零，立刻回傳當前花費的分鐘數 `time`。
4. **無法完全感染**：
   - BFS 結束後，如果 `fresh` 仍然大於 0，代表有橘子無法被觸及，回傳 `-1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。每個網格最多只會被推入 Queue 一次並向外擴展一次，所有操作皆為線性。
- **空間複雜度**: $\mathcal{O}(M \times N)$。最壞情況下，整個網格裡裝滿了腐爛的橘子，Queue 的大小會達到 $M \times N$。

### 💻 程式碼實作 (C++)

#### 版本一：使用 Next Queue 模擬層次 (初始實作)
```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        queue<int> q;
        int fresh = 0;
        
        // 1. 尋找所有擴散源與新鮮橘子數量
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(grid[i][j] == 0) continue;
                if(grid[i][j] == 1) ++fresh;
                else q.push(i * 100 + j); // 2D 轉 1D 座標壓縮
            }
        }
        
        if(fresh == 0) return 0;
        if(q.size() == 0) return -1;
        
        int time = 0;
        vector<vector<int>> dir = {{0,1}, {1,0}, {-1,0}, {0,-1}};
        
        // 2. 開始 BFS 擴散
        while(true) {
            int nextfresh = fresh;
            ++time;
            unordered_set<int> hs; // 紀錄本輪走過的點
            queue<int> nq;         // 下一輪的 Queue
            
            while(!q.empty()) {
                auto cur = q.front(); q.pop();
                int x = cur / 100, y = cur % 100;

                for(int i = 0; i < 4; ++i) {
                    int nx = x + dir[i][0];
                    int ny = y + dir[i][1];
                    int key = nx * 100 + ny;
                    
                    if(nx < 0 || nx >= m || ny < 0 || ny >= n || grid[nx][ny] == 0 || grid[nx][ny] == 2 || hs.find(key) != hs.end()) 
                        continue;
                        
                    grid[nx][ny] = 2; // 原地標記為腐爛，避免後續重複踩入
                    --nextfresh;
                    if(nextfresh == 0) return time; // 所有新鮮橘子都爛了，提早結束
                    
                    nq.push(key);
                    hs.insert(key);
                }
            }

            if(fresh == nextfresh) break; // 如果這一分鐘沒有感染任何人，提早終止
            fresh = nextfresh;
            q = nq;
        }

        return -1;
    }
};
```

<details>
<summary><b>點擊展開：版本二 (標準 BFS 單一 Queue 空間優化版)</b></summary>

透過 `int size = q.size()`，我們可以直接在同一個 Queue 裡分層 (分分鐘) 處理，不需要額外宣告 `nq` 與 `unordered_set`。
```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        queue<pair<int, int>> q;
        int fresh = 0;
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(grid[i][j] == 1) ++fresh;
                else if(grid[i][j] == 2) q.push({i, j});
            }
        }
        
        if(fresh == 0) return 0;
        
        int time = 0;
        vector<pair<int, int>> dirs = {{0,1}, {1,0}, {-1,0}, {0,-1}};
        
        while(!q.empty() && fresh > 0) {
            int size = q.size();
            // 一次處理完「這一分鐘」所有會擴散的橘子
            while(size--) {
                auto [x, y] = q.front();
                q.pop();
                
                for(auto [dx, dy] : dirs) {
                    int nx = x + dx, ny = y + dy;
                    if(nx >= 0 && nx < m && ny >= 0 && ny < n && grid[nx][ny] == 1) {
                        grid[nx][ny] = 2; // 標記腐爛，等同於 visited
                        q.push({nx, ny});
                        --fresh;
                    }
                }
            }
            ++time;
        }
        
        return fresh == 0 ? time : -1;
    }
};
```
</details>

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