---
title: "LeetCode 1674. Minimum Moves to Make Array Complementary"
date: 2026-05-13T22:00:00+08:00
lastmod: 2026-05-13T22:00:00+08:00
difficulty: 2333
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "difference-array", "segment-tree", "greedy"]
keywords: ["LC1674", "Medium", "2333", "Complementary Array", "差分陣列", "線段樹"]
description: "LeetCode 第 1674 題：Minimum Moves to Make Array Complementary。難度評分：2333。探討如何將配對和的變動成本轉化為數軸上的區間覆蓋問題，並對比線段樹與差分陣列的實作差異。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1674](https://leetcode.com/problems/minimum-moves-to-make-array-complementary/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Medium (2333)</span>  
> **核心主題**：`Array` $\cdot$ `Difference Array` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為偶數的整數陣列 `nums`，以及一個整數 `limit`。
在一次操作中，你可以將 `nums` 中的任何一個元素替換為 $1$ 到 `limit` 之間的任意整數。
若對於所有 $0 \le i < n/2$，都有 $nums[i] + nums[n-1-i]$ 等於同一個常數 $S$，則稱該陣列是「互補的」。
請回傳使陣列互補所需的 **最小操作次數**。

### 限制條件
- $n = nums.length$
- $2 \le n \le 10^5$ (n 是偶數)
- $1 \le nums[i] \le limit \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-13：初次提交 (線段樹區間更新與差分陣列最佳化)](#2026-05-13-初次提交)

---

## 💡 2026-05-13 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的本質是：**我們要找一個目標和 $S \in [2, 2 \cdot \text{limit}]$，使得所有數對變換到 $S$ 的總代價最小。**

假設一對數字為 $(a, b)$，且令 $a \le b$。要讓 $a+b = S$，所需的步數如下分類：
1. **0 步**：當 $S = a + b$ 時。
2. **1 步**：
   - 最小可能和：將 $b$ 改為 $1$，和變為 $a + 1$。
   - 最大可能和：將 $a$ 改為 $limit$，和變為 $b + limit$。
   - 因此，當 $S \in [a + 1, b + limit]$ 且 $S \neq a + b$ 時，只需 1 步。
3. **2 步**：
   - 當 $S$ 極小時：$S \in [2, a]$。
   - 當 $S$ 極大時：$S \in [b + limit + 1, 2 \cdot limit]$。
   - 這兩段區間必須改動兩個數字才能達成。

### 🛠️ 解題思路 (Approach)

#### **方法一：線段樹 (Segment Tree)**
線段樹可以直觀地對上述五個區間進行 `upd(L, R, val)` 操作。
- 優點：邏輯直觀，適合處理各類區間加值與動態查詢。
- 缺點：常數較大，空間消耗高（$4N$），在此題離線場景下略顯沉重。

#### **方法二：差分陣列 (Difference Array)**
這是本題的最優解。我們在數軸 $[2, 2 \cdot limit]$ 上標註步數的「變化點」：
- 預設每對數字都需要 2 步：`diff[2] += 2`。
- 進入 1 步區間：`diff[a + 1] -= 1` (2 步變 1 步)。
- 進入 0 步點：`diff[a + b] -= 1` (1 步變 0 步)。
- 離開 0 步點：`diff[a + b + 1] += 1` (0 步變 1 步)。
- 離開 1 步區間：`diff[b + limit + 1] += 1` (1 步變 2 步)。

最後對差分陣列求前綴和，最小值即為答案。

---

### 💻 程式碼實作

#### **Approach 1: 線段樹 (區間修改)**
```cpp
class Solution {
public:
    vector<int> tree, lazy;
    int N;
    void pushUp(int node) { tree[node] = min(tree[node<<1], tree[node<<1|1]); }
    void pushDown(int node) {
        if(lazy[node] != 0) {
            int lz = lazy[node];
            tree[node<<1] += lz; lazy[node<<1] += lz;
            tree[node<<1|1] += lz; lazy[node<<1|1] += lz;
            lazy[node] = 0;
        }
    }
    void upd(int node, int l, int r, int L, int R, int val) {
        if(L > R) return;
        if(L <= l && r <= R) {
            tree[node] += val; lazy[node] += val;
            return;
        }   
        pushDown(node);
        int mid = l + (r-l)/2;
        if(L <= mid) upd(node<<1, l, mid, L, R, val);
        if(R > mid) upd(node<<1|1, mid+1, r, L, R, val);
        pushUp(node);
    }

    int minMoves(vector<int>& nums, int limit) {
        N = 2 * limit + 5;
        tree.assign(4 * N, 0); lazy.assign(4 * N, 0);
        int n = nums.size();
        for(int i = 0; i < n / 2; ++i) {
            int a = min(nums[i], nums[n-1-i]), b = max(nums[i], nums[n-1-i]);
            upd(1, 1, N, a + b + 1, b + limit, 1);
            upd(1, 1, N, a + 1, a + b - 1, 1);
            upd(1, 1, N, b + limit + 1, 2 * limit, 2);
            upd(1, 1, N, 2, a, 2);
            // a+b 位置剛好為 0 步，因為 tree 初始為 0，此處不需 upd 額外值
        }
        return qry(1, 1, N, 2, 2 * limit);
    }
    
    int qry(int node, int l, int r, int L, int R) {
        if(L <= l && r <= R) return tree[node];
        pushDown(node);
        int mid = l + (r - l) / 2, rt = 2e9;
        if(L <= mid) rt = min(rt, qry(node << 1, l, mid, L, R));
        if(R > mid)  rt = min(rt, qry(node << 1 | 1, mid + 1, r, L, R));
        return rt;
    }
};
```

#### **Approach 2: 差分陣列 (最優解)**
```cpp
class Solution {
public:
    int minMoves(vector<int>& nums, int limit) {
        // 差分陣列範圍需要涵蓋 2 * limit
        vector<int> diff(2 * limit + 2, 0);
        int n = nums.size();
        
        for(int i = 0; i < n / 2; ++i) {
            int a = min(nums[i], nums[n-1-i]);
            int b = max(nums[i], nums[n-1-i]);

            // 1. 預設所有 S 都要 2 步
            diff[2] += 2;
            // 2. [a+1, b+limit] 區間只需 1 步，所以減去 1
            diff[a + 1] -= 1;
            // 3. 剛好等於 a+b 的點只需 0 步，再減去 1
            diff[a + b] -= 1;
            // 4. 過了 a+b 點後回到 1 步區間，加回 1
            diff[a + b + 1] += 1;
            // 5. 過了 b+limit 後回到 2 步區間，加回 1
            diff[b + limit + 1] += 1;
        }

        int min_moves = n;
        int current_moves = 0;
        // 遍歷可能的目標和 S，從 2 到 2*limit
        for(int i = 2; i <= 2 * limit; ++i) {
            current_moves += diff[i];
            min_moves = min(min_moves, current_moves);
        }
        return min_moves;
    }
};
```

---

### 📊 複雜度總結

| 方法 | 時間複雜度 | 空間複雜度 | 特點 |
| :--- | :--- | :--- | :--- |
| **線段樹** | $O(N \log (\text{limit}))$ | $O(\text{limit})$ | 適合動態查詢，常數較大 |
| **差分陣列** | $O(N + \text{limit})$ | $O(\text{limit})$ | 效率極高，代碼簡潔，推薦面試使用 |

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