---
title: "LeetCode 3895. Count Digit Appearances"
date: 2026-04-12T10:05:00+08:00
lastmod: 2026-04-12T10:05:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math"]
keywords: ["LC3895", "Medium", "N/A", "Count Digit Appearances"]
description: "LeetCode 第 3895 題：Count Digit Appearances。探討如何利用取模 (%10) 與除法 (/10) 拆解數字，計算特定數字在陣列中出現的總次數。"
---

## 💡 解題思路與程式碼

這題測驗的是基礎的「數字拆解」能力。
我們對陣列中的每個數字進行 `while` 迴圈處理，利用 `num % 10` 取出個位數進行比對，再利用 `num / 10` 將數字向右推移，直到數字歸零。

*(💡 邊界提醒：如果題目測資允許 `num` 為 0 且 `digit` 也為 0 時，原版的 `while(k)` 會直接跳過導致少算。實務上可以補一個 `if(num == 0 && digit == 0) ++cnt;` 作為防呆保護。)*

```cpp
class Solution {
public:
    int countDigitOccurrences(vector<int>& nums, int digit) {
        int cnt = 0;
        for(auto num : nums) {
            // 防呆處理：若數字本身為 0 且尋找的位數也是 0
            if(num == 0 && digit == 0) {
                ++cnt;
                continue;
            }
            
            int k = num;
            while(k) {
                if((k % 10) == digit) {
                    ++cnt;
                }
                k /= 10;
            }
        }
        return cnt;
    }
};
```
- **時間複雜度**: $\mathcal{O}(N \times \log_{10} M)$。其中 $N$ 是陣列長度，$M$ 是數字的最大值（迴圈次數取決於數字的位數）。
- **空間複雜度**: $\mathcal{O}(1)$。

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