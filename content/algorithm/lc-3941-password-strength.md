---
title: "LeetCode 3941. Password Strength"
date: 2026-05-24T16:00:00+08:00
lastmod: 2026-05-24T16:00:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table", "counting"]
keywords: ["LC3941", "Medium", "Password Strength", "ASCII", "字串處理", "雜湊陣列"]
description: "LeetCode 第 3941 題：Password Strength。探討如何利用 ASCII 碼特性與輕量級頻率陣列，在線性時間內過濾重複字元並計算具備權重差異的密碼強度得分。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3941](https://leetcode.com/problems/password-strength/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Hash Table` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串 `password`，請你計算並回傳該密碼的「強度得分」。
密碼強度的計算規則如下，對於字串中 **每個獨一無二 (Unique)** 的字元：
- 如果是 **小寫字母** ('a'-'z')，得分加 1。
- 如果是 **大寫字母** ('A'-'Z')，得分加 2。
- 如果是 **數字** ('0'-'9')，得分加 3。
- 如果是 **其他特殊字元**，得分加 5。

*(註：重複出現的同一個字元只會被計算一次。)*

### 限制條件
- $1 \le password.length \le 10^5$
- `password` 包含標準的 ASCII 字元。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-24：初次提交 (ASCII 陣列計數與權重判定)](#2026-05-24-初次提交)

---

## 💡 2026-05-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的兩個核心挑戰在於：**「去重」** 與 **「分類給分」**。
1. **去重 (Deduplication)**：因為題目要求只計算「獨一無二」的字元，如果使用雙層迴圈檢查會導致 $\mathcal{O}(N^2)$ 的時間複雜度，使用 `std::unordered_set` 則會有一定的常數開銷。
2. **ASCII 陣列映射**：由於標準的字元集範圍有限（通常落在 0 ~ 127 之間），最優雅且極速的作法是直接宣告一個大小為 200 的整數陣列 `cnt`，利用字元的 ASCII 整數值作為索引來記錄其出現次數。

### 🛠️ 解題思路 (Approach)
1. **頻率統計**：建立長度為 200 的陣列 `cnt`，並初始化為 0。遍歷字串 `password`，將對應 ASCII 碼的計數器加 1。
2. **計算得分**：遍歷 `cnt` 陣列（從索引 0 到 199）：
   - 如果 `cnt[i] == 0`，代表該字元未出現在密碼中，直接跳過 (`continue`)。
   - 利用索引 `i` 本身（代表該字元的 ASCII 值）判斷所屬區間：
     - 小寫字母區間：`'a' <= i && i <= 'z'` $\to$ 加 1 分。
     - 大寫字母區間：`'A' <= i && i <= 'Z'` $\to$ 加 2 分。
     - 數字區間：`'0' <= i && i <= '9'` $\to$ 加 3 分。
     - 其他區間：皆視為特殊字元 $\to$ 加 5 分。
3. **回傳總分** `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 為字串 `password` 的長度。第一階段遍歷字串耗時 $\mathcal{O}(N)$，第二階段遍歷大小固定為 200 的陣列耗時 $\mathcal{O}(1)$，整體為線性時間，效能極佳。
- **空間複雜度**: $\mathcal{O}(1)$。無論字串長度多長，輔助陣列的大小始終固定為 200 個整數大小，屬於常數空間級別。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int passwordStrength(string password) {
        // 1. 利用 ASCII 特性建立輕量級 Hash Table
        vector<int> cnt(200, 0);
        
        // 統計每個字元的出現次數
        for(auto c : password) {
            ++cnt[c];
        }

        int rt = 0;
        // 2. 遍歷整個字元集，進行去重與計分
        for(int i = 0; i < 200; ++i) {
            // 若字元未出現過，直接略過
            if(cnt[i] == 0) continue;
            
            // 透過 ASCII 碼所在的範圍區間，給予對應的權重分數
            if('a' <= i && i <= 'z') {
                ++rt;         // 小寫字母加 1 分
            } 
            else if('A' <= i && i <= 'Z') {
                rt += 2;      // 大寫字母加 2 分
            } 
            else if('0' <= i && i <= '9') {
                rt += 3;      // 數字加 3 分
            } 
            else {
                rt += 5;      // 其他特殊字元加 5 分
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