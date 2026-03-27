---
title: "LeetCode 2294. Partition Array Such That Maximum Difference Is K"
date: 2026-03-27T12:30:00+08:00
lastmod: 2026-03-27T12:30:00+08:00
difficulty: 1416
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting"]
keywords: ["LC2294", "1416", "Medium", "Partition Array", "Greedy", "Sorting"]
description: "LeetCode 第 2294 題：Partition Array Such That Maximum Difference Is K。難度評分：1416。探討如何利用排序與貪婪策略，以 O(N log N) 時間複雜度將陣列劃分為最少數量的子序列，使得每個子序列內的最大差值不超過 K。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2294](https://leetcode.com/problems/partition-array-such-that-maximum-difference-is-k/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1416)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums` 和一個整數 `k`。你可以將 `nums` 劃分為一個或多個子序列 (Subsequence)，使得每個子序列中的最大值與最小值之差不超過 `k`。
請回傳所需劃分的**最少**子序列數量。

*(註：子序列是從原陣列中刪除某些元素或不刪除元素後，剩餘元素保持原相對順序所形成的新序列。但本題僅關注數值差異，不限制元素的原始相對順序。)*

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 10^5$
- $0 \le k \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (排序與線性貪婪分段)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
題目要求將陣列元素分組，條件為同一組內的最大值與最小值之差 $\le k$。由於我們只關心數值大小，第一步必須將陣列進行遞增排序。
排序後，利用貪婪策略 (Greedy Strategy)：從未分組的最小元素開始作為新群組的基準值 (`left`)，並盡可能將後續數值大於等於 `left` 且差值在 $k$ 以內的元素納入同一組。當遇到第一個與 `left` 差值大於 $k$ 的元素時，立即截斷當前群組，並以該新元素作為下一個群組的基準值，遞迴此過程直到走訪完畢。

### 🛠️ 解題思路 (Approach)
1. **排序**：呼叫 `sort(nums.begin(), nums.end())` 對陣列進行遞增排序。
2. **貪婪分段**：
   - 宣告 `rt = 0` 記錄子序列的數量。取得陣列長度 `n`。
   - 建立迴圈遍歷陣列。每次進入外層迴圈時，代表開啟一個新的子序列，將 `rt` 加 1。
   - 記錄當前子序列的最小值 `left = nums[i]`。
   - 使用 `while` 迴圈向右探測：只要索引未越界 (`i < n - 1`) 且下一個元素與 `left` 的絕對差值小於等於 $k$ (`abs(nums[i+1] - left) <= k`)，就將 `i` 遞增，將其納入當前子序列。
3. **回傳結果**：迴圈結束後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是陣列長度。排序操作耗時 $\mathcal{O}(N \log N)$，後續的分段走訪每個元素僅被訪問一次，時間為 $\mathcal{O}(N)$。整體由排序主導。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(1)$。取決於 `std::sort` 的內部遞迴堆疊深度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int partitionArray(vector<int>& nums, int k) {
        // 1. 遞增排序，將數值相近的元素集中
        sort(nums.begin(), nums.end());
        
        int rt = 0;
        int n = nums.size();
        
        // 2. 貪婪分段
        for(int i = 0; i < n; ++i) {
            ++rt; // 開啟新的子序列
            int left = nums[i]; // 記錄當前子序列的最小值
            
            // 將所有與最小值的差值在 k 以內的元素納入同一組
            while(i < n - 1 && abs(nums[i+1] - left) <= k) {
                ++i;
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