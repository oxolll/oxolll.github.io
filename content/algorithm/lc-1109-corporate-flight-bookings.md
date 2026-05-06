---
title: "LeetCode 1109. Corporate Flight Bookings"
date: 2026-05-06T14:30:00+08:00
lastmod: 2026-05-06T14:30:00+08:00
difficulty: 1569
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum", "segment-tree", "difference-array"]
keywords: ["LC1109", "Medium", "Corporate Flight Bookings", "線段樹", "差分陣列"]
description: "LeetCode 第 1109 題：Corporate Flight Bookings。難度評分：1569。探討如何解決區間加值問題，並對比「線段樹 (Segment Tree)」與「差分陣列 (Difference Array)」在不同場景下的效能表現。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1109](https://leetcode.com/problems/corporate-flight-bookings/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1569)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum` $\cdot$ `Segment Tree`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
這裡有 `n` 個航班，標號為 `1` 到 `n`。
有一份航班預訂表 `bookings` ，表中第 `i` 條預訂記錄 `bookings[i] = [first_i, last_i, seats_i]` 意味著在從 `first_i` 到 `last_i` （包含 `first_i` 和 `last_i` ）的每個航班上預訂了 `seats_i` 個座位。

請你回傳一個長度為 `n` 的陣列 `answer`，其中 `answer[i]` 是航班 `i+1` 上預訂的座位總數。

### 範例
- **Input**: `bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5`
- **Output**: `[10,55,45,25,25]`
- **Explanation**: 
  航班 1: [1,2,10] -> 10
  航班 2: [1,2,10] + [2,3,20] + [2,5,25] -> 10 + 20 + 25 = 55
  航班 3: [2,3,20] + [2,5,25] -> 20 + 25 = 45
  航班 4: [2,5,25] -> 25
  航班 5: [2,5,25] -> 25

### 限制條件
- $1 \le n \le 2 \times 10^4$
- $1 \le bookings.length \le 2 \times 10^4$
- $bookings[i].length == 3$
- $1 \le first_i \le last_i \le n$
- $1 \le seats_i \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-06：初次提交 (線段樹與差分陣列雙解法對比)](#2026-05-06-初次提交)

---

## 💡 2026-05-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這道題目的本質是「區間加值 (Range Addition)」。我們需要對多個區間 $[L, R]$ 進行加值操作，最後查詢每個點的最終數值。

#### **方法一：線段樹 (Segment Tree) 搭配懶惰標記**
線段樹是處理區間問題的「萬用軍刀」。
- **優點**：支援「在線 (Online)」操作。也就是說，如果你在更新的過程中隨時需要查詢區間和或點數值，線段樹能以 $O(\log N)$ 的速度回應。
- **缺點**：對於這題來說太重了。實作複雜，且常數項較大。

#### **方法二：差分陣列 (Difference Array)**
這是解決「離線區間修改」的最佳武器。

- **原理**：定義差分陣列 $dif[i] = A[i] - A[i-1]$。
  - 對區間 $[L, R]$ 增加 $val$，只需 $dif[L] += val$ 且 $dif[R+1] -= val$。
  - 最後對 $dif$ 求前綴和 (Prefix Sum)，即可還原出原陣列 $A$。
- **優點**：修改操作只需 $O(1)$，最後還原只需 $O(N)$。

---

### 🛠️ 方法一：線段樹解法 (進階實作)
利用懶惰標記 (Lazy Propagation) 維護區間增量。
```cpp
class Solution {
public:
    vector<long long> tree, lazy;
    int N;

    void pushUp(int node) {
        tree[node] = tree[node << 1] + tree[node << 1 | 1];
    }

    void pushDown(int node, int l, int r) {
        if (lazy[node]) {
            int mid = l + (r - l) / 2;
            long long lz = lazy[node];
            tree[node << 1] += lz * (mid - l + 1);
            lazy[node << 1] += lz;
            tree[node << 1 | 1] += lz * (r - mid);
            lazy[node << 1 | 1] += lz;
            lazy[node] = 0;
        }
    }

    void upd(int node, int l, int r, int L, int R, long long val) {
        if (L <= l && r <= R) {
            tree[node] += val * (r - l + 1);
            lazy[node] += val;
            return;
        }
        pushDown(node, l, r);
        int mid = l + (r - l) / 2;
        if (L <= mid) upd(node << 1, l, mid, L, R, val);
        if (R > mid) upd(node << 1 | 1, mid + 1, r, L, R, val);
        pushUp(node);
    }

    long long qry(int node, int l, int r, int L, int R) {
        if (L <= l && r <= R) return tree[node];
        pushDown(node, l, r);
        int mid = l + (r - l) / 2;
        long long res = 0;
        if (L <= mid) res += qry(node << 1, l, mid, L, R);
        if (R > mid) res += qry(node << 1 | 1, mid + 1, r, L, R);
        return res;
    }

    vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
        N = n;
        tree.assign(4 * N + 5, 0);
        lazy.assign(4 * N + 5, 0);
        for (auto& b : bookings) upd(1, 1, N, b[0], b[1], b[2]);
        
        vector<int> rt;
        for (int i = 1; i <= n; ++i) rt.push_back(qry(1, 1, N, i, i));
        return rt;
    }
};
```

---

### 🛠️ 方法二：差分陣列解法 (最優推薦)
利用離線前綴和技巧，將空間與時間效率發揮到極致。

```cpp
class Solution {
public:
    vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
        // 1. 建立差分陣列 (多開空間避免邊界判斷)
        vector<int> dif(n + 2, 0);
        
        // 2. 區間修改轉化為端點修改 (O(1))
        for(auto& booking : bookings) {
            int l = booking[0], r = booking[1];
            int val = booking[2];
            dif[l] += val;
            dif[r + 1] -= val;
        }

        // 3. 利用前綴和還原原陣列 (O(N))
        vector<int> rt;
        int sum = 0;
        for(int i = 1; i <= n; ++i) {
            sum += dif[i];
            rt.push_back(sum);
        }
        return rt;
    }
};
```

---

### 📊 複雜度總結對比

| 特性 | 線段樹 (Approach 1) | 差分陣列 (Approach 2) |
| :--- | :--- | :--- |
| **時間複雜度** | $O((Q + N) \log N)$ | $O(Q + N)$ |
| **空間複雜度** | $O(N)$ (常數約 4~8) | $O(N)$ (常數約 1) |
| **適用場景** | 在線查詢、區間修改 | 離線修改、單次最終查詢 |
| **實作難度** | 高 | 極低 |

*(註：$Q$ 為 `bookings.length`，$N$ 為航班總數)*

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