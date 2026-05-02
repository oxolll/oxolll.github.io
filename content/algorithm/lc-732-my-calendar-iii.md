---
title: "LeetCode 732. My Calendar III"
date: 2026-05-03T12:54:00+08:00
lastmod: 2026-05-03T12:54:00+08:00
difficulty: 2000
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "segment-tree", "design", "dynamic-segment-tree"]
keywords: ["LC732", "Hard", "My Calendar III", "Dynamic Segment Tree", "動態開點線段樹", "Memory Pool"]
description: "LeetCode 第 732 題：My Calendar III。難度評分：Hard。探討如何利用「動態開點線段樹 (Dynamic Segment Tree)」解決值域高達 10^9 的區間重疊問題，並透過「陣列池化 (Memory Pool)」極致優化記憶體配置效能。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 732](https://leetcode.com/problems/my-calendar-iii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Design` $\cdot$ `Segment Tree` $\cdot$ `Ordered Set`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
當 $k$ 個行程在同一個時間點（或時間段）發生重疊時，我們稱之為一個 `$k$-booking` (k 次預訂)。
請實作 `MyCalendarThree` 類別來存放所有的行程，並隨時回傳當前行事曆中的最大 `$k$-booking` 數值。

- `MyCalendarThree()`：初始化物件。
- `int book(int startTime, int endTime)`：新增一個半開區間 `[startTime, endTime)` 的行程，並回傳行事曆中所有行程能造成的**最大重疊次數**。

