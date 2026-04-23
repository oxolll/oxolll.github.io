---
title: "LeetCode 561. Array Partition"
date: 2026-04-22T23:05:00+08:00
lastmod: 2026-04-22T23:05:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting"]
keywords: ["LC561", "Easy", "Array Partition", "Greedy", "Sorting"]
description: "LeetCode 第 561 題：Array Partition。探討如何利用貪婪策略 (Greedy)，透過陣列排序將相近數值進行配對，以 O(N log N) 的時間複雜度最大化數對最小值的總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 561](https://leetcode.com/problems/array-partition/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $2n$ 的整數陣列 `nums`。請你將這些數字分成 $n$ 對，即 $(a_1, b_1), (a_2, b_2), \dots, (a_n, b_n)$。
對於每一對數字，取其最小值 $\min(a_i, b_i)$。
你的目標是尋找一種配對方式，使得所有這些最小值的**總和最大化**，並回傳該最大總和。

### 限制條件
- $1 \le n \le 10^4$
- $nums.length == 2 \times n$
- $-10^4 \le nums[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-22：初次提交 (貪婪配對與排序)](#2026-04-22-初次提交)

---

## 💡 2026-04-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要使所有對的 $\min(a, b)$ 總和最大化，我們可以採用**貪婪策略 (Greedy Strategy)**。
思考一個極端情況：如果我們把全陣列最大的數字，跟全陣列最小的數字配成一對，那麼取最小值時，最大的數字就等同於被「犧牲」了，對總和毫無貢獻。
為了**將大數字的浪費降到最低**，我們應該「讓數值相近的數字配對在一起」。這樣一來，每一對中被捨棄的數字，都只會比留下來的數字大一點點而已。

具體作法非常簡單：
1. 將陣列由小到大**排序 (Sorting)**。
2. 排序後，相鄰的兩個數字即為一對（例如索引 $0, 1$ 一對；$2, 3$ 一對）。
3. 在每一對中，最小值必定出現在偶數索引 ($0, 2, 4, \dots$) 上。我們只需將所有偶數索引的數字加總即可。

### 🛠️ 解題思路 (Approach)
1. **陣列排序**：呼叫 `sort(nums.begin(), nums.end())`，將陣列轉換為遞增數列。
2. **走訪與加總**：
   - 宣告變數 `rt = 0` 記錄總和。
   - 使用 `for` 迴圈走訪陣列，每次步進值為 $2$ (`i += 2`)。
   - 將每個偶數索引的元素 `nums[i]` 累加至 `rt` 中。
3. **回傳結果**：回傳累加總和 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是陣列的長度。排序陣列需要 $\mathcal{O}(N \log N)$，後續走訪陣列累加僅需 $\mathcal{O}(N)$，由排序主導整體時間。
- **空間複雜度**: $\mathcal{O}(1)$ 或 $\mathcal{O}(\log N)$。取決於 C++ 內部 `std::sort` 實作所需的遞迴堆疊空間，在此未宣告額外的資料結構。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int arrayPairSum(vector<int>& nums) {
        // 1. 貪婪策略：將陣列排序，讓數值相近的元素相鄰
        sort(nums.begin(), nums.end());
        
        int rt = 0;
        
        // 2. 每兩個取一個（即取排序後每對的最小值，位於偶數索引）
        for(int i = 0; i < nums.size(); i += 2) {
            rt += nums[i];
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