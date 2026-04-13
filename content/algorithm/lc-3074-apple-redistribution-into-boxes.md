---
title: "LeetCode 3074. Apple Redistribution into Boxes"
date: 2026-04-07T10:05:00+08:00
lastmod: 2026-04-07T10:05:00+08:00
difficulty: 1197
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting", "counting sort"]
keywords: ["LC3074", "1197", "Easy", "Apple Redistribution", "Greedy", "Counting Sort"]
description: "LeetCode 第 3074 題：Apple Redistribution into Boxes。難度評分：1197。探討如何利用箱子容量上限的特性，使用計數排序 (Counting Sort) 取代傳統排序，並透過貪婪策略以 O(N + M) 時間複雜度求得所需的最少箱子數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3074](https://leetcode.com/problems/apple-redistribution-into-boxes/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1197)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為 $n$ 的陣列 `apple` 和一個長度為 $m$ 的陣列 `capacity`。
一共有 $n$ 個包裹，第 `i` 個包裹中裝有 `apple[i]` 顆蘋果。同時，有 $m$ 個不同的箱子，第 `i` 個箱子的容量為 `capacity[i]` 顆蘋果。
你可以將包裹中的蘋果重新分配到箱子中，且來自同一個包裹的蘋果可以被分裝到不同的箱子裡。
請回傳你需要用來裝載所有蘋果的**最少**箱子數量。

### 限制條件
- $1 \le n == apple.length \le 50$
- $1 \le m == capacity.length \le 50$
- $1 \le apple[i], capacity[i] \le 50$
- 測資保證所有的蘋果都能被裝進箱子中。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-07：初次提交 (計數排序與貪婪法精簡版)](#2026-04-07-初次提交)

---

## 💡 2026-04-07 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
題目的核心目標是「用最少的箱子裝完所有的蘋果」。由於蘋果可以被任意打散與重新分配，我們實際上只需要關心**蘋果的總數**。
為了讓使用的箱子數量最少，根據貪婪策略 (Greedy Strategy)，我們必須優先使用**容量最大**的箱子。
傳統做法是將 `capacity` 陣列進行遞減排序，但觀察到 `capacity[i]` 的最大值僅為 50，這極度適合使用**計數排序 (Counting Sort)**。我們可以用一個大小為 55 的陣列記錄各容量箱子的數量，再從容量 50 向下遍歷，貪婪地扣除蘋果總數，將時間複雜度優化至線性的 $\mathcal{O}(N + M)$。

### 🛠️ 解題思路 (Approach)
1. **頻率統計**：宣告大小為 55 的陣列 `cnt`，走訪 `capacity` 統計各種容量箱子的數量。
2. **計算總和**：走訪 `apple` 陣列，將所有蘋果數量加總至 `sum`。
3. **貪婪裝箱**：
   - 宣告 `rt = 0` 記錄使用的箱子數。
   - 建立迴圈，容量 `i` 從 50 遞減至 1。當 `sum > 0` 條件不成立時 (蘋果裝完) 可提前中斷。
   - 若該容量的箱子存在 (`cnt[i] > 0`)，利用 `while` 迴圈逐一消耗該容量的箱子：
     - 每使用一個箱子，箱子數 `rt` 增加 1。
     - 從 `sum` 中直接扣除箱子的容量 `i`。即使 `sum` 變成負數，也代表蘋果已全數裝完，下一輪迴圈條件會自動判定並結束裝箱。
4. **回傳結果**：回傳箱子總數 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M)$。其中 $N$ 是 `apple` 的長度，$M$ 是 `capacity` 的長度。計算蘋果總和需 $\mathcal{O}(N)$，統計箱子需 $\mathcal{O}(M)$，反向掃描頻率陣列的時間為常數 $\mathcal{O}(C)$（$C=50$），整體耗時為線性。
- **空間複雜度**: $\mathcal{O}(1)$。僅宣告了固定大小為 55 的計數陣列，空間開銷為極小的常數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumBoxes(vector<int>& apple, vector<int>& capacity) {
        // 利用箱子容量上限為 50 的特性，使用計數排序
        vector<int> cnt(55, 0);
        for(auto c : capacity) {
            ++cnt[c];
        }

        // 計算所有需要裝箱的蘋果總數
        int sum = 0;
        for(auto a : apple) sum += a;

        int rt = 0;
        
        // 從最大容量的箱子開始貪婪裝箱，直到蘋果裝完 (sum <= 0)
        for(int i = 50; i >= 1 && sum > 0; --i) {
            if(cnt[i]) {
                // 只要還有這種尺寸的箱子，且蘋果還沒裝完，就繼續裝
                while(cnt[i]-- && sum > 0) {
                    ++rt;
                    sum -= i; // 直接扣除容量，即使變負數也代表裝完了
                }
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