---
title: "LeetCode 2300. Successful Pairs of Spells and Potions"
date: 2026-03-30T16:52:00+08:00
lastmod: 2026-03-30T16:52:00+08:00
difficulty: 1476
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary search", "sorting", "two pointers"]
keywords: ["LC2300", "1476", "Medium", "Successful Pairs", "Binary Search", "Lower Bound"]
description: "LeetCode 第 2300 題：Successful Pairs of Spells and Potions。難度評分：1476。探討如何利用陣列排序搭配二分搜尋 (lower_bound)，以 O((M+N) log M) 時間複雜度快速找出滿足乘積條件的配對數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2300](https://leetcode.com/problems/successful-pairs-of-spells-and-potions/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1476)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個正整數陣列 `spells` 和 `potions`，長度分別為 $n$ 和 $m$，其中 `spells[i]` 代表第 $i$ 個咒語的能量，`potions[j]` 代表第 $j$ 瓶藥水的能量。
同時給定一個整數 `success`。一個咒語與一瓶藥水若滿足 `spell * potion >= success`，則視為一對成功的組合。
請回傳一個長度為 $n$ 的整數陣列 `pairs`，其中 `pairs[i]` 是能與第 $i$ 個咒語組成成功配對的藥水數量。

### 限制條件
- $n == spells.length$
- $m == potions.length$
- $1 \le n, m \le 10^5$
- $1 \le spells[i], potions[i] \le 10^5$
- $1 \le success \le 10^{10}$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-30：初次提交 (排序與 lower_bound 搜尋)](#2026-03-30-初次提交)

---

## 💡 2026-03-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
若對每個咒語都線性掃描所有藥水，時間複雜度將高達 $\mathcal{O}(N \times M)$，必定導致 TLE。
為了加速查找，我們可以先將 `potions` 陣列進行遞增排序。對於每一個 `spells[i]`，我們需要尋找 `potions` 中大於等於 $\lceil \frac{success}{spells[i]} \rceil$ 的元素個數。
由於 `potions` 已排序，我們可以直接利用二分搜尋 (`std::lower_bound`) 找到符合條件的第一個藥水的索引 `k`。從索引 `k` 到陣列尾端的所有藥水皆能形成成功配對，數量即為 `m - k`。

### 🛠️ 解題思路 (Approach)
1. **防溢位與排序**：
   - 宣告陣列 `sorted`，將 `potions` 複製並轉換為 `long long` 型別以防止後續運算溢位。
   - 呼叫 `sort` 將 `sorted` 進行遞增排序。
2. **計算並搜尋**：
   - 宣告結果陣列 `rt`。
   - 走訪 `spells` 陣列中的每一個元素 `x`。
   - 計算目標最低藥水能量 `tar = ceil(success * 1.0 / x)`。*(註：因 `success` 最大為 $10^{10}$，`double` 的 53-bit 精確度足以精確表示，無精度遺失風險；亦可改用純整數運算 `(success + x - 1) / x`)*。
   - 利用 `lower_bound` 在 `sorted` 中尋找第一個 $\ge tar$ 的元素位置，並減去 `begin()` 取得索引 `k`。
   - 符合條件的藥水數量為 `n - k` (此處的 `n` 為藥水陣列的長度)，將其推入 `rt`。
3. **回傳結果**：回傳陣列 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}((M + N) \log M)$。其中 $M$ 是 `potions` 的長度，$N$ 是 `spells` 的長度。排序 `potions` 耗時 $\mathcal{O}(M \log M)$，對於 $N$ 個咒語各執行一次 $\mathcal{O}(\log M)$ 的二分搜尋，耗時 $\mathcal{O}(N \log M)$。
- **空間複雜度**: $\mathcal{O}(M)$。配置了長度為 $M$ 的 `sorted` 陣列來儲存 64-bit 整數型態的藥水能量。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> successfulPairs(vector<int>& spells, vector<int>& potions, long long success) {
        vector<int> rt;
        
        // 1. 轉換型態並排序藥水陣列
        vector<long long> sorted(potions.begin(), potions.end());
        sort(sorted.begin(), sorted.end());
        
        int n = potions.size(); // 藥水總數
        
        // 2. 針對每個咒語進行二分搜尋
        for(auto x : spells) {
            // 計算所需的最低藥水能量 (使用浮點數 ceil 或純整數寫法皆可)
            long long tar = ceil(success * 1.0 / x);
            
            // 尋找第一個大於等於 tar 的藥水索引
            auto k = lower_bound(sorted.begin(), sorted.end(), tar) - sorted.begin();
            
            // 計算並推入合法的藥水數量
            rt.push_back(n - k);
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