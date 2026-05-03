---
title: "LeetCode 3918. Sum of Primes Between Number and Its Reverse"
date: 2026-05-03T12:35:00+08:00
lastmod: 2026-05-03T12:35:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "prime", "sieve"]
keywords: ["LC3918", "Medium", "Sum of Primes", "Sieve of Eratosthenes", "埃氏篩法"]
description: "LeetCode 第 3918 題：Sum of Primes Between Number and Its Reverse。探討如何利用數學運算翻轉數字，並結合「埃拉托斯特尼篩法 (Sieve of Eratosthenes)」極速找出區間內的所有質數並求和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3918](https://leetcode.com/problems/sum-of-primes-between-number-and-its-reverse/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Number Theory` $\cdot$ `Sieve of Eratosthenes`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個正整數 `n`。
請先求出 `n` 的「反轉數字」(Reverse Number)，假設為 `rev_n`。
接著，請找出在 `n` 與 `rev_n` 之間（包含雙邊的閉區間 `[min(n, rev_n), max(n, rev_n)]`）所有的**質數 (Prime Numbers)**，並回傳它們的總和。

### 限制條件
- $1 \le n \le 1000$ *(依據程式碼配置推斷範圍)*
</details>

---

## 📝 歷次打卡與更新
- [2026-05-03：初次提交 (數字翻轉 + 埃氏篩法)](#2026-05-03-初次提交)

---

## 💡 2026-05-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的解題核心可以拆解為兩個獨立的子任務：
1. **反轉數字**：使用純數學的模運算 (`% 10`) 與除法 (`/ 10`) 來反轉數字，這比轉成字串反轉後再轉回整數還要快得多，且不會有字串配置的開銷。
2. **區間質數求和**：如果對區間內的每個數字都去跑一次 $\mathcal{O}(\sqrt{X})$ 的質數判斷，在密集區間下會產生多餘的計算。最優雅且高效的做法是使用 **埃拉托斯特尼篩法 (Sieve of Eratosthenes)**，預先建立一張質數表，將範圍內（例如 1000 以內）的質數全部標記出來，後續的質數判斷就能降至 $\mathcal{O}(1)$。

### 🛠️ 解題思路 (Approach)
1. **數字翻轉**：
   - 透過迴圈 `x = x * 10 + k % 10` 將數字 `n` 反轉得到 `x`。
   - 計算區間的左右邊界：`mm = min(x, n)`，`mx = max(x, n)`。
2. **埃氏篩法 (Sieve of Eratosthenes)**：
   - 宣告一個布林陣列 `is_p`，大小為 `1005`，初始全設為 `true`。
   - `0` 和 `1` 不是質數，設為 `false`。
   - 從 `i = 2` 開始遍歷到 `1000`，若遇到質數 (`is_p[i] == true`)，就將它的所有倍數 (`2i, 3i, 4i...`) 標記為 `false` (合數)。
3. **區間求和**：
   - 走訪區間 `[mm, mx]`，利用建好的 `is_p` 表進行 $\mathcal{O}(1)$ 查詢，若為質數則將數值累加至 `rt`，最後回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_{10} n + M \log \log M)$。
  - 反轉數字的時間為 $\mathcal{O}(\log_{10} n)$。
  - 埃氏篩法的時間為 $\mathcal{O}(M \log \log M)$，其中 $M$ 是篩法的最大範圍 (本題固定為 $1000$)。
  - 整體時間複雜度在常數範圍限制下，視為 $\mathcal{O}(1)$ 的極速執行。
- **空間複雜度**: $\mathcal{O}(M)$。其中 $M = 1000$，需要建立長度為 1005 的布林陣列 `is_p`，佔用空間極小。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int sumOfPrimesInRange(int n) {
        // 1. 純數學方法翻轉數字
        int k = n;
        int x = 0;
        while(k) {
            x *= 10;
            x += k % 10;
            k /= 10;
        }
        
        // 確立區間的上下界
        int mx = max(x, n), mm = min(x, n);

        // 2. 埃拉托斯特尼篩法 (Sieve of Eratosthenes)
        // 假設範圍最大到 1000，多開一點避免越界
        vector<bool> is_p(1005, true);
        is_p[0] = is_p[1] = false; // 0 和 1 不是質數
        
        for(int i = 2; i <= 1000; ++i) {
            if(!is_p[i]) continue;
            // 將質數 i 的所有倍數標記為合數
            for(int j = 2 * i; j <= 1000; j += i) {
                is_p[j] = false;   
            }
        }
        
        // 3. 區間求和
        int rt = 0;
        for(int i = mm; i <= mx; ++i) {
            if(is_p[i]) {
                rt += i;
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