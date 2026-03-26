---
title: "LeetCode 1827. Minimum Operations to Make the Array Increasing"
date: 2026-03-26T16:47:00+08:00
lastmod: 2026-03-26T16:47:00+08:00
difficulty: 1314
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy"]
keywords: ["LC1827", "1314", "Easy", "Strictly Increasing", "Greedy"]
description: "LeetCode 第 1827 題：Minimum Operations to Make the Array Increasing。難度評分：1314。探討如何利用貪婪策略，以 O(N) 時間複雜度計算使陣列嚴格遞增所需的最小操作次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1827](https://leetcode.com/problems/minimum-operations-to-make-the-array-increasing/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1314)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。在一次操作中，你可以選擇陣列中的任意一個元素，並將其數值增加 1。
請回傳使 `nums` 成為**嚴格遞增**陣列所需的**最少**操作次數。
嚴格遞增的定義為：對於所有 $0 \le i < nums.length - 1$，皆滿足 `nums[i] < nums[i+1]`。

### 限制條件
- $1 \le nums.length \le 5000$
- $1 \le nums[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (單次走訪貪婪累加法)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要使陣列以最少的操作次數達到嚴格遞增狀態，必須遵守貪婪策略 (Greedy Strategy)：當 `nums[i] <= nums[i-1]` 時，`nums[i]` 應該僅增加至剛好大於 `nums[i-1]` 的最小值，即 `nums[i-1] + 1`。任何過度的增加都會導致後續元素需要更多次數的操作才能維持遞增狀態。

### 🛠️ 解題思路 (Approach)
1. 宣告變數 `rt = 0`，用於累加操作總次數。
2. 從索引 `i = 1` 開始線性走訪陣列 `nums`。
3. 檢查當前元素是否小於或等於前一個元素 (`nums[i] <= nums[i-1]`)。
4. 若條件成立：
   - 計算達到嚴格遞增所需的最小差值，並將其加入總次數中：`rt += (nums[i-1] - nums[i] + 1)`。
   - 將當前元素更新為合法的最小值：`nums[i] = nums[i-1] + 1`，以便提供下一個元素正確的參考基準。
5. 走訪結束後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。僅需對陣列進行一次線性走訪，每次操作皆為常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。直接在原陣列 `nums` 上進行數值更新，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minOperations(vector<int>& nums) {
        int rt = 0;
        
        for(int i = 1; i < nums.size(); ++i) {
            // 若當前元素未嚴格大於前一元素
            if(nums[i] <= nums[i-1]) {
                // 計算並累加所需的操作次數
                rt += nums[i-1] - nums[i] + 1;
                // 更新當前元素為合法的最小值
                nums[i] = nums[i-1] + 1;
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