---
title: "LeetCode 1356. Sort Integers by The Number of 1 Bits"
date: 2026-02-25T15:55:00+08:00
lastmod: 2026-02-25T15:55:00+08:00
difficulty: 1257
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Bit Manipulation", "Sorting"]
keywords: ["LC1356", "1257", "Easy", "popcount", "__builtin_popcount", "Custom Comparator"]
description: "LeetCode 第 1356 題：Sort Integers by The Number of 1 Bits。難度評分：1257。探討如何結合自訂比較函式與位元運算 (__builtin_popcount) 來進行雙條件排序。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1356](https://leetcode.com/problems/sort-integers-by-the-number-of-1-bits/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1257)</span>   
> **核心主題**：`Array` $\cdot$ `Bit Manipulation` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `arr`。請你根據每個整數的二進位表示中 **1 的數量 (Set Bits)** 將陣列進行升冪排序。
如果存在兩個或多個整數具有相同數量的 1，請將它們根據 **數值本身的大小** 進行升冪排序。

最後回傳排序後的陣列。

### 範例
- **Input**: `arr = [0,1,2,3,4,5,6,7,8]` → **Output**: `[0,1,2,4,8,3,5,6,7]`
- **Explanation**: 
  0 的二進位有 0 個 1。
  1, 2, 4, 8 的二進位都有 1 個 1。
  3, 5, 6 的二進位都有 2 個 1。
  7 的二進位有 3 個 1。
  按照 1 的數量排序，數量相同時按數值排序。

### 限制條件
- $1 \le arr.length \le 500$
- $0 \le arr[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-02-25：初次提交 (自訂比較函式與 Popcount)](#2026-02-25-初次提交)

---

## 💡 2026-02-25 初次提交

### 🎯 直覺 (Intuition)
這是一道非常經典的「雙條件排序」題。只要釐清排序的首要條件（二進位中 1 的數量）與次要條件（數值本身大小），直接透過 C++ 的 `std::sort` 搭配自訂的 `cmp` 比較函式就能輕鬆解決。計算 1 的數量時，直接呼叫硬體級別的 `__builtin_popcount` 是最高效的選擇。

### 🛠️ 解題思路 (Approach)
1. **設計自訂比較函式 (`cmp`)**：
   - 在 `cmp` 中，分別使用 `__builtin_popcount` 計算兩個數字 `a` 和 `b` 的 bit count。
   - **首要條件判斷**：如果兩者的 1 數量不同 (`cnt_a != cnt_b`)，則直接回傳 `cnt_a < cnt_b`，讓 1 較少的數字排在前面。
   - **次要條件判斷**：如果 1 的數量相同，則回傳數值本身的比較結果 `a < b`。
2. **呼叫排序**：
   - 傳入 `arr.begin()`、`arr.end()` 以及定義好的 `cmp` 給 `std::sort`，即可就地 (In-place) 完成排序。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。$N$ 為陣列長度。主要開銷來自於 `std::sort` 的排序過程。`__builtin_popcount` 的執行時間為 $\mathcal{O}(1)$ 常數級別，不會增加額外的時間複雜度。
- **空間複雜度**: $\mathcal{O}(\log N)$。用於 `std::sort` 底層（通常是 Introsort）在遞迴時所需的堆疊空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 加上 static 確保可以作為函式指標傳入 std::sort
    static bool cmp(int& a, int& b) {
        int cnt_a = __builtin_popcount(a);
        int cnt_b = __builtin_popcount(b);
        
        // 條件一：比較 1 的數量
        if(cnt_a != cnt_b) {
            return cnt_a < cnt_b;
        }
        // 條件二：若 1 數量相同，比較數值大小
        return a < b;
    }
    
    vector<int> sortByBits(vector<int>& arr) {
        sort(arr.begin(), arr.end(), cmp);
        return arr;
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