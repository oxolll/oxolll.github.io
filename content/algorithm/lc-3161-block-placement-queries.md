---
title: "LeetCode 3161. Block Placement Queries"
date: 2026-05-30T21:30:00+08:00
lastmod: 2026-05-30T21:30:00+08:00
difficulty: 2513
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "segment-tree", "binary-search-tree", "array"]
keywords: ["LC3161", "Hard", "2513", "Block Placement Queries", "線段樹", "區間最大值", "動態維護"]
description: "LeetCode 第 3161 題：Block Placement Queries。難度評分：2513。探討如何結合平衡二元搜尋樹 (std::set) 尋找鄰居節點，並利用線段樹 (Segment Tree) 動態維護區間最大空白長度，以 O(Q log X) 高效處理障礙物放置與方塊查詢問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3161](https://leetcode.com/problems/block-placement-queries/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2513)</span>  
> **核心主題**：`Segment Tree` $\cdot$ `Binary Search Tree (std::set)`

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

### 範例
- **Input**: `queries = [[1, 2], [2, 3, 3], [2, 3, 1], [2, 2, 2]]`
- **Output**: `[false, true, true]`
- **Explanation**:
  - `[1, 2]`：在 `x = 2` 放置障礙物。
  - `[2, 3, 3]`：在 `[0, 3]` 放長度 3 的方塊。由於有障礙物在 2，空白區間為 `[0, 2]` (長度 2) 和 `[2, 3]` (長度 1)，皆小於 3，回傳 `false`。
  - `[2, 3, 1]`：在 `[0, 3]` 放長度 1 的方塊。可放在 `[0, 1]` 或 `[2, 3]`，回傳 `true`。
  - `[2, 2, 2]`：在 `[0, 2]` 放長度 2 的方塊。剛好放在 `[0, 2]`，回傳 `true`。

