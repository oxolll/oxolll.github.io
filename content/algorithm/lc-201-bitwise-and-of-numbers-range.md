---
title: "LeetCode 201. Bitwise AND of Numbers Range"
date: 2026-05-18T21:55:00+08:00
lastmod: 2026-05-18T21:55:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "bit-manipulation", "math"]
keywords: ["LC201", "Medium", "Bitwise AND", "Numbers Range", "位元運算", "Lowbit"]
description: "LeetCode 第 201 題：Bitwise AND of Numbers Range。難度評分：Medium。探討如何利用樹狀陣列中的 Lowbit 跳躍思維，巧妙地消除非公共前綴的位元，在常數時間內求解區間位元與。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 201](https://leetcode.com/problems/bitwise-and-numbers-range/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個整數 `left` 和 `right` ，表示一個閉區間 $[left, right]$。
請你回傳這段區間內所有數字 **按位元與 (Bitwise AND)** 的結果（包含 `left` 和 `right`）。

### 範例
- **Input**: `left = 5, right = 7`
- **Output**: `4`
- **Explanation**: 
  $5$ 的二進位為 `101`
  $6$ 的二進位為 `110`
  $7$ 的二進位為 `111`
  三個數進行 AND 運算後的結果為 `100`，即十進位的 $4$。

- **Input**: `left = 0, right = 0`
- **Output**: `0`

- **Input**: `left = 1, right = 2147483647`
- **Output**: `0`

### 限制條件
- $0 \le left \le right \le 2^{31} - 1$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-18：初次提交 (創意 Lowbit 跳躍法)](#2026-05-18-初次提交)

---

## 💡 2026-05-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這道題最經典的數學本質是：**尋找 `left` 和 `right` 的二進位「公共前綴 (Common Prefix)」**。因為在一個連續增長的區間中，只要低位元發生過任何一次從 `1` 到 `0` 或 `0` 到 `1` 的翻轉（產生進位），該位元的 AND 結果就注定會變成 `0`。

常見的標準做法是將兩個數同時向右平移，直到它們相等為止。然而，你的解法採用了另一種非常高明且少見的思維——**利用樹狀陣列 (Fenwick Tree) 的 `lowbit` 機制進行跳躍**。

**核心破局點：`i += (i & -i)` 的妙用**
- `i & -i` 可以精準提取出整數 `i` 的最低位元的 `1`（稱為 Lowbit）。
- 當執行 `i += (i & -i)` 時，程式會強制將最右側的連續 `1` 區塊透過加法向上進位。
- **為什麼這能用來求區間 AND？** 因為每當 `i` 透過 `lowbit` 向上進位時，代表低位元在實際數值增長過程中**必定經歷過翻轉與清零**。任何經歷過這種劇烈跳躍的位元，其區間 AND 的最終命運必然是 `0`。
- 透過這個跳躍，我們直接略過了中間那些重複且無效的 AND 運算，直接前進到「下一個可能改變公共前綴結構的邊界點」。

---

### 🛠️ 解題思路 (Approach)
1. **零值攔截**：若 `left == 0`，因為 $0$ 與任何數進行 AND 都是 $0$，直接回傳 `0`。
2. **初始化**：令結果變數 `rt = left`。
3. **Lowbit 迴圈跳躍**：
   - 使用 `long long` 類型的迭代變數 `i` 避免在 `i += (i & -i)` 時發生 32 位元整數溢位。
   - 每次迴圈將 `rt &= i`。
   - 更新 `i` 為 `i + (i & -i)`。
4. **返回答案**：當 `i` 超過 `right` 時，迴圈結束，此時 `rt` 留下來的即是未受翻轉波及的最高位公共前綴。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log(\text{right})) \to \mathcal{O}(1)$。
  因為整數最多只有 32 個位元，且每次 `i += (i & -i)` 都會抹除或向上推進最低位元的 `1`，迴圈內部的跳躍次數最多不會超過 32 次，在實務上屬於極高效的常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個變數進行位元操作。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int rangeBitwiseAnd(int left, int right) {
        // 邊界特判：0 與任何數進行位元與 (AND) 結果皆為 0
        if(left == 0) return 0;
        
        int rt = left;
        
        // 使用 long long 防止 i += (i & -i) 時，若數值接近 INT_MAX 會引發有號整數溢位
        for(long long i = left; i <= right; i += (i & -i)) {
            // 利用加法進位迅速過濾並清空所有在區間內發生過翻轉的低位元
            rt &= i;
        }

        return rt;
    }
};
```

---

### 🔍 範例追蹤 (Trace)
以 `left = 5` ($101_2$), `right = 7` ($111_2$) 為例：
1. 初始化 `rt = 5`，`i = 5`。
2. **第一輪**：`i = 5` ($101_2$) $\le 7$：
   - `rt &= 5` $\to$ `rt` 仍為 $5$ ($101_2$)。
   - `i & -i` $= 5 \\& -5 = 1$。
   - `i` 更新為 $5 + 1 = 6$ ($110_2$)。
3. **第二輪**：`i = 6` ($110_2$) $\le 7$：
   - `rt &= 6` $\to$ $101_2 \\& 110_2 = 100_2$ ($4$)。
   - `i & -i` $= 6 \\& -6 = 2$。
   - `i` 更新為 $6 + 2 = 8$ ($1000_2$)。
4. **結束**：`i = 8 > right(7)`，跳出迴圈。回傳 `rt = 4`。完全正確！

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