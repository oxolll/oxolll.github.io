---
title: "LeetCode 1732. Find the Highest Altitude"
date: 2026-06-19T14:15:00+08:00
lastmod: 2026-06-19T14:15:00+08:00
difficulty: 1256
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum"]
keywords: ["LC1732", "Easy", "1256", "Highest Altitude", "前綴和", "陣列", "即時更新"]
description: "LeetCode 第 1732 題：Find the Highest Altitude。難度評分：1256。探討如何運用動態前綴和 (Prefix Sum) 的滾動變數思維，在 O(N) 時間與 O(1) 空間的極致效能下追蹤並計s算旅程中的最高海拔。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1732](https://leetcode.com/problems/find-the-highest-altitude/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1256)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
有一個單車騎士正在進行一場公路旅行。這場旅行包含 $n + 1$ 個不同的點，編號從 $0$ 到 $n$。騎士從海拔為 `0` 的點 `0` 開始出發。

給你一個長度為 $n$ 的整數陣列 `gain`，其中 `gain[i]` 代表點 `i` 和點 `i + 1` 之間的 **海拔淨增量** ($0 \le i < n$)。請你計算並回傳這場旅行中，騎士所到達的 **最高海拔**。

### 範例
- **Input**: `gain = [-5,1,5,0,-7]`
- **Output**: `1`
- **Explanation**: 
  每一步的海拔變化過程如下：
  - 起始點 0 的海拔為 `0`。
  - 走完第一段後的海拔為 `0 + (-5) = -5`。
  - 走完第二段後的海拔為 `-5 + 1 = -4`。
  - 走完第三段後的海拔為 `-4 + 5 = 1`。
  - 走完第四段後的海拔為 `1 + 0 = 1`。
  - 走完第五段後的海拔為 `1 + (-7) = -6`。
  最高的海拔高度為 1。

- **Input**: `gain = [-4,-3,-2,-1,4,3,2]`
- **Output**: `0`
- **Explanation**: 海拔變化過程為 `[0, -4, -7, -9, -10, -6, -3, -1]`，最高海拔為 0。

### 限制條件
- $n == gain.length$
- $1 \le n \le 100$
- $-100 \le gain[i] \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-19：初次提交 (動態前綴和最優解)](#2026-06-19-初次提交)

---

## 💡 2026-06-19 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的核心本質是求出**前綴和 (Prefix Sum)** 序列中的最大值。
如果我們把每一步過後的絕對海拔都記錄在一個新的陣列中，會耗費額外的空間。

**核心破局點：滾動前綴和與即時更新**
由於我們只關心「歷史最高海拔」，而不關心每一步的具體海拔紀錄。因此我們只需要維護兩個變數：
- `c` (current)：記錄當前的絕對海拔高度，隨著遍歷 `gain` 陣列而動態累加。
- `rt` (result)：記錄過程中所見過的最高海拔高度。

在每一次海拔發生變化時，立刻執行 `rt = max(rt, c)`，這樣就能在一次線性掃描中完成所有計算，並省去陣列的開銷。

---

### 🛠️ 解題思路 (Approach)
1. **初始化狀態**：
   - 騎士從點 0 出發，初始海拔高度 `c = 0`。
   - 全局最高海拔 `rt = 0` (因為起點就是 0，最高海拔絕不可能小於 0)。
2. **線性掃描**：遍歷 `gain` 陣列中的每一個元素 `g`：
   - `c += g`：更新當前所處的海拔高度。
   - `rt = max(rt, c)`：若當前海拔高於歷史紀錄，則重新整理最高海拔。
3. **回傳結果**：遍歷完成後，回傳 `rt` 即可。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是 `gain` 陣列的長度。我們只需要從頭到尾遍歷陣列一次，內部操作皆為常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了 `c` 與 `rt` 兩個整數變數，沒有使用任何額外的記憶體空間，已達物理極限最優。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int largestAltitude(vector<int>& gain) {
        int c = 0;  // 記錄當前的絕對海拔高度 (Current Altitude)
        int rt = 0; // 記錄旅程中的最高海拔高度 (Result / Highest Altitude)
        
        // 線性掃描淨增量陣列
        for(auto g : gain) {
            c += g;          // 累加當前的海拔變化量
            rt = max(rt, c); // 即時更新歷史最高紀錄
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