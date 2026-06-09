---
title: "LeetCode 2161. Partition Array According to Given Pivot"
date: 2026-06-09T14:11:00+08:00
lastmod: 2026-06-09T14:11:00+08:00
difficulty: 1377
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "two-pointers", "simulation"]
keywords: ["LC2161", "Medium", "1377", "Partition Array", "Stable Partition", "陣列分割", "穩定性"]
description: "LeetCode 第 2161 題：Partition Array According to Given Pivot。探討如何實作保證元素相對順序的穩定分割 (Stable Partition)，利用輔助陣列與計數器以 O(N) 的時間與空間複雜度達成最優模擬。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2161](https://leetcode.com/problems/partition-array-according-to-given-pivot/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1377)</span>  
> **核心主題**：`Array` $\cdot$ `Two Pointers` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個下標從 **0** 開始的整數陣列 `nums` 和一個整數 `pivot`。請你將 `nums` 重新排列，使得滿足以下條件：
1. 所有 **小於** `pivot` 的元素都出現在所有 **大於** `pivot` 的元素之前。
2. 所有 **等於** `pivot` 的元素都出現在小於和大於的元素之間。
3. 小於 `pivot` 的元素之間必須 **保持它們在原陣列中的相對順序**。
4. 大於 `pivot` 的元素之間必須 **保持它們在原陣列中的相對順序**。

請回傳重新排列後的陣列。

### 範例
- **Input**: `nums = [9,12,5,10,14,3,10], pivot = 10`
- **Output**: `[9,5,3,10,10,12,14]`
- **Explanation**: 
  - 小於 10 的元素是 `[9,5,3]`，相對順序不變。
  - 等於 10 的元素是 `[10,10]`，放在中間。
  - 大於 10 的元素是 `[12,14]`，相對順序不變。

- **Input**: `nums = [-3,4,3,2], pivot = 2`
- **Output**: `[-3,2,4,3]`
- **Explanation**: 
  - 小於 2 的元素是 `[-3]`。
  - 等於 2 的元素是 `[2]`。
  - 大於 2 的元素是 `[4,3]`，相對順序不變。

### 限制條件
- $1 \le nums.length \le 10^5$
- $-10^5 \le nums[i] \le 10^5$
- `pivot` 必定存在於 `nums` 中。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (輔助陣列分類與合併)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是經典的陣列分割問題，但加入了一個關鍵條件：**必須保持相對順序 (Stable Partition)**。
常規的快速排序 (Quick Sort) 分割法是利用雙指標進行原地 `swap`，這種方法會破壞元素的相對順序，因此不適用於本題。

為了保證穩定性，最直觀且效能極佳的作法是 **開闢輔助空間**。
我們可以將元素分為三類：「小於」、「等於」、「大於」。
1. **小於 pivot**：循序推入 `before` 陣列。
2. **等於 pivot**：只需要記錄出現的次數 `eq` 即可（省下一個陣列的空間開銷）。
3. **大於 pivot**：循序推入 `after` 陣列。
遍歷一次原陣列後，再依序將 `等於` 和 `after` 的元素附加到 `before` 陣列後方，即可完成穩定的分割與合併。

### 🛠️ 解題思路 (Approach)
1. 宣告兩個 `vector<int>`：`bf` 儲存小於 pivot 的元素，`af` 儲存大於 pivot 的元素。
2. 宣告一個整數變數 `eq` 記錄等於 pivot 的數量。
3. 遍歷 `nums` 中的每個元素 `num`：
   - 若 `num == pivot`，則 `++eq`。
   - 若 `num < pivot`，則加入 `bf`。
   - 若 `num > pivot`，則加入 `af`。
4. 第一輪合併：利用 `while(eq--)` 將所有 pivot 元素附加到 `bf` 尾端。
5. 第二輪合併：遍歷 `af`，將所有大於的元素附加到 `bf` 尾端。
6. 直接回傳合併完成的 `bf` 陣列作為答案。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。遍歷原陣列一次，再遍歷合併一次，操作均為嚴格線性。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了 `bf` 和 `af` 兩個輔助陣列，總共儲存的元素數量恰好等於 $N$，為穩定排序必要的空間妥協。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> pivotArray(vector<int>& nums, int pivot) {
        vector<int> bf; // 儲存小於 pivot 的元素
        vector<int> af; // 儲存大於 pivot 的元素
        int eq = 0;     // 計算等於 pivot 的元素數量
        
        // 1. 線性掃描並分類
        for(auto num : nums) {
            if(num == pivot) {
                ++eq; // 等於的元素只需計數，節省空間
            } else if(num < pivot) {
                bf.push_back(num);
            } else {
                af.push_back(num);
            }
        }

        // 2. 依序合併：先補齊等於 pivot 的元素
        while(eq--) {
            bf.push_back(pivot);
        }

        // 3. 依序合併：最後附加所有大於 pivot 的元素
        for(auto num : af) {
            bf.push_back(num);
        }
        
        // 直接將擴充後的 bf 陣列作為結果回傳
        return bf;
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