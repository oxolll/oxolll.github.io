---
title: "LeetCode 2966. Divide Array Into Arrays With Max Difference"
date: 2026-04-22T22:25:00+08:00
lastmod: 2026-04-22T22:25:00+08:00
difficulty: 1395
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting"]
keywords: ["LC2966", "1395", "Medium", "Divide Array", "Greedy", "Sorting"]
description: "LeetCode 第 2966 題：Divide Array Into Arrays With Max Difference。難度評分：1395。探討如何利用貪婪策略 (Greedy) 結合陣列排序，搭配 O(1) 空間預分配與單調性檢查，以 O(N log N) 的時間複雜度高效完成陣列分塊切割。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2966](https://leetcode.com/problems/divide-array-into-arrays-with-max-difference/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1395)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為 $n$ 的整數陣列 `nums`，以及一個正整數 `k`。
請將陣列分割成多個大小為 3 的陣列，並滿足以下條件：
- `nums` 中的每一個元素都必須恰好屬於其中一個大小為 3 的陣列。
- 在每一個大小為 3 的陣列中，任意兩個元素之間的差值絕對值必須 **小於等於** `k`。

回傳包含所有這些陣列的二維陣列。如果無法滿足條件，則回傳一個空的二維陣列。
如果有多種合法的分割方式，回傳任意一種皆可。

### 限制條件
- $n == nums.length$
- $1 \le n \le 10^5$
- $n$ 保證是 3 的倍數。
- $1 \le nums[i] \le 10^5$
- $1 \le k \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-22：初次提交 (貪婪排序與 O(1) 空間預分配)](#2026-04-22-初次提交)

---

## 💡 2026-04-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求我們將陣列每 3 個分作一組，且組內的最大差值不得超過 $k$。
要讓分組內的最大差值盡可能小，核心的**貪婪策略 (Greedy Strategy)** 就是：**「將數值最接近的元素分在同一組」**。
因此，我們可以先將原陣列進行**排序 (Sorting)**。排序後，每 3 個相鄰的元素就是天然數值最接近的組合。
由於陣列已經排序呈遞增狀態，一個大小為 3 的區塊中，最大值與最小值的差，必定是「第三個元素減去第一個元素」。只要這個差值 $\le k$，該區塊就絕對合法。

### 🛠️ 解題思路 (Approach)
1. **記憶體預分配 (Pre-allocation)**：
   - 已知答案必須回傳 $\frac{n}{3}$ 個大小為 3 的陣列，直接宣告 `vector<vector<int>> rt(nums.size() / 3, vector<int>(3, 0))`。這樣能省去後續迴圈內不斷重新配置記憶體的效能浪費。
2. **排序**：
   - 呼叫 `sort(nums.begin(), nums.end())` 將陣列轉為遞增序列。
3. **貪婪分塊與檢查**：
   - 走訪排序後的 `nums` 陣列。
   - 計算當前元素屬於哪一個區塊（列索引 `i / 3`）以及區塊內的位置（欄索引 `j`），並將 `nums[i]` 寫入。
   - **單調性檢查**：寫入後，立刻檢查 `nums[i] - rt[i/3][0] > k`。因為數列遞增，只要當前寫入的元素與該區塊「首個元素」的差值超過 $k$，就代表這個最優組合已經破局，直接回傳 `{}`。
4. **回傳結果**：若整個陣列都順利走訪完畢，代表全部分塊皆合法，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。陣列排序耗費 $\mathcal{O}(N \log N)$，後續走訪陣列進行分塊與檢查耗費 $\mathcal{O}(N)$，由排序時間主導。
- **空間複雜度**: $\mathcal{O}(N)$。除了回傳所需的 `rt` 二維陣列外，排序本身在 C++ 中需要 $\mathcal{O}(\log N)$ 的遞迴堆疊空間。整體額外空間可視為極小。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> divideArray(vector<int>& nums, int k) {
        // 1. O(1) 預分配記憶體，避免動態擴展浪費效能
        vector<vector<int>> rt(nums.size() / 3, vector<int>(3, 0));
        
        // 2. 貪婪策略：排序以確保相鄰元素數值最接近
        sort(nums.begin(), nums.end());
        
        int j = 0;
        for(int i = 0; i < nums.size(); ++i) {
            // i / 3 對應區塊的列，j 對應區塊內的欄
            rt[i / 3][j] = nums[i];
            
            // 3. 單調性檢查：當前元素與區塊內最小(首個)元素的差值若大於 k，直接宣告失敗
            if(nums[i] - rt[i / 3][0] > k) {
                return {};
            }
            
            // 更新欄索引，循環 0, 1, 2
            j = (j + 1) % 3;
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