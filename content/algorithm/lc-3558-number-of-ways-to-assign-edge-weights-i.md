---
title: "LeetCode 3558. Number of Ways to Assign Edge Weights I"
date: 2026-06-11T11:40:00+08:00
lastmod: 2026-06-11T11:40:00+08:00
difficulty: 1845
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "tree", "depth-first-search", "combinatorics", "math"]
keywords: ["LC3558", "Medium", "1845", "Assign Edge Weights", "樹狀結構", "深度優先搜尋", "組合數學", "取模運算", "DFS 優化"]
description: "LeetCode 第 3558 題：Number of Ways to Assign Edge Weights I。難度評分：1845。探討如何透過傳遞 Parent 節點的高效 DFS 挖掘樹狀結構的全域最大深度，並利用二進位組合原理與同餘運算，求解邊權重指派的合法方案總數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3558](https://leetcode.com/problems/number-of-ways-to-assign-edge-weights-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1845)</span>  
> **核心主題**：`Tree` $\cdot$ `Depth-First Search` $\cdot$ `Combinatorics` $\cdot$ `Modular Arithmetic`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個無向樹，包含 $E$ 條邊，共有 $E + 1$ 個節點，節點編號從 $1$ 到 $E + 1$。
我們需要為這棵樹的每條邊指派一個權重。依據特定約束條件，合法的方案數與從特定的根節點（節點 1）出發所能達到的 **最大乘積層數 / 最大深度** 呈二進位指數關係。

請你計算所有合法的權重指派方案數，並將結果對 $10^9 + 7$ 取模。

### 限制條件
- $1 \le edges.length \le 10^5$
- `edges[i] = [u, v]` 代表節點 `u` 與 `v` 之間存在一條無向邊。
- 輸入保證可以構成一棵有效的樹。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-11：優化提交 (無 visited 陣列之純粹樹狀 DFS)](#2026-06-11-優化提交)

---

## 💡 2026-06-11 優化提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的核心本質是尋找樹中以節點 1 為根時的 **最大深度 (Maximum Depth)**。

**核心破局點 1：樹的深度決定決策樹大小**
當我們把節點 1 當作根節點展開整棵樹時，每一層的邊都代表著一級獨立的決策。根據題目的規律，每增加一層有效深度，可行的指派組合就會翻倍（即引入一個新的二進位自由度）。因此，只要算出根節點到最深葉子節點的距離 `mx`，最終的答案就是 $2^{mx - 1}$。

**核心破局點 2：高效樹狀 DFS 遍歷 (Parent 節點優化)**
由於輸入保證是一棵無向樹（無環圖），從節點 1 到任何其他節點的簡單路徑都是唯一確定的。在進行 DFS 遍歷時，為了避免「走回頭路」產生無窮迴圈，我們**不需要**額外開闢 $\mathcal{O}(N)$ 的 `visited` 布林陣列。只需在遞迴參數中傳遞 `par` (父節點編號)，並在探索相鄰節點時跳過父節點即可，這能大幅降低空間常數與狀態切換開銷。

---

### 🛠️ 解題思路 (Approach)
1. **建構鄰接表 (`adj`)**：遍歷 `edges` 陣列，建立無向圖的鄰接表。注意節點總數為 `edges.size() + 2`，以防止 1-based 索引越界。
2. **DFS 探測最大深度 (`maxD`)**：
   - 傳入當前節點 `cur` 與其父節點 `par`（根節點的父節點可設為 0）。
   - 遞迴探索所有相鄰節點 `next`，若 `next == par` 則跳過。
   - 回傳子樹深度的最大值並加 1：`rt = max(rt, maxD(adj, next, cur) + 1)`。
   - 取得根節點出發的最長距離 `mx`。
3. **同餘組合結算**：
   - 初始化結果 `dp = 1`。
   - 執行 `mx - 1` 次循環，每次將 `dp` 乘以 2 並對 $10^9 + 7$ 取模。
4. **回傳結果**。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是節點的總數量（$E + 1$）。建構鄰接表與 DFS 遍歷中，每條邊與每個節點都只會被造訪常數次。最後的冪次循環耗時 $\mathcal{O}(depth)$，遠小於 $N$。
- **空間複雜度**: $\mathcal{O}(N)$。鄰接表需要 $\mathcal{O}(N)$ 的記憶體空間。DFS 遞迴呼叫堆疊在最壞情況下（樹退化為鏈狀）的深度也為 $\mathcal{O}(N)$。省略了額外的 `visited` 陣列，達成了空間的常數級優化。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const int MOD = 1e9 + 7;

    // 優化版 DFS 函數：利用 par (父節點) 參數防止走回頭路，省去 visited 陣列
    int maxD(vector<vector<int>>& adj, int cur, int par) {
        int rt = 0;
        
        for(auto next : adj[cur]) {
            // 如果相鄰節點是剛走過來的父節點，直接跳過
            if(next == par) continue;
            
            // 遞迴計算子樹深度，並與當前最大值取大者
            rt = max(rt, maxD(adj, next, cur) + 1);
        }

        return rt;
    }

    int assignEdgeWeights(vector<vector<int>>& edges) {
        // 樹中節點數 N = 邊數 + 1，多開空間防範 1-based 索引越界
        int n = edges.size() + 2;
        vector<vector<int>> adj(n, vector<int>());
        
        // 1. 建立無向樹的鄰接表
        for(auto& e : edges) {
            adj[e[0]].push_back(e[1]);
            adj[e[1]].push_back(e[0]);
        }

        // 2. 從根節點 1 開始探測最大深度 mx，根節點沒有父節點，故傳入 0
        int mx = maxD(adj, 1, 0);

        // 3. 根據深度決策樹結算答案：計算 2^(mx - 1) % MOD
        int dp = 1;
        for(int i = 1; i < mx; ++i) {
            dp = (2 * dp) % MOD;
        }
        
        return dp;
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