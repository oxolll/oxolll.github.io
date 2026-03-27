---
title: "LeetCode 1877. Minimize Maximum Pair Sum in Array"
date: 2026-03-27T12:19:00+08:00
lastmod: 2026-03-27T12:19:00+08:00
difficulty: 1301
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "two pointers", "sorting"]
keywords: ["LC1877", "1301", "Medium", "Minimize Maximum Pair Sum", "Greedy", "Two Pointers"]
description: "LeetCode 第 1877 題：Minimize Maximum Pair Sum in Array。難度評分：1301。探討如何利用排序與雙指標，依照貪婪策略將最大值與最小值配對，以 O(N log N) 時間複雜度最小化數對的最大總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1877](https://leetcode.com/problems/minimize-maximum-pair-sum-in-array/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1301)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Two Pointers` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
一個陣列的「數對和 (Pair Sum)」是指將陣列元素兩兩配對後，每一對元素的數值總和。
給定一個長度為偶數 $n$ 的整數陣列 `nums`，請將其元素劃分為 $n / 2$ 個數對，使得：
1. `nums` 中的每個元素**恰好**屬於一個數對。
2. 這些數對的最大總和 (Maximum Pair Sum) 被**最小化**。

請回傳經過最佳配對後，可能達成的最小「最大數對和」。

### 限制條件
- $n == nums.length$
- $2 \le n \le 10^5$
- `n` 是偶數。
- $1 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (排序與極值貪婪配對)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求「最小化最大值 (Minimize the Maximum)」，這是貪婪演算法 (Greedy Algorithm) 常見的應用場景。
為了避免產生一個非常大的數對和，我們必須「中和」陣列中數值最大的元素。最佳的策略就是將全域最大值與全域最小值配對，將次大值與次小值配對，依此類推。若將大數字與另一個大數字配對，必然會產生一個無法被縮小的高峰值，違反最小化最大數對和的目標。

### 🛠️ 解題思路 (Approach)
1. **排序**：對 `nums` 陣列進行遞增排序。排序後，最小值位於陣列起點，最大值位於陣列終點。
2. **初始化變數**：
   - 取得陣列長度 `n`。
   - 宣告變數 `mx` 初始化為 `INT_MIN`，用於記錄配對過程中的最大總和。
3. **雙指標配對驗證**：
   - 使用迴圈遍歷陣列的前半部（索引 `i` 從 `0` 到 `n / 2 - 1`）。
   - 將當前第 $i$ 小的元素 `nums[i]` 與第 $i$ 大的元素 `nums[n - 1 - i]` 相加。
   - 使用 `max` 函式更新 `mx = max(mx, nums[i] + nums[n - 1 - i])`。
4. **回傳結果**：迴圈結束後，`mx` 即為最小化的最大數對和。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是陣列的長度。排序操作為 $\mathcal{O}(N \log N)$，後續的配對走訪為 $\mathcal{O}(N)$，整體由排序主導。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(1)$。取決於底層排序演算法的遞迴堆疊空間需求。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minPairSum(vector<int>& nums) {
        // 1. 遞增排序
        sort(nums.begin(), nums.end());
        
        int n = nums.size();
        int mx = INT_MIN;
        
        // 2. 將第 i 小的元素與第 i 大的元素配對，並記錄最大總和
        for(int i = 0; i < n / 2; ++i) {
            mx = max(mx, nums[i] + nums[n - 1 - i]);
        }
        
        return mx;
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