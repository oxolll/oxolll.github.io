---
title: "演算法模板：線段樹 (Segment Tree)"
date: 2026-05-02T15:00:00+08:00
lastmod: 2026-05-02T15:00:00+08:00
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: true
# --- 數學公式設定 ---
math: true
weight: 4
categories: ["algorithm", "template"]
tags: ["segment-tree", "data-structure", "range-query", "cpp", "template"]
keywords: ["Segment Tree", "線段樹", "Lazy Propagation", "懶惰標記", "C++ Template", "Range Query"]
description: "提供結構高度對稱的線段樹 C++ 模板：收錄基礎版（單點修改）與進階版（區間修改 / 懶惰標記）。透過統一的遞迴邊界邏輯，大幅降低模板記憶負擔。"
---

## 📊 資料結構概述

> **線段樹 (Segment Tree)** 是一種基於分治思想的二元樹資料結構。
> 主要用於解決**區間查詢 (Range Query)** 與**修改 (Update)** 問題。其核心價值在於將原本 $O(N)$ 的區間操作，透過樹狀分割優化至 $O(\log N)$。

### 效能分析
- **建樹 (Build)**：$O(N)$
- **單點/區間修改 (Update)**：$O(\log N)$
- **區間查詢 (Query)**：$O(\log N)$
- **空間複雜度**：$O(4N)$

---

## 🛠️ 模板一：基礎線段樹 (單點修改 + 區間查詢)

此模板採用與區間更新相同的「交集判斷」風格，點更新被視為區間更新 $L = R$ 的特例。

```cpp
class SegmentTree {
private:
    vector<long long> tree;
    int N;

    // 合併子節點資訊
    void pushUp(int node){
        tree[node] = tree[node<<1] + tree[node<<1|1];
    }

    void build(int node, int l, int r, const vector<int>& nums) {
        if(l == r) 
        {
            tree[node] = nums[l-1];
            return;
        }

        int mid = l + (r-l)/2;
        build(node<<1, l, mid, nums);
        build(node<<1|1, mid+1, r, nums);

        pushUp(node);
    }

    // 點更新：為了結構對稱，參數採用 L, R (外部傳入時 L=R=idx)
    void update(int node, int l, int r, int L, int R, long long val){
        if(l == r)
        {
            tree[node] = val; 
            return;
        }

        int mid = l + (r-l)/2;
        if(L <= mid) update(node<<1, l, mid, L, R, val);
        if(R > mid)  update(node<<1|1, mid+1, r, L, R, val);

        pushUp(node);
    }

    long long query(int node, int l, int r, int L, int R){
        if (L <= l && r <= R) return tree[node];

        int mid = l + (r-l)/2;
        long long res = 0;
        if (L <= mid) res += query(node << 1, l, mid, L, R);
        if (R > mid)  res += query(node << 1 | 1, mid + 1, r, L, R);
        return res;
    }

public:
    SegmentTree(const vector<int>& nums){
        N = nums.size();
        if (N > 0) 
        {
            tree.assign(4 * N + 5, 0);
            build(1, 1, N, nums);
        }
    }
    // 外部呼叫：idx 為 1-based
    void update(int idx, long long val){ update(1, 1, N, idx, idx, val); }
    long long query(int L, int R){ return query(1, 1, N, L, R); }
};
```

---

## 🚀 模板二：進階線段樹 (區間修改 + 懶惰標記)

引入 **懶惰標記 (Lazy Tag)**。與基礎版唯一的區別在於：**向下遞迴前執行 `pushDown`，向上回溯前執行 `pushUp`**。

