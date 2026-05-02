---
title: "LeetCode 493. Reverse Pairs"
date: 2026-04-30T20:15:00+08:00
lastmod: 2026-04-30T20:15:00+08:00
difficulty: 2000
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "segment-tree", "divide-and-conquer", "coordinate-compression"]
keywords: ["LC493", "Hard", "Reverse Pairs", "Segment Tree", "離散化", "線段樹"]
description: "LeetCode 第 493 題：Reverse Pairs。難度評分：Hard。探討如何利用「雙值同池離散化 (Coordinate Compression)」結合「線段樹 (Segment Tree)」與「逆向掃描」，以極其優雅且高效的方式解決進階逆序對問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 493](https://leetcode.com/problems/reverse-pairs/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Segment Tree` $\cdot$ `Binary Indexed Tree` $\cdot$ `Divide and Conquer` $\cdot$ `Merge Sort`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
如果存在一對索引 $(i, j)$ 滿足以下兩個條件，我們稱之為一個 **重要翻轉對 (Important Reverse Pair)**：
1. $0 \le i < j < nums.length$
2. $nums[i] > 2 \times nums[j]$

請你回傳給定陣列中的重要翻轉對總數。

### 限制條件
- $1 \le nums.length \le 5 \times 10^4$
- $-2^{31} \le nums[i] \le 2^{31} - 1$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-30：初次提交 (雙值離散化 + 線段樹逆向掃描)](#2026-04-30-初次提交)

---

## 💡 2026-04-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題是經典「逆序對 (Inversion Count)」的進階版。由於判斷條件從 $nums[i] > nums[j]$ 變成了 $nums[i] > 2 \times nums[j]$，傳統的 BIT/線段樹做法會遇到一個巨大的挑戰：**數值範圍極大 ($-2^{31}$ 到 $2^{31}-1$)，且查詢的數值與更新的數值並不相同！**

**核心破局點：雙值同池離散化 (Coordinate Compression)**
為了解決值域過大與座標對齊的問題，我們使用離散化。最精妙的設計在於：我們不僅把原陣列的 $nums[i]$ 放入離散化陣列中，**連同 $2 \times nums[i]$ 也一起放入同一個陣列進行排序與去重！**
這樣做的好處是，我們建立出了一個統一的「排名座標系」。無論是查詢 $nums[i]$ 的排名，還是插入 $2 \times nums[j]$ 的排名，都能在同一個維度下進行，完全省去了後續用二分搜找座標的麻煩。

### 🛠️ 解題思路 (Approach)
1. **離散化 (Coordinate Compression)**：
   - 走訪陣列，將所有未出現過的 $nums[i]$ 與 $2 \times nums[i]$ 收集到 `sorted` 陣列中 (注意使用 `long long` 避免 $2 \times$ 運算時溢位)。
   - 對 `sorted` 進行排序，並利用 Hash Map `mp` 為每一個數值賦予一個從 $1$ 開始的排名 (Rank)。
2. **線段樹建構 (Segment Tree)**：
   - 線段樹的容量 $N$ 為離散化後不重複元素的總數。
   - 建立一個大小為 $4N+5$ 的線段樹，用於記錄各個「排名」出現的頻率。
3. **逆向掃描 (Reverse Scan)**：
   - 從陣列最右側 $i = n-1$ 一路往左掃描到 $0$。
   - **查詢 (Query)**：因為是逆向掃描，線段樹中已經存在的元素必定滿足 $j > i$。我們向線段樹查詢區間 `[1, mp[nums[i]] - 1]` 的頻率總和，這代表著目前右側有多少元素的兩倍是嚴格小於 $nums[i]$ 的。將結果累加至 `rt`。
   - **更新 (Update)**：查詢完畢後，將當前元素的兩倍 $2 \times nums[i]$ 對應的排名 `mp[2 * nums[i]]` 加入線段樹中 (單點頻率 $+1$)。
4. **回傳結果**：回傳累加總和 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。
  - 離散化：收集與排序最多 $2n$ 個元素，耗時 $\mathcal{O}(N \log N)$。
  - 線段樹操作：掃描陣列 $n$ 次，每次執行單點更新與區間查詢，單次 $\mathcal{O}(\log N)$，總計 $\mathcal{O}(N \log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。Hash Map、離散化陣列與線段樹最大皆只需 $\mathcal{O}(N)$ 的空間 ($N \le 10^5$)。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<long long> tree;
    int N;

    // 線段樹單點更新：將數值對應的排名 idx 的頻率加上 val
    void update(int node, int l, int r, int idx, long long val) {
        if(l == r) {
            tree[node] += val;
            return;
        }

        int mid = l + (r - l) / 2;
        if(idx <= mid) update(node << 1, l, mid, idx, val);
        else update((node << 1) | 1, mid + 1, r, idx, val);

        tree[node] = tree[node << 1] + tree[(node << 1) | 1];
    }

    // 線段樹區間查詢：取得區間 [L, R] 內的頻率總和
    long long query(int node, int l, int r, int L, int R) {
        if(r < L || R < l) return 0;
        if(L <= l && r <= R) return tree[node];

        int mid = l + (r - l) / 2;
        long long left = query(node << 1, l, mid, L, R);
        long long right = query((node << 1) | 1, mid + 1, r, L, R);
        
        return left + right;
    }
    
    int reversePairs(vector<int>& nums) {
        int n = nums.size();
        
        // 1. 雙值同池離散化 (Coordinate Compression)
        // 將 nums[i] 與 2 * nums[i] 一起放入池中去重排序
        unordered_set<long long> used;
        vector<long long> sorted;
        
        for(int i = 0; i < n; ++i) {
            if(used.find(nums[i]) == used.end()) {
                used.insert(nums[i]);
                sorted.push_back(nums[i]);
            }
            if(used.find(2LL * nums[i]) == used.end()) {
                used.insert(2LL * nums[i]);
                sorted.push_back(2LL * nums[i]);
            }
        }
        sort(sorted.begin(), sorted.end());
        
        // 將數值映射為 1-based index (排名)
        unordered_map<long long, int> mp;
        N = sorted.size();
        tree = vector<long long>(4 * N + 5, 0);
        for(int i = 0; i < N; ++i) {
            mp[sorted[i]] = i + 1;
        }

        int rt = 0;
        
        // 2. 逆向掃描維護線段樹
        for(int i = n - 1; i >= 0; --i) {
            int idx = mp[nums[i]];

            // 查詢右側 (已加入樹中) 有多少元素的兩倍嚴格小於當前 nums[i]
            // 即查詢排名在 [1, idx-1] 之間的總數量
            rt += query(1, 1, N, 1, idx - 1);
            
            // 將 2 * nums[i] 加入線段樹，供左側元素比較
            int idx2 = mp[2LL * nums[i]];
            update(1, 1, N, idx2, 1);
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