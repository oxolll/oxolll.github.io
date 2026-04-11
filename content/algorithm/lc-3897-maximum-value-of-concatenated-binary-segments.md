---
title: "LeetCode 3897. Maximum Value of Concatenated Binary Segments"
date: 2026-04-12T10:15:00+08:00
lastmod: 2026-04-12T10:15:00+08:00
difficulty: 2000
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "greedy", "bit-manipulation", "sorting"]
keywords: ["LC3897", "Hard", "Concatenated Binary Segments", "Modular Arithmetic"]
description: "LeetCode 第 3897 題：Maximum Value of Concatenated... 探討如何利用貪婪排序決定二進位片段的最佳串接順序，並透過分塊位移 (Bit Chunking) 技巧安全地處理大數模運算以防溢位。"
---

## 💡 解題思路與程式碼

這是一道非常高水準的**貪婪法與位元運算**題。
為了讓二進位字串代表的值最大，我們希望在最高位 (Most Significant Bits) 放置「最多 1、最少 0」的片段。
1. **貪婪排序**：將片段依照 `(1的數量, 負的0的數量)` 進行遞增排序。然後從後往前讀取（即取出 1 最多、0 最少的片段放在最高位）。特別地，完全沒有 0 的片段被獨立抽出來放在陣列最後，確保它們能被放在絕對的最高位。
2. **大數位移與取模保護**：在進行 `rt = (rt << bits) + value` 時，如果直接將大量的 1 進行左移，很容易超過 64-bit `long long` 的極限導致溢位。
   - **巧妙解法 (分塊位移)**：每次只移動 **30 bits**。因為 $10^9 + 7$ 約等於 $2^{30}$，保證在左移 30 位元並加上 $2^{30}-1$ 後，數值絕對不會超過 $2^{60}$，完全落在 `long long` 的安全範圍內，接著馬上取模 `% MOD`。這是競賽中處理大數二進位極度專業的技巧！

```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int maxValue(vector<int>& nums1, vector<int>& nums0) {
        int n = nums1.size();
        vector<pair<long long, long long>> arr;
        
        // 1. 將包含 0 的片段進行收集
        for(int i = 0; i < n; ++i) {
            if(nums0[i] == 0) continue;
            // 排序鍵值：1的數量遞增，0的數量遞減 (因為加上負號)
            arr.push_back({nums1[i], -1 * nums0[i]});
        }
        
        sort(arr.begin(), arr.end());
        
        // 將純 1 的片段放在最後，確保它們能被排在最高的位數
        for(int i = 0; i < n; ++i) {
            if(nums0[i] != 0) continue;
            arr.push_back({nums1[i], 0});
        }
        
        long long rt = 0;
        
        // 2. 由最高位開始進行二進位組合
        for(int i = n - 1; i >= 0; --i) {
            int cnt1 = arr[i].first;
            int cnt2 = -1 * arr[i].second;
            
            // 處理 1 的位移：每次最多移動 30 bits 防止溢位
            while(cnt1 >= 30) {
                rt = ((rt << 30) + ((1ll << 30) - 1)) % MOD;
                cnt1 -= 30;
            }
            rt = ((rt << cnt1) + ((1ll << cnt1) - 1)) % MOD;

            // 處理 0 的位移 (只需左移，不用加數值)
            while(cnt2 >= 30) {
                rt = (rt << 30) % MOD;
                cnt2 -= 30;
            }
            rt = (rt << cnt2) % MOD;
        }
        return rt;
    }
};
```
- **時間複雜度**: $\mathcal{O}(N \log N)$。由排序決定。處理位移的時間為攤銷 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了 `arr` 來儲存排序鍵值。

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