```cpp
class LazySegmentTree {
private:
    vector<long long> tree, lazy;

    void pushDown(int node, int l, int r){
        if(lazy[node] != 0) 
        {
            int mid = l + (r-l)/2;

            // 更新左子樹內容與標記
            tree[node<<1] += lazy[node] * (mid - l + 1);
            lazy[node<<1] += lazy[node];

            // 更新右子樹內容與標記
            tree[node<<1|1] += lazy[node] * (r - mid);
            lazy[node<<1|1] += lazy[node];
            
            lazy[node] = 0; // 清除目前節點標記
        }
    }

    void update(int node, int l, int r, int L, int R, long long val){
        if(L <= l && r <= R) 
        {
            tree[node] += val * (r - l + 1);
            lazy[node] += val;
            return;
        }

        pushDown(node, l, r); 

        int mid = l + (r - l) / 2;
        if(L <= mid) update(node<<1, l, mid, L, R, val);
        if(R > mid)  update(node<<1|1, mid+1, r, L, R, val);

        pushUp(node);
    }

    long long query(int node, int l, int r, int L, int R){
        if(L > R) return 0;
        if(L <= l && r <= R) return tree[node];

        pushDown(node, l, r);

        int mid = l + (r-l)/2;
        long long res = 0;
        if(L <= mid) res += query(node<<1, l, mid, L, R);
        if(R > mid)  res += query(node<<1|1, mid+1, r, L, R);

        return res;
    }

public:
    LazySegmentTree(const vector<int>& nums) {
        N = nums.size();
        if(N > 0) 
        {
            tree.assign(4 * N + 5, 0);
            lazy.assign(4 * N + 5, 0);
            build(1, 1, N, nums);
        }
    }
    // 外部呼叫：L, R 為 1-based 區間
    void update(int L, int R, long long val){ update(1, 1, N, L, R, val); }
    long long query(int L, int R){ return query(1, 1, N, L, R); }
};
```

---

## 💡 實戰避坑指南 (Tips & Tricks)

1. **空間複雜度與 $4N$**：
   線段樹的陣列索引最大可能達到 $4N$。為了保證安全，建議一律開 $4N + 5$ 的空間。
2. **1-indexed 的優勢**：
   本模板採用 `1-indexed` 實作（根節點為 $1$）。
   - 左子節點：`node << 1`
   - 右子節點：`node << 1 | 1`
3. **Lazy Tag 的下傳關鍵**：
   只要遞迴路徑需要「繼續向下」，就必須呼叫 `pushDown`。這保證了子節點在被訪問前，已經獲得了最新的更新資訊。
4. **資料型態與溢位**：
   區間總和問題（Range Sum）在累積過程中極易發生 **Integer Overflow**，請確保 `tree` 與 `lazy` 均使用 `long long`。

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

# 統一板

```cpp
class SegmentTree {
private:
    vector<long long> tree;
    // vector<long long> lazy;
    int N;

    // 合併子節點資訊
    void pushUp(int node){
        tree[node] = tree[node<<1] + tree[node<<1|1];
    }

    void pushDown(int node, int l, int r){
        if(lazy[node] != 0) 
        {
            int mid = l + (r-l)/2;

            // 更新左子樹內容與標記
            tree[node<<1] += lazy[node] * (mid - l + 1);
            lazy[node<<1] += lazy[node];

            // 更新右子樹內容與標記
            tree[node<<1|1] += lazy[node] * (r - mid);
            lazy[node<<1|1] += lazy[node];
            
            lazy[node] = 0; // 清除目前節點標記
        }
    }

    void build(int node, int l, int r, const vector<int>& nums){
        if(l == r) 
        {
            tree[node] = nums[l-1];
            return;
        }

        int mid = l + (r-l)/2;
        build(node<<1, l, mid, nums);
        build(node<<1|1, mid+1, r, nums);

        pushUp(node);
    }

    void update(int node, int l, int r, int L, int R, long long val){
        if(L <= l && r <= R) 
        {
            tree[node] += val * (r - l + 1);
            lazy[node] += val;
            return;
        }

        // pushDown(node, l, r); 

        int mid = l + (r - l) / 2;
        if(L <= mid) update(node<<1, l, mid, L, R, val);
        if(R > mid)  update(node<<1|1, mid+1, r, L, R, val);

        pushUp(node);
    }

    long long query(int node, int l, int r, int L, int R){
        if(L > R) return 0; 
        if(L <= l && r <= R) return tree[node];

        // pushDown(node, l, r);

        int mid = l + (r-l)/2;
        long long res = 0;
        if(L <= mid) res += query(node<<1, l, mid, L, R);
        if(R > mid)  res += query(node<<1|1, mid+1, r, L, R);

        return res;
    }

public:
    SegmentTree(const vector<int>& nums){
        N = nums.size();
        if (N > 0) 
        {
            tree.assign(4 * N + 5, 0);
            // lazy.assign(4 * N + 5, 0);
            build(1, 1, N, nums);
        }
    }
    // 外部呼叫：idx 為 1-based
    void update(int idx, long long val){ update(1, 1, N, idx, idx, val); }
    long long query(int L, int R){ return query(1, 1, N, L, R); }
};
```