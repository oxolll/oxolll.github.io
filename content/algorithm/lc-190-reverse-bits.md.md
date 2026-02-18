---
title: "LeetCode 190. Reverse Bits"
date: 2026-02-16T00:00:00+08:00
lastmod: 2026-02-16T00:00:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Bit Manipulation", "Simulation"]
keywords: ["LC190", "1200", "Reverse Bits", "Simulation"]
description: "LeetCode 第 190 題：Reverse Bits。顛倒給定 32 位元無號整數的二進位位元。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 190](https://leetcode.com/problems/reverse-bits/)  
> **難度等級**：<span style="color: #00af9b; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Bit Manipulation` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
顛倒給定 32 位元無號整數的二進位位元序列。

### 範例
- **Input**: `n = 00000010100101000001111010011100` (43261596)
- **Output**: `964176192` (`00111001011110000010100101000000`)
- **Explanation**: 輸入的二進位字串被反轉了。

### 限制條件
- 輸入必須為 32 位元整數。
</details>

---

## 📝 歷次打卡與更新
- [2026-02-16：初次提交 (Arithmetic Simulation)](#2026-02-16-初次提交)

---

## 💡 2026-02-16 初次提交

### 直覺 (Intuition)
這題的目標是將 32 個 bit 的順序完全顛倒。
這就像我們反轉十進位數字（例如將 `123` 變成 `321`）一樣：
1. 取出個位數 (`n % 10`)。
2. 加到結果中 (`rt = rt * 10 + digit`)。
3. 原數字右移 (`n / 10`)。

只不過這裡是 **二進位**，所以我們把 `10` 換成 `2` 即可。

### 解題思路 (Approach)
我們使用 **算術模擬** 的方式來處理位元移動，這比直接寫 Bitwise Operator (`<<`, `>>`, `&`) 更符合直覺：

1.  **初始化**：結果 `rt` 為 0。
2.  **固定迴圈**：因為題目保證是 32 位元整數，我們固定跑 32 次迴圈。
3.  **迭代步驟**：
    * `rt *= 2`：將目前的結果左移一位（騰出空位給新的 bit）。
    * `rt += (n % 2)`：取出 `n` 的最後一位 (LSB)，加到 `rt` 的末尾。
    * `n /= 2`：將 `n` 右移一位，處理下一個 bit。

### 📊 複雜度分析
- **時間複雜度**: $O(1)$。因為輸入固定為 32 位元，迴圈固定執行 32 次。
- **空間複雜度**: $O(1)$。只使用常數空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 注意：LeetCode 標準題型通常輸入為 uint32_t，
    // 但此解法使用 int 配合算術運算亦可通過非負整數測試
    int reverseBits(int n) {
        int rt = 0;
        // 固定處理 32 個位元
        for(int i = 0; i < 32; ++i)
        {
            rt *= 2;      // 效果等同於 rt << 1
            rt += (n%2);  // 效果等同於 rt + (n & 1)
            n /= 2;       // 效果等同於 n >> 1
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