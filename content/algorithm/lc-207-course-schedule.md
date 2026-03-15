---
title: "LeetCode 207. Course Schedule"
date: 2026-03-15T23:00:00+08:00
lastmod: 2026-03-15T23:00:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Graph", "Topological Sort", "Breadth-First Search"]
keywords: ["LC207", "N/A", "Medium", "Course Schedule", "Kahn's Algorithm", "Cycle Detection"]
description: "LeetCode 第 207 題：Course Schedule。難度評分：N/A (經典面試題)。探討如何利用 Kahn's Algorithm 進行拓樸排序 (Topological Sort)，以 BFS 搭配入度陣列偵測有向圖中是否存在環。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 207](https://leetcode.com/problems/course-schedule/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>  
> **核心主題**：`Graph` $\cdot$ `Topological Sort` $\cdot$ `Breadth-First Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
你總共需要修讀 `numCourses` 門課程，課程編號從 $0$ 到 $numCourses - 1$。
給你一個陣列 `prerequisites`，其中 $prerequisites[i] = [a_i, b_i]$，表示如果你要修讀課程 $a_i$，你**必須**先修讀課程 $b_i$。
例如，配對 $[0, 1]$ 表示：要修讀課程 $0$，你必須先修畢課程 $1$。

如果有可能完成所有課程，請回傳 `true`；否則，回傳 `false`。

### 範例
- **Input**: `numCourses = 2`, `prerequisites = [[1,0]]`
  **Output**: `true`
  **Explanation**: 總共有 2 門課程。要修讀課程 1，你應該先修讀課程 0。這是可以達成的。

- **Input**: `numCourses = 2`, `prerequisites = [[1,0],[0,1]]`
  **Output**: `false`
  **Explanation**: 總共有 2 門課程。要修讀課程 1，你應該先修讀課程 0；而要修讀課程 0，你又應該先修讀課程 1。這形成了一個循環（環），因此不可能完成。

### 限制條件
- $1 \le numCourses \le 2000$
- $0 \le prerequisites.length \le 5000$
- $prerequisites[i].length == 2$
- $0 \le a_i, b_i < numCourses$
- `prerequisites` 中的所有配對皆為**獨一無二**。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (Kahn's Algorithm 拓樸排序與環偵測)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
這是一個非常典型的「任務排程」與「相依性解析」問題。我們可以將課程視為圖的「節點」，將前置條件視為「有向邊」（從 $b_i$ 指向 $a_i$）。
如果這張有向圖裡面存在「循環（環，Cycle）」，代表課程之間互相卡死，就永遠無法修完。
為了解決這個問題，最標準的作法是使用 **Kahn's Algorithm (拓樸排序)**：
1. 計算每門課的「入度 (Indegree)」：代表這門課還有幾個前置課程沒修。
2. 從所有「入度為 0」（沒有前置課程，隨時可修）的課程開始下手。
3. 修完這些課後，把依賴它們的後續課程的入度減 1（解鎖前置條件）。
4. 如果後續課程的入度也變成 0 了，代表它的前置條件都滿足了，就把它加入待修清單。
5. 最後檢查是不是所有課程都被修過了。如果還有課程的入度大於 0，代表它們卡在某個死結（環）裡了。

### 🛠️ 解題思路 (Approach)
1. **建圖與統計入度**：
   - 宣告 `indeg` 陣列記錄每個節點的入度，初始為 0。
   - 宣告 `next` 二維陣列（Adjacency List）記錄圖的相鄰邊。
   - 遍歷 `prerequisites`，對於每條規則 `[cur, prev]`，建立有向邊 `prev -> cur`，並讓 `cur` 的入度加一。
2. **初始化 Queue**：
   - 遍歷所有課程，將所有 `indeg[i] == 0` 的獨立課程推入 Queue 中作為起點。
3. **BFS 拓樸排序**：
   - 當 Queue 不為空時，取出一門課程 `cur`（代表已修畢）。
   - 遍歷 `next[cur]` 中所有依賴這門課的後續課程 `nx`。
   - 將 `nx` 的入度減一 (`--indeg[nx]`)。
   - 若 `nx` 的入度歸零，代表其所有前置條件皆已達成，將其推入 Queue。
4. **驗證結果**：
   - 迴圈結束後，檢查 `indeg` 陣列。若仍有節點入度不為 0，代表圖中存在環，回傳 `false`；否則回傳 `true`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(V + E)$。其中 $V$ 為課程總數 (`numCourses`)，$E$ 為前置條件總數 (`prerequisites.length`)。我們只遍歷了每個節點和每條有向邊各一次。
- **空間複雜度**: $\mathcal{O}(V + E)$。儲存鄰接矩陣 (Adjacency List) 需要 $\mathcal{O}(V + E)$ 的空間，入度陣列與 Queue 則各需要 $\mathcal{O}(V)$ 的空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<int> indeg(numCourses, 0); // 記錄每門課程的入度 (欠幾個前置)
        vector<vector<int>> next(numCourses, vector<int>()); // 鄰接矩陣
        
        // 1. 建圖並計算入度
        for(auto p : prerequisites) {
            int cur = p[0], prev = p[1];
            ++indeg[cur];
            next[prev].push_back(cur);
        }

        queue<int> q;
        // 2. 將所有入度為 0 (無前置條件) 的課程加入 Queue
        for(int i = 0; i < numCourses; ++i) {
            if(indeg[i] == 0) {
                q.push(i);
            }
        }
        
        // 3. 執行 BFS 拓樸排序 (Kahn's Algorithm)
        while(!q.empty()) {
            auto cur = q.front();
            q.pop();
            
            // 釋放依賴當前課程的後續課程
            for(auto nx : next[cur]) {
                --indeg[nx];
                // 若後續課程的前置條件皆已達成，將其推入 Queue
                if(indeg[nx] == 0) {
                    q.push(nx);
                }
            }
        }

        // 4. 檢查是否所有課程都已解鎖 (若無，代表有 Cycle)
        for(int i = 0; i < numCourses; ++i) {
            if(indeg[i] != 0) {
                return false;
            }
        }
        
        return true;
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