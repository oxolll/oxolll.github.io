---
title: "LeetCode 3161. Block Placement Queries"
date: 2026-05-30T21:30:00+08:00
lastmod: 2026-05-30T22:15:00+08:00
difficulty: 2513
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "segment-tree", "binary-search-tree", "disjoint-set", "fenwick-tree", "array"]
keywords: ["LC3161", "Hard", "2513", "Block Placement Queries", "線段樹", "併查集", "樹狀陣列", "時光倒流", "倒序處理"]
description: "LeetCode 第 3161 題：Block Placement Queries。探討如何使用線段樹與平衡樹進行正向動態維護，以及如何利用「時光倒流 (Reverse Processing)」技巧結合併查集與樹狀陣列，將複雜度壓制到極限。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3161](https://leetcode.com/problems/block-placement-queries/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2513)</span>  
> **核心主題**：`Segment Tree` $\cdot$ `Disjoint Set (DSU)` $\cdot$ `Fenwick Tree (BIT)`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
有一條無限長的數線，一開始在座標 `0` 處有一個障礙物。
給你一個二維整數陣列 `queries`，包含兩種操作：
1. **操作類型 1** (`queries[i] = [1, x]`)：在座標 `x` 處放置一個障礙物。保證該位置原先沒有障礙物。
2. **操作類型 2** (`queries[i] = [2, x, sz]`)：判斷是否可以在區間 `[0, x]` 內放置一個長度為 `sz` 的方塊。方塊可以與障礙物重疊在邊緣，但不能跨越障礙物。也就是說，必須存在一段連續的空白區間，長度至少為 `sz`，且完全位於 `[0, x]` 內部。

請回傳一個布林陣列，依序包含所有類型 2 查詢的結果。

