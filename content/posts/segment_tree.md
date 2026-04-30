---
title: "演算法模板：線段樹 (Segment Tree)"
date: 2026-04-30T14:30:00+08:00
lastmod: 2026-04-30T14:30:00+08:00
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: true
# --- 數學公式設定 ---
math: true
weight: 3
categories: ["algorithm", "template"]
tags: ["segment-tree", "data-structure", "range-query", "cpp", "template"]
keywords: ["Segment Tree", "線段樹", "Lazy Propagation", "懶惰標記", "C++ Template", "Range Query"]
description: "競技程式與進階演算法必備模板：線段樹 (Segment Tree)。收錄標準版 (單點修改 / 區間查詢) 與進階版 (區間修改 / 懶惰標記 Lazy Propagation) 的高階 C++ 封裝實作與複雜度分析。"
---

## 📊 資料結構概述

> **線段樹 (Segment Tree)** 是一種基於分治思想的二元樹資料結構。
> 它主要用於解決**區間查詢 (Range Query)** 與**區間/單點修改 (Range/Point Update)** 的問題。常見的應用場景包含：區間總和 (Sum)、區間極值 (Max/Min)、區間最大公因數 (GCD) 等。

### 效能分析
- **建樹 (Build)**：$\mathcal{O}(N)$
- **單點/區間修改 (Update)**：$\mathcal{O}(\log N)$
- **區間查詢 (Query)**：$\mathcal{O}(\log N)$
- **空間複雜度**：$\mathcal{O}(4N)$（標準陣列實作需開 $4$ 倍空間以防溢位）

---

## 🛠️ 模板一：基礎線段樹 (單點修改 + 區間查詢)

此模板適用於：**單點更新值**，並查詢**任意區間的總和/極值**。
這裡以「區間總和 (Range Sum)」為例。實作上採用 `class` 封裝，避免全域變數污染，便於在 LeetCode 或大型專案中直接呼叫。

### C++ 實作碼
```cpp
#include <vector>
using namespace std;

class SegmentTree {
private:
    vector<long long> tree;
    int n;

    // 向上更新父節點 (Push Up)
    void pushUp(int node) {
        tree[node] = tree[node * 2 + 1] + tree[node * 2 + 2];
    }

    // 遞迴建樹
    void build(int node, int start, int end, const vector<int>& nums) {
        if (start == end) {
            tree[node] = nums[start];
            return;
        }
        int mid = start + (end - start) / 2;
        int left_node = node * 2 + 1;
        int right_node = node * 2 + 2;
        
        build(left_node, start, mid, nums);
        build(right_node, mid + 1, end, nums);
        pushUp(node);
    }

    // 遞迴單點修改 (將 index 的值加上 val)
    void updatePoint(int node, int start, int end, int idx, long long val) {
        if (start == end) {
            tree[node] += val; // 若為取代值，可改為 tree[node] = val;
            return;
        }
        int mid = start + (end - start) / 2;
        int left_node = node * 2 + 1;
        int right_node = node * 2 + 2;
        
        if (idx <= mid) {
            updatePoint(left_node, start, mid, idx, val);
        } else {
            updatePoint(right_node, mid + 1, end, idx, val);
        }
        pushUp(node);
    }

    // 遞迴區間查詢
    long long queryRange(int node, int start, int end, int L, int R) {
        // 1. 完全包含於查詢區間
        if (L <= start && end <= R) {
            return tree[node];
        }
        // 2. 完全不交集 (視題目要求回傳，求和回傳 0，求極值回傳極小/極大值)
        if (start > R || end < L) {
            return 0; 
        }
        // 3. 部分交集，向左右子樹查詢
        int mid = start + (end - start) / 2;
        long long sum = 0;
        if (L <= mid) sum += queryRange(node * 2 + 1, start, mid, L, R);
        if (R > mid)  sum += queryRange(node * 2 + 2, mid + 1, end, L, R);
        return sum;
    }

public:
    // 建構子
    SegmentTree(const vector<int>& nums) {
        n = nums.size();
        if (n > 0) {
            tree.assign(4 * n, 0);
            build(0, 0, n - 1, nums);
        }
    }

    // 提供給外部呼叫的 API
    void update(int idx, long long val) {
        updatePoint(0, 0, n - 1, idx, val);
    }

    long long query(int L, int R) {
        return queryRange(0, 0, n - 1, L, R);
    }
};
```

---

