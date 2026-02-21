---
title: "LeetCode 762. Prime Number of Set Bits in Binary Representation"
date: 2026-02-21T12:07:00+08:00
lastmod: 2026-02-21T12:07:00+08:00
difficulty: 1383
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Math", "Bit Manipulation"]
keywords: ["LC762", "1383", "Easy", "Sieve", "__builtin_popcount"]
description: "LeetCode 第 762 題：Prime Number of Set Bits in Binary Representation。難度評分：1383。探討結合質數篩法與位元運算 (__builtin_popcount) 的區間統計解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 762](https://leetcode.com/problems/prime-number-of-set-bits-in-binary-representation/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1383)</span>  
> **核心主題**：`Math` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個整數 `left` 與 `right`，請計算在區間 `[left, right]` 中，有多少個數字滿足其**二進位表示中「1 的個數」為質數**。

### 範例
- **Input**: `left = 6, right = 10` → **Output**: `4`
- **Explanation**:
  - 6  (110) -> 2 個 1 (質數)
  - 7  (111) -> 3 個 1 (質數)
  - 8 (1000) -> 1 個 1 (非質數)
  - 9 (1001) -> 2 個 1 (質數)
  - 10(1010) -> 2 個 1 (質數)
  共 4 個數字符合條件。

### 限制條件
- $1 \le left \le right \le 10^6$
- $0 \le right - left \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-02-21：初次提交 (建表篩與 Popcount)](#2026-02-21-初次提交)

---

## 💡 2026-02-21 初次提交

### 🎯 直覺 (Intuition)
解題的切入點在於觀察測資範圍。題目給定的區間最大值為 $10^6$，由於 $2^{20} \approx 10^6$，代表該範圍內的數字，其二進位最多只有 20 個 bit。因此，我們只需要預先建構一張 20 以內的質數表，接著掃描區間內的每個數字，並計算其 bit count 即可快速查表得出答案。

### 🛠️ 解題思路 (Approach)
1. **預處理建表**：
   使用埃拉托斯特尼篩法 (Sieve of Eratosthenes) 建立一個大小為 25 的 boolean 陣列 `isPrime`，快速篩出 20 以內的所有質數。
2. **區間掃描與位元計算**：
   遍歷區間 `[left, right]`，對每個數字使用 C++ 內建的 `__builtin_popcount(i)` 函數，它會以硬體級別別的指令快速計算出整數 `i` 二進位中 1 的數量。
3. **查表累加**：
   將算出的 bit count 直接作為索引代入 `isPrime` 陣列查詢，若為真則將結果加一。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(R - L)$，其中 $R$ 為 `right`，$L$ 為 `left`。建表的時間為常數級別，而 `__builtin_popcount` 的時間複雜度可視為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(1)$，僅使用了一個長度為 25 的固定大小陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int countPrimeSetBits(int left, int right) {
        vector<bool> isPrime(25, true);
        isPrime[0] = isPrime[1] = false;
        
        // 質數篩法建表
        for(int i = 2; i <= 20; ++i) {
            if(!isPrime[i]) continue;
            for(int j = 2 * i; j <= 20; j += i) {
                isPrime[j] = false;
            }
        }

        int rt = 0;
        for(int i = left; i <= right; ++i) {
            // 使用內建函數快速計算 bit count
            int cnt = __builtin_popcount(i);
            if(isPrime[cnt])
                ++rt;
        }

        return rt;
    }
};
// 2^10^2 ~= 10^6
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