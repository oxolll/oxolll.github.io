---
title: "LeetCode 2574. Left and Right Sum Differences"
date: 2026-06-06T18:35:00+08:00
lastmod: 2026-06-06T18:35:00+08:00
difficulty: 1206
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum"]
keywords: ["LC2574", "Easy", "1206", "Left and Right Sum Differences", "前綴和", "滾動變數", "陣列遍歷"]
description: "LeetCode 第 2574 題：Left and Right Sum Differences。難度評分：1206。探討如何利用總和與滾動變數的互補關係，捨棄傳統的前綴/後綴和陣列，以 O(N) 時間複雜度與 O(1) 輔助空間極速求出左右總和之絕對差。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2574](https://leetcode.com/problems/left-and-right-sum-differences/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1206)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個下標從 **0** 開始的整數陣列 `nums`，請你回傳一個長度相同的整數陣列 `answer`。
其中 `answer[i]` 必須滿足：
`answer[i] = |leftSum[i] - rightSum[i]|`

- `leftSum[i]` 是陣列中索引 `i` 左側元素的總和。如果沒有左側元素，則 `leftSum[i] = 0`。
- `rightSum[i]` 是陣列中索引 `i` 右側元素的總和。如果沒有右側元素，則 `rightSum[i] = 0`。

### 範例
- **Input**: `nums = [10,4,8,3]`
- **Output**: `[15,1,11,22]`
- **Explanation**: 
  陣列 `leftSum` 為 `[0, 10, 14, 22]`。
  陣列 `rightSum` 為 `[15, 11, 3, 0]`。
  陣列 `answer` 為 `[|0 - 15|, |10 - 11|, |14 - 3|, |22 - 0|] = [15, 1, 11, 22]`。

- **Input**: `nums = [1]`
- **Output**: `[0]`
- **Explanation**: 
  陣列 `leftSum` 為 `[0]`。
  陣列 `rightSum` 為 `[0]`。
  陣列 `answer` 為 `[|0 - 0|] = [0]`。

### 限制條件
- $1 \le nums.length \le 1000$
- $1 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-06：初次提交 (滾動變數與 O(1) 空間極致優化)](#2026-06-06-初次提交)

---

## 💡 2026-06-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的目標是計算每個元素左側與右側的總和差異。
最直觀的做法是建立兩個陣列 `leftSum` 和 `rightSum`，分別從左向右和從右向左掃描一次，但這會浪費 $\mathcal{O}(N)$ 的輔助空間。

**核心破局點：總和守恆與滾動變數 (Running Variables)**
由於所有元素的總和是固定的，當我們由左向右掃描陣列時：
**「當前元素右側的總和」 = 「陣列總和」 - 「當前元素左側的總和」 - 「當前元素本身」**

這意味著我們只需要：
1. 先遍歷一次算出陣列的**總和**。
2. 在第二次遍歷時，維護一個變數 `l` 作為「左側總和」。
3. 隨著指標前進，不斷將當前元素從總和中扣除（讓總和變數轉化為「右側總和」），計算兩者差值的絕對值，再把當前元素加入 `l` 以供下一回合使用。

### 🛠️ 解題思路 (Approach)
1. **初始化與計算總和**：
   - 宣告總和變數 `s = 0`。
   - 遍歷 `nums`，將所有元素累加至 `s`。
2. **滾動計算絕對差**：
   - 宣告答案陣列 `rt`，與左側總和變數 `l = 0`。
   - 遍歷 `nums` 中的每一個元素 `nums[i]` (暫存為 `tmp`)：
     - **更新右側總和**：將 `tmp` 從 `s` 中扣除，此時的 `s` 恰好等價於題目要求的 `rightSum[i]`。
     - **計算答案**：將 `abs(l - s)` 推入答案陣列 `rt` 中。
     - **更新左側總和**：將 `tmp` 加到 `l` 中，此時的 `l` 準備好作為下一個元素 `i+1` 的 `leftSum`。
3. **回傳結果**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 為陣列長度。我們只需要對陣列進行兩次獨立的線性掃描（一次求總和，一次結算答案）。
- **空間複雜度**: $\mathcal{O}(1)$。除了用於回傳答案的 `rt` 陣列外，我們僅使用了常數個整數變數 (`s`, `l`, `tmp`)，為原地演算法的極致優化。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> leftRightDifference(vector<int>& nums) {
        int n = nums.size();
        
        // 1. 計算所有元素的總和
        int s = 0;
        for(auto num : nums) {
            s += num;
        }
        
        vector<int> rt;
        // l 用於動態追蹤左側總和 (leftSum)
        int l = 0; 
        
        // 2. 邊遍歷邊動態維護左右總和
        for(int i = 0; i < n; ++i) {
            int tmp = nums[i];
            
            // 將當前元素從總和中剝離，此時 s 變為右側總和 (rightSum)
            s -= tmp;
            
            // 計算絕對差並記錄
            rt.push_back(abs(l - s));
            
            // 將當前元素加入左側總和，供下一回合使用
            l += tmp;
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