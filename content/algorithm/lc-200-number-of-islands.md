---
title: "LeetCode 200. Number of Islands"
date: 2026-03-13T23:20:00+08:00
lastmod: 2026-03-13T23:20:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Depth-First Search", "Array", "Matrix", "Graph"]
keywords: ["LC200", "N/A", "Medium", "Number of Islands", "DFS", "Connected Components"]
description: "LeetCode 第 200 題：Number of Islands。難度評分：N/A (經典面試題)。探討如何利用深度優先搜尋 (DFS) 遍歷二維網格，找出所有相連的島嶼數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 200](https://leetcode.com/problems/number-of-islands/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>    
> **核心主題**：`Depth-First Search` $\cdot$ `Array` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個由 `'1'`（陸地）和 `'0'`（水）組成的 $m \times n$ 二維網格 `grid`，請計算網格中**島嶼的數量**。

島嶼是被水包圍的，並且是透過水平或垂直方向將相鄰的陸地連接而成的。你可以假設網格的四個邊緣都被水包圍。

### 範例
- **Input**: 
  ```text
  grid = [
    ["1","1","1","1","0"],
    ["1","1","0","1","0"],
    ["1","1","0","0","0"],
    ["0","0","0","0","0"]
  ]
  ```
  **Output**: `1`

- **Input**: 
  ```text
  grid = [
    ["1","1","0","0","0"],
    ["1","1","0","0","0"],
    ["0","0","1","0","0"],
    ["0","0","0","1","1"]
  ]
  ```
  **Output**: `3`

### 限制條件
- $m == grid.length$
- $n == grid[i].length$
- $1 \le m, n \le 300$
- `grid[i][j]` 只能是 `'0'` 或 `'1'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-13：初次提交 (標準 DFS 搭配 Visited 陣列)](#2026-03-13-初次提交)

---

## 💡 2026-03-13 初次提交

### 🎯 直覺 (Intuition)
把這個二維網格想像成一張地圖。我們要做的就是「尋找尚未被發現的陸地」。
當我們在地圖上掃描，只要遇到一塊標記為 `'1'` 且還沒被我們踩過（`!visited`）的土地，這絕對就是一個「新島嶼」的起點！發現新島嶼後，我們馬上派出探險隊（DFS），往上下左右四個方向盡情探索，把這座島嶼所有相連的土地都插上旗子（標記為 `visited`）。等這支探險隊把整座島走透透退回來後，我們再繼續在地圖上尋找下一個還沒被發現的 `'1'`。

### 🛠️ 解題思路 (Approach)
1. **狀態準備**：建立一個與 `grid` 大小相同的布林陣列 `visited`，初始值全為 `false`，用來記錄走過的陸地。
2. **網格掃描**：使用雙層迴圈遍歷整個二維網格。
3. **觸發 DFS**：
   - 如果當前格子是水 (`'0'`) 或者是已經走過的陸地 (`visited[i][j] == true`)，直接跳過。
   - 否則，這是一個新島嶼的開端！將島嶼計數器 `rt` 增加 1，並呼叫 `dfs(i, j)` 開始擴展這座島嶼。
4. **深度優先搜尋 (DFS)**：
   - 檢查邊界條件：如果超出網格範圍，或者是水 (`'0'`)，或者是已訪問過的陸地，立刻 `return` (停止探索)。
   - 將當前格子標記為已訪問 (`vis[i][j] = true`)。
   - 遞迴呼叫 DFS 探索上下左右四個相鄰格子。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。其中 $M$ 是行數，$N$ 是列數。最壞情況下，整個網格都是陸地，每個格子兩層迴圈掃過一次，DFS 也會造訪一次，總體操作次數與網格大小成正比。
- **空間複雜度**: $\mathcal{O}(M \times N)$。使用了一個與網格同等大小的 `visited` 陣列。此外，在極端情況下（例如整個網格全是相連的陸地），DFS 的遞迴呼叫堆疊 (Call Stack) 深度也會達到 $\mathcal{O}(M \times N)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int m, n;
    int numIslands(vector<vector<char>>& grid) {
        m = grid.size(); 
        n = grid[0].size();
        
        // 紀錄走過的陸地，避免重複計算與無窮迴圈
        vector<vector<bool>> visited(m, vector<bool>(n, false));
        int rt = 0;
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // 如果是水，或是已經探索過的陸地，直接跳過
                if(grid[i][j] == '0' || visited[i][j]) continue;
                
                // 發現新島嶼！
                ++rt;
                // 派出 DFS 探險隊將整座島嶼標記為已探索
                dfs(i, j, grid, visited);
            }
        }

        return rt;
    }
private:
    void dfs(int i, int j, vector<vector<char>>& g, vector<vector<bool>>& vis) {
        // 邊界條件與終止條件：越界、遇到水、或是已經走過
        if(i < 0 || i >= m || j < 0 || j >= n || g[i][j] == '0' || vis[i][j]) 
            return;
            
        // 標記當前土地
        vis[i][j] = true;
        
        // 往上下左右四個方向繼續擴展
        dfs(i + 1, j, g, vis); // 下
        dfs(i - 1, j, g, vis); // 上
        dfs(i, j + 1, g, vis); // 右
        dfs(i, j - 1, g, vis); // 左
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