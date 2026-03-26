---
title: "LeetCode 2864. Maximum Odd Binary Number"
date: 2026-03-26T16:45:00+08:00
lastmod: 2026-03-26T16:45:00+08:00
difficulty: 1237
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "math", "greedy"]
keywords: ["LC2864", "1237", "Easy", "Maximum Odd Binary Number", "Greedy"]
description: "LeetCode 第 2864 題：Maximum Odd Binary Number。難度評分：1237。探討如何利用二進位奇數的最低有效位元特性，配合貪婪策略將剩餘位元集中至高位，以 O(N) 時間複雜度構造最大數值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2864](https://leetcode.com/problems/maximum-odd-binary-number/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1237)</span>  
> **核心主題**：`String` $\cdot$ `Math` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個二進位字串 `s`，其中至少包含一個 `'1'`。
你可以重新排列字串中的字元，以構造出一個能夠表示為**奇數**的**最大**二進位數字。
請回傳這個經過重新排列後，表示最大奇數的字串。
注意：回傳的字串可以包含前導零。

### 限制條件
- $1 \le s.length \le 100$
- `s` 僅由字元 `'0'` 和 `'1'` 組成。
- `s` 中至少包含一個 `'1'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (雙指標原地置換法)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的目標是構造出最大的奇數。在二進位表示法中：
1. **奇數條件**：最低有效位元 (Least Significant Bit, LSB)，也就是字串的最後一個字元，必須是 `'1'`。
2. **最大值條件**：在確保最低位為 `'1'` 的前提下，剩餘的所有 `'1'` 必須盡可能放置在最高有效位元 (Most Significant Bits, MSB)，也就是字串的最左側。

因此，演算法的貪婪策略為：將所有的 `'1'` 移動到字串的開頭，最後再將其中一個 `'1'` 移至字串的最末端以滿足奇數條件。

### 🛠️ 解題思路 (Approach)
1. **集中 `'1'` 位元**：
   - 宣告指標 `p = 0`，代表下一個 `'1'` 應該放置的位置。
   - 宣告變數 `last1 = -1`，記錄最後一次進行 `'1'` 置換的索引位置。
   - 線性走訪字串 `s`。當遇到 `s[i] == '1'` 時，將 `s[i]` 與 `s[p]` 進行交換 (Swap)，並更新 `last1 = p`，接著將 `p` 遞增。
   - 此步驟結束後，所有的 `'1'` 皆會集中在字串的左側區塊。
2. **滿足奇數條件**：
   - 檢查字串最後一個字元 `s.back()`。若為 `'0'`，代表目前的最低位為偶數狀態。
   - 由於第一步已確保所有的 `'1'` 集中在前方，直接將最後一個移動過去的 `'1'` (位於索引 `last1`) 與字串最後一個字元 `s.back()` 進行交換，即可確保字串結尾為 `'1'`。
3. **回傳結果**：回傳修改後的字串 `s`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串的長度。僅需對字串進行一次線性走訪與常數次數的字元交換。
- **空間複雜度**: $\mathcal{O}(1)$。直接在原字串 `s` 上進行原地 (In-place) 操作，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string maximumOddBinaryNumber(string s) {
        int p = 0;
        int last1 = -1;
        
        // 1. 將所有的 '1' 移動至字串最左側
        for(int i = 0; i < s.size(); ++i) {
            if(s[i] == '1') {
                char tmp = s[i];
                s[i] = s[p];
                s[p] = tmp;
                last1 = p++;
            }
        }

        // 2. 若結尾不為 '1'，將最後一個收集到的 '1' 交換至末端
        if(s.back() == '0') {
            swap(s[last1], s.back());
        }
            
        return s;
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