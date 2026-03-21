---
title: "LeetCode 1685. Sum of Absolute Differences in a Sorted Array"
date: 2026-03-21T19:47:00+08:00
lastmod: 2026-03-21T19:47:00+08:00
difficulty: 1495
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix sum", "math"]
keywords: ["LC1685", "1495", "Medium", "Absolute Differences", "Prefix Sum", "Math Derivation"]
description: "LeetCode 第 1685 題：Sum of Absolute Differences in a Sorted Array。難度評分：1495。探討如何利用陣列已排序的特性拆解絕對值，並透過前綴和與嚴謹的代數推導，將 O(N^2) 的運算完美降維至 O(N) 的線性解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1685](https://leetcode.com/problems/sum-of-absolute-differences-in-a-sorted-array/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1495)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個以**非遞減順序**排序的整數陣列 `nums`。
請建構並回傳一個長度相同的整數陣列 `result`，其中 `result[i]` 等於 `nums[i]` 與陣列中其他所有元素之間**絕對差的總和**。

換句話說，`result[i]` 的計算公式為：
`result[i] = sum(|nums[i] - nums[j]|)` 對於所有 `0 <= j < nums.length` 且 `j != i`（實際上 `j == i` 時差值為 0，亦可包含在內）。

### 限制條件
- $2 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^4$
- `nums` 已按照非遞減順序排序。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (前綴和與代數推導法)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
如果對每個元素都去跑一次迴圈計算絕對差，時間複雜度高達 $\mathcal{O}(N^2)$，對於 $N = 10^5$ 必定會 Time Limit Exceeded (TLE)。
這題的破局點在於題目給定的**「已排序 (Sorted)」**特性。
當我們站在索引 $i$ 時：
1. 它左邊的元素 ($j < i$) 必然 $\le nums[i]$。因此絕對值可拆解為正值：$nums[i] - nums[j]$。
2. 它右邊的元素 ($k > i$) 必然 $\ge nums[i]$。因此絕對值可拆解為正值：$nums[k] - nums[i]$。

透過將絕對值拆解，原本的問題變成單純的區間總和加減。只要預先計算好**前綴和 (Prefix Sum)**，我們就能在 $\mathcal{O}(1)$ 的時間內瞬間算出左半部與右半部的總和差值。

### 🛠️ 解題思路 (Approach)
1. **建立前綴和陣列**：
   - 宣告 `pre` 陣列，`pre[i]` 記錄從 `nums[0]` 到 `nums[i]` 的累積和。
2. **數學公式推導**：
   對於索引 $i$，其絕對差總和可以分為左右兩塊：
   - **左半部差值和** ($j < i$)：共有 $i$ 個元素。
     $$\sum_{j=0}^{i-1} (nums[i] - nums[j]) = i \cdot nums[i] - \sum_{j=0}^{i-1} nums[j]$$
     對應程式碼：`i * nums[i] - pre[i-1]` (若 `i > 0`)。
   - **右半部差值和** ($k > i$)：共有 $n - 1 - i$ 個元素。
     $$\sum_{k=i+1}^{n-1} (nums[k] - nums[i]) = \sum_{k=i+1}^{n-1} nums[k] - (n - 1 - i) \cdot nums[i]$$
     對應程式碼：`(pre.back() - pre[i]) - (n - 1 - i) * nums[i]`。
   - **合併化簡**：
     將左右兩塊相加，並把 `nums[i]` 的係數合併：
     $$i - (n - 1 - i) = 2i - n + 1$$
     最終公式為：`右區間和 - 左區間和 + nums[i] * (2i - n + 1)`。
3. **計算結果**：
   - 遍歷陣列，直接套用上述推導出的公式。由於使用了三元運算子 `(i > 0 ? pre[i-1] : 0)`，完美避開了索引 $0$ 的越界問題。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。共計執行兩次陣列走訪：一次計算前綴和，一次套用公式結算，皆為線性時間。
- **空間複雜度**: $\mathcal{O}(N)$。使用了一個長度為 $N$ 的 `pre` 陣列來儲存前綴和。（註：若進階優化，可僅使用單一變數維護左側前綴和，以達到 $\mathcal{O}(1)$ 額外空間）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> getSumAbsoluteDifferences(vector<int>& nums) {
        int n = nums.size();
        vector<int> pre(n, 0);
        
        // 1. 計算前綴和
        for(int i = 0; i < n; ++i) {
            pre[i] = (i > 0 ? pre[i-1] : 0) + nums[i];
        }
            
        vector<int> rt(n, 0);
        
        // 2. 利用推導公式計算每個位置的絕對差總和
        for(int i = 0; i < n; ++i) {
            // 右區間和：pre.back() - pre[i]
            // 左區間和：(i > 0 ? pre[i-1] : 0)
            // 係數合併：i - (n - 1 - i) = 2*i - n + 1
            rt[i] = (pre.back() - pre[i]) - (i > 0 ? pre[i-1] : 0) + nums[i] * (2 * i - n + 1);
        }
        
        return rt;
    }
};

/* 數學推導註記：
   rt[i] = sum(nums[i] - nums[j]) for j < i 
         + sum(nums[k] - nums[i]) for k > i
         
   rt[i] = pre.back() - pre[i] - nums[i] * (n - 1 - i) 
         + nums[i] * i - pre[i-1] 
         
   nums[i] 的合併係數：-(n - 1) + i + i = 2i - n + 1
*/
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