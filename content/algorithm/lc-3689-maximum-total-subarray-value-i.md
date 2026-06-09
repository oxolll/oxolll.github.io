---
title: "LeetCode 3689. Maximum Total Subarray Value I"
date: 2026-06-09T13:52:00+08:00
lastmod: 2026-06-09T13:52:00+08:00
difficulty: 1370
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "brainteaser", "math"]
keywords: ["LC3689", "Medium", "1370", "Maximum Total Subarray Value", "貪心演算法", "腦筋急轉彎", "極值維護"]
description: "LeetCode 第 3689 題：Maximum Total Subarray Value I。難度評分：1370。探討如何識破子陣列區間極值的偽裝，利用貪心直覺 (Greedy) 直接捕捉全域最大與最小值，以 O(N) 線性時間與 O(1) 空間達成極致最優解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3689](https://leetcode.com/problems/maximum-total-subarray-value-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1370)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Brainteaser`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums` 和一個整數 `k`。
請你找出 `nums` 中某個連續子陣列，並計算該子陣列的 **Total Value (總價值)**。
總價值的計算方式為：該子陣列中的 **最大值** 減去 **最小值**，並將結果乘上 $k$。
請回傳所有可能子陣列中，最大的總價值。

### 限制條件
- $1 \le nums.length \le 10^5$
- $-10^4 \le nums[i] \le 10^4$
- $1 \le k \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (全域極值貪心法)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道標準的 **腦筋急轉彎 (Brainteaser)** 與 **貪心演算法 (Greedy)** 題。
題目要求尋找某個「連續子陣列」使其 $(\max - \min) \times k$ 最大化。
如果按照常規思維，可能會試圖去枚舉所有子陣列並動態維護極值，這將導致演算法變得極度複雜。

**核心破局點：極值包容性**
我們反過來想：對於任何陣列，**局部的最大差值絕對不可能大於全域的最大差值**。
也就是說，如果我們找出整個 `nums` 陣列中的「全域最大值 `mx`」與「全域最小值 `mm`」，那麼 `mx - mm` 就是這個陣列所能產生的極限最大差值。
因為題目允許任意長度的連續子陣列，我們只要把子陣列的範圍「拉到最長」，或者至少長到「剛好同時包含全域 `mx` 與 `mm`」，就能無條件獲得這個極限最大差值。

因此，我們根本不需要去尋找具體的子陣列邊界，只需要找出整個陣列的 `mx` 和 `mm` 即可！

### 🛠️ 解題思路 (Approach)
1. **初始化極值**：宣告變數 `mm` 為 `INT_MAX` (用來尋找最小值)，`mx` 為 `INT_MIN` (用來尋找最大值)。
2. **線性掃描**：遍歷陣列 `nums` 中的每一個元素 `num`。
   - `mm = min(mm, num)`：不斷刷新全域最小值。
   - `mx = max(mx, num)`：不斷刷新全域最大值。
3. **計算與溢位防禦**：
   - 計算最大差值 `(mx - mm)`。
   - 乘上 $k$。為了防止相乘後突破 32-bit 整數的上限，強制轉型：`(mx - mm) * 1ll * k`。
4. **回傳結果**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。只需對陣列進行一次線性掃描。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個變數，空間開銷為極限最佳。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long maxTotalValue(vector<int>& nums, int k) {
        int mm = INT_MAX, mx = INT_MIN;
        
        // 貪心策略：局部的極值差絕對不會大於全域的極值差
        // 因此直接尋找整個陣列的最大值與最小值
        for(auto num : nums) {
            mm = min(mm, num);
            mx = max(mx, num);
        }
        
        // 使用 1ll 進行強制轉型，防止 32-bit int 溢位 (Overflow)
        return (mx - mm) * 1ll * k;
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