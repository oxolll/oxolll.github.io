---
title: "LeetCode 3896. Minimum Operations to Transform Array into Alternating Prime"
date: 2026-04-12T10:10:00+08:00
lastmod: 2026-04-12T10:10:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "number-theory", "greedy"]
keywords: ["LC3896", "Medium", "Alternating Prime", "Sieve of Eratosthenes"]
description: "LeetCode 第 3896 題：Minimum Operations... 探討如何利用埃拉托斯特尼篩法 (Sieve of Eratosthenes) 預處理質數表，並透過貪婪法計算轉換為交替質數與非質數所需的最少操作次數。"
---

## 💡 解題思路與程式碼

這題是標準的「數論預處理」結合「貪婪法 (Greedy)」。
由於我們需要頻繁判斷一個數字是否為質數，最優解是先使用 **埃拉托斯特尼篩法 (Sieve of Eratosthenes)** 在 $\mathcal{O}(M \log \log M)$ 的時間內建構出範圍至 $2 \times 10^5$ 的質數表。
建表完成後，我們走訪原陣列：
- 若索引為偶數：目標是質數。如果當前不是質數，就貪婪地往上加 (`++k`)，直到遇到質數為止，並記錄操作次數。
- 若索引為奇數：目標是非質數 (合數)。如果當前是質數，同樣往上加，直到遇到非質數為止。

```cpp
class Solution {
public:
    int minOperations(vector<int>& nums) {
        // 1. 質數篩法預處理 (Sieve of Eratosthenes)
        vector<bool> isprime(2e5 + 5, true);
        isprime[0] = false;
        isprime[1] = false;
        for(int i = 2; i <= 2e5; ++i) {
            if(isprime[i] == false) continue;
            // 質數的倍數皆為合數
            for(int j = 2 * i; j <= 2e5; j += i) {
                isprime[j] = false;
            }
        }
        
        int rt = 0;
        // 2. 貪婪計算操作次數
        for(int i = 0; i < nums.size(); ++i) {
            if((i % 2) == 1) {
                // 奇數索引：目標為非質數
                if(!isprime[nums[i]]) continue;
                int up = 0;
                int k = nums[i];
                // 貪婪往上找直到變成非質數
                while(k <= 2e5 && isprime[k]) {
                    ++up;
                    ++k;
                }
                rt += up;
            } else {
                // 偶數索引：目標為質數
                if(isprime[nums[i]]) continue;
                int up = 0;
                int k = nums[i];
                // 貪婪往上找直到變成質數
                while(k <= 2e5 && !isprime[k]) {
                    ++up;
                    ++k;
                }
                rt += up;
            }
        }
        return rt;
    }
};
```
- **時間複雜度**: $\mathcal{O}(M \log \log M + N \times G)$。其中 $M$ 是最大範圍 ($200000$)，$N$ 是陣列長度，$G$ 是相鄰質數/合數之間的最大間距（通常極小）。
- **空間複雜度**: $\mathcal{O}(M)$。質數表佔用的空間。

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