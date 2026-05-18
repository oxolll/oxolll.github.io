---
title: "LeetCode 371. Sum of Two Integers"
date: 2026-05-18T22:15:00+08:00
lastmod: 2026-05-18T22:15:00+08:00
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
keywords: ["LC371", "Medium", "Sum of Two Integers", "位元運算", "無加號加法", "半加器"]
description: "LeetCode 第 371 題：Sum of Two Integers。難度評分：Medium。探討如何在嚴禁使用加減號的前提下，利用數位邏輯中的異或（XOR）與按位與（AND）模擬半加器電路，實現高效的整數加法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 371](https://leetcode.com/problems/sum-of-two-integers/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Bit Manipulation` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個整數 `a` 和 `b` ，在 **不使用** 運算子 `+` 和 `-` 的前提下，回傳兩個整數之和。

### 範例
- **Input**: `a = 1, b = 2`
- **Output**: `3`

- **Input**: `a = 2, b = 3`
- **Output**: `5`

### 限制條件
- $-1000 \le a, b \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-18：初次提交 (數位邏輯全加器模擬)](#2026-05-18-初次提交)

---

## 💡 2026-05-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這道題是計算機基礎架構中「算術邏輯單元 (ALU)」如何實現加法器的經典重現。在沒有加減號的限制下，我們必須藉助**二進位位元運算**來手動處理「本位和」與「進位」。

**核心破局點：拆解加法為邏輯閘**
我們可以將兩個二進位數的加法拆解為兩個部分：
1. **無進位加法（本位和）**：
   觀察二進位加法：$0+0=0$，$0+1=1$，$1+0=1$，$1+1=0$。
   這與 **互斥或 (XOR)** 的邏輯完全一致。因此，可以用 `a ^ b` 來計算不考慮進位時的初步結果。
2. **計算進位**：
   只有當兩個位元皆為 `1` 時（$1+1$），才會向高一位產生進位。
   這與 **按位與 (AND)** 的邏輯一致。而進位必須作用在更高的位元，所以需要向左平移一位，即 `(a & b) << 1`。

我們只要不斷地把「無進位加法結果」與「進位」重新相加，直到「進位」收斂為 $0$ 為止，此時的本位和就是最終的答案。

> **⚠️ 關於 C++ 中 `unsigned int` 的關鍵細節：**
> 你在代碼中將 `a` 和 `b` 轉換為 `unsigned int` 是一個極其專業且必要的作法。
> 根據 C++ 標準，對**有號整數 (signed int)** 進行左移（`<<`）操作時，如果影響到了符號位元，或者對負數進行左移，會引發**未定義行為 (Undefined Behavior)**。而將其強制轉換為無號整數，位元會完全遵循二補數 (2's Complement) 的循環溢位規則自然運算，完美避開了負數加法的邊界陷阱。

---

### 🛠️ 解題思路 (Approach)
1. **型態轉換**：將有號整數 `a` 與 `b` 暫存至無號整數 `x` 與 `y` 中。
2. **迭代消除進位**：當進位 `y` 不為 0 時持續循環：
   - 計算無進位和：`tmp = x ^ y`。
   - 計算當前進位並左移一位：`y = (x & y) << 1`。
   - 更新本位和：`x = tmp`。
3. **返回答案**：當 `y == 0` 代表沒有任何進位需要處理，此時將 `x` 強制轉回 `int` 並回傳。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。因為整數在 32 位元系統中長度固定，進位最多只會向前推進 32 次（最壞情況如 $-1 + 1$），因此迴圈執行的上限為常數次。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用常數個無號整數變數，空間效率達到極致。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int getSum(int a, int b) {
        // 1. 轉換為無號整數，避免有號整數在進行左移操作(<<)或處理負數進位時引發未定義行為
        unsigned int x = a, y = b;
        
        // 2. 當仍有進位需要加回本位時，持續循環
        while (y) {
            // 計算本位和 (不含進位)
            unsigned int tmp = x ^ y;
            
            // 計算進位，並向高位推進一格
            y = (x & y) << 1;
            
            // 將初步和更新給 x，準備在下一輪與新進位 y 相加
            x = tmp;
        }

        // 3. 轉回有號整數輸出正確結果
        return int(x);
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