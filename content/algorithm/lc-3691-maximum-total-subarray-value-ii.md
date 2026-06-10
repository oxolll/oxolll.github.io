---
title: "LeetCode 3691. Maximum Total Subarray Value II"
date: 2026-06-10T22:45:00+08:00
lastmod: 2026-06-11T03:50:00+08:00
difficulty: 2469
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "sparse-table", "segment-tree", "priority-queue", "greedy", "rmq"]
keywords: ["LC3691", "Hard", "2469", "Maximum Total Subarray Value II", "稀疏表", "線段樹", "RMQ", "優先佇列", "多路歸併"]
description: "LeetCode 第 3691 題：Maximum Total Subarray Value II。難度評分：2469。探討兩種高階資料結構解法：一是雙重稀疏表 (Sparse Table) 搭配 O(1) 查詢；二是高空間效率的線段樹 (Segment Tree) 搭配指標收縮技術，皆在 O((N+k) log N) 時間內高效動態提取前 k 個最大的子陣列價值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3691](https://leetcode.com/problems/maximum-total-subarray-value-ii/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2469)</span>  
> **核心主題**：`RMQ` $\cdot$ `Sparse Table` $\cdot$ `Segment Tree` $\cdot$ `Priority Queue` $\cdot$ `Lazy Exploration`

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

## 📝 歷次打卡與演算法進化
- [解法二：線段樹與優先佇列指標收縮 (極致空間優化)](#💡-解法二線段樹與優先佇列指標收縮-極致空間優化)
- [解法一：雙重 RMQ 稀疏表與優先佇列指標收縮 (極致時間優化)](#💡-解法一雙重-rmq-稀疏表與優先佇列指標收縮-極致時間優化)

---

## 💡 解法一：雙重 RMQ 稀疏表與優先佇列指標收縮 (極致時間優化)

### 🎯 演算法分析
由於全域的子陣列總數高達 $\mathcal{O}(N^2)$ 量級，我們無法預先將所有子陣列的價值算出來排序。我們必須採用 **多路歸併 (Multi-way Merging)** 的**延遲決策 (Lazy Exploration)** 思想，把所有子陣列依據「起點 `idx`」分成 $N$ 組，動態收縮右端點。

為了解決頻繁查詢區間極值的效能瓶頸，此解法使用 **雙重稀疏表 (Sparse Table)**。透過 $\mathcal{O}(N \log N)$ 的預處理，後續不論查詢哪個區間，都可以透過重疊的兩個 $2^k$ 區間在 $\mathcal{O}(1)$ 常數時間內結算出 `max - min`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N + k \log N)$。建構稀疏表與初始化大根堆皆為 $\mathcal{O}(N \log N)$。迭代提取 Top-$k$ 時，每次 Pop 與 Push 耗時 $\mathcal{O}(\log N)$，執行 $k$ 次，總時間效能極致。
- **空間複雜度**: $\mathcal{O}(N \log N)$。主要消耗在稀疏表靜態二維陣列的開銷（約 $32 \times 50005 \times 4$ bytes $\approx 6.4$ MB）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    using ll = long long;
    using pii = pair<int, int>;
    
    // 宣告靜態稀疏表，防範在重複呼叫中產生動態記憶體配置開銷
    int mx[32][50005];
    int mn[32][50005];
    
    long long maxTotalValue(vector<int>& nums, int k) {
        int n = nums.size();
        
        // 1. 初始化稀疏表邊界值
        memset(mx, 0xcf, sizeof(mx)); // 極小值
        memset(mn, 0x7f, sizeof(mn)); // 極大值
        
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
        
        // 4. 延遲決策探索：每次只收縮並補推被選中起點的下一個最優候選者
        while(k--) {
            auto [val, idx] = pq.top(); 
            pq.pop();
            
            rt += val; // 累加目前全域最大價值
            
            --pos[idx]; // 該起點的右端點向左收縮一格
            
            if(pos[idx] < idx) continue;
            
            // 計算收縮後新子陣列 [idx, p] 的價值並補推回 Heap
            int p = pos[idx];
            int len = p - idx + 1;
            int lg = log2(len);
            
            int cur_max = max(mx[lg][idx], mx[lg][p - (1 << lg) + 1]);
            int cur_min = min(mn[lg][idx], min(mn[lg][p - (1 << lg) + 1]));
            
            pq.push({cur_max - cur_min, idx});
        }

        return rt;
    }
};
```

---

## 💡 解法二：線段樹與優先佇列指標收縮 (極致空間優化)

### 🎯 演算法分析
與解法一的稀疏表不同，本解法引入了經典的高階資料結構 —— **線段樹 (Segment Tree)**。

**為什麼選擇線段樹？**
1. **空間極致優化**：稀疏表需要 $\mathcal{O}(N \log N)$ 的二維空間，而線段樹採用標準的一維陣列編碼二元樹（大小固定為 `4 * N`），屬於嚴格的 **$\mathcal{O}(N)$ 線性空間**，記憶體開銷大幅縮減。
2. **時間閉環**：線段樹的區間查詢 `qry` 需要 $\mathcal{O}(\log N)$ 的時間。在初始化和 $k$ 次迭代的過程中，我們每次都進行一次 $\mathcal{O}(\log N)$ 的區間極值查詢，整體複雜度依然完美被控制在 **$\mathcal{O}((N + k) \log N)$**，在不對時間造成量級破壞的前提下，完成了空間的逆襲。
3. **位元運算提速**：在樹狀遍歷時，利用 `node << 1` 代替乘 2（左子節點），`node << 1 | 1` 代替乘 2 加 1（右子節點），將常數常開銷優化到極致。

本實作使用 1-based 索引將 `nums` 的元素動態建入樹中，`pos[idx]` 追蹤當前組別即將被切斷查詢的右端點，邏輯精準嚴密。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}((N + k) \log N)$。初始化線段樹需 $\mathcal{O}(N \log N)$；主迴圈執行 $k$ 次，每次從大根堆 Pop 與 Push 需 $\mathcal{O}(\log N)$，同時進行一次線段樹查詢 `qry` 需 $\mathcal{O}(\log N)$。整體運算次數與解法一在同一數量級。
- **空間複雜度**: $\mathcal{O}(N)$。線段樹僅佔用固定大小的靜態一維陣列（`4 * 50005`），空間複雜度優於稀疏表。

### 💻 程式碼實作 (C++)
```cpp
// 宣告全域/靜態線段樹陣列，4 * N 大小保證二元樹節點不越界
int mx[4 * 50005];
int mn[4 * 50005];