### 限制條件
- $1 \le queries.length \le 1.5 \cdot 10^5$
- $2 \le x \le 5 \cdot 10^4$
- $1 \le sz \le 3 \cdot 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-30：新增解法二 (時光倒流 + 併查集 + 樹狀陣列最優解)](#解法二-時光倒流-併查集--樹狀陣列-常數極小最優解)
- [2026-05-30：初次提交 (Set 鄰居查找 + 線段樹區間最大值)](#解法一-正向處理--stdset--線段樹)

---

## 💡 解法一：正向處理 + `std::set` + 線段樹

### 🎯 演算法分析
正向思考時，我們需要動態維護被障礙物切割的「空白線段長度」。
1. 利用 `std::set` 儲存所有障礙物的座標，這樣可以在 $\mathcal{O}(\log N)$ 時間內利用 `lower_bound` 找到新增障礙物時的左右鄰居。
2. 讓線段樹在座標 $P$ 處，記錄「以 $P$ 點為右端點的空白線段長度」。當在 $x$ 新增障礙物時，原本的區間 $(pre, nex)$ 會被切成兩段，我們只需進行兩次單點更新。
3. 查詢 `[0, x]` 時，最大空白可能來自「完整的區間 (透過線段樹查詢)」或「邊緣被 $x$ 截斷的碎區間 (手動計算 $x - pre$)」，兩者取最大值即可。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> tree;
    int N;

    void pushUp(int node) { tree[node] = max(tree[node<<1], tree[node<<1|1]); }
    
    void upd(int node, int l, int r, int L, int R, int val) {
        if(L > R) return;
        if(L <= l && r <= R) {
            tree[node] = val;
            return;
        }
        int mid = l + (r-l)/2;
        if(L <= mid) upd(node<<1, l, mid, L, R, val);
        if(R > mid) upd(node<<1|1, mid+1, r, L, R, val);
        pushUp(node);
    }
    
    int qry(int node, int l, int r, int L, int R) {
        if(L > R) return 0;
        if(L <= l && r <= R) return tree[node];
        int mid = l + (r-l)/2;
        int rt = 0;
        if(L <= mid) rt = max(rt, qry(node<<1, l, mid, L, R));
        if(R > mid) rt = max(rt, qry(node<<1|1, mid+1, r, L, R));
        return rt;
    }
    
    vector<bool> getResults(vector<vector<int>>& queries) {
        N = 50005;
        tree.assign(4*N, 0);
        set<int> s;
        s.insert(0);
        s.insert(50001);

        vector<bool> rt;
        for(auto& q : queries) {
            if(q.size() == 2) {
                int x = q[1];
                s.insert(x);
                auto it = s.lower_bound(x);
                int pre = *prev(it), nex = *next(it);
                upd(1, 1, N, x+1, x+1, x - pre);
                upd(1, 1, N, nex+1, nex+1, nex - x);
            } else {
                int x = q[1], sz = q[2];
                auto it = s.lower_bound(x);
                int pre = *prev(it);
                int mx = max(qry(1, 1, N, 1, x+1), x - pre);
                rt.push_back(mx >= sz);
            }
        }
        return rt;
    }
};
```

---

## 💡 解法二：時光倒流 + 併查集 + 樹狀陣列 (常數極小最優解)

### 🎯 演算法分析
這是一種極度優雅且常數極小的降維打擊作法。
一般來說，**樹狀陣列 (Fenwick Tree / BIT)** 只能求前綴和，很難求前綴最大值，因為當某個位置的數值變小時，BIT 無法有效向下更新。而在本題正向操作中，新增障礙物會把大區間切小，這對 BIT 是致命傷。

**核心破局點 1：時光倒流 (Reverse Time Processing)**
如果我們**將所有查詢倒過來處理**，原本的「新增障礙物」就變成了「移除障礙物」。
移除一個障礙物，會讓左右兩個小空白區間**合併成一個大空白區間**。這意味著在倒序處理的過程中，空白區間長度**只會單調遞增，永遠不會變小**！這完美滿足了 BIT 求前綴最大值的條件，讓我們可以徹底捨棄笨重的線段樹。

**核心破局點 2：併查集 (DSU) 找左鄰居**
正向解法用了 `std::set` 去找左邊的障礙物，會消耗 $\mathcal{O}(\log N)$。
我們可以使用併查集：將所有「沒有障礙物」的座標強制與左邊合併 (`parent[i] = i - 1`)。
如此一來，對任何座標執行 `find(x)`，併查集會瞬間跳過所有沒有障礙物的點，直接回傳 $x$ 左邊最近的障礙物座標！時間複雜度降至 $\mathcal{O}(\alpha(N))$。

### 🛠️ 解題思路 (Approach)
1. **建立最終狀態**：先掃描一次所有查詢，記錄出經過所有「放置障礙物」操作後，最終的障礙物分佈 (`obs` 陣列)。
2. **初始化併查集與 BIT**：
   - 如果 `obs[i] == false`，則 `parent[i] = i - 1`。
   - 計算最終狀態下，相鄰障礙物的距離，並寫入 BIT (`upd(i+1, i - last)`)。同時記錄 `right[last] = i`。
3. **倒序處理查詢**：
   - **遇到查詢 (`q[0] == 2`)**：利用 `L = find(x-1)` 找出左邊界，然後 `max(qry(L+1), x-L)` 即為最大空白。
   - **遇到移除 (`q[0] == 1`)**：拔除座標 $x$ 的障礙物。找出左鄰居 `L = find(x-1)` 與右鄰居 `R = right[x]`。合併空間 `parent[x] = L`，並更新右指標 `right[L] = R`。接著把新合併的龐大長度 `R - L` 更新到 BIT 中 (`upd(R+1, R-L)`)。
4. **反轉結果**：因為查詢是倒序做的，最後將答案陣列 `reverse()` 即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(Q \log (\max X))$。雖然和線段樹同級，但 BIT 和 DSU 的常數極小，實戰速度碾壓線段樹與 `std::set`。
- **空間複雜度**: $\mathcal{O}(\max X)$。使用了純陣列實作的 BIT 與 DSU。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> tree;
    vector<int> parent;
    vector<int> right;
    int N;
    
    // 併查集：尋找左側最近的障礙物
    int find(int x) {
        return parent[x] == x ? x : parent[x] = find(parent[x]);
    }
    
    // 樹狀陣列 (BIT)：更新前綴最大值 (僅支援單調遞增)
    void upd(int idx, int val) {
        while(idx <= tree.size()) {
            tree[idx] = max(tree[idx], val);
            idx += (idx & -idx);
        }
    }
    
    // 樹狀陣列 (BIT)：查詢前綴最大值
    int qry(int idx) {
        int mx = 0;
        while(idx > 0) {
            mx = max(mx, tree[idx]);
            idx -= (idx & -idx);
        }
        return mx;
    }
    
    vector<bool> getResults(vector<vector<int>>& queries) {
        N = 50000;
        tree.assign(N + 5, 0);
        parent.assign(N + 5, 0);
        right.assign(N + 5, 0);

        vector<bool> obs(N + 5, false);
        obs[0] = true; // 座標 0 始終有障礙物

        // 1. 預處理：找出經過所有操作後的「最終障礙物狀態」
        for(auto& q : queries) {
            if(q.size() == 2) obs[q[1]] = true;
        }

        // 2. 初始化併查集：若無障礙物，則與左邊座標合併
        for(int i = 1; i <= N; ++i) {
            parent[i] = i;
            if(!obs[i]) {
                parent[i] = i - 1;
            }
        }

        // 3. 初始化 BIT：將最終狀態的區間長度寫入
        int last = 0;
        for(int i = 1; i <= N; ++i) {
            if(obs[i]) {
                right[last] = i;
                upd(i + 1, i - last); // BIT 為 1-based index
                last = i;
            }
        }
        right[last] = N;
        upd(N + 1, N - last);

        vector<bool> rt;
        
        // 4. 時光倒流：由後往前處理操作
        for(int i = queries.size() - 1; i >= 0; --i) {
            auto& q = queries[i];
            
            // 操作 1：在倒序中代表「移除」障礙物
            if(q[0] == 1) {
                int x = q[1];
                int L = find(x - 1);  // 尋找左鄰居
                int R = right[x];     // 尋找右鄰居
                
                // 拔除 x 的障礙物，將其與左邊合併
                parent[x] = L;
                right[L] = R;
                
                // 區間合併，長度變大 (R - L)，更新至 BIT
                upd(R + 1, R - L);
            }
            // 操作 2：區間查詢
            else {
                int x = q[1], sz = q[2];
                int L = find(x - 1);
                
                // 最大空白 = max(完整區間的最大值, 被 x 截斷的邊緣區間)
                int mx = max(qry(L + 1), x - L);
                rt.push_back(mx >= sz);
            }
        }

        // 5. 因為是倒序處理，最後必須將結果反轉
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