---
title: "LeetCode 1752. Check if Array Is Sorted and Rotated"
date: 2026-05-23T11:15:00+08:00
lastmod: 2026-05-23T11:15:00+08:00
difficulty: 1324
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "simulation"]
keywords: ["LC1752", "Easy", "1324", "Sorted and Rotated", "環形陣列", "下降點計數"]
description: "LeetCode 第 1752 題：Check if Array Is Sorted and Rotated。難度評分：1324。探討如何利用模除技巧模擬環形陣列，透過統計數值下降點 (Drop Point) 的次數，在 O(N) 時間內優雅判定陣列是否為旋轉後的遞增序列。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1752](https://leetcode.com/problems/check-if-array-is-sorted-and-rotated/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1324)</span>  
> **核心主題**：`Array` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個陣列 `nums`，請你判斷它是否原本是一個 **非遞減排序** 的陣列，並且經過了若干次（可能為 0 次）的旋轉。如果是，請回傳 `true`；否則回傳 `false`。

一個陣列被旋轉 $x$ 次的定義是：將陣列的最後 $x$ 個元素移動到陣列的最前面，其餘元素的順序保持不變。
*(註：陣列中可能包含重複的元素。)*

### 範例
- **Input**: `nums = [3,4,5,1,2]`
- **Output**: `true`
- **Explanation**: 原陣列為 `[1,2,3,4,5]`，向右旋轉 3 次得到 `[3,4,5,1,2]`。

- **Input**: `nums = [2,1,3,4]`
- **Output**: `false`
- **Explanation**: 沒有任何排序過的陣列經過旋轉後可以得到此陣列。

- **Input**: `nums = [1,2,3]`
- **Output**: `true`
- **Explanation**: 旋轉 0 次（即未旋轉）。

### 限制條件
- $1 \le nums.length \le 100$
- $1 \le nums[i] \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-23：初次提交 (環形陣列下降點計數法)](#2026-05-23-初次提交)

---

## 💡 2026-05-23 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
一個已經排序好的陣列（非遞減），無論怎麼旋轉，都具備一個極為鮮明的特徵：
如果我們把陣列的首尾相連，看作一個**環形陣列 (Circular Array)**，那麼在這個環中，**「前一個元素大於後一個元素」的情況（即下降點 Drop Point）最多只會發生一次**。

1. **未旋轉的陣列**（例如 `[1,2,3,4]`）：即使首尾相連，最後一個元素比較第一個元素依然是非遞減，下降點次數為 $0$。
2. **旋轉過的陣列**（例如 `[3,4,5,1,2]`）：只會在原本陣列的最大值與最小值交界處（`5` 掉到 `1`）發生一次下降，下降點次數為 $1$。
3. **無效的陣列**（例如 `[2,1,3,4,0]`）：會發生兩次以上的下降（`2->1` 以及 `4->0`），不合規定。

### 🛠️ 解題思路 (Approach)
1. **環形遍歷**：迴圈從 $i = 0$ 執行到 $n$（共 $n+1$ 步），並透過 `nums[i % n]` 來存取元素。這巧妙地讓最後一步（$i = n$）去比較了陣列尾端 `nums[n-1]` 與陣列前端 `nums[0]`。
2. **計算下降點**：維護一個計數器 `inc`（代表下降次數）與前驅變數 `prev`。
3. **初始值巧思**：題目限制 `nums[i] >= 1`，因此將 `prev` 初始化為 `-1`，可保證在 $i=0$ 時 `nums[0] < prev` 絕對為假，不會誤判。
4. **驗證結果**：只要整趟遍歷中下降次數 `inc <= 1`，即為合法旋轉陣列。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需對陣列進行一次長度為 $N+1$ 的完整線性遍歷。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個記錄變數（`inc`, `prev`）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool check(vector<int>& nums) {
        int inc = 0; // 記錄數值下降 (Drop) 的次數
        // 題目測資保證 nums[i] >= 1，所以預設為 -1 可以完美避開首次迴圈的誤判
        int prev = -1; 
        int n = nums.size();
        
        // 迴圈跑到 i = n，總共 n+1 次迭代，藉以涵蓋首尾相接的檢查
        for(int i = 0; i <= n; ++i) {
            // 使用 i % n 模擬環形陣列
            if(nums[i % n] < prev) {
                ++inc;
            }
            // 更新 prev 為當前元素，供下一輪比對
            prev = nums[i % n];
        }

        // 下降點不超過 1 個，即為合法的旋轉排序陣列
        return inc <= 1;
    }
};
```

---

### 💡 延伸優化 (Alternative Optimization)
上述寫法非常優美，但如果你想將迴圈次數精簡為正好 $N$ 次，我們也可以在迴圈內直接比較 `nums[i]` 與 `nums[(i+1) % n]`。這是在實戰中也相當受歡迎的另一種等價寫法：

```cpp
class Solution {
public:
    bool check(vector<int>& nums) {
        int drop_count = 0;
        int n = nums.size();
        
        for (int i = 0; i < n; ++i) {
            // 直接比較當前元素與下一個元素 (並利用 modulo 處理最後一個元素與首元素的邊界)
            if (nums[i] > nums[(i + 1) % n]) {
                ++drop_count;
            }
        }
        
        return drop_count <= 1;
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