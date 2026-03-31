---
title: "LeetCode 435. Non-overlapping Intervals"
date: 2026-04-01T10:20:00+08:00
lastmod: 2026-04-01T10:20:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting"]
keywords: ["LC435", "Medium", "N/A", "Non-overlapping Intervals", "Greedy", "Interval Scheduling"]
description: "LeetCode 第 435 題：Non-overlapping Intervals。難度評分：Medium (N/A)。探討如何透過依據區間右端點排序的貪婪策略 (Greedy)，以 O(N log N) 時間複雜度求得必須移除的最小重疊區間數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 435](https://leetcode.com/problems/non-overlapping-intervals/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (右端點排序貪婪法)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的「區間排程問題 (Interval Scheduling)」。題目要求我們找出需要移除的「最少」區間數量，以使剩下的區間不重疊；這等價於尋找能夠保留下來的「最多」不重疊區間數量。
貪婪策略 (Greedy Strategy) 的核心在於：**若要留下最多的區間，我們應優先保留「結束時間最早」的區間**。因為越早結束的區間，就能留給後續區間越多的空間。
因此，我們將所有區間依照「右端點」進行遞增排序。遍歷陣列時，維護一個代表當前安全邊界的變數 `r`。若下一個區間的左端點小於 `r`，代表發生重疊，必須移除該區間；若大於等於 `r`，代表不重疊，則將該區間納入保留名單，並更新邊界 `r` 為該區間的右端點。

### 🛠️ 解題思路 (Approach)
1. **定義排序規則**：實作自定義比較函式 `cmp`，確保陣列中的區間完全按照 `a[1] < b[1]` (右端點遞增) 的順序排列。
2. **初始化變數**：
   - 呼叫 `sort` 進行排序。
   - 設定安全邊界 `r = INT_MIN`。
   - 設定移除計數器 `rt = 0`。
3. **線性掃描與貪婪決策**：
   - 遍歷所有的區間 `intervals[i]`。
   - 若當前區間的左端點小於安全邊界 (`intervals[i][0] < r`)，發生重疊，必須捨棄，計數器 `rt` 加 1。
   - 否則，不發生重疊，接納此區間，並將安全邊界更新為此區間的右端點 (`r = intervals[i][1]`)。
4. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是區間的數量。主要效能瓶頸在於排序，後續的貪婪判斷只需線性掃描 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(1)$。取決於 C++ `std::sort` 內部的遞迴呼叫深度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 自定義比較器：按照右端點遞增排序
    static bool cmp(vector<int>& a, vector<int>& b) {
        return a[1] < b[1];
    }
    
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end(), cmp);

        int r = INT_MIN; // 記錄當前不重疊區間的最右側邊界
        int rt = 0;      // 需要移除的區間數量
        
        for(int i = 0; i < intervals.size(); ++i) {
            // 左端點小於當前安全邊界，發生重疊，必須移除
            if(r > intervals[i][0]) {
                ++rt;
            } 
            // 無重疊，保留此區間並更新邊界
            else {
                r = intervals[i][1];
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