---
title: "LeetCode 2037. Minimum Number of Moves to Seat Everyone"
date: 2026-03-26T16:00:00+08:00
lastmod: 2026-03-26T16:00:00+08:00
difficulty: 1356
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting"]
keywords: ["LC2037", "1356", "Easy", "Minimum Moves", "Greedy", "Sorting"]
description: "LeetCode 第 2037 題：Minimum Number of Moves to Seat Everyone。難度評分：1356。探討如何利用貪婪演算法與排序，以 O(N log N) 時間複雜度計算將學生分配至座位所需的最小移動總距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2037](https://leetcode.com/problems/minimum-number-of-moves-to-seat-everyone/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1356)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個長度皆為 $n$ 的整數陣列 `seats` 與 `students`，分別代表座位的位置與學生的初始位置。
你可以任意多次執行以下操作：將一名學生的位置增加 1 或減少 1（即移動 1 步）。
請回傳將所有學生移動到座位上，使得每個座位恰好有一名學生所需的**最少總移動次數**。

### 限制條件
- $n == seats.length == students.length$
- $1 \le n \le 100$
- $1 \le seats[i], students[j] \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (排序與貪婪配對)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要使總移動距離最小，必須採用貪婪策略 (Greedy Strategy)。最佳的分配方式是將位置最小（最靠左）的學生分配到位置最小的座位，次小的學生分配到次小的座位，依此類推。
如果產生交叉分配，總移動距離必定會大於或等於順序分配的距離。因此，只要將兩個陣列分別進行遞增排序，然後將對應索引的元素相減取絕對值並加總，即可得到全局最佳解。

### 🛠️ 解題思路 (Approach)
1. 呼叫 `sort` 將 `seats` 陣列進行遞增排序。
2. 呼叫 `sort` 將 `students` 陣列進行遞增排序。
3. 宣告變數 `rt = 0` 記錄總移動步數。
4. 建立迴圈遍歷長度 $n$。在每個索引 $i$，計算 `abs(seats[i] - students[i])` 並累加至 `rt`。
5. 回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。主要的效能瓶頸在於對長度為 $N$ 的兩個陣列進行排序。後續的配對計算僅需 $\mathcal{O}(N)$ 的線性時間。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(1)$。取決於 `std::sort` 實作所需的遞迴呼叫堆疊空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minMovesToSeat(vector<int>& seats, vector<int>& students) {
        sort(seats.begin(), seats.end());
        sort(students.begin(), students.end());
        
        int n = seats.size();
        int rt = 0;
        for(int i = 0; i < n; ++i) {
            rt += abs(seats[i] - students[i]);
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