class Solution {
public:
    using pli = pair<long long, int>;
    using pll = pair<long long, long long>;
    
    // 線段樹單點更新 (初始化建樹)
    void upd(int node, int L, int R, int l, int r, int val) {
        if(L > R) return;
        // 當前區間完全被目標區間包含
        if(l <= L && R <= r) {
            mx[node] = val;
            mn[node] = val;
            return;
        }

        int mid = L + (R - L) / 2;
        // 位元運算優化：node << 1 等同於 node * 2，node << 1 | 1 等同於 node * 2 + 1
        if(l <= mid) upd(node << 1, L, mid, l, r, val);
        if(r > mid)  upd(node << 1 | 1, mid + 1, R, l, r, val);

        // Push-up 結算父節點極值
        mx[node] = max(mx[node << 1], mx[node << 1 | 1]);
        mn[node] = min(mn[node << 1], mn[node << 1 | 1]);
    }

    // 線段樹區間查詢：回傳 {區間最大值, 區間最小值}
    pll qry(int node, int L, int R, int l, int r) {
        if(L > R || l > R || r < L) return {INT_MIN, INT_MAX};
        if(l <= L && R <= r) return {mx[node], mn[node]};

        int mid = L + (R - L) / 2;
        long long rmx = INT_MIN, rmn = INT_MAX;
        
        if(l <= mid) {
            auto [a, b] = qry(node << 1, L, mid, l, r);
            rmx = max(rmx, a);
            rmn = min(rmn, b);
        }
        if(r > mid) {
            auto [a, b] = qry(node << 1 | 1, mid + 1, R, l, r);
            rmx = max(rmx, a);
            rmn = min(rmn, b);
        }

        return {rmx, rmn};
    }
    
    long long maxTotalValue(vector<int>& nums, int k) {
        int n = nums.size();
        
        // 1. 初始化靜態記憶體快取
        memset(mx, 0xcf, sizeof(mx));
        memset(mn, 0x7f, sizeof(mn));

        // 2. 將陣列元素以 1-based 索引打入線段樹
        for(int i = 0; i < n; ++i) {
            upd(1, 1, n, i + 1, i + 1, nums[i]);
        }

        // pos[i] 追蹤 1-based 起點 i 下一次即將被限縮的右端點邊界
        vector<int> pos(n + 1);
        priority_queue<pli, vector<pli>> pq; // 大根堆：{極值差, 起點索引}
        
        // 3. 多路歸併初始化：每個左端點都先查詢最長區間 [i, n]
        for(int i = 1; i <= n; ++i) {
            pos[i] = n - 1; // 記錄下一輪要縮小的指標對應位置
            auto [MX, MN] = qry(1, 1, n, i, n);
            pq.push({MX - MN, i});
        }

        long long rt = 0;
        
        // 4. 貪心延遲探索
        while(k--) {
            auto [val, idx] = pq.top(); 
            pq.pop();
            
            rt += val; // 累加當前最大值
            
            int next = pos[idx]; // 取得當前組別下一個要結算的右邊界
            if(next < idx) continue; // 超出左邊界代表此起點已全數枚舉完
            
            --pos[idx]; // 右端點向左收縮
            
            // 查詢收縮後新區間 [idx, next] 的極值差並推回 Heap
            auto [MX, MN] = qry(1, 1, n, idx, next);
            pq.push({MX - MN, idx});
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