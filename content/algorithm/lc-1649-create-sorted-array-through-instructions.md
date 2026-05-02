---
title: "LeetCode 1649. Create Sorted Array through Instructions"
date: 2026-02-19T19:40:00+08:00
lastmod: 2026-04-30T17:05:00+08:00
difficulty: 2207
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Binary Indexed Tree (Fenwick Tree)", "Segment Tree", "Math"]
keywords: ["LC1649", "2207", "Hard", "BIT", "Fenwick Tree", "線段樹"]
description: "LeetCode 第 1649 題：Create Sorted Array through Instructions。難度評分：2207。探討如何利用 Binary Indexed Tree (BIT) 與 Segment Tree (線段樹) 的值域特徵來動態計算嚴格大於與嚴格小於的元素數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1649](https://leetcode.com/problems/create-sorted-array-through-instructions/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(2207)</span>   
> **核心主題**：`Binary Indexed Tree` $\cdot$ `Segment Tree` $\cdot$ `Divide and Conquer`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `instructions`，你需要根據這些指示將元素依序插入一個初始為空的陣列 `nums` 中。
每次插入一個元素 `instructions[i]` 時，所需花費的 **代價 (Cost)** 為以下兩者的 **最小值**：
1. `nums` 中 **嚴格小於** `instructions[i]` 的元素數量。
2. `nums` 中 **嚴格大於** `instructions[i]` 的元素數量。

請回傳將所有元素插入 `nums` 後的總代價。因為答案可能很大，請將結果對 $10^9 + 7$ 取餘數。

### 範例
- **Input**: `instructions = [1,5,6,2]` → **Output**: `1`
- **Explanation**: 
  - 插入 1，代價為 `min(0, 0) = 0`，`nums = [1]`。
  - 插入 5，代價為 `min(1, 0) = 0`，`nums = [1,5]`。
  - 插入 6，代價為 `min(2, 0) = 0`，`nums = [1,5,6]`。
  - 插入 2，代價為 `min(1, 2) = 1`，`nums = [1,2,5,6]`。
  總代價 = $0 + 0 + 0 + 1 = 1$。

### 限制條件
- $1 \le instructions.length \le 10^5$
- $1 \le instructions[i] \le 10^5$

</details>

---

