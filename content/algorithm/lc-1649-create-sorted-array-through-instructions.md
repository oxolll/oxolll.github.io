---
title: "LeetCode 1649. Create Sorted Array through Instructions"
date: 2026-02-19T19:40:00+08:00
lastmod: 2026-02-19T19:40:00+08:00
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
keywords: ["LC1649", "2207", "Hard", "BIT", "Fenwick Tree"]
description: "LeetCode 第 1649 題：Create Sorted Array through Instructions。難度評分：2207。探討如何利用 Binary Indexed Tree (BIT) 的值域特徵來動態計算嚴格大於與嚴格小於的元素數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1649](https://leetcode.com/problems/create-sorted-array-through-instructions/)  
> **難度評分**：<span style="color: var(--diff-color); font-weight: bold;">Hard(2207)</span>   
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
- [2026-02-19：初次提交 (運用 BIT 值域特徵解題)](#2026-02-19-初次提交)

---

## 💡 2026-02-19 初次提交

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