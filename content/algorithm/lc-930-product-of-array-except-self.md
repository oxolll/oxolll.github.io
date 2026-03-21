---
title: "LeetCode 930. Binary Subarrays With Sum"
date: 2026-03-21T19:00:00+08:00
lastmod: 2026-03-21T19:00:00+08:00
difficulty: 1591
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash table", "prefix sum"]
keywords: ["LC930", "1591", "Medium", "Binary Subarrays", "Prefix Sum"]
description: "LeetCode 第 930 題：Binary Subarrays With Sum。難度評分：1591。探討如何利用前綴和與 Hash Table，以 O(N) 時間複雜度高效計算總和為特定目標值的子陣列數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 930](https://leetcode.com/problems/binary-subarrays-with-sum/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1591)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個二進位陣列 `nums`（僅包含 0 和 1）以及一個整數 `goal`。
請回傳總和剛好等於 `goal` 的非空子陣列數量。

子陣列 (Subarray) 是陣列中連續的一段序列。

### 限制條件
- $1 \le nums.length \le 3 \times 10^4$
- `nums[i]` 不是 0 就是 1。
- $0 \le goal \le nums.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (前綴和與 Hash Table)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要尋找連續區間總和等於 `goal` 的子陣列，若使用雙層迴圈暴力枚舉，時間複雜度為 $\mathcal{O}(N^2)$。
利用前綴和 (Prefix Sum) 的性質：若從索引 $0$ 到 $i$ 的總和為 $sum_i$，從索引 $0$ 到 $j$ 的總和為 $sum_j$（其中 $j < i$），則從索引 $j+1$ 到 $i$ 的子陣列總和即為 $sum_i - sum_j$。
題目要求子陣列總和為 `goal`，即 $sum_i - sum_j = goal$。這可以移項轉換為 $sum_j = sum_i - goal$。
因此，我們只需在遍歷陣列計算當前前綴和 $sum_i$ 的同時，利用 Hash Table 查詢過去是否出現過總和為 $sum_i - goal$ 的前綴和，若有，其出現次數即代表以當前索引 $i$ 結尾的合法子陣列數量。

### 🛠️ 解題思路 (Approach)
1. **初始化資料結構**：
   - 宣告 `unordered_map<int, int> mp` 來記錄每種前綴和出現的次數。
   - **關鍵初始化**：設定 `mp[0] = 1`。這是為了處理子陣列是從索引 0 開始計算的狀況。若當前的前綴和本身就等於 `goal`（即 $sum - goal = 0$），這也是一組有效的子陣列，必須被計算進去。
2. **單次遍歷與計數**：
   - 宣告 `sum` 記錄當前的累積總和，`rt` 記錄滿足條件的子陣列總數。
   - 遍歷 `nums` 陣列：
     - 若 `nums[i] == 1`，將 `sum` 加 1。
     - 計算所需的目標前綴和 `key = sum - goal`。
     - 若 `key` 存在於 `mp` 中，將其對應的次數累加至 `rt`。
     - 將當前的前綴和 `sum` 記錄至 `mp`，出現次數加 1 (`++mp[sum]`)。
3. **回傳結果**：迴圈結束後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。遍歷陣列一次，每次對 `unordered_map` 的查詢與插入操作平均時間複雜度皆為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N)$。在最差情況下（陣列全為 1），前綴和會有 $N$ 種不同的值，`unordered_map` 需要儲存 $N$ 個鍵值對。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numSubarraysWithSum(vector<int>& nums, int goal) {
        unordered_map<int, int> mp; // sum -> cnt
        mp[0] = 1;
        int sum = 0;
        int rt = 0;
        
        for(int i = 0; i < nums.size(); ++i) {
            if(nums[i] == 1) {
                ++sum;
            }
            
            int key = sum - goal;
            // 查詢過去是否出現過符合條件的前綴和
            if(mp.find(key) != mp.end()) {
                rt += mp[key];
            }
            
            // 更新當前前綴和的出現次數
            ++mp[sum];
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