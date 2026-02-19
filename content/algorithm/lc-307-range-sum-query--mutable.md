---
title: "🧩 LeetCode 307. Range Sum Query - Mutable"
date: 2026-02-19T18:47:00+08:00
lastmod: 2026-02-19T18:47:00+08:00
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
keywords: ["LC307", "Medium", "BIT", "區間和"]
description: "LeetCode 第 307 題：Range Sum Query - Mutable。難度評分：Medium。探討如何使用 Binary Indexed Tree (Fenwick Tree) 解決動態陣列的區間和查詢，並比較 O(N log N) 與 O(N) 兩種建樹方法的差異。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 307](https://leetcode.com/problems/range-sum-query-mutable/)  
> **難度評分**：<span style="color: var(--diff-color); font-weight: bold;">Medium(N/A)</span>  
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
- [2026-02-19：初次提交 (熟悉 BIT 結構與 $\mathcal{O}(N)$ 建樹優化)](#2026-02-19-初次提交)

---

## 💡 2026-02-19 初次提交

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