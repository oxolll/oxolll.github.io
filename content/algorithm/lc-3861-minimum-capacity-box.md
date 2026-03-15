---
title: "LeetCode 3861. Minimum Capacity Box"
date: 2026-03-09T13:00:00+08:00
lastmod: 2026-03-09T13:00:00+08:00
difficulty: 1154
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array"]
keywords: ["LC3861", "1154", "Easy", "Minimum Capacity"]
description: "LeetCode 第 3861 題：Minimum Capacity Box。難度評分：1154。探討如何透過一次遍歷尋找滿足容量條件且容量最小的箱子索引。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3861](https://leetcode.com/problems/minimum-capacity-box/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1154)</span>   
> **核心主題**：`Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `capacity`，其中 `capacity[i]` 代表第 `i` 個箱子的容量。另外給定一個整數 `itemSize` 代表物品的大小。
如果 `capacity[i] >= itemSize`，則第 `i` 個箱子可以存放該物品。

請回傳能存放該物品且**容量最小**的箱子索引。如果有多個箱子符合此條件，請回傳**最小的索引**。
如果沒有任何箱子可以存放該物品，回傳 `-1`。

### 限制條件
- $1 \le capacity.length \le 100$
- $1 \le capacity[i] \le 100$
- $1 \le itemSize \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-09：初次提交 (線性掃描尋找極值)](#2026-03-09-初次提交)

---

## 💡 2026-03-09 初次提交

### 🎯 直覺 (Intuition)
這是一道標準的陣列尋找極值題。我們只需要遍歷一次陣列，找出所有大於等於 `itemSize` 的容量中最小的那一個。題目要求如果有相同容量的解，必須回傳「最小索引」。只要我們從左到右掃描，並設定只有在找到「嚴格小於」當前最小容量時才更新，就能自然保留最早遇到的（最小的）索引。

### 🛠️ 解題思路 (Approach)
1. 宣告變數 `mm` 來記錄目前找到的最小合適容量（初始設為 `INT_MAX`），並宣告 `idx = -1` 記錄對應索引。
2. 從頭到尾遍歷 `capacity` 陣列。
3. 若當前容量大於等於 `itemSize`，且**嚴格小於**目前記錄的最小容量 `mm`：
   - 更新 `mm = capacity[i]`。
   - 更新 `idx = i`。
4. 遍歷結束後回傳 `idx`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$，其中 $N$ 為陣列長度。只需遍歷陣列一次。
- **空間複雜度**: $\mathcal{O}(1)$，僅使用常數空間變數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumIndex(vector<int>& capacity, int itemSize) {
        int mm = INT_MAX;
        int idx = -1;
        for(int i = 0; i < capacity.size(); ++i) {
            // 嚴格小於才更新，保證相同容量時留下最小的索引
            if(capacity[i] >= itemSize && mm > capacity[i]) {
                mm = capacity[i];
                idx = i;
            }
        }
        return idx;
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