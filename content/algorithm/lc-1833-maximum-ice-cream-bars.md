---
title: "LeetCode 1833. Maximum Ice Cream Bars"
date: 2026-04-03T22:55:00+08:00
lastmod: 2026-04-03T22:55:00+08:00
difficulty: 1252
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting", "counting sort"]
keywords: ["LC1833", "1252", "Medium", "Maximum Ice Cream Bars", "Greedy", "Counting Sort"]
description: "LeetCode 第 1833 題：Maximum Ice Cream Bars。難度評分：1252。探討如何利用冰棒價格上限的值域特性，以計數排序 (Counting Sort) 取代傳統排序，結合貪婪策略 (Greedy) 達成 O(N + M) 的最佳時間複雜度。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1833](https://leetcode.com/problems/maximum-ice-cream-bars/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1252)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
夏天到了，男孩想買一些冰棒。
給定一個長度為 $n$ 的整數陣列 `costs`，其中 `costs[i]` 代表第 $i$ 支冰棒的價格（單位：硬幣）。男孩最初有 `coins` 個硬幣可以花費。
請回傳男孩用這些硬幣**最多**可以買多少支冰棒。
注意：男孩可以按任意順序購買冰棒。

### 限制條件
- $costs.length == n$
- $1 \le n \le 10^5$
- $1 \le costs[i] \le 10^5$
- $1 \le coins \le 10^8$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-03：初次提交 (計數排序與貪婪法)](#2026-04-03-初次提交)

---

## 💡 2026-04-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求最大化購買的冰棒數量，根據貪婪策略 (Greedy Strategy)，我們應該**優先購買最便宜的冰棒**。
直觀作法是將 `costs` 陣列進行遞增排序後依序扣除金幣，時間複雜度為 $\mathcal{O}(N \log N)$。
然而，觀察題目限制可以發現，冰棒的最大價格 $costs[i]$ 僅為 $10^5$。這個極小的值域非常適合使用 **計數排序 (Counting Sort)**。我們可以宣告一個長度為 $10^5+5$ 的頻率陣列，一次遍歷統計各個價位有幾支冰棒，接著從價格 1 遍歷至 $10^5$ 進行貪婪購買，將整體時間複雜度優化至線性的 $\mathcal{O}(N + M)$。

### 🛠️ 解題思路 (Approach)
1. **頻率統計**：
   - 宣告大小為 $10^5 + 5$ 的陣列 `cnt`，並初始化為 0。
   - 走訪 `costs` 陣列，對每一個價格 `c` 執行 `++cnt[c]`。
2. **貪婪購買**：
   - 宣告變數 `rt = 0` 記錄已購買的冰棒總數。
   - 建立迴圈，價格 `i` 從 1 遍歷至 $10^5$。迴圈條件額外加入 `coins > 0`，若金幣已耗盡則提前中斷 (Early Exit)。
   - 當該價位有冰棒時 (`cnt[i] > 0`)：
     - 計算最多能買幾支：取「該價位全部買下的總花費 (`cnt[i] * i`)」與「剩餘金幣 (`coins`)」兩者之中的較小值，然後除以單價 `i`，即為在該價位實際購買的數量。*(註：為防止整數溢位，乘法運算時需轉換為 `1LL`)*。
     - 將購買數量累加至 `rt`。
     - 從 `coins` 中扣除相應的花費 `i * 1LL * cnt[i]`。*(註：即便扣到變負數，迴圈條件 `coins > 0` 也會保證在下一輪直接跳出)*。
3. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M)$。其中 $N$ 是 `costs` 陣列的長度，$M$ 是最大冰棒價格 ($10^5$)。統計頻率需 $\mathcal{O}(N)$，貪婪遍歷陣列需 $\mathcal{O}(M)$，皆為線性時間。
- **空間複雜度**: $\mathcal{O}(M)$。宣告了長度為 $M$ 的 `cnt` 陣列來儲存頻率。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxIceCream(vector<int>& costs, int coins) {
        // 利用值域上限 10^5，改用計數排序 (Counting Sort)
        vector<int> cnt(1e5 + 5, 0);
        for(auto c : costs) {
            ++cnt[c];
        }

        int rt = 0;
        
        // 由最便宜的價格開始貪婪購買，金幣耗盡時提前跳出
        for(int i = 1; i <= 1e5 && coins > 0; ++i) {
            if(cnt[i]) {
                // 取「該價位全部冰棒總價」與「剩餘金幣」之較小值，換算為可購買數量
                // 使用 1LL 避免整數溢位 (Integer Overflow)
                rt += min(cnt[i] * 1LL * i, coins * 1LL) / i;
                
                // 扣除假設全買的花費 (若金幣不足，下一輪判斷 coins > 0 會自動跳出)
                coins -= i * 1LL * cnt[i]; 
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