## 📝 歷次打卡與更新
- [2026-04-30：更新提交 (線段樹 Segment Tree 與根節點優化)](#2026-04-30-更新提交-線段樹)
- [2026-02-19：初次提交 (運用 BIT 值域特徵解題)](#2026-02-19-初次提交-bit)

---

## 💡 2026-04-30 更新提交 (線段樹)

### 🎯 演算法分析 (Algorithm Analysis)
除了 BIT，這題也非常適合使用**線段樹 (Segment Tree)** 作為動態頻率陣列。
線段樹的底層同樣是維護各個數值出現的區間總和。這次實作中有一個非常漂亮且極致的效能優化細節：
- 嚴格小於 $cur$ 的數量：直接查詢區間 `[1, cur - 1]`。
- 嚴格大於 $cur$ 的數量：傳統做法是查詢區間 `[cur + 1, N]`。但是，線段樹的**根節點 `tree[1]` 隨時都儲存著整棵樹的總元素數量**！所以我們只需要用 `tree[1]` 扣除掉 `[1, cur]` 的數量，就能在少跑一次遞迴查詢的情況下，精準算出嚴格大於的數量，大幅降低了常數時間的開銷。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \log N)$，其中 $M$ 是 `instructions` 的長度，$N$ 是陣列中的最大值。每次單點更新與區間查詢的極限深度皆為 $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(N)$，線段樹固定配置 $4N$ 的陣列空間以防溢位。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    vector<long long> tree;
    int N;

    // 單點更新：將數值 idx 的頻率加上 val
    void update(int node, int l, int r, int idx, long long val) {
        if(l == r) {
            tree[node] += val;
            return;
        }

        int mid = l + (r - l) / 2;
        if(idx <= mid) update(node << 1, l, mid, idx, val);
        else update((node << 1) | 1, mid + 1, r, idx, val);

        // Push Up
        tree[node] = tree[node << 1] + tree[(node << 1) | 1];
    }

    // 區間查詢：取得區間 [L, R] 的頻率總和
    long long query(int node, int l, int r, int L, int R) {
        if(L > r || R < l) return 0;
        if(L <= l && r <= R) return tree[node];

        int mid = l + (r - l) / 2;
        long long left = query(node << 1, l, mid, L, R);
        long long right = query((node << 1) | 1, mid + 1, r, L, R);

        return left + right;
    }

    int createSortedArray(vector<int>& instructions) {
        N = *max_element(instructions.begin(), instructions.end());
        tree = vector<long long>(4 * N + 5, 0);

        long long rt = 0;
        for(int i = 0; i < instructions.size(); ++i) {
            int cur = instructions[i];
            
            // 將當前數字加入線段樹
            update(1, 1, N, cur, 1);

            // query(1, 1, N, 1, cur-1) => 嚴格小於 cur 的數量
            // tree[1] 代表目前插入的總數量
            // tree[1] - query(1, 1, N, 1, cur) => 嚴格大於 cur 的數量
            rt += min(query(1, 1, N, 1, cur - 1), tree[1] - query(1, 1, N, 1, cur));
            rt %= MOD;
        }

        return rt;
    }
};
```

---

## 💡 2026-02-19 初次提交 (BIT)

### 🎯 直覺 (Intuition)
這題的本質是要在動態新增元素的過程中，快速查詢「比 $x$ 小」和「比 $x$ 大」的元素數量。
如果我們將思維轉換一下，**把輸入的數值 $x$ 當作陣列的 Index，並把該數值出現的次數當作 Value**，那麼「比 $x$ 小的元素總數」不就等價於頻率陣列的 **前綴和 (Prefix Sum)** 嗎？既然涉及動態單點更新與區間查詢，**Binary Indexed Tree (BIT)** 絕對是最完美的兵器。

### 🛠️ 解題思路 (Approach)
1. **確定 BIT 的大小**：
   與其盲目開一個大小為 $10^5$ 的陣列，我們可以先用 `max_element` 找出輸入陣列中的最大值 $N$，以此為界限初始化 BIT，節省空間。
2. **區間查詢轉換**：
   對於當前要插入的數字 $x$：
   - 嚴格小於 $x$ 的數量：即查詢 `[1, x-1]` 的頻率總和，可寫成 `getSum(x - 1)`。
   - 嚴格大於 $x$ 的數量：即查詢 `[x+1, N]` 的頻率總和，可寫成 `getSum(N) - getSum(x)`。
3. **計算代價與更新**：
   將上述兩者取 `min` 加總至 `cost`，並對 $10^9 + 7$ 取餘數。隨後，呼叫 `update(x, 1)` 將數字 $x$ 的頻率加 1，繼續處理下一個數字。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M \log N)$，其中 $M$ 是 `instructions` 的長度，$N$ 是 `instructions` 中的最大值（`max_element` 需 $\mathcal{O}(M)$，後續每次更新/查詢需 $\mathcal{O}(\log N)$）。
- **空間複雜度**: $\mathcal{O}(N)$，用於建構長度為 $N+5$ 的 `BIT` 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long MOD = 1e9 + 7;
    vector<int> BIT;
    int n;
    
    int createSortedArray(vector<int>& instructions) {
        // 動態尋找最大值以決定 BIT 陣列大小
        n = *max_element(instructions.begin(), instructions.end());
        BIT = vector<int>(n + 5, 0);
        long long cost = 0;
        
        for(auto x : instructions) {
            // cost += min(嚴格大於 x 的數量, 嚴格小於 x 的數量)
            cost += min(getSum(n) - getSum(x), getSum(x - 1));
            
            // 將數值 x 的出現頻率 +1
            update(x, 1);
            cost %= MOD;
        }

        return cost;
    }
    
private:
    void update(int index, int val) {
        while(index <= n) {
            BIT[index] += val;
            index += (index & -index);
        }
    }
    
    long long getSum(int index) {
        long long sum = 0;
        while(index > 0) {
            sum += BIT[index];
            index -= (index & -index);
        }
        return sum;
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