### 限制條件
- $0 \le startTime < endTime \le 10^9$
- 最多會呼叫 $400$ 次 `book` 函式。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-03：初次提交 (動態開點線段樹 + Memory Pool 優化)](#2026-05-03-初次提交)

---

## 💡 2026-05-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題本質上是求解「區間最大重疊次數」，也就是要對區間 `[startTime, endTime)` 進行 $+1$ 操作，並隨時查詢全域的區間最大值。這正是**線段樹 (Segment Tree) 搭配懶惰標記 (Lazy Propagation)** 的拿手好戲。

然而，這題的值域 $C = 10^9$，標準的線段樹需要宣告 $4 \times 10^9$ 大小的陣列，這不僅會超過記憶體限制 (MLE)，就連建樹的時間也會超時 (TLE)。

**破局核心：動態開點線段樹 (Dynamic Segment Tree)**
既然我們最多只會呼叫 400 次 `book`，每次區間更新在樹上最多切分出 $\mathcal{O}(\log C)$ 個節點。也就是說，整棵樹大部分的區間其實都是 $0$，根本不需要實體建立出來。
「動態開點」的思想是：**一開始只有一個根節點，只有當我們遞迴存取到某個子區間時，才實體建立該節點**。

**極致優化：陣列池化 (Memory Pool)**
在 C++ 中頻繁使用 `new Node()` 分配記憶體是非常耗時的。我們可以預先宣告一個足夠大的 `vector<Node> pool`，並用一個整數變數 `freepos` 作為指標。每次需要新節點時，直接從池子中劃撥一塊空間 `return freepos++`。這讓記憶體位置連續，大幅提升 Cache 命中率並避免內存碎片化。

### 🛠️ 解題思路 (Approach)
1. **節點設計 (`Node`)**：包含 `x` (該區間的最大重疊數)、`lazy` (懶惰標記)、`left` 與 `right` (左右子節點在 pool 中的索引，預設為 -1 表示尚未建立)。
2. **動態配置 (`getl`, `getr`)**：當需要訪問左/右子節點時，如果發現索引是 `-1`，則立即從 `pool` 中分配一個新節點給它。
3. **下放與上推 (`pushDown`, `pushUp`)**：
   - `pushDown`：若當前節點有 `lazy` 標記，強制開啟左右子節點，並將標記與數值下放，然後清空自己的標記。
   - `pushUp`：子節點更新完畢後，父節點的 `x` 會更新為左右子樹的最大值 `max(left, right)`。
4. **區間轉換**：因為題目是半開區間 `[startTime, endTime)`，實作時將其映射為 `[startTime + 1, endTime]` 的閉區間，完美契合 1-based 的線段樹邏輯。
5. **查詢與更新**：呼叫 `upd` 完成區間加值後，根節點 `pool[root].x` 隨時都維護著整個 $[1, 10^9]$ 的全域最大值，直接回傳即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(Q \log C)$。其中 $Q$ 是 `book` 呼叫的次數 ($Q \le 400$)，$C$ 是時間的最大值 ($10^9$)。每次呼叫最多深入樹的深度 $\approx 30$，極其快速。
- **空間複雜度**: $\mathcal{O}(Q \log C)$。我們最多只會建立約 $400 \times 30$ 個節點。預開 `100000` 大小的 `pool` 絕對充足且安全。

### 💻 程式碼實作 (C++)
```cpp
class MyCalendarThree {
public:
    // 定義動態線段樹的節點
    class Node {
    public:
        int x;      // 該區間的最大重疊次數 (Max)
        int lazy;   // 懶惰標記 (Lazy Tag)
        int left;   // 左子節點在 pool 中的索引
        int right;  // 右子節點在 pool 中的索引
        Node() : x(0), lazy(0), left(-1), right(-1) {}
    };
    
    // Memory Pool 陣列池化，避免 new 帶來的記憶體配置開銷
    vector<Node> pool;
    int N = 1e9 + 5;

    int freepos = 0;
    int root;
    
    MyCalendarThree() {
        // 預分配足夠的空間 (400 次查詢 * 約每次 30~50 個新節點)
        pool = vector<Node>(100000 + 5);
        root = freepos++; // 分配根節點
    }
    
    // 由下而上更新區間最大值
    void pushUp(int u) {
        int l = pool[u].left, r = pool[u].right;
        // 如果子節點尚未建立，視為 0
        long long left = l == -1 ? 0 : pool[l].x;
        long long right = r == -1 ? 0 : pool[r].x;

        pool[u].x = max(left, right);
    }
    
    // 由上而下下放懶惰標記
    void pushDown(int u, int l, int r) {
        if(pool[u].lazy == 0) return;
        
        // 確保左右子節點存在
        int left = getl(u), right = getr(u), lz = pool[u].lazy;
        
        // 將懶惰標記下放給左右子節點
        pool[left].x += lz;
        pool[left].lazy += lz;

        pool[right].x += lz;
        pool[right].lazy += lz;

        pool[u].lazy = 0; // 清空當前標記
    }
    
    // 動態開點：獲取左子節點，若不存在則立刻分配
    int getl(int u) {
        if(pool[u].left == -1) return pool[u].left = freepos++;
        return pool[u].left;
    }
    
    // 動態開點：獲取右子節點，若不存在則立刻分配
    int getr(int u) {
        if(pool[u].right == -1) return pool[u].right = freepos++;
        return pool[u].right;
    }
    
    // 區間更新 (區間 L~R 加上 val)
    void upd(int u, int l, int r, int L, int R, long long val) {
        // 完全包含
        if(L <= l && r <= R) {
            pool[u].x += val;
            pool[u].lazy += val;
            return;
        }

        pushDown(u, l, r);

        int mid = l + (r - l) / 2;
        // 部分包含，遞迴更新
        if(L <= mid) upd(getl(u), l, mid, L, R, val);
        if(R > mid)  upd(getr(u), mid + 1, r, L, R, val);

        pushUp(u);
    }
    
    int book(int startTime, int endTime) {
        // 題目給定 [startTime, endTime)，平移轉換為閉區間 [startTime+1, endTime]
        upd(root, 1, N, startTime + 1, endTime, 1);

        // 根節點儲存的永遠是全域區間的最大值
        return pool[root].x;
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
````</Node></Node></Node>