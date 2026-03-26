---
title: "LeetCode 2160. Minimum Sum of Four Digit Number After Splitting Digits"
date: 2026-03-26T16:10:00+08:00
lastmod: 2026-03-26T16:10:00+08:00
difficulty: 1314
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "greedy", "sorting"]
keywords: ["LC2160", "1314", "Easy", "Minimum Sum", "Counting Sort", "Greedy"]
description: "LeetCode 第 2160 題：Minimum Sum of Four Digit Number After Splitting Digits。難度評分：1314。探討如何利用計數排序法 (Counting Sort) 與貪婪策略，將四位數拆解並重組為總和最小的兩個新數字。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2160](https://leetcode.com/problems/minimum-sum-of-four-digit-number-after-splitting-digits/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1314)</span>  
> **核心主題**：`Math` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個四位數的正整數 `num`，請將其拆分為兩個新的整數 `new1` 和 `new2`。
你可以任意重新排列 `num` 中的數字，且新數字可以包含前導零。
請回傳 `new1` 與 `new2` 可能的**最小總和**。

### 限制條件
- $1000 \le num \le 9999$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (計數排序與交替分配)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要使兩個新數字的總和最小，必須依循貪婪策略 (Greedy Strategy)：將最小的數字分配到權重最高的位數（十位數），較大的數字分配到權重較低的位數（個位數）。
因為總共只有四個位數，最佳分配結果必然是將這四個數字遞增排序後，最小的兩個數字作為 `new1` 與 `new2` 的十位數，最大的兩個數字作為個位數。
在實作上，可利用計數排序法 (Counting Sort) 統計 `0` 到 `9` 出現的頻率，並依序交替將取出的最小數字賦值給 `a` 與 `b`，避免了呼叫標準庫排序函式的開銷。

### 🛠️ 解題思路 (Approach)
1. **數字頻率統計**：宣告一個大小為 10 的陣列 `cnt`。使用 `while` 迴圈不斷取 `num % 10` 獲得末位數字，並將對應的計數器加 1，隨後將 `num` 除以 10。
2. **貪婪交替分配**：
   - 宣告結果變數 `a = 0` 與 `b = 0`。宣告布林變數 `flag = true` 控制分配對象。
   - 建立迴圈遍歷數字 `1` 到 `9`（數字 `0` 置於任何位置皆不增加數值，無需特別分配）。
   - 當 `cnt[i]` 大於 0 時，依據 `flag` 將數字 `i` 附加到 `a` 或 `b` 的末端 (`a = a * 10 + i`)。
   - 切換 `flag` 的狀態，確保數字均勻分配給 `a` 與 `b`，並將計數器減 1。
3. **回傳結果**：回傳 `a + b`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。數字固定為四位數，數值提取與頻率陣列的遍歷次數皆為常數級別。
- **空間複雜度**: $\mathcal{O}(1)$。宣告了一個固定大小為 10 的計數陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumSum(int num) {
        vector<int> cnt(10, 0);
        
        // 1. 統計各數字出現頻率
        while(num) {
            ++cnt[num % 10];
            num /= 10;
        }

        int a = 0, b = 0;
        bool flag = true;
        
        // 2. 從最小的非零數字開始，交替分配給 a 與 b
        for(int i = 1; i <= 9; ++i) {
            while(cnt[i]) {
                if(flag) {
                    a *= 10;
                    a += i;
                } else {
                    b *= 10;
                    b += i;
                }
                flag = !flag;
                --cnt[i];
            }
        }

        return a + b;
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