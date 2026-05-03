---
title: "LeetCode 3917. Count Indices With Opposite Parity"
date: 2026-05-03T12:45:00+08:00
lastmod: 2026-05-03T12:45:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "counting", "math"]
keywords: ["LC3917", "Easy", "Opposite Parity", "Suffix Count", "奇偶性"]
description: "LeetCode 第 3917 題：Count Indices With Opposite Parity。探討如何避免 O(N^2) 的暴力雙層迴圈，利用「後綴奇偶數統計 (Suffix Frequency)」與滑動更新技巧，將時間複雜度完美優化至 O(N)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3917](https://leetcode.com/problems/count-indices-with-opposite-parity/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
對於每一個索引 `i`，請計算在其右側（即索引 $j > i$）有多少個數字與 `nums[i]` 的**奇偶性相反 (Opposite Parity)**。
也就是說：
- 如果 `nums[i]` 是偶數，請計算右側有幾個奇數。
- 如果 `nums[i]` 是奇數，請計算右側有幾個偶數。

請回傳一個與原陣列長度相同的陣列，代表每個索引對應的計算結果。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-03：初次提交 (O(N) 後綴統計與滑動更新)](#2026-05-03-初次提交)

---

## 💡 2026-05-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題如果使用雙層迴圈暴力求解（對於每個 $i$，往後掃描所有 $j$），時間複雜度會高達 $\mathcal{O}(N^2)$，在陣列長度為 $10^5$ 時會導致超時 (TLE)。

**破局關鍵：後綴頻率統計 (Suffix Frequency)**
對於每個索引 $i$，我們只關心「它右邊」的奇數和偶數數量。
如果我們能預先知道所有數字的奇偶分布，當我們從左往右掃描時，每經過一個數字，它就不再屬於「右側」的範圍了。因此，我們只需要將該數字的奇偶屬性從總計數中「扣除」即可。這是一種非常經典的滑動計數 (Sliding Count) 技巧，能將時間複雜度降維至 $\mathcal{O}(N)$。

### 🛠️ 解題思路 (Approach)
1. **第一次掃描 (初始化右側計數)**：
   - 宣告 `odd` 和 `even` 變數來統計數量。
   - 因為我們一開始停在索引 `0`，所以它的「右側」是從索引 `1` 到 `n-1`。我們走訪這段區間，統計出所有的奇數與偶數總數。
2. **第二次掃描 (生成答案與動態更新)**：
   - 走訪整個陣列 $i$ 從 $0$ 到 $n-1$。
   - **生成答案**：判斷當前的 `nums[i]`。如果是偶數，答案就是目前右側的 `odd` 數量；如果是奇數，答案就是目前右側的 `even` 數量。
   - **動態更新 (滑動)**：準備移動到下一個索引 $i+1$ 之前，`nums[i+1]` 即將變成我們「當前」的數字，不再屬於「右側」。因此，我們檢查 `nums[i+1]` 的奇偶性，並從對應的 `even` 或 `odd` 變數中扣除 $1$。
3. **回傳結果陣列**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。陣列總共被走訪了將近兩次，沒有任何巢狀迴圈，效能極佳。
- **空間複雜度**: $\mathcal{O}(1)$（不含回傳結果的空間）。只使用了 `odd` 與 `even` 兩個變數進行計數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> countOppositeParity(vector<int>& nums) {
        int odd = 0, even = 0;
        int n = nums.size();
        
        // 1. 初始化：統計索引 0 右側 (從索引 1 開始) 的奇偶數總數
        for(int i = 1; i < n; ++i) {
            if(nums[i] % 2 == 0) ++even;
            else ++odd;
        }
            
        vector<int> rt;
        // 2. 邊走訪邊產生答案，並動態更新右側的奇偶數總數
        for(int i = 0; i < n; ++i) {
            // 如果自己是偶數，需要右側的奇數數量；反之亦然
            if(nums[i] % 2 == 0) {
                rt.push_back(odd);
            } else {
                rt.push_back(even);
            }

            // 準備移動到 i+1，所以 nums[i+1] 將不再屬於 "右側"
            // 從計數器中扣除它
            if(i + 1 < n) {
                if(nums[i+1] % 2 == 0) --even;
                else --odd;
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