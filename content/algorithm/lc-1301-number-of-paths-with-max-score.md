---
title: "LeetCode 1301. Number of Paths with Max Score"
date: 2026-07-05T14:52:00+08:00
lastmod: 2026-07-05T14:52:00+08:00
difficulty: 1853
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "breadth-first-search", "hash-table", "combinatorics"]
keywords: ["LC1301", "Hard", "1853", "Paths with Max Score", "廣度優先搜尋", "路徑計數", "同餘運算", "Set去重"]
description: "LeetCode 第 1301 題：Number of Paths with Max Score。難度評分：1853。探討如何利用帶有雙維度狀態的廣度優先搜尋 (BFS)，搭配 Hash Set 進行同層座標去重，安全且嚴謹地解決帶有障礙物的網格最大分數與路徑計數問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1301](https://leetcode.com/problems/number-of-paths-with-max-score/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (1853)</span>  
> **核心主題**：`Breadth-First Search (BFS)` $\cdot$ `Hash Set` $\cdot$ `Combinatorics`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個正方形字元陣列 `board`，你從右下角的字元 `'S'` 出發，目標是到達左上角的字元 `'E'`。
你的移動規則如下：
1. 每次只能向上、向左，或者向左上對角線移動一格。
2. 不能走到帶有 `'X'`（障礙物）的格子上。
3. 如果走到數字 `1` 到 `9` 的格子上，你可以獲得等同於該數字的分數。

請你回傳一個長度為 2 的陣列：
- 第一個元素是你能獲得的 **最大分數**。
- 第二個元素是能獲得該最大分數的 **路徑數量**。
由於路徑數量可能非常大，請將其對 $10^9 + 7$ 取模後回傳。如果根本無法到達終點，則回傳 `[0, 0]`。

### 限制條件
- $2 \le board.length == board[i].length \le 100$
</details>

---

