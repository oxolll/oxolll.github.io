---
title: "LeetCode 1561. Maximum Number of Coins You Can Get"
date: 2026-03-27T12:17:00+08:00
lastmod: 2026-03-27T12:17:00+08:00
difficulty: 1405
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "greedy", "sorting"]
keywords: ["LC1561", "1405", "Medium", "Maximum Coins", "Greedy", "Sorting", "Two Pointers"]
description: "LeetCode 第 1561 題：Maximum Number of Coins You Can Get。難度評分：1405。探討如何利用排序與貪婪策略，藉由雙指標從兩端取值，確保每次都能獲取次大值並將最小值分配出去，以 O(N log N) 時間複雜度最大化硬幣總數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1561](https://leetcode.com/problems/maximum-number-of-coins-you-can-get/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1405)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
桌上有 `3n` 堆硬幣，每堆的硬幣數量由陣列 `piles` 給定。
你和你的朋友 Alice 和 Bob 將依照以下規則分硬幣：
1. 你挑選任意 3 堆硬幣。
2. Alice 將拿走 3 堆中數量最多的那一堆。
3. 你將拿走 3 堆中數量第二多的那一堆。
4. Bob 將拿走 3 堆中數量最少的那一堆。
5. 重複上述步驟直到沒有硬幣堆為止。

請回傳你能獲得的最大硬幣總數。

### 限制條件
- $3 \le piles.length \le 10^5$
- `piles.length % 3 == 0`
- $1 \le piles[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (排序與雙指標貪婪分配)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的目標是最大化你所能取得的硬幣數量（即每次挑選 3 堆時的「次大值」）。
基於貪婪策略 (Greedy Strategy)：
- 為了讓自己拿到盡可能大的數字，我們必須將全域最大的數字交給 Alice（因為 Alice 必定拿最大值）。
- 緊接著的全域第二大數字，就是我們能拿到的最大值。
- 同時，我們必須將全域最小的數字分配給 Bob，以消耗掉那些最沒有價值的硬幣堆。
因此，每回合的最佳選擇是：**取出當前剩餘最大的兩堆與最小的一堆**。

### 🛠️ 解題思路 (Approach)
1. **排序**：對 `piles` 陣列進行遞增排序。
2. **初始化雙指標**：
   - 左指標 `l = 0`，指向當前最小的硬幣堆（預計分給 Bob）。
   - 右指標 `r = piles.size() - 1`，指向當前最大的硬幣堆（預計分給 Alice）。
   - 宣告變數 `rt = 0` 記錄獲得的總硬幣數。
3. **貪婪選取**：
   - 建立 `while(l <= r)` 迴圈進行配對。
   - 每次迴圈中，最大值分配給 Alice（`--r`，右指標向左移一位）。
   - 次大值分配給自己，累加至結果（`rt += piles[r--]`，累加後右指標再向左移一位）。
   - 最小值分配給 Bob（`++l`，左指標向右移一位）。
4. **回傳結果**：當所有硬幣堆分配完畢後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是陣列的長度。對陣列進行排序主導了整體時間複雜度，後續的雙指標走訪僅需 $\mathcal{O}(N)$ 時間。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(1)$。取決於 `std::sort` 的內部實作。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxCoins(vector<int>& piles) {
        // 1. 遞增排序
        sort(piles.begin(), piles.end());
        
        int l = 0, r = piles.size() - 1;
        int rt = 0;
        
        // 2. 利用雙指標從兩端進行貪婪分配
        while(l <= r) {
            --r;              // Alice 拿走當前最大值
            rt += piles[r--]; // 自己拿走當前次大值，並累加
            ++l;              // Bob 拿走當前最小值
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