---
title: "LeetCode 921. Minimum Add to Make Parentheses Valid"
date: 2026-04-04T10:00:00+08:00
lastmod: 2026-04-04T10:00:00+08:00
difficulty: 1242
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy", "stack"]
keywords: ["LC921", "1242", "Medium", "Minimum Add", "Parentheses", "Greedy", "Stack Simulation"]
description: "LeetCode 第 921 題：Minimum Add to Make Parentheses Valid。難度評分：1242。探討如何利用貪婪策略與計數器模擬堆疊操作，以 O(N) 時間複雜度與 O(1) 空間複雜度計算使括號字串合法所需的最少添加次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 921](https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1242)</span>  
> **核心主題**：`String` $\cdot$ `Greedy` $\cdot$ `Stack`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
只有滿足以下條件之一的括號字串才是有效的：
- 它是一個空字串。
- 它可以被寫作 `AB`（即字串 `A` 與字串 `B` 串接），其中 `A` 和 `B` 都是有效字串。
- 它可以被寫作 `(A)`，其中 `A` 是一個有效字串。

給定一個括號字串 `s`，在一次操作中，你可以在字串的任何位置插入一個括號。
- 例如，如果 `s = "()))"`，你可以插入一個左括號變成 `"(()))"`，或是插入一個右括號變成 `"())))"`。

請回傳使字串 `s` 有效所需的最少添加次數。

### 限制條件
- $1 \le s.length \le 1000$
- `s[i]` 只能是 `'('` 或 `')'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-04：初次提交 (計數器貪婪模擬法)](#2026-04-04-初次提交)

---

## 💡 2026-04-04 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理括號配對問題，標準的資料結構是堆疊 (Stack)。但在本題中，由於字元種類僅有單一的一組括號 `()`，我們不需要真正儲存字元，只需追蹤其「數量狀態」即可。
這是一種貪婪策略 (Greedy Strategy)：從左至右掃描字串時，每一個右括號 `)` 都希望能找到一個在其左側且尚未被配對的左括號 `(` 來進行抵銷。
- 若遇到右括號時，左方沒有多餘的左括號，則代表這個右括號處於「無效狀態」，我們必須手動在它前面添加一個左括號。
- 掃描結束後，若仍有未被抵銷的左括號，代表這些左括號缺乏對應的右括號，我們必須在字串尾端手動補上同等數量的右括號。

### 🛠️ 解題思路 (Approach)
1. 宣告變數 `rt = 0`，用於記錄必須手動添加的括號總數。
2. 宣告變數 `c = 0`，作為模擬堆疊的計數器，用於記錄「目前尚未配對的左括號數量」。
3. 遍歷字串 `s` 中的每一個字元 `ch`：
   - **遇到左括號 `(`**：直接推入模擬堆疊，即 `++c`。
   - **遇到右括號 `)`**：
     - 若堆疊不為空 (`c > 0`)：代表有可配對的左括號，進行抵銷，即 `--c`。
     - 若堆疊為空 (`c == 0`)：代表此右括號無法配對，必須手動添加一個左括號，因此將 `rt` 加 1 (`++rt`)。
4. 遍歷結束後，`c` 的值代表剩餘未配對的左括號數量。這些都需要手動補上右括號。將其累加至 `rt` (實作中使用 `while(c--) ++rt;` 邏輯等同於 `rt += c;`)。
5. 回傳最終的添加總數 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串 `s` 的長度。只需對字串進行一次線性掃描。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了兩個整數變數 `rt` 與 `c`，無須額外的記憶體配置。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minAddToMakeValid(string s) {
        int rt = 0; // 記錄必須添加的括號數量
        int c = 0;  // 記錄當前尚未配對的左括號 '(' 數量
        
        for(auto ch : s) {
            if(ch == '(') {
                ++c;
            } else {
                // 若有左括號可供配對，則互相抵銷
                if(c > 0) {
                    --c;
                } 
                // 若無左括號可配對，則必須為此右括號添加一個左括號
                else {
                    ++rt;
                }
            }
        }
        
        // 將剩餘未配對的左括號數量，轉換為必須添加的右括號數量並累加
        while(c--) {
            ++rt;
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