### 限制條件
- $1 \le queries.length \le 1.5 \cdot 10^5$
- $2 \le x \le 5 \cdot 10^4$
- $1 \le sz \le 3 \cdot 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-30：初次提交 (Set 鄰居查找 + 線段樹區間最大值)](#2026-05-30-初次提交)

---

## 💡 2026-05-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要快速回答「區間 `[0, x]` 內是否有長度 $\ge sz$ 的空白」，我們需要維護所有被障礙物分割出來的「空白線段長度」，並且能快速查詢這些長度中的「最大值」。這正是 **線段樹 (Segment Tree)** 的標準應用。

**核心破局點 1：線段樹的儲存定義**
如果線段樹維護的是「區間內的具體線段」，合併狀態會非常複雜。
我們可以轉換視角：**讓線段樹在座標 $P$ 處，記錄「以 $P$ 點為右端點的空白線段長度」**。
例如，若障礙物分別在 $0, 2, 5$，則線段樹在座標 $2$ 的值為 $2 - 0 = 2$，在座標 $5$ 的值為 $5 - 2 = 3$。
這樣查詢 `[0, x]` 內的最大空白，就等於查詢線段樹在區間 `[1, x]` 內的最大值。

**核心破局點 2：動態新增障礙物的區間分裂**
當我們在 $x$ 新增一個障礙物時，原本的區間 $(pre, nex)$ 會被切成兩段：$(pre, x)$ 與 $(x, nex)$。
利用 `std::set` 找出左鄰居 $pre$ 與右鄰居 $nex$，我們只需要進行兩次線段樹單點更新：
- 更新座標 $x$ 的值為 $x - pre$。
- 更新座標 $nex$ 的值為 $nex - x$。

**核心破局點 3：查詢時的碎邊處理 (Partial Segment)**
當我們查詢 `[0, x]` 時，最大的空白可能來自兩種情況：
1. **完整的區間**：在 $x$ 之前的兩個障礙物所夾出的區間（可透過線段樹 `qry(1, x)` 直接查出）。
2. **邊緣的碎區間**：從 $x$ 左邊的最後一個障礙物 $pre$ 一直到 $x$ 的這段距離（這段可能沒有以障礙物收尾，線段樹查不到，需手動計算為 $x - pre$）。
最終的最大長度即為 `max(qry(1, x), x - pre)`。

---

### 🛠️ 解題思路 (Approach)
1. **初始化**：
   - 座標最大值限制為 $50000$，宣告大小為 $50005$ 的線段樹。
   - `std::set<int> s` 初始化加入 `0` 與邊界 `50001`。
2. **處理操作 1 (新增障礙物 `x`)**：
   - 插入 `x` 到 `s`。
   - 透過 `lower_bound` 找到 `x` 的左右鄰居 `pre` 與 `nex`。
   - 線段樹單點更新 (Point Update)：
     - 將 $x$ (實作上平移為 $x+1$) 的值更新為 $x - pre$。
     - 將 $nex$ (實作上平移為 $nex+1$) 的值更新為 $nex - x$。
3. **處理操作 2 (查詢區間 `[0, x]`)**：
   - 在 `s` 中尋找嚴格小於等於 $x$ 的最後一個障礙物 `pre` (`*prev(s.lower_bound(x+1))`，在程式碼中用 `s.lower_bound(x)` 找到剛好 $\ge x$ 的前一個元素)。
   - 線段樹查詢 (Range Query)：取得 `[1, x]` 的最大值。
   - 結算最大空白長度：`mx = max(qry(1, x+1), x - pre)`。
   - 若 `mx >= sz`，回傳 `true`；否則回傳 `false`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(Q \log (\max X))$。
  - 對於每個查詢，`std::set` 的操作耗時 $\mathcal{O}(\log (\max X))$。
  - 線段樹的單點更新與區間查詢同樣耗時 $\mathcal{O}(\log (\max X))$。
  - 總時間複雜度極佳，能輕鬆通過 $1.5 \cdot 10^5$ 次查詢。
- **空間複雜度**: $\mathcal{O}(\max X + Q)$。
  - 線段樹需要 $4 \times \max X$ 的陣列大小。
  - `std::set` 最多儲存 $Q$ 個障礙物座標。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> tree;
    int N;

    // 線段樹：向上合併，取子區間的最大值
    void pushUp(int node) { 
        tree[node] = max(tree[node << 1], tree[node << 1 | 1]); 
    }

    // 線段樹：單點更新
    void upd(int node, int l, int r, int L, int R, int val) {
        if(L > R) return;
        
        // 找到目標單點
        if(L <= l && r <= R) {
            tree[node] = val;
            return;
        }

        int mid = l + (r - l) / 2;
        if(L <= mid) upd(node << 1, l, mid, L, R, val);
        if(R > mid) upd(node << 1 | 1, mid + 1, r, L, R, val);

        pushUp(node);
    }

    // 線段樹：區間查詢最大值
    int qry(int node, int l, int r, int L, int R) {
        if(L > R) return 0;
        
        if(L <= l && r <= R) return tree[node];

        int mid = l + (r - l) / 2;
        int rt = 0;
        if(L <= mid) rt = max(rt, qry(node << 1, l, mid, L, R));
        if(R > mid) rt = max(rt, qry(node << 1 | 1, mid + 1, r, L, R));

        return rt;
    }

    vector<bool> getResults(vector<vector<int>>& queries) {
        // 依據題目限制，座標最大為 50000
        N = 50005;
        tree.assign(4 * N, 0);
        
        set<int> s;
        s.insert(0);      // 初始障礙物
        s.insert(50001);  // 右方虛擬邊界

        vector<bool> rt;
        
        for(auto& q : queries) {
            // 操作 1：放置障礙物
            if(q.size() == 2) {
                int x = q[1];
                s.insert(x);
                
                auto it = s.lower_bound(x);
                int pre = *prev(it);
                int nex = *next(it);
                
                // 1-based index 處理：座標平移 +1
                // 切割舊區間，建立 x 與其左鄰居的距離
                upd(1, 1, N, x + 1, x + 1, x - pre);
                // 更新右鄰居與 x 的新距離
                upd(1, 1, N, nex + 1, nex + 1, nex - x);
            }
            // 操作 2：查詢區間能否放下 sz 的方塊
            else {
                int x = q[1], sz = q[2];
                
                auto it = s.lower_bound(x);
                // 找出 x 左邊最近的障礙物 (包含 x 本身若 x 剛好是障礙物)
                int pre = *prev(it); 
                
                // 最大空白長度 = max(區間內完整的障礙物間距, 最右側被截斷的碎邊間距)
                int mx = max(qry(1, 1, N, 1, x + 1), x - pre);
                
                if(mx >= sz) {
                    rt.push_back(true);
                } else {
                    rt.push_back(false);
                }
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