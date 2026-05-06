---
title: "LeetCode 1622. Fancy Sequence"
date: 2026-05-06T23:20:00+08:00
lastmod: 2026-05-06T23:20:00+08:00
difficulty: 2476
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "segment-tree", "lazy-propagation", "mathematics"]
keywords: ["LC1622", "Hard", "Fancy Sequence", "線段樹", "線性變換", "懶標記合併"]
description: "LeetCode 第 1622 題：Fancy Sequence。難度評分：2476。探討如何利用線段樹維護複雜的區間加法與乘法複合操作，並將其建模為線性變換 (Linear Transformation) 的合併問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1622](https://leetcode.com/problems/fancy-sequence/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2476)</span>  
> **核心主題**：`Segment Tree` $\cdot$ `Lazy Propagation` $\cdot$ `Mathematics` $\cdot$ `Modular Arithmetic`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
請實作一個 `Fancy` 序列，支援以下操作：
1. `append(val)`：將整數 `val` 新增至序列末尾。
2. `addAll(inc)`：將序列中當前所有的數字都加上 `inc`。
3. `multAll(m)`：將序列中當前所有的數字都乘以 `m`。
4. `getIndex(idx)`：取得下標為 `idx` 的值（模 $10^9+7$）。如果下標超出範圍，回傳 `-1`。

### 限制條件
- $1 \le val, inc, m \le 100$
- $0 \le idx \le 10^5$
- 總呼叫次數（含 `append`, `addAll`, `multAll`, `getIndex`）最多為 $10^5$ 次。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-06：初次提交 (區間線性變換線段樹解法)](#2026-05-06-初次提交)

---

## 💡 2026-05-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的關鍵挑戰在於 `addAll`（區間加法）與 `multAll`（區間乘法）同時存在。由於乘法對加法具有分配律，兩者的更新順序會導致不同的結果。這類問題最優雅的建模方式是將操作視為**線性變換 (Linear Transformation)**。

**核心破局點：懶標記合併邏輯**
我們定義一個區間操作為 $f(x) = m \cdot x + c$，其中 $m$ 為乘數，$c$ 為增量。
當前節點已經持有一個懶標記 $(m_{old}, c_{old})$，代表該區間的數值已經歷過 $x \to m_{old} \cdot x + c_{old}$。
現在有一個新的標記 $(m_{new}, c_{new})$ 下放：
$$x' = m_{new} \cdot (m_{old} \cdot x + c_{old}) + c_{new}$$
$$x' = (m_{new} \cdot m_{old}) \cdot x + (m_{new} \cdot c_{old} + c_{new})$$

透過這個推導，我們得到新的合併懶標記：
- **新乘數**：$M = (m_{new} \cdot m_{old}) \pmod{MOD}$
- **新增量**：$C = (m_{new} \cdot c_{old} + c_{new}) \pmod{MOD}$

### 🛠️ 解題思路 (Approach)
1. **線段樹架構**：
   - 由於 `append` 是向後增加，我們預開一個足夠大的線段樹（例如 $10^5$）。
   - `lazy` 陣列儲存一個 `pair<long long, long long>`，分別代表加法增量 (`first`) 與乘法倍數 (`second`)。
2. **初始化**：
   - 懶標記的加法初始值應為 `0`，乘法初始值應為 `1`。
3. **區間更新 (`upd`)**：
   - `addAll(inc)` 相當於執行線性變換 $(1, inc)$。
   - `multAll(m)` 相當於執行線性變換 $(m, 0)$。
   - `append(val)` 相當於在當前最後一個位置 `cur` 執行單點更新，將初值設為 `val`。
4. **模運算安全**：
   所有中間運算與最終結果都必須對 $10^9+7$ 取模。

### 📊 複雜度分析
- **時間複雜度**: 每項操作（`append`, `addAll`, `multAll`, `getIndex`）皆為 $\mathcal{O}(\log N)$，其中 $N = 10^5$。總時間複雜度為 $\mathcal{O}(Q \log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。主要是線段樹與懶標記陣列的開銷，約需 $4N$ 個節點空間。

### 💻 程式碼實作 (C++)
```cpp
class Fancy {
public:
    int N;
    vector<long long> tree;
    // lazy.first: 增量 (add), lazy.second: 乘數 (mul)
    vector<pair<long long, long long>> lazy;
    const long long MOD = 1e9 + 7;
    int cur = 1;

    void pushUp(int node) {
        tree[node] = (tree[node << 1] + tree[node << 1 | 1]) % MOD;
    }

    void pushDown(int node, int l, int r) {
        // 如果標記是初始狀態 (0, 1)，則跳過
        if (lazy[node].first == 0 && lazy[node].second == 1) return;

        long long m = lazy[node].second;
        long long inc = lazy[node].first;
        int mid = l + (r - l) / 2;

        // 更新左子樹的值與懶標記
        tree[node << 1] = ((tree[node << 1] * m) % MOD + inc * (mid - l + 1)) % MOD;
        lazy[node << 1].first = ((lazy[node << 1].first * m) % MOD + inc) % MOD;
        lazy[node << 1].second = (lazy[node << 1].second * m) % MOD;

        // 更新右子樹的值與懶標記
        tree[node << 1 | 1] = ((tree[node << 1 | 1] * m) % MOD + inc * (r - mid)) % MOD;
        lazy[node << 1 | 1].first = ((lazy[node << 1 | 1].first * m) % MOD + inc) % MOD;
        lazy[node << 1 | 1].second = (lazy[node << 1 | 1].second * m) % MOD;

        // 重置當前節點標記
        lazy[node].first = 0;
        lazy[node].second = 1;
    }

    void upd(int node, int l, int r, int L, int R, long long m, long long inc) {
        if (L <= l && r <= R) {
            // 應用線性變換: x = x * m + inc
            tree[node] = ((tree[node] * m) % MOD + inc * (r - l + 1)) % MOD;
            lazy[node].first = (lazy[node].first * m + inc) % MOD;
            lazy[node].second = (lazy[node].second * m) % MOD;
            return;
        }

        pushDown(node, l, r);
        int mid = l + (r - l) / 2;
        if (L <= mid) upd(node << 1, l, mid, L, R, m, inc);
        if (R > mid)  upd(node << 1 | 1, mid + 1, r, L, R, m, inc);
        pushUp(node);
    }

    long long qry(int node, int l, int r, int L, int R) {
        if (L <= l && r <= R) return tree[node];

        pushDown(node, l, r);
        int mid = l + (r - l) / 2;
        long long res = 0;
        if (L <= mid) res = (res + qry(node << 1, l, mid, L, R)) % MOD;
        if (R > mid)  res = (res + qry(node << 1 | 1, mid + 1, r, L, R)) % MOD;

        return res % MOD;
    }

    Fancy() {
        N = 1e5;
        tree.assign(4 * N + 5, 0);
        lazy.assign(4 * N + 5, {0, 1});
    }
    
    void append(int val) {
        // 在當前位置 cur 插入數值，初始線性變換為 (1, val)
        upd(1, 1, N, cur, cur, 1, val);
        ++cur;
    }
    
    void addAll(int inc) {
        if (cur > 1) upd(1, 1, N, 1, cur - 1, 1, inc);
    }
    
    void multAll(int m) {
        if (cur > 1) upd(1, 1, N, 1, cur - 1, m, 0);
    }
    
    int getIndex(int idx) {
        // 轉為 1-indexed 查詢
        if (idx + 1 >= cur) return -1;
        return qry(1, 1, N, idx + 1, idx + 1) % MOD;
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