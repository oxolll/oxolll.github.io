---
title: "LeetCode 399. Evaluate Division"
date: 2026-03-30T16:35:00+08:00
lastmod: 2026-03-30T16:35:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "depth-first-search", "hash-table"]
keywords: ["LC399", "Medium", "N/A", "Evaluate Division", "Graph", "DFS"]
description: "LeetCode 第 399 題：Evaluate Division。難度評分：Medium (N/A)。探討如何將數學除法等式轉化為有向權重圖，並透過深度優先搜尋 (DFS) 尋找節點間的路徑以求出未知的除法結果。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 399](https://leetcode.com/problems/evaluate-division/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Graph` $\cdot$ `Depth-First-Search` $\cdot$ `Hash Table`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個變數對陣列 `equations` 和一個實數陣列 `values` 作為已知條件，其中 $equations[i] = [A_i, B_i]$ 和 $values[i]$ 代表方程式 $A_i / B_i = values[i]$。每個 $A_i$ 或 $B_i$ 是一個表示單一變數的字串。
另給你一個陣列 `queries`，其中 $queries[j] = [C_j, D_j]$ 表示第 $j$ 個問題，要求你計算 $C_j / D_j$ 的答案。

請回傳所有問題的答案。如果存在某個無法確定的答案，則該答案回傳 `-1.0`。

### 限制條件
- $1 \le equations.length \le 20$
- $equations[i].length == 2$
- $1 \le A_i.length, B_i.length \le 5$
- $values.length == equations.length$
- $0.0 < values[i] \le 20.0$
- $1 \le queries.length \le 20$
- $queries[i].length == 2$
- $1 \le C_j.length, D_j.length \le 5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-30：初次提交 (有向圖建立與 DFS 路徑搜尋)](#2026-03-30-初次提交)

---

## 💡 2026-03-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的本質是「連通性」與「路徑權重乘積」的計算。
我們可以將每個變數視為圖中的一個「節點」。若已知 $A / B = 2.0$，這等價於存在一條從節點 $A$ 指向節點 $B$、權重為 $2.0$ 的有向邊；同時，根據數學倒數關係，必定存在一條從節點 $B$ 指向節點 $A$、權重為 $1 / 2.0 = 0.5$ 的有向邊。
當系統詢問 $A / C$ 的結果時，問題即轉換為：**「在圖中尋找一條從起點 $A$ 到終點 $C$ 的路徑，並將沿途所有邊的權重相乘。」**
若找不到路徑，或者 $A$、$C$ 中有任何一個節點根本不存在於圖中，則結果為無法判定（回傳 `-1.0`）。

### 🛠️ 解題思路 (Approach)
1. **建立有向權重圖**：
   - 宣告一個 Hash Table `mp`，鍵為變數字串，值為 `vector<pair<string, double>>`，記錄該節點相鄰的目的地與對應權重。
   - 走訪 `equations` 與 `values` 陣列。對於每對關係 $(U, V)$ 及其數值 $val$，在 `mp` 中加入 $U \to V$ (權重 $val$) 以及 $V \to U$ (權重 $1.0/val$) 的邊。
2. **處理查詢 (Queries)**：
   - 宣告結果陣列 `rt`。走訪每一個查詢 $q = [C, D]$。
   - **防呆檢查**：若節點 $C$ 或 $D$ 任何一個不存在於 `mp` 中，必定無法算出答案，直接將 `-1.0` 加入結果陣列。
   - **深度優先搜尋 (DFS)**：宣告 `visited` 集合並將起點 $C$ 加入其中。呼叫 `dfs(C, D, visited)` 進行路徑搜尋。
3. **DFS 實作細節**：
   - **終止條件**：若當前節點 `cur` 等於目標節點 `tar`，回傳 $1.0$ 作為連乘的基準值。
   - **狀態轉移**：遍歷 `cur` 的所有相鄰節點 `next`。若 `next` 已在 `visited` 中，直接跳過以避免循環走訪。
   - 將 `next` 標記為已走訪，遞迴呼叫 `dfs` 繼續向深處尋找。
   - 回溯：走訪結束後將 `next` 從 `visited` 中移除 (`visited.erase(next)`)。
   - 若遞迴結果 `val2` 不為 `-1.0`，代表成功找到通往目標的路徑，將 `val2` 乘上當前邊的權重 `val` 後回傳。
   - 若遍歷所有相鄰節點皆無法到達目標，回傳 `-1.0`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(Q \times (V + E))$。其中 $Q$ 為查詢的數量，$V$ 為變數節點數，$E$ 為已知方程式的數量。建立圖的時間為 $\mathcal{O}(E)$；對於每個查詢，最差情況下 DFS 需要走訪圖中所有的節點與邊。
- **空間複雜度**: $\mathcal{O}(V + E)$。Hash Table `mp` 需要儲存所有的節點與邊。DFS 的遞迴呼叫堆疊深度最大為 $V$，且 `visited` 集合最多儲存 $V$ 個節點。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    unordered_map<string, vector<pair<string, double>>> mp;
    
    vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries) {
        // 1. 建圖：處理每一組方程式與倒數關係
        for(int i = 0; i < values.size(); ++i) {
            string u = equations[i][0], v = equations[i][1];
            double val = values[i];
            mp[u].push_back({v, val});
            mp[v].push_back({u, 1.0 / val});
        }

        vector<double> rt;
        
        // 2. 處理每個查詢
        for(auto q : queries) {
            // 若查詢的變數不存在於圖中，直接回傳 -1.0
            if(mp.find(q[0]) == mp.end() || mp.find(q[1]) == mp.end()) {
                rt.push_back(-1.0);
                continue;
            }
            
            // 透過 DFS 尋找路徑與權重乘積
            unordered_set<string> visited({q[0]});
            rt.push_back(dfs(q[0], q[1], visited));
        }
        
        return rt;
    }
    
private:
    double dfs(string cur, string tar, unordered_set<string>& visited) {
        // 抵達目標，回傳基準值 1.0 供上層邊權重相乘
        if(cur == tar) return 1.0;

        // 遍歷相鄰節點
        for(auto [next, val] : mp[cur]) {
            if(visited.find(next) != visited.end()) continue; // 避免循環走訪
            
            visited.insert(next);
            double val2 = dfs(next, tar, visited);
            visited.erase(next); // 回溯復原狀態
            
            // 若成功找到路徑，計算連乘結果並立即回傳
            if(val2 != -1.0) {
                val2 *= val;
                return val2;
            }
        }

        return -1.0; // 該分支無法到達目標
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