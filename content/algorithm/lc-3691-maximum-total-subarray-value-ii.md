---
title: "LeetCode 3691. Maximum Total Subarray Value II"
date: 2026-06-10T22:45:00+08:00
lastmod: 2026-06-10T22:45:00+08:00
difficulty: 2469
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "sparse-table", "priority-queue", "greedy", "sliding-window"]
keywords: ["LC3691", "Hard", "2469", "Maximum Total Subarray Value II", "稀疏表", "RMQ", "優先佇列", "多路歸併"]
description: "LeetCode 第 3691 題：Maximum Total Subarray Value II。難度評分：2469。探討如何完美結合雙重稀疏表 (Sparse Table) 與優先佇列延遲收縮技術，在 O((N+k) log N) 的極致時間內高效動態提取前 k 個最大的子陣列價值總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3691](https://leetcode.com/problems/maximum-total-subarray-value-ii/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2469)</span>  
> **核心主題**：`Sparse Table (RMQ)` $\cdot$ `Priority Queue` $\cdot$ `Greedy` $\cdot$ `Lazy Exploration`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums` 和一個整數 `k`。
我們定義一個連續子陣列的 **Value (價值)** 為：該子陣列中的 **最大值** 減去 **最小值**。

請你找出所有可能的子陣列中，價值前 `k` 大的子陣列，並回傳這 `k` 個子陣列的 **價值總和**。
題目保證子陣列的總數至少有 `k` 個。

### 限制條件
- $1 \le nums.length \le 50000$
- $1 \le k \le 50000$
- $-10^9 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-10：初次提交 (雙重 RMQ 稀疏表 + 優先佇列指標收縮最優解)](#2026-06-10-初次提交)

---

## 💡 2026-06-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求前 $k$ 個最大的子陣列價值總和。由於陣列長度 $N \le 50000$，全域的子陣列總數高達 $\mathcal{O}(N^2)$ 量級，我們絕對不可能預先將所有子陣列的價值算出來排序。

**核心破局點 1：雙重稀疏表達成 $\mathcal{O}(1)$ 區間極值查詢**
為了能瞬間獲得任意區間 $[L, R]$ 的最大值與最小值，我們在演算法開頭建構 **雙重稀疏表 (Sparse Table)**：
- `mx[i][j]`：儲存從索引 `j` 開始，長度為 $2^i$ 的區間最大值。
- `mn[i][j]`：儲存從索引 `j` 開始，長度為 $2^i$ 的區間最小值。
透過 $\mathcal{O}(N \log N)$ 的預處理，後續不論查詢哪個區間，都可以透過重疊的兩個 $2^k$ 區間在 $\mathcal{O}(1)$ 時間內結算出 `max - min`。

**核心破局點 2：延遲決策與優先佇列收縮法 (Lazy Heap Exploration)**
這是一個非常經典的 **多路歸併 (Multi-way Merging)** 思想。
我們可以把所有子陣列依據「起點 `idx`」分成 $N$ 組。對於每一個固定的起點 `idx`，其初始最優的右端點候選人是 `n - 1`（因為區間越長，包含進來的極值差通常越大，具有貪心潛力）。

1. **初始化**：我們為每個起點 `i` 計算區間 `[i, n-1]` 的價值，並將 `{Value, 起點 i}` 推入一個最大優先佇列 (`priority_queue`) 中。此時 Heap 的大小恰好為 $N$。
2. **動態迭代 $k$ 次**：
   - 每次從 Heap 頂端彈出當前全局價值最大的子陣列。
   - 將其價值累加至總和 `rt`。
   - **關鍵延遲步驟**：被彈出的子陣列其右端點為 `pos[idx]`。我們隨即將該起點的右端點向左收縮一位：`--pos[idx]`。只要新的右端點依然合法 (`pos[idx] >= idx`)，我們就利用稀疏表 $\mathcal{O}(1)$ 算出新區間 `[idx, pos[idx]]` 的價值，並重新推回 Heap 中。
   
這種「彈出一個，才生出下一個」的動態收縮策略，成功將優先佇列的規模死死釘在 $\mathcal{O}(N)$，從而完美突破了 $\mathcal{O}(N^2)$ 的物理限制！

---

### 🛠️ 解題思路 (Approach)
1. **靜態快取配置**：配置 `mx[32][50005]` 與 `mn[32][50005]` 陣列，並使用 `memset` 初始化邊界。
2. **建構 Sparse Table**：雙層迴圈遞迴填表，實作區間最大/最小值的動態規劃。
3. **建立狀態追蹤與初始化 Heap**：
   - 建立 `pos` 陣列，`pos[i]` 記錄起點 `i` 目前對應的右端點，初始皆為 `n - 1`。
   - 將所有起點的最長區間價值算好放入 `pq`。
4. **貪心提取前 $k$ 大值**：
   - 執行 `while(k--_` 迴圈。
   - 彈出最大值，累加至 `rt`。
   - 指標左移，若未越界則計算新價值並補推回 `pq`。
5. **回傳總價值 `rt`**。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N + k \log N)$。
  - 建構稀疏表耗時 $\mathcal{O}(N \log N)$。
  - 初始化優先佇列耗時 $\mathcal{O}(N \log N)$。
  - 提取前 $k$ 大值，每次 Pop 與 Push 耗時 $\mathcal{O}(\log N)$，執行 $k$ 次，耗時 $\mathcal{O}(k \log N)$。
  - 整體時間複雜度在 $N, k = 50000$ 時運算量僅約百萬級別，極速通關。
- **空間複雜度**: $\mathcal{O}(N \log N)$。主要消耗在稀疏表靜態陣列的開銷（$32 \times 50005 \times 4$ bytes $\approx 6.4$ MB），記憶體佔用極小。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    using ll = long long;
    using pii = pair<int, int>;
    
    // 宣告靜態稀疏表，防範在遞迴/重複呼叫中產生動態記憶體配置開銷
    int mx[32][50005];
    int mn[32][50005];
    
    long long maxTotalValue(vector<int>& nums, int k) {
        int n = nums.size();
        
        // 1. 初始化稀疏表邊界值
        memset(mx, 0xcf, sizeof(mx)); // 初始化為極小值
        memset(mn, 0x7f, sizeof(mn)); // 初始化為極大值
        
        // 2. 建立常數時間區間查詢表 (Sparse Table DP)
        for(int j = 0; j < n; ++j) {
            mx[0][j] = mn[0][j] = nums[j];
        }
        for(int i = 1; i < 31; ++i) {
            for(int j = 0; j + (1 << i) <= n; ++j) {
                mx[i][j] = max(mx[i - 1][j], mx[i - 1][j + (1 << (i - 1))]);
                mn[i][j] = min(mn[i - 1][j], mn[i - 1][j + (1 << (i - 1))]);
            }
        }

        // pos[i] 記錄以 i 為起點的子陣列，目前考量到的右端點位置
        vector<int> pos(n);
        priority_queue<pii, vector<pii>> pq; // 大根堆：{區間價值, 起點索引}
        
        // 3. 初始化：每個起點都先將最長可能區間 [i, n-1] 丟入 Heap 中
        for(int i = 0; i < n; ++i) {
            pos[i] = n - 1;
            int len = n - 1 - i + 1;
            int lg = log2(len);
            
            // 透過 RMQ O(1) 計算極值差
            int cur_max = max(mx[lg][i], mx[lg][pos[i] - (1 << lg) + 1]);
            int cur_min = min(mn[lg][i], mn[lg][pos[i] - (1 << lg) + 1]);
            
            pq.push({cur_max - cur_min, i});
        }

        long long rt = 0;
        
        // 4. 延遲決策探索：動態迭代 k 次，每次只收縮並補推被選中起點的下一個最優候選者
        while(k--) {
            auto [val, idx] = pq.top(); 
            pq.pop();
            
            rt += val; // 累加目前全域最大價值
            
            --pos[idx]; // 該起點的右端點向左收縮一格
            
            // 若右端點小於左起點，代表該起點的所有子陣列已全數枚舉完畢
            if(pos[idx] < idx) continue;
            
            // 計算收縮後新子陣列 [idx, p] 的價值並補推回 Heap
            int p = pos[idx];
            int len = p - idx + 1;
            int lg = log2(len);
            
            int cur_max = max(mx[lg][idx], mx[lg][p - (1 << lg) + 1]);
            int cur_min = min(mn[lg][idx], mn[lg][p - (1 << lg) + 1]);
            
            pq.push({cur_max - cur_min, idx});
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