---
title: "LeetCode 3881. Direction Assignments with Exactly K Visible People"
date: 2026-03-29T01:40:00+08:00
lastmod: 2026-03-29T01:40:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "combinatorics", "number theory"]
keywords: ["LC3881", "Medium", "N/A", "Combinatorics", "Fermat's Little Theorem", "Modular Inverse"]
description: "LeetCode 第 3881 題：Direction Assignments with Exactly K Visible People。難度評分：Medium (N/A)。探討如何運用費馬小定理求模反元素，並結合快速冪與組合數公式，以 O(K + log(MOD)) 時間複雜度高效計算排列組合數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3881](https://leetcode.com/problems/direction-assignments-with-exactly-k-visible-people/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Combinatorics` $\cdot$ `Number Theory`

---

## 📝 歷次打卡與更新
- [2026-03-29：初次提交 (費馬小定理與組合數計算)](#2026-03-29-初次提交)

---

## 💡 2026-03-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的本質是計算特定的排列組合數量，且最終結果必須對 $10^9+7$ 取餘數。
經過數學推導後，題目的所求被完美化簡為一個公式：$2 \times \binom{n-1}{k} \pmod{10^9+7}$。
在程式實作中，計算組合數 $\binom{N}{K} = \frac{N!}{K!(N-K)!}$ 時，會遇到巨大的階乘與除法。由於除法不能直接套用模運算的分配律，我們必須將「除以 $K!$」轉化為「乘上 $K!$ 的模反元素」。
因為 $10^9+7$ 是一個質數，我們可以利用 **費馬小定理 (Fermat's Little Theorem)**：
$$a^{p-1} \equiv 1 \pmod p$$
$$a \cdot a^{p-2} \equiv 1 \pmod p$$
由此可知，$a$ 的模反元素即為 $a^{p-2} \pmod p$。我們可以利用「快速冪 (Fast Exponentiation)」在 $\mathcal{O}(\log p)$ 的時間內快速求出這個次方值。

### 🛠️ 解題思路 (Approach)
1. **實作快速冪與模反元素 (`inver`)**：
   - 接收底數 `a` 與指數 `pow` (此處傳入 `MOD - 2`)。
   - 利用位元運算 (`pow & 1` 與 `pow >>= 1`) 將指數拆解為二進位，快速將底數不斷平方並取模，求出 $a^{MOD-2}$。
2. **實作組合數 (`comb`)**：
   - 要計算 $\binom{n}{k}$，為了避免算出完整的階乘，我們只計算分子與分母的必要部分。
   - 分子 `up` 為 $n \times (n-1) \times \dots \times (n-k+1)$。
   - 分母 `low` 為 $k!$ 即 $1 \times 2 \times \dots \times k$。
   - 使用單一迴圈同時計算 `up` 與 `low`，每次相乘後皆對 `MOD` 取餘數。
   - 迴圈結束後，回傳 `(up * inver(low, MOD - 2)) % MOD`。
3. **主函式計算**：
   - 直接套用化簡後的公式，呼叫 `comb(n - 1, k)`，將其結果乘以 2 並對 `MOD` 取餘數後回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(K + \log(\text{MOD}))$。在 `comb` 函式中，迴圈執行了 $K$ 次。計算模反元素的 `inver` 函式透過快速冪執行了 $\mathcal{O}(\log(\text{MOD}))$ 次運算。兩者皆為極小的常數級別開銷。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個 `long long` 變數來儲存中間的乘積與餘數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    
    // 利用快速冪計算 a^pow % MOD (用於求模反元素)
    long long inver(long long a, long long pow) {
        long long rt = 1;
        long long base = a;
        while(pow) {
            if(pow & 1) rt = (rt * base) % MOD;
            base = (base * base) % MOD;
            pow >>= 1;
        }
        return rt;
    }
    
    // 計算組合數 C(n, k) % MOD
    long long comb(int n, int k) {
        long long up = 1, low = 1;
        // n! / (k!(n-k)!) 簡化為 (n-k+1)*...*(n) / (1*2*...*k)
        for(int i = 1; i <= k; ++i) {
            up = (up * (n - k + i)) % MOD;
            low = (low * i) % MOD;
        }

        // 分子乘上分母的模反元素
        return (up * inver(low, MOD - 2)) % MOD;
    }
    
    int countVisiblePeople(int n, int pos, int k) {
        // 套用推導公式：2 * C(n-1, k) % MOD
        return (2 * 1LL * comb(n - 1, k)) % MOD;
    }
};

/* 數學推導註記：
   費馬小定理求模反元素：
   a^(mod-1) ≡ 1 (mod p)
   => a * a^(mod-2) ≡ 1 (mod p)
   => a^-1 ≡ a^(mod-2) (mod p)
   因此，除以 a 等同於乘上 a 的模反元素，即乘上 a^(mod-2)
*/
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