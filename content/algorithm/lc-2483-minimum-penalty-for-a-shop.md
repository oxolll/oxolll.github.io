---
title: "LeetCode 2483. Minimum Penalty for a Shop"
date: 2026-03-21T02:05:00+08:00
lastmod: 2026-03-21T02:05:00+08:00
difficulty: 1494
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Prefix Sum"]
keywords: ["LC2483", "1494", "Medium", "Minimum Penalty", "Prefix Sum", "狀態壓縮"]
description: "LeetCode 第 2483 題：Minimum Penalty for a Shop。難度評分：1494。探討如何利用十進位狀態壓縮技巧，將兩個前綴和合併至單一 64-bit 整數陣列中，以 O(N) 時間找出商店的最佳打烊時間。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2483](https://leetcode.com/problems/minimum-penalty-for-a-shop/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1494)</span>    
> **核心主題**：`String` $\cdot$ `Prefix Sum` $\cdot$ `State Compression`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的字串 `customers`，其中字元為 `'Y'`（代表有顧客）或 `'N'`（代表沒有顧客），這代表了一家商店在每個小時的營業狀況。
如果商店在第 $j$ 小時打烊（$0 \le j \le n$），則罰款 (Penalty) 的計算方式如下：
- 在打烊**之前**的每個小時，如果沒有顧客 (`'N'`)，罰款增加 1。
- 在打烊**之後**（包含第 $j$ 小時）的每個小時，如果有顧客 (`'Y'`)，罰款增加 1。

請回傳能讓罰款降到**最低**的**最早**打烊時間。
*(注意：商店可以選擇在第 0 小時打烊，或在第 $n$ 小時打烊。)*

### 限制條件
- $1 \le customers.length \le 10^5$
- `customers` 僅包含字元 `'Y'` 和 `'N'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (十進位狀態壓縮與前綴和)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的核心是計算在每個時間點 $i$ 打烊的總罰款：
`總罰款 = (0 到 i-1 出現的 'N' 數量) + (i 到 n-1 出現的 'Y' 數量)`
為了避免每次都重新計算 $\mathcal{O}(N)$，我們需要利用前綴和 (Prefix Sum)。

由於字串長度最大為 $10^5$，任何字元的總數都不會超過 $10^5$。我們可以利用**十進位狀態壓縮**的技巧：將 `'Y'` 的計數乘上 $10^6$，而 `'N'` 的計數則保持為 $1$。
這樣一來，單一一個 `long long` 變數就能同時儲存兩種字元的前綴和：
- 高位元 (`val / 1000000`)：代表 `'Y'` 的數量。
- 低位元 (`val % 1000000`)：代表 `'N'` 的數量。
此舉不但減少了陣列宣告的數量，也讓快取局部性 (Cache Locality) 更好。

### 🛠️ 解題思路 (Approach)
1. **建立壓縮前綴和**：
   - 宣告 `pre` 陣列。
   - 遍歷字串：若遇到 `'Y'` 則累加 `1e6`，若遇到 `'N'` 則累加 `1`。這一步完成了同時記錄 `Y` 與 `N` 數量的前綴和。
2. **尋找最佳打烊時間**：
   - 設定初始的最小罰款 `mm = INT_MAX`，最佳時間 `rt = -1`。
   - 遍歷打烊時間 $i$ (從 $0$ 到 $n-1$)：
     - 打烊前的 `'N'` 罰款：即為 `pre[i-1] % 1e6`。
     - 打烊後的 `'Y'` 罰款：即為 `總 'Y' 數量 - 打烊前的 'Y' 數量`，也就是 `(pre.back() / 1e6) - (pre[i-1] / 1e6)`。
     - 將兩者相加得到當前打烊時間的總罰款 `pan`。
     - 如果 `pan < mm`，更新最小罰款並記錄當前時間 `rt = i`。
3. **處理邊界情況**：
   - 商店可以選擇在最後一個小時結束後才打烊 (即 $i = n$)。此時沒有打烊後的 `'Y'` 罰款，總罰款純粹為整天的 `'N'` 數量，也就是 `pre.back() % 1e6`。
   - 如果這個值比之前的最小罰款 `mm` 還小，則更新 `rt = n`。
4. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。執行了兩次長度為 $N$ 的迴圈，常數極小。
- **空間複雜度**: $\mathcal{O}(N)$。使用了一個大小為 $N$ 的 `long long` 陣列來儲存前綴和。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int bestClosingTime(string customers) {
        int n = customers.size();
        vector<long long> pre(n, 0);
        
        // 1. 十進位狀態壓縮：'Y' 存於百萬位以上，'N' 存於十萬位以下
        for(int i = 0; i < n; ++i) {
            pre[i] = (i > 0 ? pre[i-1] : 0) + (customers[i] == 'Y' ? 1e6 : 1);
        }

        long long mm = INT_MAX;
        int rt = -1;
        
        // 2. 遍歷可能的打烊時間 0 到 n-1
        for(int i = 0; i < n; ++i) {
            // 打烊前 'N' 的數量 + 打烊後 'Y' 的數量
            long long pan = (i > 0 ? pre[i-1] % 1000000 : 0) + 
                            (pre.back() / 1000000) - 
                            (i > 0 ? pre[i-1] / 1000000 : 0);
            
            // 尋找「最早」的最小罰款時間，所以用 > 嚴格大於才更新
            if(mm > pan) {
                mm = pan;
                rt = i;
            }
        }
        
        // 3. 處理邊界：在第 n 小時(全部營業完)打烊的罰款
        if(pre.back() % 1000000 < mm) {
            rt = n;
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