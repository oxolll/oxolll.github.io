---
title: "LeetCode 3300. Minimum Element After Replacement With Digit Sum"
date: 2026-05-29T13:10:00+08:00
lastmod: 2026-05-29T13:10:00+08:00
difficulty: 1181
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math"]
keywords: ["LC3300", "Easy", "1181", "Minimum Element", "Digit Sum", "數學", "位數和"]
description: "LeetCode 第 3300 題：Minimum Element After Replacement With Digit Sum。難度評分：1181。探討如何避免昂貴的字串轉換，直接利用模除與除法等數學操作，以 O(1) 空間與最佳時間複雜度高效計算數字的位數和並尋找極值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3300](https://leetcode.com/problems/minimum-element-after-replacement-with-digit-sum/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1181)</span>  
> **核心主題**：`Array` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums`。
請你將陣列中的每個元素替換為其 **位數總和 (Digit Sum)**。
請回傳替換後陣列中的 **最小元素**。

### 範例
- **Input**: `nums = [10, 12, 13, 14]`
- **Output**: `1`
- **Explanation**: 
  - 10 的位數和為 1 + 0 = 1。
  - 12 的位數和為 1 + 2 = 3。
  - 13 的位數和為 1 + 3 = 4。
  - 14 的位數和為 1 + 4 = 5。
  替換後的陣列為 `[1, 3, 4, 5]`，最小值為 1。

- **Input**: `nums = [1, 2, 3, 4]`
- **Output**: `1`
- **Explanation**: 位數和分別為 `[1, 2, 3, 4]`，最小值為 1。

- **Input**: `nums = [999, 19, 199]`
- **Output**: `10`
- **Explanation**: 位數和分別為 `[27, 10, 19]`，最小值為 10。

### 限制條件
- $1 \le nums.length \le 100$
- $1 \le nums[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-29：初次提交 (純數學位數拆解法)](#2026-05-29-初次提交)

---

## 💡 2026-05-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標是找出所有數字在「位數相加」後的最小值。
最直觀但不夠優雅的作法是將整數轉換為字串（如 `to_string(num)`），然後遍歷字元進行相加。字串轉換會涉及動態記憶體分配，帶來不必要的常數時間開銷。

**核心破局點：數學拆解 (Mathematical Extraction)**
處理數字位數最有效率的標準作法，是利用基本的數學運算：
1. **取餘數 (`k % 10`)**：精準萃取出該數字的「最後一個位數」(個位數)。
2. **整數除法 (`k /= 10`)**：將數字向右推移，捨棄剛剛萃取出的個位數。
透過 `while(k > 0)` 迴圈反覆執行這兩個動作，我們就能在完全不依賴字串操作的情況下，原地 (In-place) 計算出總和。

### 🛠️ 解題思路 (Approach)
1. **初始化最小值追蹤器**：建立一個變數 `mm` 並初始化為 `INT_MAX`，用來記錄我們目前看過的最小位數和。
2. **遍歷陣列**：使用 `for` 迴圈掃描 `nums` 陣列中的每一個數字 `num`。
3. **計算位數和**：
   - 針對每個數字，宣告一個區域變數 `tmp = 0` 來累加位數和。
   - 將 `num` 複製給變數 `k`。
   - 使用 `while(k)` 迴圈，每次將 `k % 10` 加進 `tmp` 中，並執行 `k /= 10`。
4. **更新極值**：該數字計算完畢後，利用 `mm = min(mm, tmp)` 更新全局最小值。
5. **回傳結果**：掃描完整個陣列後，`mm` 即為最終答案。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \cdot \log_{10}(M))$
  其中 $N$ 是 `nums` 陣列的長度，$M$ 是陣列中的最大數值。
  外層迴圈執行 $N$ 次，內層的 `while` 迴圈執行次數取決於數字的位數長度，即 $\approx \log_{10}(M)$。在限制條件下，時間開銷微乎其微。
- **空間複雜度**: $\mathcal{O}(1)$
  演算法完全就地執行 (In-place)。我們僅配置了常數個基本整數變數 (`mm`, `tmp`, `k`)，記憶體足跡不隨輸入規模成長而改變。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minElement(vector<int>& nums) {
        // Track the global minimum digit sum
        int mm = INT_MAX;
        
        for(auto num : nums) {
            int tmp = 0;
            int k = num;
            
            // Extract and sum each digit mathematically
            while(k) {
                tmp += k % 10;
                k /= 10;
            }
            
            // Update the minimum digit sum found so far
            mm = min(mm, tmp);
        }
        
        return mm;
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