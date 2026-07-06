---
title: "LeetCode 1288. Remove Covered Intervals"
date: 2026-07-06T14:45:00+08:00
lastmod: 2026-07-06T14:45:00+08:00
difficulty: 1375
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "sorting", "greedy"]
keywords: ["LC1288", "Medium", "1375", "Remove Covered Intervals", "區間問題", "貪婪演算法", "自訂排序"]
description: "LeetCode 第 1288 題：Remove Covered Intervals。難度評分：1375。探討如何利用自訂排序策略 (起點遞增、終點遞減)，搭配貪婪演算法 (Greedy) 的右邊界追蹤，在 O(N log N) 時間內完美剃除所有被覆蓋的子區間。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1288](https://leetcode.com/problems/remove-covered-intervals/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1375)</span>  
> **核心主題**：`Array` $\cdot$ `Sorting` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個二維陣列 `intervals`，其中 $intervals[i] = [l_i, r_i]$ 代表一個區間。
區間 $[a, b]$ 被區間 $[c, d]$ **覆蓋 (Covered)** 的嚴格定義為：$c \le a$ 且 $b \le d$。

請你刪除所有被其他區間覆蓋的子區間。
回傳陣列中**剩餘的區間數量**。

### 範例
- **Input**: `intervals = [[1,4],[3,6],[2,8]]`
- **Output**: `2`
- **Explanation**: 
  區間 `[3,6]` 被區間 `[2,8]` 完全覆蓋 ($2 \le 3$ 且 $6 \le 8$)，因此將其刪除。
  剩餘的區間為 `[1,4]` 和 `[2,8]`。

- **Input**: `intervals = [[1,4],[2,3]]`
- **Output**: `1`

### 限制條件
- $1 \le intervals.length \le 1000$
- $intervals[i].length == 2$
- $0 \le l_i < r_i \le 10^5$
- 所有給定的區間皆 **互不相同**。
</details>

---

## 📝 歷次打卡與演算法進化
- [2026-07-06：初次提交 (自訂排序與 O(N) 貪婪掃描)](#2026-07-06-初次提交)

---

## 💡 2026-07-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理區間問題 (Interval Problems) 的起手式幾乎都是**排序 (Sorting)**。
若想用 $\mathcal{O}(N)$ 的時間掃描出所有被覆蓋的區間，我們必須讓「可能覆蓋別人的大區間」盡量排在前面。

**核心破局點 1：自訂排序策略 (Custom Sorting)**
我們將區間依照以下規則排序：
1. **起點由小到大 (Ascending by Start)**：這樣能保證後面的區間，起點一定大於或等於前面的區間（滿足覆蓋條件 $c \le a$）。
2. **起點相同時，終點由大到小 (Descending by End)**：這是最關鍵的一步！假設有 `[1, 8]` 和 `[1, 4]` 兩個區間。如果終點由小到大排，會變成 `[1, 4]` 先出現，此時它無法吞噬 `[1, 8]`；但如果終點由大到小排，`[1, 8]` 會先出現，接下來掃描到 `[1, 4]` 時，就能輕鬆將其判定為被覆蓋。

**核心破局點 2：貪婪右邊界追蹤 (Greedy Right Bound Tracking)**
排序完成後，我們只需維護一個變數 `r` 來記錄「目前為止所見過的最遠右邊界」。
- 如果當前區間的右邊界 `x[1] <= r`，代表這個區間完全被包含在之前的某個大區間內，我們將剩餘總數 `c` 減 1。
- 如果 `x[1] > r`，代表這個區間突破了目前的覆蓋範圍，我們更新 `r = x[1]`，繼續往後探測。

### 🛠️ 解題思路 (Approach)
1. 取得區間總數 `n`。
2. 使用 `std::sort` 搭配自訂 Lambda 函式對 `intervals` 進行排序。
   - `x[0] < y[0]` (起點遞增)。
   - 起點相同時，`x[1] > y[1]` (終點遞減)。
3. 初始化 `r = -1` (紀錄最遠右邊界) 與 `c = n` (紀錄剩餘區間數)。
4. 遍歷排序後的 `intervals`：
   - 若 `x[1] <= r`，觸發覆蓋條件，剩餘數量 `--c`。
   - 若否，更新最遠右邊界 `r = x[1]`。
5. 回傳最終剩餘數量 `c`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是 `intervals` 的長度。主要的效能瓶頸在於排序操作，後續的貪婪掃描只需要 $\mathcal{O}(N)$ 的線性時間。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(N)$。取決於 `std::sort` 在不同程式語言底層實作（例如 C++ 的 IntroSort）所需要的遞迴堆疊空間。演算法本身只需常數級別 $\mathcal{O}(1)$ 的額外變數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int removeCoveredIntervals(vector<vector<int>>& intervals) {
        int n = intervals.size();
        
        // 1. 自訂排序：起點小到大；若起點相同，終點大到小
        sort(intervals.begin(), intervals.end(), [](auto& x, auto& y){
            if(x[0] == y[0]) return x[1] > y[1];
            return x[0] < y[0];
        });

        int r = -1; // 記錄目前為止的最遠右邊界
        int c = n;  // 記錄剩餘區間數量，預設為全保留
        
        // 2. 貪婪掃描
        for(auto& x : intervals) {
            // 因為起點已經遞增排序，只要終點 <= 目前的最遠右邊界，就必定被覆蓋
            if(x[1] <= r) {
                --c;
            } else {
                // 無法被覆蓋，更新最遠右邊界
                r = x[1];
            }
        }

        return c;
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