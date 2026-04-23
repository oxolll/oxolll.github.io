---
title: "LeetCode 3849. Maximum Bitwise XOR After Rearrangement"
date: 2026-04-22T23:15:00+08:00
lastmod: 2026-04-22T23:15:00+08:00
difficulty: 1566
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy", "bit-manipulation", "math"]
keywords: ["LC3849", "Medium", "1566", "Maximum Bitwise XOR", "Rearrangement", "Greedy"]
description: "LeetCode 第 3849 題：Maximum Bitwise XOR After Rearrangement。難度評分：1566。探討如何利用貪婪策略 (Greedy) 結合字元頻率統計，以 O(N) 線性時間構造出具有最大 XOR 結果的二進位字串。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3849](https://leetcode.com/problems/maximum-bitwise-xor-after-rearrangement/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1566)</span>  
> **核心主題**：`String` $\cdot$ `Greedy` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個長度相同的二進位字串 `s` 和 `t`。
你可以將字串 `t` 中的字元進行**任意順序的重新排列**。
你的目標是讓重排後的 `t` 與 `s` 進行「逐位元互斥或 (Bitwise XOR)」運算後，得到的結果數值**最大化**。
請回傳這個能構成最大數值的 XOR 結果字串。

### 限制條件
- $1 \le s.length == t.length \le 10^5$
- `s` 和 `t` 僅包含字元 `'0'` 與 `'1'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-22：初次提交 (頻率統計與貪婪字串構造)](#2026-04-22-初次提交)

---

## 💡 2026-04-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要讓一個二進位字串代表的數值最大化，核心的**貪婪策略 (Greedy Strategy)** 就是：「從最高位 (Most Significant Bit, 即字串最左側) 開始，盡可能讓每一位都是 `1`」。

在 XOR 運算的特性中：
- $1 \oplus 0 = 1$
- $0 \oplus 1 = 1$
也就是說，如果我們希望 XOR 的結果是 `1`，我們必須拿「相反的位元」來配對。
既然題目允許任意重排 `t`，我們其實不需要真的去移動 `t` 的字元。我們只需要把 `t` 當作一個「資源庫」，統計裡面有幾個 `1` 和幾個 `0`。
接著，我們由左至右掃描字串 `s`：
- 若當前 `s[i]` 為 `'1'`，我們優先從資源庫拿一個 `'0'` 來配對，產生結果 `'1'`。
- 若當前 `s[i]` 為 `'0'`，我們優先從資源庫拿一個 `'1'` 來配對，產生結果 `'1'`。
- 若資源庫裡需要的反向位元已經耗盡，我們只能拿相同的位元配對 ($1 \oplus 1 = 0$ 或 $0 \oplus 0 = 0$)，此時結果必然為 `'0'`。

### 🛠️ 解題思路 (Approach)
1. **資源統計**：宣告變數 `c0` 與 `c1`，走訪字串 `t`，統計字元 `'0'` 與 `'1'` 的總數量。
2. **貪婪構造**：
   - 宣告空字串 `rt` 準備儲存結果。
   - 由左至右走訪字串 `s` 中的每個字元 `c`。
   - **情況一**：若 `c == '1'` 且還有剩餘的 `'0'` 資源 (`c0 > 0`)，則結果補上 `'1'`，並消耗掉一個 `'0'` 資源 (`--c0`)。
   - **情況二**：若 `c == '0'` 且還有剩餘的 `'1'` 資源 (`c1 > 0`)，則結果補上 `'1'`，並消耗掉一個 `'1'` 資源 (`--c1`)。
   - **情況三 (退化)**：若上述兩者皆不滿足，代表無法構成 `'1'`，結果只能補上 `'0'`。
3. **回傳結果**：回傳構造完成的字串 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串的長度。統計頻率需要掃描 `t` 一次，構造字串需要掃描 `s` 一次，兩者皆為線性時間。
- **空間複雜度**: $\mathcal{O}(N)$ 或 $\mathcal{O}(1)$。若不計入回傳字串 `rt` 所需的空間，我們僅使用了常數個變數進行計數，額外空間為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string maximumXor(string s, string t) {
        string rt = "";
        
        // 1. 統計 t 字串中 0 和 1 的「資源數量」
        int c0 = 0, c1 = 0;
        for(auto& c : t) {
            if(c == '0') ++c0;
            else ++c1;
        }
        
        // 2. 貪婪構造：由最高位開始盡可能構造 1
        for(auto& c : s) {
            if(c == '1' && c0 > 0) {
                rt += '1';
                --c0;
            }
            else if(c == '0' && c1 > 0) {
                rt += '1';
                --c1;
            }
            else {
                // 若缺乏配對的反向資源，只能產生 0
                rt += '0';
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