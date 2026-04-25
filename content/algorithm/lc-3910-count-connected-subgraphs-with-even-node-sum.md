---
title: "LeetCode 3910. Count Connected Subgraphs with Even Node Sum"
date: 2026-04-26T01:21:00+08:00
lastmod: 2026-04-26T01:21:00+08:00
difficulty: 1900
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "dfs", "bitmask", "enumeration"]
keywords: ["LC3910", "Hard", "Count Connected Subgraphs", "Bitmask", "State Compression"]
description: "LeetCode 第 3910 題：Count Connected Subgraphs with Even Node Sum。探討如何利用狀態壓縮 (Bitmask) 枚舉所有可能的子圖，並結合 DFS 與位元遮罩比對，高效驗證圖的連通性與計算節點總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3910](https://leetcode.com/problems/count-connected-subgraphs-with-even-node-sum/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Graph` $\cdot$ `DFS` $\cdot$ `Bitmask` $\cdot$ `Enumeration`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個無向圖，包含 $n$ 個節點（編號從 $0$ 到 $n-1$）。
你有一個長度為 $n$ 的整數陣列 `nums`，其中 `nums[i]` 代表節點 $i$ 的權重。
同時給定一個二維陣列 `edges`，其中 `edges[i] = [u, v]` 代表節點 $u$ 與節點 $v$ 之間有一條無向邊。
請計算並回傳滿足以下兩個條件的**連通子圖 (Connected Subgraphs)** 數量：
1. 子圖中的所有節點必須透過邊互相連通。
2. 子圖中所有節點的權重總和必須是**偶數 (Even)**。

### 限制條件
- $1 \le n \le 13$ (根據時間複雜度推估)
- $0 \le nums[i] \le 10^5$
- `edges` 構成一個有效的無向圖。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-26：初次提交 (狀態壓縮枚舉與 DFS 連通性驗證)](#2026-04-26-初次提交)

---

## 💡 2026-04-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題最大的破局點在於節點數量 $n \le 13$。這麼小的測資範圍，是標準的 **狀態壓縮枚舉 (Bitmask Enumeration)** 題型。
我們可以利用一個 $n$ 位元的整數來代表一個「子圖 (Subgraph)」的狀態：
- 第 $k$ 個位元為 $1$，代表節點 $k$ 被選入該子圖中。
- 第 $k$ 個位元為 $0$，代表節點 $k$ 未被選入。
總共有 $2^n - 1$ 種非空的組合。($2^{13} = 8192$，數量極小，完全在暴力枚舉的守備範圍內)。

對於每一個枚舉出的狀態 `val`，我們需要進行兩階段驗證：
1. **連通性驗證 (Connectivity Check)**：
   從該狀態中的「任意一個節點」出發進行 DFS，且 DFS 只能走訪**同樣存在於該狀態中**的節點。我們用一個整數 `check` 記錄走過的節點，如果 DFS 結束後 `check == val`，代表該子圖是完美連通的！
2. **總和驗證 (Sum Validation)**：
   若連通，則加總該狀態內所有節點的權重。若和為偶數，則將計數器加一。

### 🛠️ 解題思路 (Approach)
1. **建構無向圖**：將 `edges` 轉換為相鄰串列 `adj`。
2. **位元枚舉**：
   - 外層迴圈 `i` 從 $1$ 遍歷到 $(1 \ll n) - 1$。
3. **DFS 尋找連通分塊**：
   - 在當前狀態 `i` 中，尋找第一個存在的節點 $j$ (即 `(i & (1 << j)) != 0`) 作為 DFS 起點。
   - 宣告 `check = 0` 與 `visited` 陣列。
   - 執行 `dfs(i, j, visited, check, adj)`：
     - 將當前節點標記為已訪問：`visited[shift] = true`，並加入 `check` 遮罩：`check |= (1 << shift)`。
     - 遍歷相鄰節點 `next`，如果該節點未被訪問，**且存在於目標狀態 `val` 中** `(val & (1 << next)) != 0`，則繼續向下 DFS。
     - 只要探索完畢時 `check == val`，代表子圖連通。
4. **偶數總和判斷**：
   - 若 DFS 驗證連通失敗 (`!nfailed`)，則 `continue` 跳過。
   - 否則，走訪該狀態包含的所有節點並累加 `nums[j]`。
   - 判斷總和是否為偶數 `(sum % 2) == 0`，是則累加答案 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(2^n \cdot N)$。外層枚舉 $2^n$ 個狀態。每個狀態內部最多進行一次 DFS 驗證，DFS 遍歷節點與邊的複雜度不超過 $\mathcal{O}(N + E)$。整體執行次數約在 $10^5$ 數量級，效能優異。
- **空間複雜度**: $\mathcal{O}(N + E)$。建立圖的相鄰串列需要 $\mathcal{O}(N + E)$ 的空間，DFS 遞迴堆疊與 `visited` 陣列皆為 $\mathcal{O}(N)$ 空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // DFS 遍歷驗證連通性
    // val: 當前枚舉的子圖狀態 (Bitmask)
    // shift: 當前所在的節點編號
    // check: 用於記錄 DFS 實際走訪到的節點集合
    bool dfs(int val, int shift, vector<bool>& visited, int& check, vector<vector<int>>& adj) {
        // 安全邊界防護 (實務上 shift 恆小於 n)
        if(shift == visited.size()) {
            return check == val;
        }

        // 將當前節點加入已訪問的遮罩中
        check |= (1 << shift);
        visited[shift] = true;
        
        for(auto next : adj[shift]) {
            // 提早剪枝：若已訪問過，或該相鄰節點「不在」我們選定的子圖狀態 val 中，則略過
            if(visited[next] || (val & (1 << next)) == 0) continue;
            
            // 繼續深入探索，若子樹回傳已完全連通則提早返回 true
            if(dfs(val, next, visited, check, adj)) return true;
        }
    
        // 探索完畢後，檢查實際走訪到的節點 (check) 是否等於我們預期的子圖節點 (val)
        return check == val;
    }
    
    int evenSumSubgraphs(vector<int>& nums, vector<vector<int>>& edges) {
        int n = nums.size();
        
        // 1. 建立相鄰串列
        vector<vector<int>> adj(n, vector<int>());
        for(auto e : edges) {
            int u = e[0], v = e[1];
            adj[u].push_back(v);
            adj[v].push_back(u);
        }
        
        int rt = 0;
        
        // 2. 狀態壓縮枚舉：從 1 到 2^n - 1 遍歷所有非空的子圖組合
        // 2^13 = 1024 * 8 ~ 8192，運算量極小
        for(int i = 1; i <= (1 << n) - 1; ++i) {
            vector<bool> visited(n, false);
            int sum = 0;
            int check = 0;
            bool nfailed = true; // 記錄連通性驗證結果
            
            // 尋找該狀態中的第一個節點作為 DFS 起點
            for(int j = 0; j <= n - 1; ++j) {
                int num = (1 << j);
                if((i & num) == 0) continue;
                
                nfailed = dfs(i, j, visited, check, adj);
                break; // 找到起點且完成 DFS 後即可中斷
            }

            // 若不連通，直接跳過此狀態
            if(!nfailed) continue;
            
            // 3. 連通驗證通過，計算總和
            for(int j = 0; j <= n - 1; ++j) {
                int num = (1 << j);
                if((i & num) == 0) continue;
                sum += nums[j];
            }
            
            // 4. 若總和為偶數，將結果加 1
            if((sum % 2) == 0) {
                ++rt;
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