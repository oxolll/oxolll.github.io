---
title: "LeetCode 2640. Find the Score of All Prefixes of an Array"
date: 2026-03-21T00:41:00+08:00
lastmod: 2026-03-21T00:41:00+08:00
difficulty: 1314
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Prefix Sum"]
keywords: ["LC2640", "1314", "Medium", "Prefix Score", "Running Maximum"]
description: "LeetCode 第 2640 題：Find the Score of All Prefixes of an Array。難度評分：1314。探討如何透過單次遍歷，同時維護局部最大值與前綴和，以 O(N) 時間複雜度與 O(1) 額外空間高效計算陣列的轉換分數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2640](https://leetcode.com/problems/find-the-score-of-all-prefixes-of-an-array/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1314)</span>      
> **核心主題**：`Array` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的正整數陣列 `nums`。
我們定義一個陣列 `conver`，其中 `conver[i] = nums[i] + max(nums[0...i])`。也就是說，`conver[i]` 等於原陣列該位置的數值，加上從索引 0 到 $i$ 之間出現過的最大值。

另外，定義一個陣列的前綴分數 (Prefix Score)：對於索引 $i$，其前綴分數為 `conver` 陣列從索引 0 到 $i$ 的總和。
請回傳一個長度為 $n$ 的陣列 `ans`，其中 `ans[i]` 是給定陣列 `nums` 在索引 $i$ 的前綴分數。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (單次遍歷與動態前綴和)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
題目定義了兩個轉換步驟：先算出局部最大值，再將其與原數值相加得到 `conver`，最後求 `conver` 的前綴和。
如果對每個前綴都重新掃描一次最大值與總和，時間複雜度將會退化至 $\mathcal{O}(N^2)$，導致 Time Limit Exceeded (TLE)。
由於每次計算索引 $i$ 的狀態時，只會依賴索引 $i-1$ 的狀態以及當前的數值 `nums[i]`，因此我們可以在單次 $\mathcal{O}(N)$ 的線性遍歷中，直接以動態規劃 (Dynamic Programming) 的思想來進行狀態轉移。

### 🛠️ 解題思路 (Approach)
1. **防範溢位**：因為 `nums[i]` 最大可達 $10^9$，兩個 $10^9$ 相加再加上前綴和的累積，極易超過 32-bit 整數的上限。因此，所有負責累加與記錄狀態的變數 (`mx`, `cov`, `pre`) 必須宣告為 64-bit 整數 (`long long`)。
2. **狀態維護**：
   - `mx`：記錄從索引 0 到當前索引 $i$ 的最大值。
   - `cov`：計算當前的轉換數值 (`nums[i] + mx`)。
   - `pre`：記錄 `conver` 陣列的前綴和。
3. **線性掃描**：
   - 遍歷 `nums` 陣列，逐一更新 `mx`。利用 `nums[i] * 1ll` 強制轉型，確保比較時的型別安全。
   - 計算當前的 `cov` 值，並將其累加至 `pre`。
   - 將計算完畢的 `pre` 推入結果陣列 `rt` 中。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需對長度為 $N$ 的陣列進行一次線性掃描，每次迴圈內的運算皆為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(1)$。除了用於回傳結果的陣列 `rt` 之外，僅使用了三個 `long long` 變數來儲存轉移狀態，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<long long> findPrefixScore(vector<int>& nums) {
        int n = nums.size();
        vector<long long> rt; 
        rt.reserve(n); // 預先配置記憶體，避免動態擴容開銷

        long long mx = INT_MIN; // 維護區間最大值
        long long cov = 0;      // 暫存目前的轉換數值
        long long pre = 0;      // 維護前綴分數總和
        
        for(int i = 0; i < n; ++i) {
            // 使用 1ll 確保比較與賦值過程以 64-bit 進行，防止溢位
            mx = max(mx, nums[i] * 1ll);
            cov = mx + nums[i];
            pre = pre + cov;
            rt.push_back(pre);
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