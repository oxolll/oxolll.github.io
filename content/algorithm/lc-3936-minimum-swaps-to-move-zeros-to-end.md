---
title: "LeetCode 3936. Minimum Swaps to Move Zeros to End"
date: 2026-05-24T15:30:00+08:00
lastmod: 2026-05-24T15:30:00+08:00
difficulty: 1346
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "counting", "math", "greedy"]
keywords: ["LC3936", "Easy", "Minimum Swaps", "Move Zeros", "陣列", "貪心算法"]
description: "LeetCode 第 3936 題：Minimum Swaps to Move Zeros to End。難度評分：Easy。探討在允許任意交換且不要求相對順序的前提下，如何將「交換次數」轉化為「錯位佔用計數」，以 O(N) 時間極速求解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3936](https://leetcode.com/problems/minimum-swaps-to-move-zeros-to-end/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Counting` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums`，你可以執行任意次數的操作：選擇陣列中的**任意兩個**元素並交換它們的位置。
請你計算將所有的 `0` 移動到陣列末尾所需的 **最少交換次數**。
*(註：不需要保持非零元素的相對順序。)*

### 範例
- **Input**: `nums = [0, 1, 0, 3, 12]`
- **Output**: `2`
- **Explanation**: 
  陣列中有 3 個非零元素，2 個零。
  將前面的 0 與後面的非零元素交換，最少只需 2 次即可讓所有 0 到達末端。

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-24：初次提交 (坑位計數貪心法)](#2026-05-24-初次提交)

---

## 💡 2026-05-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
如果題目要求「保持非零元素的相對順序」，我們通常會使用雙指標模擬。但本題只要求「最少交換次數」將所有的 $0$ 移到末尾，這使得問題能大幅簡化為一個**數學分區問題**。

**核心破局點：錯位佔用 (Misplaced Elements)**
假設陣列長度為 $N$，其中 `0` 的數量為 $Z$。
1. **最終理想狀態**：陣列的前 $N - Z$ 個位置必須全部都是非零元素（我們稱之為「非零區」），最後的 $Z$ 個位置全是 `0`。
2. **交換的本質**：每一次最優的交換，必定是將「非零區」裡的一個 `0`，與「零區」裡的一個非零元素進行互換。一次交換可以完美修復兩個錯誤。
3. **結論**：我們只需要統計**「非零區」裡面目前到底混進了幾個 `0`**，這個數量就是我們必須執行交換的最少次數。

### 🛠️ 解題思路 (Approach)
1. **第一次遍歷（統計）**：走訪整個陣列，計算 `0` 的總數量，記為 `zeroc`。
2. **界定目標區域**：計算出「非零區」的長度，即 `nums.size() - zeroc`。
3. **第二次遍歷（清點錯位）**：只走訪「非零區」 $[0, \text{nums.size()} - \text{zeroc} - 1]$。每遇到一個 `0`，就代表需要一次交換來把它踢出去，將結果 `rt` 加 1。
4. **回傳結果**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。共需進行兩次陣列掃描（第二次甚至是部分掃描），運行時間與陣列長度成正比，達到理論最優。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了 `zeroc` 和 `rt` 兩個常數變數，完全不需要額外的儲存空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumSwaps(vector<int>& nums) {
        // 1. 統計陣列中 0 的總數量
        int zeroc = 0;
        for(auto num : nums) {
            if(num == 0) ++zeroc;
        }

        int rt = 0;
        // 2. 理想狀態下，前 (nums.size() - zeroc) 個位置應該全是非零元素
        // 我們只需要檢查這個「非零專屬區」裡，被幾個 0 給佔據了
        for(int i = 0; i < nums.size() - zeroc; ++i) {
            // 每發現一個佔著茅坑不拉屎的 0，就需要一次交換把它丟到後面
            if(nums[i] == 0) {
                ++rt;
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