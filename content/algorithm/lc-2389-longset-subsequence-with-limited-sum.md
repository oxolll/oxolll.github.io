---
title: "LeetCode 2389. Longest Subsequence With Limited Sum"
date: 2026-03-20T23:45:00+08:00
lastmod: 2026-03-20T23:45:00+08:00
difficulty: 1387
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Greedy", "Prefix Sum", "Binary Search", "Sorting"]
keywords: ["LC2389", "1387", "Easy", "Subsequence", "Limited Sum", "upper_bound"]
description: "LeetCode 第 2389 題：Longest Subsequence With Limited Sum。難度評分：1387。探討如何利用貪心策略選取最小元素，並結合前綴和與二分搜尋 (upper_bound) 高效處理多筆查詢。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2389](https://leetcode.com/problems/longest-subsequence-with-limited-sum/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1387)</span>    
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Prefix Sum` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的整數陣列 `nums`，以及一個長度為 $m$ 的整數陣列 `queries`。
請回傳一個長度為 $m$ 的陣列 `answer`，其中 `answer[i]` 是 `nums` 中元素總和不大於 `queries[i]` 的**最長子序列**的長度。

*子序列 (Subsequence)* 是一個從原陣列中刪除某些（或不刪除）元素後，不改變剩餘元素相對順序所形成的新陣列。但在本題中，因為我們只在乎子序列的「元素總和」與「長度」，原陣列元素的順序並不影響結果。

### 限制條件
- $n == nums.length$
- $m == queries.length$
- $1 \le n, m \le 1000$
- $1 \le nums[i], queries[i] \le 10^6$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-20：初次提交 (排序、In-place 前綴和與 upper_bound 二分搜)](#2026-03-20-初次提交)

---

## 💡 2026-03-20 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要使子序列的長度最大化，同時確保元素總和不超過特定限制，依據貪心 (Greedy) 策略，我們必須優先選取數值最小的元素。
由於題目所求為子序列的和，元素的原始順序並不重要。我們可以先將陣列進行遞增排序。
對於多次的獨立查詢 `queries[i]`，若每次都重新從頭累加必定會導致 $\mathcal{O}(N \times M)$ 的時間複雜度。因此，我們先計算排序後陣列的「前綴和 (Prefix Sum)」。
因為原始元素皆為正整數，其前綴和陣列必然為嚴格單調遞增。對於給定的查詢值 $q$，我們只需在單調遞增的前綴和陣列中，尋找最後一個 $\le q$ 的元素位置，這可以透過二分搜尋法 (Binary Search) 在 $\mathcal{O}(\log N)$ 時間內完成。

### 🛠️ 解題思路 (Approach)
1. **貪心排序**：將原陣列 `nums` 進行遞增排序 (`sort`)。
2. **建構前綴和**：遍歷排序後的 `nums`，執行 `nums[i] += nums[i-1]`。此 In-place 操作直接將 `nums` 轉換為前綴和陣列，空間複雜度為 $\mathcal{O}(1)$。
3. **二分搜尋查詢**：
   - 遍歷 `queries` 陣列。
   - 利用 STL 的 `upper_bound` 尋找前綴和陣列中第一個嚴格大於 `q` 的指標。
   - 將該指標減去起點指標 `nums.begin()`，所得到的距離即為不大於 `q` 的最大元素數量。
   - 將計算結果推入結果陣列 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N + M \log N)$。其中 $N$ 為 `nums` 長度，$M$ 為 `queries` 長度。排序花費 $\mathcal{O}(N \log N)$，建立前綴和為 $\mathcal{O}(N)$，對於 $M$ 次查詢，每次二分搜尋花費 $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(1)$。除了儲存結果的 `rt` 陣列外，前綴和操作直接覆寫原陣列，僅使用常數額外空間（不計算排序底層的遞迴堆疊）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> answerQueries(vector<int>& nums, vector<int>& queries) {
        int n = nums.size();
        
        // 1. 貪心策略：排序以優先取得最小元素
        sort(nums.begin(), nums.end());
        
        // 2. In-place 轉換為前綴和陣列
        for(int i = 1; i < n; ++i) {
            nums[i] += nums[i-1];
        }
        
        vector<int> rt; 
        rt.reserve(queries.size());
        
        // 3. 處理每筆查詢
        for(auto q : queries) {
            // upper_bound 尋找第一個大於 q 的位置，其索引即為不大於 q 的元素總數
            rt.push_back(upper_bound(nums.begin(), nums.end(), q) - nums.begin());
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