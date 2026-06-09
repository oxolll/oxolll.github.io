---
title: "LeetCode 3950. Exactly One Consecutive Set Bits Pair"
date: 2026-06-09T14:15:00+08:00
lastmod: 2026-06-09T14:15:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "bit-manipulation"]
keywords: ["LC3950", "Easy", "Consecutive Set Bits", "位元運算", "二進位", "狀態記錄"]
description: "LeetCode 第 3950 題：Exactly One Consecutive Set Bits Pair。探討如何利用位元運算 (Bit Manipulation) 逐位元掃描，配合狀態變數記錄前一位元，以 O(1) 空間與對數時間高效過濾並判斷二進位表示中是否「恰好包含一對」連續的 1。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3950](https://leetcode.com/problems/exactly-one-consecutive-set-bits-pair/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `n`。
請你判斷該整數的二進位表示中，是否 **恰好只包含一對** 連續的設定位元 (Set Bits，即 `1`)。
如果是，請回傳 `true`；否則回傳 `false`。

*(註：如果二進位表示中出現 `111`，這代表包含了**兩對**連續的 1，不符合「恰好一對」的條件。)*

### 範例
- **Input**: `n = 3` (二進位 `11`)
- **Output**: `true`
- **Explanation**: 恰好有一對連續的 1。

- **Input**: `n = 7` (二進位 `111`)
- **Output**: `false`
- **Explanation**: 包含了兩對連續的 1 (`11` 和 `11`)。

- **Input**: `n = 27` (二進位 `11011`)
- **Output**: `false`
- **Explanation**: 包含了兩對獨立的 `11`。

### 限制條件
- $1 \le n \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (位元運算與狀態計數)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是基礎的位元掃描題。
要尋找「連續的 1」，我們只需要在每次萃取最低位元 (`n & 1`) 的同時，記住**「上一個位元的狀態」** (`prev`) 即可。

**核心破局點：重疊區間的自然過濾**
題目要求「恰好一對」。如果我們遇到 `111`，前兩個 `1` 會讓計數器加 1，後兩個 `1` 又會讓計數器再加 1，使得總數變成 2。
這代表我們不需要為 `111` 這種連續三個 1 的情況撰寫特別的邏輯，只要單純地統計「前一個是 1 且當前也是 1」發生的總次數 `c`，最後判斷 `c == 1`，就能完美過濾掉重疊以及多個獨立 `11` 的情況。

### 🛠️ 解題思路 (Approach)
1. **初始化狀態**：宣告 `prev = 0` 記錄上一個位元，以及計數器 `c = 0` 統計連續對數。
2. **位元掃描迴圈**：當 `n > 0` 時，執行迴圈：
   - 透過 `n & 1` 取得當前最低位元。
   - 若當前位元為 `1` 且 `prev` 也是 `1`，代表找到一對連續的 `11`，將 `c` 加 1。
   - 將當前位元賦值給 `prev`。
   - 透過右移運算 `n >>= 1`，將數字向右推移一位，準備檢查下一個位元。
3. **回傳判定**：迴圈結束後，回傳布林值 `c == 1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_2 N)$。迴圈執行次數等同於數字 `n` 的二進位有效位數長度，最大不超過 32 次。屬於極致的常數級別效能。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個整數變數，完全無需額外記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool consecutiveSetBits(int n) {
        int prev = 0; // 記錄上一個位元的狀態
        int c = 0;    // 記錄連續 '11' 出現的次數
        
        while(n) {
            // 如果當前位元是 1 且上一個位元也是 1，則找到一對
            if((n & 1) && prev == 1) {
                ++c;
            }
            
            // 更新狀態並向右推移
            prev = n & 1;
            n >>= 1;
        }

        // 必須「恰好」只有一對連續的 1
        return c == 1;
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