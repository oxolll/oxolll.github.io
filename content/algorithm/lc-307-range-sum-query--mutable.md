---
title: "LeetCode 307. Range Sum Query - Mutable"
date: 2026-02-19T18:47:00+08:00
lastmod: 2026-04-30T16:37:00+08:00
difficulty: 1650
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Binary Indexed Tree (Fenwick Tree)", "Design", "Segment Tree"]
keywords: ["LC307", "Medium", "BIT", "區間和", "線段樹"]
description: "LeetCode 第 307 題：Range Sum Query - Mutable。難度評分：Medium。探討如何使用 Binary Indexed Tree (Fenwick Tree) 與 Segment Tree (線段樹) 解決動態陣列的區間和查詢，並比較兩種資料結構的實作差異。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 307](https://leetcode.com/problems/range-sum-query-mutable/)  
> **難度評分**：<span style="color: var(--diff-color); font-weight: bold;">Medium(1650)</span>  
> **核心主題**：`Array` $\cdot$ `Design` $\cdot$ `Binary Indexed Tree` $\cdot$ `Segment Tree`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`，請實作 `NumArray` 類別來支援以下兩種操作：
1. **`update(index, val)`**：將 `nums[index]` 的值更新為 `val`。
2. **`sumRange(left, right)`**：回傳陣列從索引 `left` 到 `right`（包含雙邊）的元素總和。

### 範例
- **Input**:
  `["NumArray", "sumRange", "update", "sumRange"]`
  `[[[1, 3, 5]], [0, 2], [1, 2], [0, 2]]`
- **Output**:
  `[null, 9, null, 8]`
- **Explanation**: 
  初始化陣列為 `[1, 3, 5]`。
  第一次查詢 `sumRange(0, 2)`，總和為 $1 + 3 + 5 = 9$。
  更新 `update(1, 2)`，陣列變為 `[1, 2, 5]`。
  第二次查詢 `sumRange(0, 2)`，總和為 $1 + 2 + 5 = 8$。

### 限制條件
- $1 \le nums.length \le 3 \times 10^4$
- $-100 \le nums[i] \le 100$
- $0 \le index < nums.length$
- $-100 \le val \le 100$
- $0 \le left \le right < nums.length$
- 最多會呼叫 $3 \times 10^4$ 次 `update` 與 `sumRange` 函式。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-30：更新提交 (線段樹 Segment Tree 實作)](#2026-04-30-更新提交-線段樹)
- [2026-02-19：初次提交 (熟悉 BIT 結構與 O(N) 建樹優化)](#2026-02-19-初次提交-bit)

---

## 💡 2026-04-30 更新提交 (線段樹)

### 🎯 演算法分析 (Algorithm Analysis)
除了 BIT，這題也是練習**線段樹 (Segment Tree)** 的絕佳模板題。
比起 BIT 只能處理「符合結合律且可逆」的操作（例如區間和），線段樹的擴充性更強，還可以處理區間最大/最小值 (Max/Min) 等不可逆操作。
實作上，我們將底層線段樹設計為 $1$-indexed（根節點為 $1$），利用位元運算 `node << 1` 與 `(node << 1) | 1` 來快速存取左右子節點。
而在呼叫 API 時，只需將原本 $0$-indexed 的查詢區間 `+1` 傳入，就能完美將介面與底層實作解耦。

### 📊 複雜度分析
- **時間複雜度**:
  - **建樹 (Build)**: $\mathcal{O}(N)$。遞迴拜訪 $2N-1$ 個節點。
  - **更新 (Update)**: $\mathcal{O}(\log N)$。
  - **查詢 (Query)**: $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。為了避免越界，線段樹標準做法會開到 $4N$ 的陣列空間。

### 💻 程式碼實作 (C++)
```cpp
class NumArray {
public:
    vector<long long> tree;
    int N;
    
    // 遞迴建樹 O(N)
    void build(int node, int l, int r, vector<int>& nums) {
        if(l == r) {
            tree[node] = nums[l-1]; // nums 是 0-indexed，l 是 1-indexed
            return;
        }

        int mid = l + (r - l) / 2;
        build(node << 1, l, mid, nums);
        build((node << 1) | 1, mid + 1, r, nums);

        // Push Up
        tree[node] = tree[node << 1] + tree[(node << 1) | 1];
    }
    
    // 單點更新 O(log N)
    void up(int node, int l, int r, int idx, long long val) {
        if(l == r) {
            tree[node] = val;
            return;
        }

        int mid = l + (r - l) / 2;
        if(mid >= idx) {
            up(node << 1, l, mid, idx, val);
        } else {
            up((node << 1) | 1, mid + 1, r, idx, val);
        }
        
        // Push Up
        tree[node] = tree[node << 1] + tree[(node << 1) | 1];
    }

    // 區間查詢 O(log N)
    long long q(int node, int l, int r, int L, int R) {
        // 1. 完全不交集
        if(r < L || R < l) return 0;
        // 2. 完全包含
        if(L <= l && r <= R) return tree[node];

        // 3. 部分交集
        int mid = l + (r - l) / 2;
        long long left = q(node << 1, l, mid, L, R);
        long long right = q((node << 1) | 1, mid + 1, r, L, R);

        return left + right;
    }
    
    NumArray(vector<int>& nums) {
        N = nums.size();
        tree = vector<long long>(4 * N, 0); // 確保空間不越界
        build(1, 1, N, nums);
    }
    
