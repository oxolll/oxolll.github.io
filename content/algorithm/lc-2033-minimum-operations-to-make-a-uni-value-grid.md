---
title: "LeetCode 2033. Minimum Operations to Make a Uni-Value Grid"
date: 2026-04-28T11:25:00+08:00
lastmod: 2026-04-28T11:25:00+08:00
difficulty: 1671
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "sorting", "matrix"]
keywords: ["LC2033", "Medium", "1671", "Uni-Value Grid", "Median", "Modulo"]
description: "LeetCode 第 2033 題：Minimum Operations to Make a Uni-Value Grid。難度評分：1671。探討如何利用模運算 (Modulo) 快速驗證可行性，並套用數學上的「中位數最短距離定理」，以排序找出最佳的目標數值，算出最小操作次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2033](https://leetcode.com/problems/minimum-operations-to-make-a-uni-value-grid/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1671)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Sorting` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個大小為 $m \times n$ 的二維整數網格 `grid` 還有一個整數 $x$。
每一次操作，你可以選擇網格中的**任意一個**元素，並將它加上 $x$ 或是減去 $x$。
你的目標是讓網格中的所有元素都變成**同一個數值**。
請回傳達成目標所需的**最小操作次數**。如果無法讓所有元素變得相同，則回傳 `-1`。

### 限制條件
- $m == grid.length$
- $n == grid[i].length$
- $1 \le m, n \le 10^5$
- $1 \le m \times n \le 10^5$
- $1 \le grid[i][j], x \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-28：初次提交 (同餘驗證與中位數定理)](#2026-04-28-初次提交)

---

## 💡 2026-04-28 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這道題目可以拆解為兩個獨立的數學子問題：
1. **判斷是否可能 (Feasibility)**：
   因為每次操作只能變動 $x$，這意味著兩個數字若要能變成同一個數字，它們之間的差值必須是 $x$ 的倍數。
   換言之，所有數字除以 $x$ 的**餘數必須完全相同**（同餘）。我們只需用 `grid[0][0] % x` 作為基準，遍歷檢查即可。若有任何不符，直接回傳 `-1`。

2. **尋找最佳目標值 (Optimality)**：
   當所有數字都有可能變成同一個數時，我們該選擇哪個數字作為「集合點」，才能讓總操作次數最少？
   這是一個經典的絕對值不等式問題：要求 $\sum |A_i - target|$ 最小，數學上已證明 $target$ 必須是數列的**中位數 (Median)**。（註：如果是要求平方和最小，才是取平均數）。
   因此，我們只需將所有元素放入一維陣列並排序，取出中間的元素作為目標值，再計算所有人與目標值的距離即可。

### 🛠️ 解題思路 (Approach)
1. **基準餘數**：計算第一個元素的餘數 `mod = grid[0][0] % x`。
2. **提取與驗證**：
   - 宣告一維陣列 `tmp`。
   - 走訪整個二維網格 `grid`。如果遇到 `grid[i][j] % x != mod`，代表永遠無法達成目標，提早回傳 `-1`。
   - 否則，將 `grid[i][j]` 推入 `tmp` 陣列。
3. **尋找中位數**：
   - 將 `tmp` 陣列從小到大排序。
   - 取得中位數 `tar = tmp[m * n / 2]`。
4. **計算操作次數**：
   - 宣告 `rt = 0` 記錄總操作數。
   - 再次走訪網格 (或走訪 `tmp` 亦可)，計算每個元素與目標值的差距，除以 $x$ 後即為所需的操作次數：`rt += abs(tar - grid[i][j]) / x`。
5. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N \log(M \times N))$。其中 $M$ 和 $N$ 是網格的長寬。收集元素耗時 $\mathcal{O}(M \times N)$，將陣列排序為最大的時間瓶頸，耗時 $\mathcal{O}(K \log K)$ ($K = M \times N$)。
- **空間複雜度**: $\mathcal{O}(M \times N)$。宣告了一個大小等同於網格總元素數量的一維陣列 `tmp` 來進行排序。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minOperations(vector<vector<int>>& grid, int x) {
        int m = grid.size(), n = grid[0].size();
        
        // 1. 取得基準餘數
        int mod = grid[0][0] % x;
        vector<int> tmp;
        
        // 2. 驗證同餘特性，並攤平為一維陣列
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // 若餘數不同，絕對不可能化為同一個數值
                if(mod != grid[i][j] % x) return -1;
                tmp.push_back(grid[i][j]);
            }
        }

        // 3. 排序以尋找中位數
        sort(tmp.begin(), tmp.end());
        
        // 數學定理：中位數能使所有點到此點的絕對值距離總和最小
        int tar = tmp[m * n / 2];
        
        int rt = 0;
        // 4. 計算所需的操作總次數
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // 兩數差值除以 x，即為需要加減的步數
                rt += abs(tar - grid[i][j]) / x;
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