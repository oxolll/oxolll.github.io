---
title: "LeetCode 3783. Mirror Distance of an Integer"
date: 2026-04-18T12:15:00+08:00
lastmod: 2026-04-18T12:15:00+08:00
difficulty: 1170
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math"]
keywords: ["LC3783", "1170", "Easy", "Mirror Distance", "Math", "Reverse Integer"]
description: "LeetCode 第 3783 題：Mirror Distance of an Integer。難度評分：1170。探討如何利用取模與除法運算反轉數字，並計算原數字與反轉數字之間的絕對距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3783](https://leetcode.com/problems/mirror-distance-of-an-integer/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1170)</span>  
> **核心主題**：`Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數 `n`，請計算該數字的「鏡像距離 (Mirror Distance)」。
一個數字的鏡像距離定義為：原數字 `n` 與其數字反轉後的結果 `rev` 之間的**絕對差值**。
例如，若 `n = 123`，其反轉為 `321`，則鏡像距離為 `|123 - 321| = 198`。
*(註：反轉過程中產生的前導零可直接忽略，例如 100 反轉為 1)*

### 限制條件
- $1 \le n \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-18：初次提交 (數字拆解與絕對值計算)](#2026-04-18-初次提交)

---

## 💡 2026-04-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的數學模擬題，核心在於「數字反轉」。
我們可以透過一個簡單的 `while` 迴圈，每次取出數字的最後一位數（`k % 10`），並將其推入反轉結果的末端（`rev = rev * 10 + digit`），接著將原數字向右推移一位（`k /= 10`）。
建構出反轉數字後，直接回傳 $|n - rev|$ 即可。

### 🛠️ 解題思路 (Approach)
1. 宣告變數 `k` 複製原數值 `n`（為了在迴圈結束後保留原本的 `n` 用於計算差值）。
2. 宣告變數 `rev = 0` 儲存反轉結果。
3. 利用 `while(k)` 迴圈處理數字反轉：
   - 每次迴圈將 `rev` 乘以 10 挪出個位數空間。
   - 加上 `k` 的個位數 `k % 10`。
   - `k` 除以 10 捨去已處理的個位數。
4. 迴圈結束後，回傳 `abs(n - rev)`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_{10} N)$。其中 $N$ 是整數數值。迴圈執行的次數等於數字的位數。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個變數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int mirrorDistance(int n) {
        int k = n;
        int rev = 0;
        
        // 進行數字反轉
        while(k) {
            rev *= 10;       // 挪出空位
            rev += k % 10;   // 填入當前個位數
            k /= 10;         // 捨棄已處理的個位數
        }
        
        // 回傳原數與反轉數的絕對差值
        return abs(n - rev);
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