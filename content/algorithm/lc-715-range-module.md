---
title: "LeetCode 715. Range Module"
date: 2026-05-06T17:40:00+08:00
lastmod: 2026-05-06T17:40:00+08:00
difficulty: 2000
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "design", "segment-tree", "ordered-set"]
keywords: ["LC715", "Hard", "Range Module", "Dynamic Segment Tree", "std::map", "Interval Merging"]
description: "LeetCode 第 715 題：Range Module。難度評分：Hard。探討如何解決極大值域 (10^9) 下的區間覆蓋與完整性查詢問題，對比「動態開點線段樹」與「有序映射 (std::map) 區間合併」兩種核心演算法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 715](https://leetcode.com/problems/range-module/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Design` $\cdot$ `Segment Tree` $\cdot$ `Ordered Set`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
設計一個 `RangeModule` 類別來追蹤整數區間。它必須支援以下三種操作：
1. `addRange(left, right)`：新增半開區間 `[left, right)`，追蹤該區間內的所有實數。
2. `queryRange(left, right)`：查詢半開區間 `[left, right)` 內的**所有**實數是否目前都在被追蹤中。
3. `removeRange(left, right)`：停止追蹤半開區間 `[left, right)` 內的實數。

### 限制條件
- $1 \le left < right \le 10^9$
- 三種操作的呼叫總次數不超過 $10^4$ 次。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-06：初次提交 (動態開點線段樹與 map 區間維護雙解法)](#2026-05-06-初次提交)

---

## 💡 2026-05-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的挑戰在於值域極大 ($10^9$) 且需要频繁地執行區間覆蓋 (1)、區間清空 (0) 與區間完整性查詢 (AND 邏輯)。

#### **方法一：動態開點線段樹 (Dynamic Segment Tree)**
適合處理值域極大且區間操作多樣的通用場景。
- **動態開點**：因為 $10^9$ 無法預開陣列，僅在訪問到該節點時才 `push_back` 到 `pool`。
- **懶惰標記 (Lazy Propagation)**：`lazy = 1` 代表全部覆蓋，`lazy = 0` 代表全部移除。
- **PushUp**：父節點的狀態 `flag = left->flag && right->flag`。
- **座標轉換**：將半開區間 `[left, right)` 轉為閉區間 `[left, right - 1]` 處理。

#### **方法二：`std::map` 區間管理 (Interval Merging)**
利用 `std::map<int, int>` 維護一組**互不相交且有序**的閉區間 `[start, end]`。
- **Add**：找到所有與新區間有交集的舊區間，將它們合併成一個單一的大區間。
- **Remove**：找到所有重疊部分，進行切點處理（可能產生新的碎片區間）。
- **Query**：只需檢查是否存在一個現有區間完全包含目標區間。

---

### 🛠️ 解法一：動態開點線段樹 (記憶體池優化)

```cpp
class RangeModule {
public:
    class Node {
    public:
        int l, r, left, right, flag, lazy;
        Node(int l, int r) : l(l), r(r), flag(0), lazy(-1), left(-1), right(-1) {}
    };

    vector<Node> pool;
    int cur = 0, root;

    RangeModule() {
        pool.reserve(1000000); // 預留空間，優化 vector 擴張效能
        pool.push_back(Node(1, 1000000000));
        root = cur++;
    }

    void pushUp(int u) {
        int left = pool[u].left, right = pool[u].right;
        int l_val = (left == -1 ? 0 : pool[left].flag);
        int r_val = (right == -1 ? 0 : pool[right].flag);
        pool[u].flag = l_val && r_val;
    }

    void pushDown(int u, int l, int mid, int r) {
        if (pool[u].lazy == -1) return;
        int left = getl(u, l, mid), right = getr(u, mid + 1, r);
        int lz = pool[u].lazy;
        pool[left].lazy = lz; pool[left].flag = lz;
        pool[right].lazy = lz; pool[right].flag = lz;
        pool[u].lazy = -1;
    }

    int getl(int u, int l, int r) {
        if (pool[u].left == -1) {
            pool.push_back(Node(l, r));
            pool[u].left = cur++;
        }
        return pool[u].left;
    }

    int getr(int u, int l, int r) {
        if (pool[u].right == -1) {
            pool.push_back(Node(l, r));
            pool[u].right = cur++;
        }
        return pool[u].right;
    }

    void upd(int u, int L, int R, int val) {
        int l = pool[u].l, r = pool[u].r;
        if (L <= l && r <= R) {
            pool[u].flag = val;
            pool[u].lazy = val;
            return;
        }
        int mid = l + (r - l) / 2;
        pushDown(u, l, mid, r);
        if (L <= mid) upd(getl(u, l, mid), L, R, val);
        if (R > mid)  upd(getr(u, mid + 1, r), L, R, val);
        pushUp(u);
    }

    int qry(int u, int L, int R) {
        int l = pool[u].l, r = pool[u].r;
        if (L <= l && r <= R) return pool[u].flag;
        int mid = l + (r - l) / 2;
        pushDown(u, l, mid, r);
        bool res = true;
        if (L <= mid) res = res && qry(getl(u, l, mid), L, R);
        if (res && R > mid) res = res && qry(getr(u, mid + 1, r), L, R);
        return res;
    }

    void addRange(int left, int right) { upd(root, left, right - 1, 1); }
    bool queryRange(int left, int right) { return qry(root, left, right - 1); }
    void removeRange(int left, int right) { upd(root, left, right - 1, 0); }
};
```

---

### 🛠️ 解法二：`std::map` 區間管理 (高效合併)

```cpp
class RangeModule {
public:
    map<int, int> mp; // 儲存 [left, right] (閉區間)

    void addRange(int left, int right) {
        right--; // 轉閉區間
        auto it = mp.upper_bound(left);
        if (it != mp.begin()) --it;
        
        int mm = left, mx = right;
        if (it != mp.end() && it->second >= left) {
            mm = min(mm, it->first); // 與前一個區間相連或重疊
        } else {
            ++it;
        }

        vector<int> del;
        while (it != mp.end() && it->first <= right) {
            mx = max(mx, it->second);
            del.push_back(it->first);
            ++it;
        }
        for (int l : del) mp.erase(l);
        mp[mm] = mx;
    }
    
    bool queryRange(int left, int right) {
        right--;
        auto it = mp.upper_bound(left);
        if (it == mp.begin()) return false;
        --it;
        // 只要目標區間被某個單一區間完全包含，即為 true
        return it->first <= left && it->second >= right;
    }
    
    void removeRange(int left, int right) {
        right--;
        auto it = mp.upper_bound(left);
        if (it != mp.begin()) --it;
        if (it != mp.end() && it->second < left) ++it;

        vector<pair<int, int>> add;
        vector<int> del;
        while (it != mp.end() && it->first <= right) {
            int l = it->first, r = it->second;
            del.push_back(l);
            if (l < left) add.push_back({l, left - 1});   // 保留左側碎片
            if (r > right) add.push_back({right + 1, r}); // 保留右側碎片
            ++it;
        }
        for (int l : del) mp.erase(l);
        for (auto& p : add) mp[p.first] = p.second;
    }
};
```

---

### 📊 複雜度對比

| 特性 | 方法一：動態開點線段樹 | 方法二：`std::map` 區間管理 |
| :--- | :--- | :--- |
| **時間複雜度 (單次)** | $O(\log 10^9)$ | 均攤 $O(\log N)$ |
| **空間複雜度** | $O(Q \log 10^9)$ | $O(N)$ (N 為獨立區間數) |
| **實作特點** | 通用性極強，邏輯標準化 | 記憶體極省，面試中展現對 STL 熟練度 |

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