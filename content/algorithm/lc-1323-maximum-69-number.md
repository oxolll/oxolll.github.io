---
title: "LeetCode 1323. Maximum 69 Number"
date: 2026-03-26T16:27:00+08:00
lastmod: 2026-03-26T16:27:00+08:00
difficulty: 1193
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "greedy"]
keywords: ["LC1323", "1193", "Easy", "Maximum 69 Number", "Greedy", "Math"]
description: "LeetCode 第 1323 題：Maximum 69 Number。難度評分：1193。探討如何利用數學運算取代字串轉換，基於貪婪策略替換最高位的數字，以 O(log N) 時間複雜度求得極值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1323](https://leetcode.com/problems/maximum-69-number/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1193)</span>  
> **核心主題**：`Math` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個僅由數字 `6` 和 `9` 組成的正整數 `num`。
你最多只能更改一個數字（將 `6` 變成 `9`，或是將 `9` 變成 `6`）。
請回傳經過操作後能得到的最大數字。

### 限制條件
- $1 \le num \le 10^4$
- `num` 僅包含數字 `6` 和 `9`。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (數學拆解與貪婪替換)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要使一個數字變得盡可能大，必須採用貪婪策略 (Greedy Strategy)：從該數字的「最高位數」開始檢查，遇到第一個 `6` 就將其轉換為 `9`。若該數字已經全為 `9`，則不進行任何操作。
實作上，可以直接將數字轉換為字串並進行字元替換，但此作法會產生額外的字串配置開銷。若單純使用數學上的模運算 (`% 10`) 與除法 (`/ 10`)，將每一位數字提取至陣列中，記錄最後一次出現 `6` 的位置（因為由右向左提取，最後出現的位置即為原數字的最高位），再將陣列反向還原，便能以最基礎的運算完成這項任務。

### 🛠️ 解題思路 (Approach)
1. **數字拆解**：
   - 宣告陣列 `nums` 儲存各個位數。
   - 宣告 `p6 = -1` 用於記錄最高位 `6` 的索引位置，以及 `c = 0` 作為當前計數器。
   - 利用 `while(num)` 迴圈，每次取出末位數字 `cal = num % 10`。
   - 若 `cal == 6`，更新 `p6 = c`。
   - 將 `cal` 推入 `nums` 陣列中，並將 `num /= 10`。
2. **貪婪替換**：
   - 拆解完畢後，若 `p6 != -1`，代表數字中包含 `6`，將 `nums[p6]` 修改為 `9`。
3. **數字重組**：
   - 宣告 `rt = 0`。
   - 因為 `nums` 是從個位數開始依序存入的，因此必須從陣列的尾端（索引 `n-1`）開始向左遍歷。
   - 每次執行 `rt *= 10` 並加上 `nums[i]`，逐步將數字還原。
4. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_{10} N)$。其中 $N$ 是給定的數值 `num`。提取位數與還原位數的次數等於該數字的長度，時間複雜度為常數級別。
- **空間複雜度**: $\mathcal{O}(\log_{10} N)$。使用了一個陣列 `nums` 來儲存每一位數字，對於 32-bit 整數而言長度不超過 10，空間開銷可視為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximum69Number (int num) {
        vector<int> nums;
        int p6 = -1;
        int c = 0;
        
        // 1. 從右至左拆解數字，並記錄最後一個出現的 6 (即最高位的 6)
        while(num) {
            int cal = num % 10;
            if(cal == 6) {
                p6 = c;
            }
            nums.push_back(cal);
            num /= 10;
            ++c;
        }
        
        int n = nums.size();
        
        // 2. 將最高位的 6 替換為 9
        if(p6 != -1) {
            nums[p6] = 9;
        }
            
        int rt = 0;
        
        // 3. 由左至右還原數字 (從陣列尾端遍歷)
        for(int i = n - 1; i >= 0; --i) {
            rt *= 10;
            rt += nums[i];
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