## 🚀 模板二：進階線段樹 (區間修改 + 懶惰標記 Lazy Propagation)

當題目要求「對整個區間 $[L, R]$ 的所有元素同時加上 $X$」，若使用單點修改會退化成 $\mathcal{O}(N \log N)$。
此時必須引入 **懶惰標記 (Lazy Tag)**，將修改操作「延遲」到真正需要查詢該子樹時才向下傳遞 (`pushDown`)，從而確保區間修改的複雜度維持在 $\mathcal{O}(\log N)$。

### C++ 實作碼
```cpp
#include <vector>
using namespace std;

class LazySegmentTree {
private:
    vector<long long> tree;
    vector<long long> lazy;
    int n;

    void pushUp(int node) {
        tree[node] = tree[node * 2 + 1] + tree[node * 2 + 2];
    }

    // 將懶惰標記下放給左右子節點 (Push Down)
    void pushDown(int node, int start, int end) {
        if (lazy[node] != 0) {
            int mid = start + (end - start) / 2;
            int left_node = node * 2 + 1;
            int right_node = node * 2 + 2;

            // 更新子節點的值 (加上 lazy 值 * 該區間長度)
            tree[left_node] += lazy[node] * (mid - start + 1);
            tree[right_node] += lazy[node] * (end - mid);

            // 將標記傳遞給子節點
            lazy[left_node] += lazy[node];
            lazy[right_node] += lazy[node];

            // 清除當前節點的標記
            lazy[node] = 0;
        }
    }

    void build(int node, int start, int end, const vector<int>& nums) {
        if (start == end) {
            tree[node] = nums[start];
            return;
        }
        int mid = start + (end - start) / 2;
        build(node * 2 + 1, start, mid, nums);
        build(node * 2 + 2, mid + 1, end, nums);
        pushUp(node);
    }

    // 遞迴區間修改 (區間 [L, R] 加上 val)
    void updateRange(int node, int start, int end, int L, int R, long long val) {
        if (L <= start && end <= R) {
            tree[node] += val * (end - start + 1);
            lazy[node] += val;
            return;
        }
        
        pushDown(node, start, end); // 往下走之前，必須先下放之前的標記
        
        int mid = start + (end - start) / 2;
        if (L <= mid) updateRange(node * 2 + 1, start, mid, L, R, val);
        if (R > mid)  updateRange(node * 2 + 2, mid + 1, end, L, R, val);
        
        pushUp(node); // 子樹更新完後，更新自己
    }

    // 遞迴區間查詢
    long long queryRange(int node, int start, int end, int L, int R) {
        if (L <= start && end <= R) {
            return tree[node];
        }
        
        pushDown(node, start, end); // 查詢時如果遇到未結算的標記，也必須下放
        
        int mid = start + (end - start) / 2;
        long long sum = 0;
        if (L <= mid) sum += queryRange(node * 2 + 1, start, mid, L, R);
        if (R > mid)  sum += queryRange(node * 2 + 2, mid + 1, end, L, R);
        
        return sum;
    }

public:
    LazySegmentTree(const vector<int>& nums) {
        n = nums.size();
        if (n > 0) {
            tree.assign(4 * n, 0);
            lazy.assign(4 * n, 0);
            build(0, 0, n - 1, nums);
        }
    }

    void update(int L, int R, long long val) {
        updateRange(0, 0, n - 1, L, R, val);
    }

    long long query(int L, int R) {
        return queryRange(0, 0, n - 1, L, R);
    }
};
```

---

## 💡 實戰避坑指南 (Tips & Tricks)

1. **空間複雜度與 `4N`**：
   滿二元樹的葉子節點有 $N$ 個時，整棵樹的節點數大約是 $2N-1$。但由於線段樹的底層通常是不完美的二元樹，為了確保陣列索引 `node * 2 + 2` 不會越界，實務上**一律開 $4N$ 的空間**是最安全的作法。
2. **0-indexed vs 1-indexed**：
   本模板採用 `0-indexed` (根節點為 $0$，子節點為 $2i+1$ 與 $2i+2$)。若採用 `1-indexed` (根節點為 $1$)，左右子節點的位移運算可以寫成極致優化的位元運算 `node << 1` 與 `node << 1 | 1`，這在追求極限常數時間的 CP 競賽中非常常見。
3. **資料型態防溢位**：
   區間總和的題目極易發生整數溢位 (Integer Overflow)，因此 `tree` 和 `lazy` 陣列內部強烈建議直接宣告為 `long long`。

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