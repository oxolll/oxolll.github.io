---
title: "LeetCode 3914. Minimum Operations to Make Array Non Decreasing"
date: 2026-04-26T15:35:00+08:00
lastmod: 2026-04-26T15:35:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "math"]
keywords: ["LC3914", "Medium", "Minimum Operations", "Non Decreasing", "Greedy"]
description: "LeetCode 第 3914 題：Minimum Operations to Make Array Non Decreasing。探討如何利用貪婪策略 (Greedy)，透過計算相鄰元素的落差總和，以 O(N) 線性時間算出透過區間/後綴平移使陣列不遞減的最小操作次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3914](https://leetcode.com/problems/minimum-operations-to-make-array-non-decreasing/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。你可以執行特定操作來修改陣列元素（根據解法邏輯，此處的操作定義為：選定一個索引 $i$，並將 $nums[i]$ 及其之後的所有元素皆增加 1，即後綴平移）。
請計算並回傳使陣列 `nums` 變為**非遞減 (Non-decreasing)** 所需的**最小操作次數**。
*(非遞減代表對於所有 $i \ge 1$，都滿足 $nums[i-1] \le nums[i]$)*。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-26：初次提交 (貪婪落差累加法)](#2026-04-26-初次提交)

---

## 💡 2026-04-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這類使陣列非遞減的題目，解題核心完全取決於「操作的定義」：
如果操作允許我們將一個「後綴 (Suffix)」整體往上提升，那麼我們其實根本不需要理會陣列後方的絕對數值，只需要關注**「相鄰元素之間的相對落差」**。

**貪婪策略 (Greedy Strategy)**：
從左掃描到右，每當我們遇到一個「斷層」，也就是 $nums[i] < nums[i-1]$ 時，為了讓陣列不遞減，我們**必定且至少**需要將 $nums[i]$ 往上拉提 $(nums[i-1] - nums[i])$ 次。
因為操作是針對後綴進行的，當我們把 $nums[i]$ 往上拉提時，它後面的所有元素也會跟著一起被往上拉提相同的幅度。這保證了：
1. 我們修補了當前的斷層。
2. 我們完全沒有破壞 $nums[i]$ 之後元素的相對大小關係。
因此，最小操作總次數，剛好就會等於所有「相鄰下降落差的總和」。

### 🛠️ 解題思路 (Approach)
1. **初始化**：宣告 `rt = 0` 準備記錄總操作次數。
2. **走訪陣列**：
   - 使用迴圈從索引 $1$ 開始走訪陣列至 $n-1$。
   - 檢查是否發生「遞減斷層」：若 `nums[i] < nums[i-1]`。
   - 計算落差：`pre = nums[i-1] - nums[i]`。
   - 累加操作次數：`rt += pre`。
3. **回傳結果**：迴圈結束後，回傳累加總和 `rt`。

*(備註：因為我們只關心落差總和，所以不需要實際去修改 `nums[i]` 的數值，大幅降低了常數時間的操作開銷。)*

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。只需要對陣列進行一次線性掃描。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個變數 `pre` 與 `rt`，不需要額外的記憶體配置。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long minOperations(vector<int>& nums) {
        int n = nums.size();
        int pre = 0;
        long long rt = 0; // 使用 long long 避免落差總和造成整數溢位
        
        // 貪婪策略：找出所有相鄰下降的斷層，並將落差累加
        for(int i = 1; i < n; ++i) {
            if(nums[i] < nums[i-1]) {
                // 計算需要將後綴平移的次數
                pre = (nums[i-1] - nums[i]);
                rt += pre;
            } else {
                continue;
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