## 📝 歷次打卡與演算法進化
- [2026-07-05：初次提交 (雙維度狀態 BFS + Set 座標去重)](#2026-07-05-初次提交)

---

## 💡 2026-07-05 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求我們在網格中尋找「最大分數」以及「達成最大分數的方法數」。
在不依賴純動態規劃 (DP) 拓撲順序的情況下，使用 **廣度優先搜尋 (BFS)** 逐層推進是非常穩健的工程解法。

**核心破局點 1：雙維度狀態紀錄**
對於每一個格子，我們必須同時追蹤兩個變數。
宣告 `visited[x][y] = {max_score, ways}`。
當我們從周圍的合法格子走到 `(nx, ny)` 時，會有兩種更新情況：
1. **打破紀錄 (`ns > visited`)**：這是一條更高分的新路，直接覆蓋過去的分數，並繼承該路徑的組合數 `c`。
2. **並列第一 (`ns == visited`)**：這是一條殊途同歸的等價路，將路徑數累加 `visited.second += c`，並對 $10^9 + 7$ 取模。

**核心破局點 2：Hash Set 阻斷指數級擴張 (Deduplication)**
這是這套 BFS 解法能存活的關鍵！
因為我們只能往上、左、左上走，不同路徑非常容易在「同一層級」走到完全相同的格子。如果不加思索地全部 `push` 進 Queue 中，佇列的大小會呈指數級別爆炸。
為了解決這個問題，我們在每一層的展開中引入 `unordered_set<int> mp`：
- 將走到/更新過狀態的新座標進行編碼（如 `nx * 1000 + ny`）存入 Set。
- 在該層所有擴張結束後，才統一將 Set 裡的「唯一座標」提取出來放入下一層的 Queue 中。
如此一來，每個座標在每一波推進中只會被處理一次，完美壓制了時間複雜度。

### 🛠️ 解題思路 (Approach)
1. **資料結構初始化**：
   - `visited[n][n]` 陣列初始化為 `{-1, 0}`，起點 `visited[n-1][n-1] = {0, 1}`。
   - 宣告 `Queue` 放入起點，宣告 `unordered_set` 準備去重。
2. **展開 BFS (層次遍歷)**：
   - 記錄當前層的大小 `t = q.size()`，進行迴圈。
   - 取出狀態，向三個方向探索 (`dx, dy`)。
   - 過濾越界與障礙物 `'X'`。
   - 計算新分數 `ns`，進行上述的「打破紀錄/並列第一」狀態更新。
   - 將合法推進的座標編碼為 `nx * 1000 + ny` 存入 Set 中。
3. **準備下一波 Queue**：
   - 遍歷 Set，將唯一化的座標解碼 (`x = key / 1000`, `y = key % 100`)，從 `visited` 中提取最新狀態塞入 Queue。
   - 清空 Set。
4. **結算與防禦**：
   - 檢查終點 `visited[0][0]` 是否仍為 `-1`。若是，代表無路可達，回傳 `{0, 0}`。
   - 否則回傳 `visited[0][0]` 的分數與路徑數。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。其中 $N$ 是網格邊長。因為有 Set 去重機制，BFS 實際上是以層級推進的拓撲排序，每個網格點最多只會被放入 Queue 中常數次。
- **空間複雜度**: $\mathcal{O}(N^2)$。維護了大小為 $N \times N$ 的 `visited` 陣列，以及最壞情況下佔用 $\mathcal{O}(N)$ 的 Queue 和 Set，空間耗用在合理範圍。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    using pii = pair<int, int>;
    using ppp = pair<pii, pii>;
    
    // 方向陣列：左、上、左上
    vector<pii> dir = {{0, -1}, {-1, 0}, {-1, -1}};
    long long MOD = 1e9 + 7;
    
    vector<int> pathsWithMaxScore(vector<string>& board) {
        int n = board.size();

        // visited 儲存 {最大分數, 抵達該分數的路徑數}
        vector<vector<pii>> visited(n, vector<pii>(n, {-1, 0})); 
        visited[n - 1][n - 1] = {0, 1}; // 起點初始化
        
        queue<ppp> q;
        q.push({{n - 1, n - 1}, {0, 1}});
        
        // 用於同一層級座標去重的 Hash Set
        unordered_set<int> mp;
        
        while(!q.empty()) {
            int t = q.size();
            while(t--) {
                auto [p, p2] = q.front(); q.pop();
                auto [x, y] = p;
                auto [s, c] = p2;
                
                // 往三個方向擴張
                for(auto [dx, dy] : dir) {
                    int nx = x + dx, ny = y + dy;
                    
                    // 邊界防禦與障礙物迴避
                    if(nx < 0 || ny < 0 || board[nx][ny] == 'X') continue;
                    
                    // 計算到達新格子的分數 (若為終點 E 則加 0)
                    int ns = s + (board[nx][ny] == 'E' ? 0 : board[nx][ny] - '0');
                    
                    // 剪枝：如果這條路徑的分數比較低，直接淘汰
                    if(ns < visited[nx][ny].first) continue;
                    
                    // 狀態更新：並列第一則累加路徑數，打破紀錄則直接覆蓋
                    if(ns == visited[nx][ny].first) {
                        visited[nx][ny].second += c;
                        visited[nx][ny].second %= MOD;
                    } else {
                        visited[nx][ny] = {ns, c};
                    }

                    // 將座標編碼並放入 Set 進行去重
                    // 由於 N <= 100，ny 最大為 99，乘以 1000 絕對不會碰撞
                    int key = nx * 1000 + ny;
                    mp.insert(key);
                }
            }
            
            // 該層探索完畢，將 Set 中唯一化的座標推入 Queue 準備下一波推進
            for(auto key : mp) {
                int x = key / 1000, y = key % 100;
                q.push({{x, y}, {visited[x][y].first, visited[x][y].second}});
            }
            mp.clear(); // 清空供下一層使用
        }

        int rs, rc;
        
        // 終點防禦：若仍為 -1 代表被障礙物完全封死，無法抵達
        if(visited[0][0].first == -1) {
            rs = 0, rc = 0;
        } else {
            rs = visited[0][0].first;
            rc = visited[0][0].second;
        }
        
        return {rs, rc};
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