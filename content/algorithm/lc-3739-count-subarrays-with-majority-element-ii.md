---
title: "LeetCode 3739. Count Subarrays With Majority Element II"
date: 2026-06-25T16:01:00+08:00
lastmod: 2026-06-25T16:01:00+08:00
difficulty: 2089
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum", "binary-indexed-tree", "math"]
keywords: ["LC3739", "Hard", "2089", "Majority Element", "樹狀陣列", "Fenwick Tree", "前綴和", "數值映射", "順序對"]
description: "LeetCode 第 3739 題：Count Subarrays With Majority Element II。難度評分：2089。探討如何利用 +1/-1 數值映射將多數元素條件轉化為區間和大於 0，並結合加上偏移量的樹狀陣列 (Fenwick Tree)，在 O(N log N) 的時間內極速統計順序對。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3739](https://leetcode.com/problems/count-subarrays-with-majority-element-ii/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2089)</span>  
> **核心主題**：`Prefix Sum` $\cdot$ `Binary Indexed Tree` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
*(此為 Count Subarrays With Majority Element I 的進階版)*
給你一個整數陣列 `nums` 和一個整數 `target`。
請你計算並回傳 `nums` 中，有多少個連續子陣列滿足：`target` 是該子陣列的 **多數元素 (Majority Element)**。

一個元素被稱為子陣列的「多數元素」，若且唯若它在該子陣列中出現的次數 **嚴格大於** 子陣列長度的一半。

### 限制條件
- $1 \le nums.length \le 10^5$ (測資量級大幅提升，$\mathcal{O}(N^2)$ 將會 TLE)
- $1 \le nums[i], target \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-25：初次提交 (+1/-1 數值映射與 BIT 順序對優化)](#2026-06-25-初次提交)

---

## 💡 2026-06-25 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在 Range II 中，陣列長度高達 $10^5$，必須尋找 $\mathcal{O}(N \log N)$ 的最佳化演算法。

**核心破局點 1：布林轉換為極性映射 (+1 / -1)**
如何快速判斷一個子陣列中 `target` 的數量是否「嚴格大於」其他元素的總和？
我們進行數值映射：
- 若 `nums[k] == target`，權重設為 `1`。
- 若 `nums[k] != target`，權重設為 `-1`。
如此一來，一個區間 `[i, j]` 滿足多數條件 $\iff$ 該區間的權重總和 $> 0$。

**核心破局點 2：前綴和與順序對 (Inversion-like Pairs)**
利用前綴和 `pre`，區間 `[i, j]` 的總和可以表示為 `pre[j] - pre[i-1]`。
條件轉化為：`pre[j] - pre[i-1] > 0` $\implies$ **`pre[j] > pre[i-1]`**。
這意味著，對於每一個位置 `j`，我們只需要去尋找「在它之前出現過，且數值嚴格小於 `pre[j]` 的前綴和數量」。這正是經典的使用樹狀陣列 (Binary Indexed Tree) 來解決的問題。

**核心破局點 3：平移前綴和以適應 BIT**
樹狀陣列的索引必須是 **正整數 ( $\ge 1$ )**。
但 `pre` 陣列中的值最小可能為 `-n` (當整個陣列都沒有 `target` 時)。
因此，我們在操作 BIT 之前，統一將所有前綴和加上一個 **偏移量 (Offset)** `n + 1`。這樣最小的 `-n` 就會映射到 `1`，完美符合 BIT 的 1-based 特性。所需的 BIT 總大小則為 `2n + 5`。

### 🛠️ 解題思路 (Approach)
1. **初始化 BIT**：計算最大可能空間 $N = 2 \times n + 5$，並初始化長度為 $N$ 的 `BIT` 陣列。
2. **建構極性前綴和**：宣告 `pre` 陣列，遍歷 `nums`，遇到 `target` 加 1，否則減 1。
3. **動態查詢與更新**：
   - 遍歷 `pre` 陣列中的每一個元素（包含初始的 `pre[0] = 0`）。
   - 對於當前的值 `pre[i]`，加上偏移量轉換為 `val = pre[i] + n + 1`。
   - 呼叫 `qry(val - 1)`，查詢在此之前出現過，且值**嚴格小於** `val` 的數量，累加到 `rt` 中。
   - 呼叫 `upd(val)`，將當前的值註冊到樹狀陣列中，供後續的元素查詢。
4. **回傳總數 `rt`**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是陣列長度。我們遍歷 $N+1$ 個前綴和，每次進行 BIT 的 `qry` 與 `upd` 操作，耗時皆為 $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了長度為 $2N+5$ 的樹狀陣列與長度為 $N+1$ 的前綴和陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> BIT;
    int N;
    
    // 樹狀陣列：查詢區間 [1, idx] 的元素總數
    long long qry(int idx) {
        long long c = 0;
        while(idx > 0) {
            c += BIT[idx];
            idx -= (idx & -idx); // 減去 LSB，往左上移動
        }
        return c;
    }
    
    // 樹狀陣列：單點更新，將 idx 位置的計數 + 1
    void upd(int idx) {
        while(idx < N) {
            ++BIT[idx];
            idx += (idx & -idx); // 加上 LSB，往右上移動
        }
    }
    
    long long countMajoritySubarrays(vector<int>& nums, int target) {
        int n = nums.size();
        
        // 前綴和範圍為 [-n, n]，加上偏移量 n+1 後，映射至 [1, 2n+1]
        // 故 BIT 大小開 2n+5 足矣
        N = 2 * n + 5;
        BIT.resize(N, 0);

        // 1. 建構極性前綴和
        vector<int> pre(n + 1, 0);
        for(int i = 0; i < n; ++i) {
            pre[i + 1] = pre[i] + (nums[i] == target ? 1 : -1);
        }
        
        long long rt = 0;
        
        // 2. 利用 BIT 動態計算順序對
        // 遍歷包含 pre[0] 在內的所有前綴和
        for(int i = 0; i <= n; ++i) {
            // 加上偏移量，保證數值 >= 1
            int val = pre[i] + n + 1;
            
            // 查詢之前出現過，且數值嚴格小於 val (即查詢 1 ~ val-1) 的前綴和數量
            rt += qry(val - 1);
            
            // 將當前的前綴和加入 BIT 中
            upd(val);
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