    void update(int index, int val) {
        // 對齊 1-indexed
        up(1, 1, N, index + 1, val);
    }
    
    int sumRange(int left, int right) {
        // 對齊 1-indexed
        return q(1, 1, N, left + 1, right + 1);
    }
};
```

---

## 💡 2026-02-19 初次提交 (BIT)

### 🎯 直覺 (Intuition)
如果陣列是靜態的，我們可以用 Prefix Sum (前綴和) 在 $\mathcal{O}(1)$ 時間內算出區間和；但這題包含了 `update` 操作，如果用 Prefix Sum，每次更新都需要 $\mathcal{O}(N)$ 的時間去修改後面的所有值，一定會 TLE。
為了同時兼顧「快速更新」與「快速查詢區間和」，**Binary Indexed Tree (Fenwick Tree)** 或 **Segment Tree (線段樹)** 是最完美的解法。這題剛好用來刻意練習較為精簡但抽象的 BIT 結構。

### 🛠️ 解題思路 (Approach)
這題的實作核心在於維護一個大小為 $N+1$ 的 `BIT` 陣列（因為 BIT 的索引必須從 1 開始），並利用位元運算 `lowbit = x & -x` 來進行快速跳躍。

實作上經歷了兩種建樹 (Initialization) 的優化：

1. **版本一：暴力建樹（呼叫 $N$ 次 update）**
   - 直接將原陣列當作全是 0，然後遍歷 `nums`，對每個元素呼叫 `update(i, nums[i])`。
   - 邏輯最簡單、最直觀，但每次 `update` 需要 $\mathcal{O}(\log N)$ 的時間，總建樹時間為 $\mathcal{O}(N \log N)$。

2. **版本二：線性建樹（$\mathcal{O}(N)$ 最佳化）**
   - 觀察 BIT 的結構可以發現，節點 `i` 的值必定會貢獻給它的直接父節點 `i + (i & -i)`。
   - 因此，我們可以在 $\mathcal{O}(N)$ 的迴圈內，直接先把當前的值塞入 `BIT[i]`，然後立刻將 `BIT[i]` 的值向上傳遞（累加）給它唯一的直接父節點。這個做法不僅程式碼乾淨，還省去了大量的位元跳躍操作，將建樹時間壓到了真正的 $\mathcal{O}(N)$。

### 📊 複雜度分析
- **時間複雜度**:
  - **建構子 (Constructor)**: 版本一為 $\mathcal{O}(N \log N)$，版本二優化為 $\mathcal{O}(N)$。
  - **`update` 操作**: $\mathcal{O}(\log N)$。
  - **`sumRange` 操作**: $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(N)$，需要額外配置長度為 $N+1$ 的 `BIT` 陣列（以及一個備份原數值的 `Nums` 陣列）。

### 💻 程式碼實作 (C++)

#### 版本二：最佳化 $\mathcal{O}(N)$ 線性建樹解法 (推薦)
```cpp
class NumArray {
public:
    vector<int> Nums;
    vector<int> BIT;
    int n;
    
    NumArray(vector<int>& nums) {
        Nums = nums;
        n = nums.size();
        BIT = vector<int>(n + 1, 0);
        
        // O(N) 線性建樹優化
        for(int i = 1; i <= n; ++i) {
            BIT[i] += Nums[i - 1]; // 先放入自身的值
            int j = i + (i & -i);  // 尋找直接父節點
            if(j <= n) {
                BIT[j] += BIT[i];  // 將自身的區間和向上貢獻給父節點
            }
        }
    }
    
    void update(int index, int val) {
        int dif = val - Nums[index];
        Nums[index] = val; // 更新備份陣列
        ++index; // BIT 索引從 1 開始
        
        while(index <= n) {
            BIT[index] += dif;
            index += (index & -index); // 向上更新所有包含此元素的區間
        }
    }
    
    int sumRange(int left, int right) {
        // 利用前綴和的相減原理：Sum(L, R) = PrefixSum(R) - PrefixSum(L-1)
        return getSum(right) - getSum(left - 1);
    }

private:
    int getSum(int index) {
        ++index; // BIT 索引從 1 開始
        int sum = 0;
        
        while(index > 0) {
            sum += BIT[index];
            index -= (index & -index); // 向下尋找組成該前綴和的各個子區間
        }
        return sum;
    }
};
```

<details>
<summary><b>點擊展開：版本一 (暴力建樹 O(N log N))</b></summary>

```cpp
class NumArray {
public:
    vector<int> BIT;
    vector<int> NUM;
    int n;
    
    NumArray(vector<int>& nums) {
        n = nums.size();
        NUM = vector<int>(n, 0);
        BIT = vector<int>(n + 1, 0);
        
        // 呼叫 N 次 update，時間 O(N log N)
        for(int i = 0; i < n; ++i)
            update(i, nums[i]);
    }
    
    void update(int index, int val) {
        int dif = val - NUM[index];
        NUM[index] = val;
        ++index;
        while(index <= n) {
            BIT[index] += dif;
            index += (index & -index);
        }
    }
    
    int sumRange(int left, int right) {
        return getSum(right) - getSum(left - 1);
    }
    
private:    
    int getSum(int index) {
        ++index;
        int sum = 0;
        while(index > 0) {
            sum += BIT[index];
            index -= (index & -index);
        }
        return sum;
    }
};
```
</details>

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