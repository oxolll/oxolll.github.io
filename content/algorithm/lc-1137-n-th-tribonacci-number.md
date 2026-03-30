---
title: "LeetCode 1137. N-th Tribonacci Number"
date: 2026-03-31T10:17:00+08:00
lastmod: 2026-03-31T10:17:00+08:00
difficulty: 1142
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "dynamic programming", "memoization"]
keywords: ["LC1137", "1142", "Easy", "Tribonacci", "Dynamic Programming", "Rolling Variables"]
description: "LeetCode 第 1137 題：N-th Tribonacci Number。難度評分：1142。探討如何利用自底向上 (Bottom-Up) 的動態規劃法求解泰波那契數列，並解析空間複雜度從 O(N) 降至 O(1) 的滾動變數優化技巧。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1137](https://leetcode.com/problems/n-th-tribonacci-number/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1142)</span>  
> **核心主題**：`Math` $\cdot$ `Dynamic Programming` $\cdot$ `Memoization`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
泰波那契序列 (Tribonacci sequence) $T_n$ 定義如下：
$T_0 = 0$, $T_1 = 1$, $T_2 = 1$，且對於 $n \ge 0$，有 $T_{n+3} = T_n + T_{n+1} + T_{n+2}$。

給定一個整數 `n`，請回傳第 `n` 個泰波那契數 $T_n$ 的值。

### 限制條件
- $0 \le n \le 37$
- 答案保證適合 32 位元整數，即 $T_n \le 2^{31} - 1$。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-31：初次提交 (一維陣列 DP 實作)](#2026-03-31-初次提交)

---

## 💡 2026-03-31 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是費氏數列的延伸。由於遞迴解法會產生大量重複計算導致 $\mathcal{O}(3^N)$ 的指數級時間複雜度，必須採用動態規劃 (Dynamic Programming) 來快取已計算的結果。
使用自底向上 (Bottom-Up) 的迭代法，建立一個陣列依序儲存 $T_0$ 到 $T_n$ 的值，狀態轉移方程式為：`trib[i] = trib[i-1] + trib[i-2] + trib[i-3]`。

### 🛠️ 解題思路 (Approach)
1. **陣列初始化**：宣告大小為 38 的一維陣列 `trib`（因 $N \le 37$），並初始化為 0。
2. **設定基礎條件**：將 `trib[0] = 0`，`trib[1] = 1`，`trib[2] = 1` 賦值。
3. **邊界處理**：若輸入 $n \le 2$，直接回傳對應的初始值。
4. **狀態推導**：使用 `for` 迴圈從 $i = 3$ 遍歷至 $n$，利用前三項的總和計算並更新 `trib[i]`。
5. **回傳結果**：迴圈結束後，回傳 `trib[n]`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int tribonacci(int n) {
        // N 最大為 37，陣列開到 38 足矣
        vector<int> trib(38, 0);
        trib[0] = 0; // 修正了原本雙等號的 typo 
        trib[1] = 1;
        trib[2] = 1;
        
        if(n <= 2) return trib[n];
        
        for(int i = 3; i <= n; ++i) {
            trib[i] = trib[i-1] + trib[i-2] + trib[i-3];
        }
        
        return trib[n];
    }
};
```
- **時間複雜度**: $\mathcal{O}(N)$。只需執行一個長度為 $N$ 的單一迴圈。
- **空間複雜度**: $\mathcal{O}(N)$。使用了一個大小固定的陣列儲存所有狀態。

---

### 💡 進階最佳化：$\mathcal{O}(1)$ 滾動變數 (Rolling Variables)
觀察狀態轉移方程式：`trib[i]` 的值**僅依賴於它前面的三個數值**，更早之前的歷史狀態完全不會被用到。
因此，我們可以捨棄整條陣列，改用三個變數 `a, b, c` 來滾動儲存前三項的值。在每次迭代中計算出新數值後，將變數依序平移推進即可。這能將空間複雜度從 $\mathcal{O}(N)$ 降至 $\mathcal{O}(1)$。

<details>
<summary><b>點擊展開 O(1) 空間最佳化實作程式碼</b></summary>

```cpp
class Solution {
public:
    int tribonacci(int n) {
        if(n == 0) return 0;
        if(n == 1 || n == 2) return 1;
        
        int a = 0; // T_0
        int b = 1; // T_1
        int c = 1; // T_2
        
        for(int i = 3; i <= n; ++i) {
            int next_val = a + b + c;
            a = b;
            b = c;
            c = next_val;
        }
        
        return c;
    }
};
```
</details>

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