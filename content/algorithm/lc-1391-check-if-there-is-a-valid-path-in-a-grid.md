---
title: "LeetCode 1391. Check if There is a Valid Path in a Grid"
date: 2026-04-27T11:31:00+08:00
lastmod: 2026-04-27T11:31:00+08:00
difficulty: 1745
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "bfs", "matrix", "simulation"]
keywords: ["LC1391", "Medium", "1745", "Valid Path in a Grid", "BFS", "Connectivity"]
description: "LeetCode 第 1391 題：Check if There is a Valid Path in a Grid。難度評分：1745。探討如何在網格圖中利用 BFS 進行路徑搜尋，並透過分離「出發方向配置」與「接收端合法性驗證」，優雅處理複雜的雙向連通性問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1391](https://leetcode.com/problems/check-if-there-is-a-valid-path-in-a-grid/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1745)</span>  
> **核心主題**：`Graph` $\cdot$ `BFS` $\cdot$ `Matrix` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個 $m \times n$ 的網格 `grid`。網格中的每個儲存格代表一種街道（或管線）類型，共有 6 種：
1. `1`：左右連通的水平管線。
2. `2`：上下連通的垂直管線。
3. `3`：左下連通的彎管。
4. `4`：右下連通的彎管。
5. `5`：左上連通的彎管。
6. `6`：右上連通的彎管。

你的任務是判斷是否存在一條從左上角 $(0, 0)$ 到右下角 $(m-1, n-1)$ 的有效路徑。
有效的路徑不僅要求相鄰，還要求兩格的管線必須能夠**完美對接**（即 A 能走向 B，且 B 有開口能接納來自 A 的方向）。

### 限制條件
- $1 \le m, n \le 300$
- $1 \le grid[i][j] \le 6$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-27：初次提交 (BFS 與雙向連通性驗證)](#2026-04-27-初次提交)

---

## 💡 2026-04-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
尋找最短或有效路徑，毫無疑問首選 **廣度優先搜尋 (BFS)**。
但本題與一般走迷宮最大的不同在於，連通性是「有條件的」。我們必須同時滿足：
1. **主動出擊 (Outgoing)**：當前儲存格的管線類型，必須有朝向目標方向的開口。
2. **被動接收 (Incoming)**：目標儲存格的管線類型，必須有朝向當前儲存格的開口，才能對接。

為了解耦這個複雜的判斷，我們可以將邏輯拆分為兩塊：
- 利用一個三維陣列 (或二維 vector) `types`，定義每種管線 $1 \sim 6$ 允許「走出去」的兩個相對座標 `(dx, dy)`。
- 獨立撰寫一個 `check(dx, dy, target_type)` 函式，專門負責把關「走過去的那個儲存格，是否具備對應的反向開口」。

### 🛠️ 解題思路 (Approach)
1. **定義出口方向 (`types`)**：
   - 建立陣列對應 $1 \sim 6$ 號管線的出口方向。例如 Type 1 是左右 `{{0,1}, {0,-1}}`，Type 2 是上下 `{{1,0}, {-1,0}}` 等。*(註：需仔細核對 Type 6 為右上 `{{0,1}, {-1,0}}`)*。
2. **實作接收端驗證 (`check`)**：
   - 如果我們向右走 `(0, 1)`，目標格必須有向左的開口 (Type 1, 3, 5)。
   - 如果我們向左走 `(0, -1)`，目標格必須有向右的開口 (Type 1, 4, 6)。
   - 如果我們向下走 `(1, 0)`，目標格必須有向上的開口 (Type 2, 5, 6)。
   - 如果我們向上走 `(-1, 0)`，目標格必須有向下的開口 (Type 2, 3, 4)。
3. **BFS 尋找路徑**：
   - 將起點 $(0, 0)$ 加入佇列 `q`，並標記 `visited[0][0] = true`。
   - 每次從 `q` 取出節點，若到達終點 $(m-1, n-1)$ 則回傳 `true`。
   - 查詢當前管線類型 `type`，並根據 `types[type]` 嘗試向外擴展。
   - 檢查邊界條件、是否已訪問，以及最重要的：**呼叫 `check` 驗證目標端是否能接收**。
   - 若一切合法，則將新座標加入佇列並標記已訪問。
4. **結束判斷**：若 BFS 佇列清空仍未走到終點，回傳 `false`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。每個儲存格最多被推入佇列一次，並向兩個方向擴展檢查，皆為常數時間操作。
- **空間複雜度**: $\mathcal{O}(M \times N)$。使用了 `visited` 陣列記錄訪問狀態，且 BFS 佇列最大可能佔用與網格大小等同的空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 驗證目標管線 (type2) 是否具備接收從 (i, j) 方向來的水的開口
    bool check(int i, int j, int type2) {
        // 向右走 (0, 1)：目標必須具備向左的開口
        if(i == 0 && j == 1 && (type2 == 1 || type2 == 3 || type2 == 5)) return true;
        // 向左走 (0, -1)：目標必須具備向右的開口
        if(i == 0 && j == -1 && (type2 == 1 || type2 == 4 || type2 == 6)) return true;
        // 向下走 (1, 0)：目標必須具備向上的開口
        if(i == 1 && j == 0 && (type2 == 2 || type2 == 5 || type2 == 6)) return true;
        // 向上走 (-1, 0)：目標必須具備向下的開口
        if(i == -1 && j == 0 && (type2 == 2 || type2 == 3 || type2 == 4)) return true;
        
        return false;
    }
    
    bool hasValidPath(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<bool>> visited(m, vector<bool>(n, false));

        queue<pair<int, int>> q;
        visited[0][0] = true;
        q.push({0, 0});
        
        // 定義 1~6 號管線的主動出發方向 (dx, dy)
        // 💡 修正了原版 types[6] 的筆誤，Type 6 為右上，應為 {0, 1} 與 {-1, 0}
        vector<vector<pair<int, int>>> types = {
            {}, // 0 號空置
            {{0, 1}, {0, -1}},  // 1: 左右
            {{1, 0}, {-1, 0}},  // 2: 上下
            {{0, -1}, {1, 0}},  // 3: 左下
            {{0, 1}, {1, 0}},   // 4: 右下
            {{0, -1}, {-1, 0}}, // 5: 左上
            {{0, 1}, {-1, 0}}   // 6: 右上
        };
        
        while(!q.empty()) {
            int t = q.size();
            while(t--) {
                auto [x, y] = q.front(); 
                q.pop();
                
                // 抵達終點
                if(x == m - 1 && y == n - 1) return true;
                
                int type = grid[x][y];
                // 根據當前管線類型，嘗試往允許的方向擴展
                for(auto d : types[type]) {
                    int nx = x + d.first;
                    int ny = y + d.second;
                    
                    // 1. 邊界檢查
                    if(nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                    // 2. 避免走回頭路
                    if(visited[nx][ny]) continue;
                    // 3. 雙向連通性驗證 (目標端是否能接收)
                    if(!check(d.first, d.second, grid[nx][ny])) continue;
                    
                    visited[nx][ny] = true;
                    q.push({nx, ny});
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