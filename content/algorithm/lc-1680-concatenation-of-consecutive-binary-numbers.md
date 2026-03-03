---
title: "LeetCode 1680. Concatenation of Consecutive Binary Numbers"
date: 2026-02-28T17:24:00+08:00
lastmod: 2026-02-28T17:24:00+08:00
difficulty: 1629
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Bit Manipulation", "Math", "Simulation"]
keywords: ["LC1680", "1629", "Medium", "__builtin_clz", "std::bit_width", "Bitwise Operations"]
description: "LeetCode 第 1680 題：Concatenation of Consecutive Binary Numbers。難度評分：1629。探討如何利用位元運算與硬體指令 (__builtin_clz) 最佳化二進位字串的連續串接操作。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1680](https://leetcode.com/problems/concatenation-of-consecutive-binary-numbers/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1629)</span>   
> **核心主題**：`Bit Manipulation` $\cdot$ `Math` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數 `n`，請將 `1` 到 `n` 的二進位表示連續串接起來，並回傳該二進位字串所對應的十進位數值，對 $10^9 + 7$ 取餘數後的結果。

### 範例
- **Input**: `n = 3` → **Output**: `27`
- **Explanation**: 
  二進位中，1, 2, 3 分別為 `"1"`, `"10"`, `"11"`。
  將它們串接後得到 `"11011"`，對應的十進位數值為 27。

- **Input**: `n = 12` → **Output**: `505379714`
- **Explanation**: 串接後的二進位字串長度相當大，必須在每次串接時取餘數。

### 限制條件
- $1 \le n \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-02-28：初次提交 (探討浮點數運算、硬體指令與連續遞增特性的優化)](#2026-02-28-初次提交)

---
在 $i$ 為 2 的冪次（即 $1, 2, 4, 8, \dots$）時才會增加 1。
   - 判斷是否為 2 的冪次只需使用位元運算 `(i & (i - 1)) == 0`。這允許我們以全整數、全位元運算的方式動態維護平移量，進一步壓低運算常數。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需遍歷 1 到 $n$ 一次。上述三種方法的理論複雜度皆為線性，但常數時間開銷為：`log2` > `__builtin_clz` $\approx$ 動態維護。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用常數個變數儲存累積狀態與平移量。

### 💻 程式碼實作 (C++)

#### 版本二與版本三：最佳化實作 (利用 `__builtin_clz` 或連續遞增特性)
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int concatenatedBinary(int n) {
        long long ans = 0;
        int len = 0; // 用於動態維護當前數字 i 的 bit 長度

        for(int i = 1; i <= n; ++i) {
            // [方法 A] 硬體指令計算：直接求長度
            // len = 32 - __builtin_clz(i);
            // C++20 寫法：len = std::bit_width(static_cast<unsigned>(i));

            // [方法 B] 動態維護計算：當 i 是 2 的冪次時，長度加 1
            if ((i & (i - 1)) == 0) {
                ++len;
            }

            // 將當前累積結果左移 len 位，並透過 OR 操作合併 i
            ans = ((ans << len) | i) % MOD;
        }

        return ans;
    }
};
```

<details>
<summary><b>點擊展開：版本一 (基於 log2 的浮點數運算)</b></summary>

```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int concatenatedBinary(int n) {
        long long ans = 0;
        
        for(int i = 1; i <= n; ++i) {
            // 呼叫 log2 涉及浮點數轉換與計算，執行速度較慢
            int len = static_cast<int>(log2(i)) + 1;
            ans = ((ans << len) | i) % MOD;
        }

        return ans;
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