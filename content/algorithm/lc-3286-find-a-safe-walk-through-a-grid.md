---
title: "LeetCode 3286. Find a Safe Walk Through a Grid"
date: 2026-07-04T21:30:00+08:00
lastmod: 2026-07-04T21:30:00+08:00
difficulty: 1607
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "breadth-first-search", "graph", "matrix"]
keywords: ["LC3286", "Medium", "1607", "Safe Walk", "狀態 BFS", "剪枝", "圖論"]
description: "LeetCode 第 3286 題：Find a Safe Walk Through a Grid。難度評分：1607。探討如何利用帶有狀態紀錄的廣度優先搜尋 (State-based BFS)，透過紀錄各座標的最高殘存血量來進行高效剪枝，安全穿越網格。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3286](https://leetcode.com/problems/find-a-safe-walk-through-a-grid/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1607)</span>  
> **核心主題**：`Breadth-First Search (BFS)` $\cdot$ `Graph` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個大小為 $m \times n$ 的二維整數矩陣 `grid` 和一個整數 `health`。
矩陣中的每一個格子 `grid[i][j]` 不是 $0$ 就是 $1$：
- $0$ 代表該格子是安全的。
- $1$ 代表該格子是不安全的。

你一開始站在起點 `(0, 0)`。每當你踏入一個不安全的格子（包含起點和終點）時，你的 `health` 會扣減 $1$。
你可以向上下左右四個方向移動。
請你判斷是否能找到一條從 `(0, 0)` 抵達終點 `(m - 1, n - 1)` 的路徑，並且在抵達終點後（包含結算終點的扣血），你的 `health` **嚴格大於** $0$。

### 限制條件
- $1 \le m, n \le 50$
- $grid[i][j]$ 為 $0$ 或 $1$
- $1 \le health \le m \times n$
</details>

---

## 📝 歷次打卡與更新
- [2026-07-04：初次提交 (狀態矩陣與 BFS 剪枝)](#2026-07-04-初次提交)

---

## 💡 2026-07-04 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
尋找網格路徑是典型的 BFS 問題。但本題與一般「尋找最短路徑」的最大差異在於：**路徑的長短不重要，總扣血量才是重點**。

如果使用常規的 `visited` 布林陣列，會產生一個致命缺陷：我們可能會先透過一條「步數少但扣血多」的路徑造訪某格，導致該格被標記為 `visited`；隨後，另一條「步數多但扣血少」的更優路徑來到同一格時，就會被 `visited` 擋在門外，最終導致抵達終點時血量不足。

**核心破局點：狀態矩陣 (State Matrix) 與動態剪枝**
我們捨棄 `visited` 陣列，改用一個二維陣列 `H`。`H[i][j]` 記錄的是**「到達座標 $(i, j)$ 時，所擁有的最高剩餘血量」**。
在 BFS 擴張時，我們檢查走到下一格的新血量 `nh`：
1. **死亡判定**：如果 `nh <= 0`，代表血量耗盡，這條路徑報廢，直接跳過。
2. **無效繞路判定 (剪枝)**：如果 `nh <= H[nx][ny]`，代表我們曾經以「更高或相同的血量」抵達過這格，當前這條路徑屬於劣勢，直接跳過。
只有當 `nh > H[nx][ny]` 時，我們才更新 `H` 陣列，並將這個更優的狀態推入 Queue 中繼續探索。

### 🛠️ 解題思路 (Approach)
1. **起點預處理**：先扣除起點 `grid[0][0]` 的血量。若一開始就 `health <= 0`，直接回傳 `false`。
2. **初始化狀態**：宣告 `H` 矩陣並全部初始化為 `-1`。設定起點 `H[0][0] = health`。
3. **初始化 BFS**：使用 Queue `q` 儲存 `{座標, 當前血量}`，將起點狀態推入。
4. **展開 BFS 遍歷**：
   - 取出隊列首的狀態。
   - 若到達終點 `(m-1, n-1)`，由於我們已經保證推入 Queue 的血量必然 $> 0$，因此只要抵達就代表成功，提早回傳 `true`。
   - 向四個方向探索，計算踏入下一格的剩餘血量 `nh`。
   - 執行嚴格剪枝：若 `nh <= 0` 或 `nh <= H[nx][ny]` 則 `continue`。
   - 更新 `H[nx][ny] = nh` 並推入新狀態。
5. **回傳結果**：若 Queue 清空仍未抵達終點，回傳 `false`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N \times health)$。在最壞情況下，每一個格子最多會被更新並重新推入 Queue `health` 次（每次血量嚴格遞增 1）。因此運算次數的上限非常穩定。
- **空間複雜度**: $\mathcal{O}(M \times N)$。狀態矩陣 `H` 和 Queue 最大需要消耗 $\mathcal{O}(M \times N)$ 的空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    using pii = pair<int, int>;
    using ppi = pair<pii, int>;
    
    // 方向陣列：右、左、下、上
    vector<pii> dir = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
    
    bool findSafeWalk(vector<vector<int>>& grid, int health) {
        int m = grid.size(), n = grid[0].size();
        
        // 1. 起點扣血結算
        health -= grid[0][0];
        if(health <= 0) return false;

        // 2. 初始化狀態矩陣，記錄到達每格的「最高剩餘血量」
        vector<vector<int>> H(m, vector<int>(n, -1));
        H[0][0] = health;

        queue<ppi> q;
        q.push({{0, 0}, health});
        
        // 3. 展開帶有狀態的廣度優先搜尋
        while(!q.empty()) {
            int t = q.size();
            while(t--) {
                auto [p, h] = q.front(); 
                q.pop();
                auto [x, y] = p;
                
                // 若順利走到終點，代表必定存在一條安全路徑
                if(x == m - 1 && y == n - 1) return true;
                
                // 向四周探索
                for(auto [dx, dy] : dir) {
                    int nx = x + dx, ny = y + dy;
                    
                    // 邊界防禦
                    if(nx < 0 || nx >= m || ny < 0 || ny >= n) continue;

                    int nh = h - grid[nx][ny];
                    
                    // 核心剪枝：
                    // 1. 血量耗盡 (nh <= 0)
                    // 2. 劣勢路徑：曾經有更高或等同的血量到達過此格 (nh <= H[nx][ny])
                    if(nh <= 0 || nh <= H[nx][ny]) continue;
                    
                    // 刷新最高血量紀錄，並推進 Queue
                    H[nx][ny] = nh;
                    q.push({{nx, ny}, nh});
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