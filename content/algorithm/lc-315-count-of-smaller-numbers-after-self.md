---
title: "LeetCode 315. Count of Smaller Numbers After Self"
date: 2026-04-30T16:00:00+08:00
lastmod: 2026-04-30T16:00:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "segment-tree", "array", "divide-and-conquer"]
keywords: ["LC315", "Hard", "Count of Smaller Numbers After Self", "Segment Tree", "線段樹"]
description: "LeetCode 第 315 題：Count of Smaller Numbers After Self。難度評分：Hard。探討如何利用線段樹 (Segment Tree) 作為動態頻率陣列，結合逆向掃描與座標平移技巧，以 O(N log M) 高效算出每個元素右側較小元素的數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 315](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Segment Tree` $\cdot$ `Binary Indexed Tree` $\cdot$ `Divide and Conquer`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。請你回傳一個相同長度的新陣列 `counts`，其中 `counts[i]` 代表在 `nums[i]` 的**右側**，且數值**嚴格小於** `nums[i]` 的元素數量。

### 限制條件
- $1 \le nums.length \le 10^5$
- $-10^4 \le nums[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-30：初次提交 (線段樹 + 逆向掃描 + 座標平移)](#2026-04-30-初次提交)

---

## 💡 2026-04-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道非常經典的「逆序數對 (Inversion Count)」變形題。如果使用雙層迴圈暴力解，時間複雜度會高達 $\mathcal{O}(N^2)$。
為了達到 $\mathcal{O}(N \log N)$ 等級的效能，我們可以使用 **線段樹 (Segment Tree)** 或 **樹狀數組 (BIT)**。

**核心思想：動態頻率陣列 (Dynamic Frequency Map)**
我們將線段樹作為一個「頻率直方圖」來使用，線段樹的「索引」代表 `nums` 中的「數值」，線段樹的「值」代表該數值出現的「次數」。
1. **逆向掃描**：因為題目要求的是「右側」較小元素的數量，我們從陣列的最右邊開始往左掃描。這樣能確保我們每次查詢線段樹時，樹裡面的資料**全都是當前元素右側的數字**。
2. **座標平移 (Offset)**：由於 `nums[i]` 可能包含負數（最小為 $-10000$），而線段樹的底層結構是從索引 $1$ 開始的陣列。因此我們將所有數值加上 $10001$，將範圍 $[-10000, 10000]$ 強制平移到正整數區間 $[1, 20001]$。
3. **查詢與更新**：
   - **查詢**：對於當前平移後的數值 $X$，我們在線段樹中查詢區間 $[1, X-1]$ 的總和，這個總和就是「右邊比自己小的數字總數」。
   - **更新**：查詢完畢後，將數值 $X$ 的頻率加 $1$（單點更新）。

### 🛠️ 解題思路 (Approach)
1. **確定值域與建樹**：找出陣列最大值並加上平移量 $10001$ 得到上限 $N$。宣告大小為 $4N$ 的線段樹陣列。
2. **由右至左掃描**：
   - 從 $i = n-1$ 遍歷到 $0$。
   - `query(1, 1, N, 1, nums[i] + 10000)`：查詢區間 $[1, nums[i] - 1]$ 的頻率總和。
   - `update(1, 1, N, nums[i] + 10001, 1)`：將當前數字加入線段樹，對應位置的頻率 $+1$。
   - 將查詢結果推入 `rt` 陣列中。
3. **反轉結果**：因為我們是從右向左搜集答案的，最後必須將 `rt` 陣列反轉 (`reverse`) 才能符合題目要求的順序。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log M)$。其中 $N$ 是陣列長度，$M$ 是數值範圍的跨度 (約為 $20000$)。每次查詢與更新線段樹耗時 $\mathcal{O}(\log M)$，共執行 $N$ 次。
- **空間複雜度**: $\mathcal{O}(M)$。線段樹需要開 $4 \times M$ 的空間。因為數值範圍不大，空間消耗極低。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<long long> tree;
    int N; // 線段樹維護的數值範圍上限

    // 線段樹：單點更新 (將 idx 位置的頻率加上 val)
    void update(int node, int l, int r, int idx, long long val) {
        if(l == r) {
            tree[node] += val;
            return;
        }

        int mid = l + (r - l) / 2;
        // 使用 1-indexed，左子節點為 node<<1，右子節點為 (node<<1)|1
        if(idx <= mid) {
            update(node << 1, l, mid, idx, val);
        } else {
            update((node << 1) | 1, mid + 1, r, idx, val);
        }

        // Push Up: 更新父節點
        tree[node] = tree[node << 1] + tree[(node << 1) | 1];
    }

    // 線段樹：區間查詢 (查詢區間 [L, R] 內的頻率總和)
    long long query(int node, int l, int r, int L, int R) {
        if(r < L || R < l) return 0; // 完全不交集

        if(L <= l && r <= R) {       // 完全包含
            return tree[node];
        }

        int mid = l + (r - l) / 2;
        long long left = query(node << 1, l, mid, L, R);
        long long right = query((node << 1) | 1, mid + 1, r, L, R);

        return left + right;
    }
    
    vector<int> countSmaller(vector<int>& nums) {
        // 數值範圍平移：最低為 -10000，加上 10001 確保從 1 開始
        N = *max_element(nums.begin(), nums.end()) + 10001;
        tree = vector<long long>(N * 4, 0);

        int n = nums.size();
        vector<int> rt;
        
        // 逆向掃描：確保每次查詢時，線段樹內只有當前元素右側的數字
        for(int i = n - 1; i >= 0; --i) {
            // 平移量 10001
            int shifted_val = nums[i] + 10001;
            
            // 查詢 [1, 嚴格小於當前數值] 的總數量
            rt.push_back(query(1, 1, N, 1, shifted_val - 1));
            
            // 將當前數字加入線段樹 (頻率 +1)
            update(1, 1, N, shifted_val, 1);
        }

        // 因為是從後往前加入結果的，最後需反轉
        reverse(rt.begin(), rt.end());
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