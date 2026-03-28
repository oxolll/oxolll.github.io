---
title: "LeetCode 452. Minimum Number of Arrows to Burst Balloons"
date: 2026-03-28T13:45:00+08:00
lastmod: 2026-03-28T13:45:00+08:00
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
keywords: ["LC452", "Medium", "N/A", "Burst Balloons", "Intervals", "Greedy"]
description: "LeetCode 第 452 題：Minimum Number of Arrows to Burst Balloons。難度評分：Medium (N/A)。探討如何利用依據右端點排序的貪婪策略，以 O(N log N) 時間複雜度求出射破所有重疊區間所需的最小箭數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 452](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
在二維空間中，有一些球形氣球沿著 X 軸排列。
給你一個陣列 `points`，其中 $points[i] = [x_{start}, x_{end}]$ 代表第 $i$ 個氣球橫跨的區間範圍。
一支箭可以沿著 X 軸的任意點 $x$ 垂直向上射出。若 $x_{start} \le x \le x_{end}$，則該氣球會被射破。一支箭射出後可以無限向上飛行，射破沿途的所有氣球。
請回傳射破所有氣球所需的**最少**箭數。

### 限制條件
- $1 \le points.length \le 10^5$
- $points[i].length == 2$
- $-2^{31} \le x_{start} < x_{end} \le 2^{31} - 1$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-28：初次提交 (右端點排序與區間貪婪法)](#2026-03-28-初次提交)

---

## 💡 2026-03-28 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是極為經典的區間重疊問題。為了使用最少的箭，我們希望一支箭能穿透盡可能多的氣球。
貪婪策略 (Greedy Strategy) 的核心在於：**將所有區間依照「右端點」遞增排序**。
當我們從左向右掃描時，為了射破當前遇到的第一個氣球，且盡可能牽連後面的氣球，我們應該將箭射在**當前氣球的最右側邊界**。
如果下一個氣球的左端點小於或等於這支箭的位置，代表它也會被順便射破；如果下一個氣球的左端點大於這支箭的位置，代表必須再發射一支新箭，並更新射擊位置為新氣球的右端點。

*(註：原程式碼使用了維護發射點陣列與二分搜尋 `lower_bound` 的邏輯，這在邏輯上可行，但維護單一發射點即可達到 $\mathcal{O}(N)$ 的掃描效能。以下解析保留原程式碼邏輯並加以說明。)*

### 🛠️ 解題思路 (Approach)
1. **排序**：自定義比較函式 `cmp`，將 `points` 陣列依照區間的**右端點**由小到大進行排序。
2. **維護發射點陣列**：宣告一維陣列 `back`，儲存每一支箭的發射座標（即氣球的右端點）。
3. **線性掃描與驗證**：
   - 遍歷排序後的 `points` 陣列，取得當前氣球的左右端點 `l` 與 `r`。
   - 使用 `lower_bound` 在 `back` 陣列中尋找第一個 $\ge l$ 的箭的位置 `k`。
   - 條件判斷：若找不到這樣的箭 (`k == back.size()`)，或者找到的箭已經超過了當前氣球的右邊界 (`back[k] > r`)，代表之前的箭無法射破當前氣球。
   - 此時必須發射一支新箭，將當前氣球的右端點 `r` 推入 `back` 陣列。
4. **回傳結果**：回傳 `back.size()`，即為所需發射的箭數。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是區間的數量。主要的效能瓶頸在於對 `points` 進行排序。後續迴圈內使用二分搜尋的總時間亦為 $\mathcal{O}(N \log N)$。*(註：若僅使用單一變數記錄上一支箭的位置，後續掃描可優化至 $\mathcal{O}(N)$，但整體複雜度仍被排序主導。)*
- **空間複雜度**: $\mathcal{O}(N)$。宣告了陣列 `back` 來儲存箭的發射位置，最差情況下（所有氣球互不重疊）需要發射 $N$ 支箭。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 依據右端點進行遞增排序
    static bool cmp(const vector<int>& a, const vector<int>& b) {
        return a[1] < b[1];
    }
    
    int findMinArrowShots(vector<vector<int>>& points) {
        sort(points.begin(), points.end(), cmp);
        
        vector<int> back; // 儲存已發射箭的 X 座標
        int n = points.size();
        
        for(int i = 0; i < n; ++i) {
            int l = points[i][0], r = points[i][1];
            
            // 尋找是否有一支已經發射的箭，其位置落在當前氣球的範圍內 [l, r]
            auto k = lower_bound(back.begin(), back.end(), l) - back.begin();
            
            // 若找不到落在左邊界右側的箭，或是找到的箭超出了右邊界，則必須射新箭
            if(k == back.size() || back[k] > r) {
                back.push_back(r); // 貪婪地射在當前氣球的最右側
            }
        }

        return back.size();
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