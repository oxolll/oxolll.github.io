---
title: "LeetCode 3905. Multi Source Flood Fill"
date: 2026-04-19T16:00:00+08:00
lastmod: 2026-04-19T16:00:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "bfs", "hash-table"]
keywords: ["LC3905", "Medium", "Multi Source Flood Fill", "BFS", "Coordinate Compression"]
description: "LeetCode 第 3905 題：Multi Source Flood Fill。探討如何利用多源廣度優先搜尋 (Multi-Source BFS) 解決擴散問題，並透過層次遍歷與暫存雜湊表 (Hash Map) 完美解決同時抵達同一格子的顏色覆蓋衝突。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3905](https://leetcode.com/problems/multi-source-flood-fill/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Graph` $\cdot$ `BFS` $\cdot$ `Hash Table`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個大小為 $n \times m$ 的二維網格，初始時所有格子皆為空白（可用 `-1` 表示）。
同時給定一個陣列 `sources`，其中 `sources[i] = [x, y, color]` 代表在座標 $(x, y)$ 有一個顏色為 `color` 的源頭。
在每一秒鐘，每個源頭都會向其上、下、左、右四個相鄰的空白格子進行擴散 (Flood Fill)，並將空白格子染成自己的顏色。
如果有多個不同顏色的源頭在**同一秒鐘**擴散到了同一個空白格子，該格子將會被染成這些顏色之中的**最大值**。
已經被染色的格子不會再次被覆蓋。
請模擬這個過程，直到網格中無法再進行擴散為止，並回傳最終的網格狀態。

### 限制條件
- $1 \le n, m \le 1000$ (推測值)
- $1 \le sources.length \le n \times m$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-19：初次提交 (多源 BFS 與競爭狀態處理)](#2026-04-19-初次提交)

---

## 💡 2026-04-19 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的 **多源廣度優先搜尋 (Multi-Source BFS)**。
與單源 BFS 不同的是，我們一開始要把所有的源頭同時推入 Queue 中。
本題最大的難點在於**「競爭狀態 (Race Condition) 的處理」**：當兩個不同顏色的點，同時在下一步走到同一個空白格子時，必須留下數字較大的顏色。
如果我們在擴散時直接把新座標寫入 Queue 與 visited 紀錄中，會導致「先擴散的點」直接佔領格子，使後來同層的點無法比較顏色。
**解決方案**：採用嚴格的「層次遍歷」。每一秒的擴散先將候選名單存入一個暫存的 Hash Map (`nextcand`)，遇到同一個座標就保留最大的顏色值 `max(nextcand[key], color)`。確保這一秒所有的衝突都結算完畢後，再將勝出的結果正式推入 Queue 與全域紀錄中。

### 🛠️ 解題思路 (Approach)
1. **座標降維壓縮**：
   - 使用 `x * 1000000LL + y` 將二維座標壓縮成一維的 `long long` 鍵值，方便存入 Hash Map。
2. **初始化多源 Queue**：
   - 建立 `queue<pair<long long, int>> q` 儲存 `{鍵值, 顏色}`。
   - 將 `sources` 中的所有初始點推入 `q`，並記錄到全域的 `unordered_map<long long, int> colors` 中。
3. **層次遍歷 BFS**：
   - 每次處理一個完整的層級：記錄當前 Queue 的大小 `len = q.size()`。
   - 宣告 `unordered_map<long long, int> nextcand` 用於結算這一層的擴散衝突。
   - 從 Queue 取出點，向四個方向擴散。若新座標未越界且未出現在全域的 `colors` 中，則將其加入 `nextcand`，並使用 `max()` 保留最大顏色值。
   - 該層遍歷結束後，走訪 `nextcand`，將結算完畢的格子正式寫入 `colors` 並推入 Queue 準備下一秒的擴散。
4. **還原二維網格**：
   - 擴散結束後，宣告大小為 $n \times m$、初始值為 `-1` 的 `grid`。
   - 走訪整個 `grid`，利用壓縮鍵值去 `colors` 中查表，有上色的就填入，最後回傳 `grid`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times M)$。最差情況下，網格中的每一個格子都只會被 Queue 處理一次。Hash Map 的插入與查詢在平均情況下為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N \times M)$。Queue 與 `colors` 最大可能儲存所有格子的狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> colorGrid(int n, int m, vector<vector<int>>& sources) {
        queue<pair<long long, int>> q;
        unordered_map<long long, int> colors;
        
        // 1. 初始化多源 BFS，將所有起點推入 Queue
        for(auto s : sources) {
            // 座標壓縮：x * 10^6 + y
            long long key = s[0] * 1000000LL + s[1];
            q.push({key, s[2]});
            colors[key] = s[2];
        }

        vector<pair<int, int>> dir = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        
        // 2. 層次遍歷 (Level-by-Level BFS)
        while(!q.empty()) {
            int len = q.size();
            // nextcand 用於解決同層擴散時的顏色衝突 (Race Condition)
            unordered_map<long long, int> nextcand;
            
            while(len--) {
                auto cur = q.front(); 
                q.pop();
                int x = cur.first / 1000000;
                int y = cur.first % 1000000;
                int color = cur.second;
                
                // 向四個方向擴散
                for(auto [i, j] : dir) {
                    if(x + i < 0 || x + i >= n || y + j < 0 || y + j >= m)
                        continue;
                        
                    long long key = (x + i) * 1000000LL + (y + j);
                    
                    // 若該格子已經被過往的層級染過色，直接跳過
                    if(colors.find(key) != colors.end()) continue;
                    
                    // 暫存至下一層候選名單，若發生衝突則保留較大的顏色值
                    nextcand[key] = max(nextcand[key], color);
                }
            }
            
            // 3. 將本層結算完畢的新格子，正式寫入全域紀錄與 Queue 中
            for(auto [key, c] : nextcand) {
                colors[key] = c;
                q.push({key, c});
            }
        }

        // 4. 將 Hash Map 展開回二維陣列
        vector<vector<int>> grid(n, vector<int>(m, -1));
        for(int i = 0; i < n; ++i) {
             for(int j = 0; j < m; ++j) {
                 long long key = i * 1000000LL + j;
                 // 如果該格子有被染色，覆蓋 -1
                 if(colors.find(key) != colors.end()) {
                     grid[i][j] = colors[key];
                 }
             }
        }

        return grid;
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