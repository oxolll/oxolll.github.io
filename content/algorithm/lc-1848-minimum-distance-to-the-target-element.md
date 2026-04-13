---
title: "LeetCode 1848. Minimum Distance to the Target Element"
date: 2026-04-13T14:30:00+08:00
lastmod: 2026-04-13T14:30:00+08:00
difficulty: 1216
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "simulation"]
keywords: ["LC1848", "1216", "Easy", "Minimum Distance", "Target Element", "Simulation"]
description: "LeetCode 第 1848 題：Minimum Distance to the Target Element。難度評分：1216。探討如何透過線性掃描 (Linear Scan) 或是雙向擴展 (Bidirectional Search) 提早結束的技巧，以 O(N) 時間複雜度快速找出陣列中目標元素距離指定起點的最短絕對距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1848](https://leetcode.com/problems/minimum-distance-to-the-target-element/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1216)</span>  
> **核心主題**：`Array` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums` （索引從 **0** 開始）以及兩個整數 `target` 和 `start`。已知 `target` 必定存在於 `nums` 中。
請找出一個索引 `i`，滿足 `nums[i] == target` 且 `abs(i - start)` 的值為**最小**。
請回傳這個最小的絕對距離 `abs(i - start)`。

### 限制條件
- $1 \le nums.length \le 1000$
- $1 \le nums[i] \le 10^4$
- $0 \le start < nums.length$
- `target` 必定存在於 `nums` 中。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-13：初次提交 (線性掃描與雙向擴展優化)](#2026-04-13-初次提交)

---

## 💡 2026-04-13 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題目標是找到距離起點 `start` 最近的目標元素 `target`。
- **解法一 (直球對決)：** 直接遍歷整個陣列，只要找到數值等於 `target` 的元素，就計算它與 `start` 的絕對距離，並隨時更新全域最小值。這個寫法最直觀、最不容易出錯。
- **解法二 (雙向擴展優化)：** 既然要找「距離 `start` 最近」的目標，我們可以改變尋找順序。直接以 `start` 為中心，設定距離 $d = 0, 1, 2 \dots$，同步向左檢查 `start - d` 與向右檢查 `start + d`。因為我們是依照距離由近往遠找，所以**第一次遇到 `target` 時的距離 $d$，就絕對是最小距離**，可以直接提早中斷 (Early Exit)，在平均情況下效能會更好。

### 🛠️ 解題思路 (Approach)
**(此處以線性掃描法為例)**
1. 宣告一個變數 `rt` 記錄最短距離，初始值設為無限大 (`INT_MAX`)。
2. 建立 `for` 迴圈，索引 `i` 從 `0` 遍歷至 `nums.size() - 1`。
3. 若 `nums[i] == target`，計算其絕對距離 `abs(i - start)`。
4. 使用 `min` 函數比較並更新 `rt`。
5. 迴圈結束後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。線性掃描需檢查每個元素一次；雙向擴展在最差情況下（目標在陣列最遠端）也只需 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個變數。

### 💻 程式碼實作 (C++)

#### 版本一：線性掃描 (直觀易懂)
```cpp
class Solution {
public:
    int getMinDistance(vector<int>& nums, int target, int start) {
        int rt = INT_MAX;
        
        for(int i = 0; i < nums.size(); ++i) {
            if(nums[i] == target) {
                // 找到目標，更新最小絕對距離
                rt = min(rt, abs(i - start));
            }
        }
        
        return rt;
    }
};
```

#### 版本二：雙向擴展 (面試優化版，提早結束)
```cpp
class Solution {
public:
    int getMinDistance(vector<int>& nums, int target, int start) {
        int n = nums.size();
        
        // 距離 d 從 0 開始逐漸增加
        for(int d = 0; d < n; ++d) {
            // 向右檢查
            if(start + d < n && nums[start + d] == target) return d;
            // 向左檢查
            if(start - d >= 0 && nums[start - d] == target) return d;
        }
        
        return 0;
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