---
title: "LeetCode 3912. Valid Elements in an Array"
date: 2026-04-26T15:23:00+08:00
lastmod: 2026-04-26T15:23:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum"]
keywords: ["LC3912", "Easy", "Valid Elements", "Prefix Max", "Suffix Max"]
description: "LeetCode 第 3912 題：Valid Elements in an Array。探討如何利用前綴最大值與後綴最大值 (Prefix/Suffix Maximum) 的預處理技巧，將 O(N^2) 的尋找降維成 O(N) 單次掃描，並搭配 vector::reserve 進行記憶體配置優化。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3912](https://leetcode.com/problems/valid-elements-in-an-array/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Max / Suffix Max`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
我們定義一個元素是「有效的 (Valid)」，當它滿足以下**至少一個**條件：
1. 該元素**嚴格大於**其左邊所有的元素。
2. 該元素**嚴格大於**其右邊所有的元素。

請找出陣列中所有有效的元素，並將它們以陣列形式回傳（保持原有的相對順序）。

### 限制條件
- $1 \le nums.length \le 10^5$ (推測值)
- $1 \le nums[i] \le 10^9$ (假設皆為正整數)
</details>

---

## 📝 歷次打卡與更新
- [2026-04-26：初次提交 (前後綴極值預處理)](#2026-04-26-初次提交)

---

## 💡 2026-04-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要判斷一個元素是否嚴格大於左邊或右邊的所有元素，最暴力的解法是雙層迴圈，時間複雜度 $\mathcal{O}(N^2)$。
為了優化至 $\mathcal{O}(N)$，我們需要利用 **前綴與後綴極值 (Prefix / Suffix Maximum)** 的概念：
- 判斷「是否嚴格大於左邊所有元素」，等價於判斷「是否嚴格大於左側的前綴最大值」。
- 判斷「是否嚴格大於右邊所有元素」，等價於判斷「是否嚴格大於右側的後綴最大值」。

因此，我們可以先由右至左掃描一次陣列，建立一個「後綴最大值」陣列。接著再由左至右掃描一次，同時利用一個變數維護「前綴最大值」，並在當下判斷該元素是否符合條件即可。

### 🛠️ 解題思路 (Approach)
1. **建立後綴最大值陣列**：
   - 宣告長度為 $n+1$ 的陣列 `backmx`，並初始化為 `0` (預設邊界值)。
   - 從陣列最右側 ($n-1$) 倒序掃描至 $0$，更新 `backmx[i] = max(backmx[i+1], nums[i])`。
2. **走訪並驗證條件**：
   - 宣告解答陣列 `rt`，並使用 `rt.reserve(n)` 預先分配記憶體，避免動態擴充陣列帶來的效能浪費。
   - 宣告變數 `mx = 0` 作為滾動維護的前綴最大值。
   - 由左至右走訪 `nums`，對於每個元素 `nums[i]`：
     - 如果 `mx < nums[i]` (大於左邊所有) **或 (`||`)** `nums[i] > backmx[i+1]` (大於右邊所有)，則該元素有效，推入 `rt`。
     - 更新左側前綴最大值 `mx = max(mx, nums[i])`。
3. **回傳結果**：回傳 `rt` 陣列。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。建構後綴最大值陣列需掃描一次，驗證元素需掃描一次，總共 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(N)$。使用了大小為 $N+1$ 的 `backmx` 陣列來記錄後綴狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> findValidElements(vector<int>& nums) {
        int n = nums.size();
        
        // 1. 預處理：計算每個位置右側的「後綴最大值」
        vector<int> backmx(n + 1, 0);
        for(int i = n - 1; i >= 0; --i) {
            backmx[i] = max(backmx[i + 1], nums[i]);
        }

        // 2. 預分配記憶體，避免 push_back 觸發動態重配
        vector<int> rt; 
        rt.reserve(n);
        
        int mx = 0; // 滾動維護左側的「前綴最大值」
        
        // 3. 走訪陣列驗證有效性
        for(int i = 0; i < n; ++i) {
            // 條件：嚴格大於左邊最大值，或嚴格大於右邊最大值
            if(mx < nums[i] || nums[i] > backmx[i + 1]) {
                rt.push_back(nums[i]);
            }
            // 更新左邊的前綴最大值
            mx = max(mx, nums[i]);
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