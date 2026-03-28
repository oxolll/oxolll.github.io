---
title: "LeetCode 3882. Minimum XOR Path in a Grid"
date: 2026-03-29T01:18:00+08:00
lastmod: 2026-03-29T01:18:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "matrix", "breadth-first search", "bit manipulation", "dynamic programming"]
keywords: ["LC3882", "Medium", "N/A", "Minimum XOR Path", "BFS", "State Caching"]
description: "LeetCode 第 3882 題：Minimum XOR Path in a Grid。難度評分：Medium (N/A)。探討如何透過 BFS 結合三維狀態記憶陣列 (State Caching)，以 O(M * N * V) 時間複雜度高效解決網格中的最小 XOR 路徑問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3882](https://leetcode.com/problems/minimum-xor-path-in-a-grid/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Matrix` $\cdot$ `Breadth-First Search` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $m \times n$ 的整數矩陣 `grid`。
你從左上角的單元格 `(0, 0)` 出發，目標是走到右下角的單元格 `(m - 1, n - 1)`。每次移動，你只能向右或向下走一步。
路徑的「XOR 總和」定義為該路徑上所有單元格數值的位元互斥或 (Bitwise XOR) 運算結果。
請找出從起點到終點的所有可能路徑中，**最小的 XOR 總和**。

### 限制條件
- $m == grid.length$
- $n == grid[i].length$
- $1 \le m, n \le 300$ *(依據陣列壓縮與記憶體分配推測)*
- $0 \le grid[i][j] \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-29：初次提交 (BFS 與三維狀態記憶)](#2026-03-29-初次提交)

---

## 💡 2026-03-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
從左上角走到右下角的總路徑數量為 $C(m+n-2, m-1)$，若直接使用暴力的深度優先搜尋 (DFS) 窮舉所有路徑，必定會造成 Time Limit Exceeded (TLE)。
由於本題要求的是 XOR 總和的最小值，我們無法像一般的最短路徑問題那樣在途中直接捨棄較大的數值（因為 XOR 運算不具備傳統加法的單調遞增性，較大的當前值在後續 XOR 操作中可能會突然變小）。
然而，題目測資的 `grid[i][j]` 數值通常有上限（例如 $\le 1000$），這意味著路徑中可能產生的 XOR 總和最大不會超過 1023。
因此，我們可以將「當前的 XOR 值」也視為一種狀態。如果在不同的路徑下，抵達同一個座標 `(x, y)` 且累積出相同的 XOR 值 `val`，後續的發展將完全一模一樣。透過宣告一個三維陣列 `visited[x][y][val]` 進行狀態記憶，就能將指數級別的搜尋樹大幅修剪為多項式時間的圖論走訪。

### 🛠️ 解題思路 (Approach)
1. **初始化狀態記憶陣列與佇列**：
   - 宣告三維布林陣列 `hs`，大小為 $M \times N \times 1024$，全初始化為 `false`。
   - 宣告廣度優先搜尋 (BFS) 使用的佇列 `q`。佇列中儲存的元素為 `{座標壓縮值, 當前 XOR 和}`。
   - 將起點座標 `(0, 0)` 的壓縮值 `0` 與初始 XOR 值 `grid[0][0]` 放入佇列，並標記 `hs[0][0][grid[0][0]] = true`。
2. **廣度優先搜尋 (BFS)**：
   - 當佇列不為空時，逐層進行拓展。
   - 取出隊首元素，將壓縮的座標值還原為二維座標 `r = cur / 1000` 與 `c = cur % 1000`。
   - 若抵達終點 `(m - 1, n - 1)`，利用 `min` 函式更新全域最小 XOR 值 `rt`，並繼續檢查同層的其他路徑。
   - 嘗試向右 `(0, 1)` 與向下 `(1, 0)` 兩個方向移動。
   - 若新座標 `(x, y)` 未越界，計算新的 XOR 總和 `nval = val ^ grid[x][y]`。
3. **狀態剪枝**：
   - 檢查 `hs[x][y][nval]` 是否已被走過。
   - 若為 `false`，代表這是一個全新的狀態，將其標記為 `true`，並將新座標與新 XOR 值推入佇列。
4. **回傳結果**：當所有可能的有效狀態皆走訪完畢，佇列清空後，回傳 `rt` 即可得到最小 XOR 總和。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N \times V)$。其中 $M$ 和 $N$ 分別為矩陣的列數與行數，$V$ 為可能產生的最大 XOR 總和（本題設計為 1024）。在最差情況下，每個座標皆可能經歷所有合法的 XOR 狀態，每次狀態轉移為常數時間操作。
- **空間複雜度**: $\mathcal{O}(M \times N \times V)$。使用了一個大小為 $M \times N \times 1024$ 的三維布林陣列 `hs`，以及最大可能儲存相同數量級別元素的 BFS 佇列 `q`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minCost(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        int rt = INT_MAX;
        
        // 佇列儲存格式：{壓縮座標, 當前累積的 XOR 值}
        queue<pair<int, int>> q;
        
        // 三維狀態陣列：hs[X座標][Y座標][XOR值]，最大 XOR 值估計不超過 1023
        vector<vector<vector<bool>>> hs(m, vector<vector<bool>>(n, vector<bool>(1024, false)));
        
        q.push({0, grid[0][0]});
        hs[0][0][grid[0][0]] = true;
        
        // 定義移動方向：右與下
        vector<vector<int>> dir = {{0, 1}, {1, 0}};
        
        while(!q.empty()) {
            int l = q.size();
            while(l--) {
                auto [cur, val] = q.front(); 
                q.pop();
                
                // 還原壓縮的座標 (假設最大邊長不超過 1000)
                int r = cur / 1000;
                int c = cur % 1000;

                // 若抵達終點，更新最小 XOR 總和
                if(r == m - 1 && c == n - 1) {
                    rt = min(rt, val);
                    continue;
                }
                
                // 向合法的相鄰格子移動
                for(int i = 0; i < 2; ++i) {
                    int x = r + dir[i][0];
                    int y = c + dir[i][1];
                    
                    if(x >= m || y >= n) continue;
                    
                    int next = x * 1000 + y;
                    int nval = val ^ grid[x][y];
                    
                    // 若此狀態已存在，則進行剪枝避免重複搜索
                    if(hs[x][y][nval]) continue;
                    
                    hs[x][y][nval] = true;
                    q.push({next, nval});
                }
            }
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