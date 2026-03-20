---
title: "LeetCode 2848. Points That Intersect With Cars"
date: 2026-03-21T00:02:00+08:00
lastmod: 2026-03-21T00:02:00+08:00
difficulty: 1229
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Sorting"]
keywords: ["LC2848", "1229", "Easy", "Merge Intervals", "Sweep Line"]
description: "LeetCode 第 2848 題：Points That Intersect With Cars。難度評分：1229。探討如何利用排序與區間合併 (Merge Intervals) 技巧，在 O(N log N) 時間內計算多個重疊線段所覆蓋的總整數點數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2848](https://leetcode.com/problems/points-that-intersect-with-cars/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1229)</span>   
> **核心主題**：`Array` $\cdot$ `Sorting` $\cdot$ `Merge Intervals`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個以 0 為起始索引的二維整數陣列 `nums`，代表數線上的車輛停放位置。對於每一輛車 $i$，$nums[i] = [start_i, end_i]$ 代表該車輛停放在數線上從 $start_i$ 到 $end_i$（包含首尾）的位置。

請回傳數線上至少被一輛車覆蓋的**整數點**總數。

### 限制條件
- $1 \le nums.length \le 100$
- $nums[i].length == 2$
- $1 \le start_i \le end_i \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (排序與區間合併法)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的本質是求「多個可能重疊的一維線段，其聯集 (Union) 的總長度/點數」。
如果在測資範圍極小的情況下（例如 $1 \dots 100$），可以使用一個 Hash Set 或是長度為 100 的布林陣列直接把車子停放的點全部標記為 `true` 再去計算。
然而，為了適應更大範圍的測資，最佳的泛用解法是**區間合併 (Merge Intervals)**：
1. 先將所有線段依照「起點」從小到大排序。
2. 維護一個「當前正在擴張的連續區間」。
3. 依序檢查後面的線段：如果起點落在當前區間內，就嘗試將當前區間的終點向右延伸；如果起點超出了當前區間，就代表當前區間已經斷開，我們可以將其長度結算，並將新線段設為下一個正在擴張的區間。

### 🛠️ 解題思路 (Approach)
1. **排序**：對 `nums` 進行排序。C++ 的 `sort` 預設會優先比對 `vector` 的第一個元素（起點），若相同則比對第二個元素（終點），這完全符合我們的需求。
2. **初始化狀態**：
   - `rt = 0` 記錄覆蓋的總點數。
   - `l = -1, r = -2` 記錄當前正在處理的區間。這是一個巧妙的虛擬區間設定，因為計算點數的公式為 $r - l + 1$，代入後會得到 $-2 - (-1) + 1 = 0$，確保第一圈結算時不會產生錯誤的點數。
3. **線性掃描與合併**：
   - 遍歷排序後的 `nums` 陣列。
   - **無重疊 (斷開)**：若 `nums[i][0] > r`，代表新區間與當前區間沒有交集。此時結算當前區間包含的點數 `rt += r - l + 1`，並將當前區間更新為新區間 `l = nums[i][0]`, `r = nums[i][1]`。
   - **有重疊 (延伸)**：若 `nums[i][0] <= r`，代表有交集。此時不需要更新 `l`，只需確認是否需要延伸右邊界 `r = max(r, nums[i][1])`。
4. **最終結算**：迴圈結束後，最後一個區間的長度還沒有被加進去，因此在 `return` 前必須補上 `rt += r - l + 1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。主要的效能瓶頸在於對 `nums` 進行排序。後續的區間合併只需 $\mathcal{O}(N)$ 的一次線性掃描。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(1)$。取決於 C++ 內部 `std::sort` 實作時所需的遞迴呼叫堆疊空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numberOfPoints(vector<vector<int>>& nums) {
        // 依據區間起點進行遞增排序
        sort(nums.begin(), nums.end());
        int rt = 0;
        
        // 設定虛擬的初始區間，長度為 (-2) - (-1) + 1 = 0
        int l = -1, r = -2; 
        
        for(int i = 0; i < nums.size(); ++i) {
            // 若新區間的起點大於當前區間的終點，表示發生斷層
            if(nums[i][0] > r) {
                // 結算上一段連續區間的覆蓋點數
                rt += r - l + 1;
                
                // 開啟新的一段連續區間
                l = nums[i][0];
                r = nums[i][1];
            }
            
            // 不斷將當前區間的右邊界往外擴張
            r = max(r, nums[i][1]);
        }
        
        // 補上最後一段連續區間的點數
        rt += r - l + 1;
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