---
title: "LeetCode 2569. Handling Sum Queries After Update"
date: 2026-05-06T19:15:00+08:00
lastmod: 2026-05-06T19:15:00+08:00
difficulty: 2397
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "segment-tree", "lazy-propagation", "bit-manipulation"]
keywords: ["LC2569", "Hard", "Handling Sum Queries After Update", "線段樹", "區間翻轉"]
description: "LeetCode 第 2569 題：Handling Sum Queries After Update。難度評分：2397。探討如何利用線段樹搭配懶惰標記維護 0/1 序列的區間翻轉操作，並結合動態維護全域和來處理高頻查詢。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2569](https://leetcode.com/problems/handling-sum-queries-after-update/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2397)</span>  
> **核心主題**：`Segment Tree` $\cdot$ `Lazy Propagation` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個下標從 $0$ 開始的陣列 `nums1` 和 `nums2`，以及一個二維陣列 `queries`。
針對每個查詢，根據其類型執行以下操作：
1. **Type 1** (`queries[i] = [1, l, r]`)：將 `nums1` 中從下標 $l$ 到 $r$（包含端點）的所有位元進行**翻轉 (Flip)**。即 $0 \to 1$, $1 \to 0$。
2. **Type 2** (`queries[i] = [2, p, 0]`)：對於所有滿足 $0 \le i < n$ 的下標，$nums2[i] = nums2[i] + nums1[i] \times p$。
3. **Type 3** (`queries[i] = [3, 0, 0]`)：計算並回傳 `nums2` 陣列中所有元素的總和。

### 限制條件
- $1 \le n \le 10^5$
- $1 \le queries.length \le 10^5$
- $0 \le nums1[i] \le 1$
- $0 \le nums2[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-06：初次提交 (區間翻轉線段樹 + 懶惰標記 XOR 優化)](#2026-05-06-初次提交)

---

## 💡 2026-05-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的挑戰在於 `nums1` 的頻繁區間翻轉與 `nums2` 的全域求和。
1. **Type 1 (區間翻轉)**：
   翻轉操作 $0 \leftrightarrow 1$ 對於區間和的影響是確定的。若一個長度為 $L$ 的區間原本有 $count$ 個 $1$，翻轉後 $1$ 的個數會變成 $L - count$。這可以使用**線段樹 (Segment Tree)** 搭配**懶惰標記 (Lazy Propagation)** 來達成 $\mathcal{O}(\log N)$ 修改。
2. **Type 2 (全域累加)**：
   觀察 $nums2$ 的更新公式：$\sum nums2_{new} = \sum (nums2_{old} + nums1_i \times p) = \sum nums2_{old} + p \times (\sum nums1_i)$。
   這意味著我們不需要真的去更新 `nums2` 的每個元素，只需要知道當前 `nums1` 中所有 $1$ 的總數即可。
3. **Type 3 (回傳總和)**：
   直接回傳我們動態維護的 `nums2` 總和。

### 🛠️ 解題思路 (Approach)
1. **建構線段樹**：
   線段樹的節點儲存該區間內 $1$ 的個數。`tree[1]` 始終代表全域 `nums1` 中 $1$ 的總數。
2. **區間翻轉與 XOR 標記**：
   - 翻轉兩次等於沒翻轉，因此懶惰標記 `lazy` 使用 **XOR 1** 來切換狀態。
   - `pushDown` 時，若標記存在，則左右子樹的 $1$ 個數更新為 `區間長度 - 當前 1 個數`。
3. **動態維護全域和 (`sum`)**：
   - 初始化 `sum` 為 `nums2` 的所有元素總和。
   - 執行 Type 2 查詢時：`sum += (long long)tree[1] * p`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + Q \log N)$。
  - `build` 線段樹耗時 $\mathcal{O}(N)$。
  - $Q$ 次查詢，Type 1 區間更新耗時 $\mathcal{O}(\log N)$，其餘操作皆為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N)$。線段樹與懶惰標記陣列約需 $4N$ 空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int N;
    vector<long long> tree; // 儲存區間內 1 的個數
    vector<long long> lazy; // 懶惰標記，使用 XOR 邏輯 (0: 不翻轉, 1: 翻轉)

    // 由下而上更新 1 的個數
    void pushUp(int node) {
        tree[node] = tree[node << 1] + tree[node << 1 | 1];
    }

    // 下放懶惰標記
    void pushDown(int node, int l, int r) {
        if (lazy[node] != 0) {
            int lz = lazy[node];
            int mid = l + (r - l) / 2;
            
            // 左子樹翻轉：新 1 個數 = 長度 - 舊 1 個數
            tree[node << 1] = (long long)(mid - l + 1) - tree[node << 1];
            lazy[node << 1] ^= lz;

            // 右子樹翻轉
            tree[node << 1 | 1] = (long long)(r - mid) - tree[node << 1 | 1];
            lazy[node << 1 | 1] ^= lz;

            lazy[node] = 0; // 清空當前標記
        }
    }

    // 初始化建樹
    void build(int node, int l, int r, vector<int>& nums1) {
        if (l == r) {
            tree[node] = nums1[l - 1];
            return;
        }
        int mid = l + (r - l) / 2;
        build(node << 1, l, mid, nums1);
        build(node << 1 | 1, mid + 1, r, nums1);
        pushUp(node);
    }

    // 區間翻轉更新 (Type 1)
    void upd(int node, int l, int r, int L, int R) {
        if (L <= l && r <= R) {
            // 翻轉：1 的個數變為 區間長度 - 原有 1 的個數
            tree[node] = (long long)(r - l + 1) - tree[node];
            lazy[node] ^= 1;
            return;
        }

        pushDown(node, l, r);
        int mid = l + (r - l) / 2;
        if (L <= mid) upd(node << 1, l, mid, L, R);
        if (R > mid)  upd(node << 1 | 1, mid + 1, r, L, R);
        pushUp(node);
    }

    vector<long long> handleQuery(vector<int>& nums1, vector<int>& nums2, vector<vector<int>>& queries) {
        int n = nums1.size();
        N = n;
        tree.assign(4 * N + 5, 0);
        lazy.assign(4 * N + 5, 0);
        build(1, 1, N, nums1);

        long long current_nums2_sum = 0;
        for (int num : nums2) current_nums2_sum += num;
        
        vector<long long> rt;
        for (auto& q : queries) {
            int type = q[0];
            if (type == 1) {
                // Type 1: nums1 區間翻轉 (轉為 1-indexed)
                upd(1, 1, N, q[1] + 1, q[2] + 1);
            } else if (type == 2) {
                // Type 2: 全域增量 = p * (nums1 中 1 的總數)
                // tree[1] 即為當前 nums1 全域的 1 之總量
                current_nums2_sum += (long long)tree[1] * q[1];
            } else {
                // Type 3: 回傳當前 nums2 總和
                rt.push_back(current_nums2_sum);
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