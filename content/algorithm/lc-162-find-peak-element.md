---
title: "LeetCode 162. Find Peak Element"
date: 2026-03-30T16:55:00+08:00
lastmod: 2026-03-30T16:55:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary search"]
keywords: ["LC162", "Medium", "N/A", "Peak Element", "Binary Search"]
description: "LeetCode 第 162 題：Find Peak Element。難度評分：Medium (N/A)。探討如何尋找陣列中的局部峰值，包含 O(N) 的線性掃描法，以及符合題目嚴格要求的 O(log N) 二分搜尋最佳化解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 162](https://leetcode.com/problems/find-peak-element/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
峰值元素是指嚴格大於其相鄰元素的元素。
給定一個以 0 為起始索引的整數陣列 `nums`，請找到一個峰值元素並回傳其索引。如果陣列包含多個峰值，回傳其中任何一個的索引皆可。
你可以假設 `nums[-1] = nums[n] = -∞`（即邊界外的元素視為負無窮大）。

**進階要求**：你必須實作時間複雜度為 $\mathcal{O}(\log N)$ 的演算法。

### 限制條件
- $1 \le nums.length \le 1000$
- $-2^{31} \le nums[i] \le 2^{31} - 1$
- 對於所有有效的 `i`，`nums[i] != nums[i + 1]`。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-30：初次提交 (線性掃描)](#2026-03-30-初次提交)

---

## 💡 2026-03-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
尋找峰值最直觀的方法是**線性掃描 (Linear Scan)**。由於題目定義兩端點外側視為 $-\infty$，我們只要確認陣列中的某個元素同時大於其左側與右側的相鄰元素，該元素即為峰值。在實作上，可利用三元運算子配合極小值 (`LLONG_MIN`) 處理邊界條件，避免存取越界。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int n = nums.size();
        for(int i = 0; i < n; ++i) {
            // 利用 LLONG_MIN 處理邊界外的負無窮大假設
            if(nums[i] > (i > 0 ? nums[i-1] : LLONG_MIN) && 
               nums[i] > (i < n-1 ? nums[i+1] : LLONG_MIN)) {
                return i;
            }
        }
        return -1;
    }
};
```
- **時間複雜度**: $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(1)$。

---

### 💡 進階最佳化：$\mathcal{O}(\log N)$ 二分搜尋 (Binary Search)
雖然線性掃描能通過測資，但題目明確要求實作 $\mathcal{O}(\log N)$ 的演算法。要達到對數時間，必須使用**二分搜尋法**。
二分搜尋能應用於此題的核心數學性質在於：**循著上升的斜率前進，必定能找到一個峰值**。
當我們比較中點 `mid` 與其右側相鄰元素 `mid + 1`：
- 若 `nums[mid] < nums[mid + 1]`，代表當前處於上坡段，峰值必然出現在右半部（包含 `mid + 1`）。
- 若 `nums[mid] > nums[mid + 1]`，代表當前處於下坡段，峰值必然出現在左半部（包含 `mid` 本身）。

<details>
<summary><b>點擊展開 O(log N) 二分搜尋實作程式碼</b></summary>

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        
        while (l < r) {
            int mid = l + (r - l) / 2;
            
            // 處於上坡，峰值在右側
            if (nums[mid] < nums[mid + 1]) {
                l = mid + 1;
            } 
            // 處於下坡，峰值在左側 (或 mid 本身即為峰值)
            else {
                r = mid;
            }
        }
        
        // 當 l == r 時，即鎖定峰值位置
        return l;
    }
};
```
</details>

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