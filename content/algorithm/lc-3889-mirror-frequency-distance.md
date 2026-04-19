---
title: "LeetCode 3889. Mirror Frequency Distance"
date: 2026-04-09T14:00:00+08:00
lastmod: 2026-04-09T14:00:00+08:00
difficulty: 1312
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table", "counting"]
keywords: ["LC3889", "Medium", "1312", "Mirror Frequency Distance", "Hash Table", "ASCII"]
description: "LeetCode 第 3889 題：Mirror Frequency Distance。難度評分：Medium (1312)。探討如何利用字元頻率統計 (Counting) 搭配 ASCII 編碼特性，以 O(N) 時間複雜度快速計算字串中對稱字元（字母與數字）的頻率差值總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3889](https://leetcode.com/problems/mirror-frequency-distance/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1312)</span>  
> **核心主題**：`String` $\cdot$ `Hash Table` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個僅包含小寫英文字母與數字的字串 `s`。
我們定義兩個字元互為「鏡像對稱 (Mirror)」如下：
- 對於小寫字母：`'a'` 與 `'z'` 對稱，`'b'` 與 `'y'` 對稱，以此類推（即第 $k$ 個字母與倒數第 $k$ 個字母對稱）。
- 對於數字：`'0'` 與 `'9'` 對稱，`'1'` 與 `'8'` 對稱，以此類推（即數字 $d$ 與 $9-d$ 對稱）。

字串的「鏡像頻率距離 (Mirror Frequency Distance)」定義為：所有對稱字元對 (Mirror Character Pairs) 之間出現頻率的**絕對差值總和**。
請計算並回傳字串 `s` 的鏡像頻率距離。

### 限制條件
- $1 \le s.length \le 10^5$
- `s` 僅包含小寫英文字母 (`'a'` - `'z'`) 與數字 (`'0'` - `'9'`)。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-09：初次提交 (字元頻率統計與對稱查表)](#2026-04-09-初次提交)

---

## 💡 2026-04-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是一道標準的字串頻率統計問題。
因為字串僅包含小寫字母與數字，我們可以使用一個大小為 150 的整數陣列（涵蓋所有標準 ASCII 碼範圍）來充當 Hash Table 統計各字元的出現次數。
統計完成後，我們只需針對題目定義的兩組對稱關係（小寫字母 26 個，分 13 對；數字 10 個，分 5 對）進行遍歷：
1. **小寫字母**：索引 `i` 從 0 到 12，對應的字元為 `'a' + i`，其對稱字元為 `'a' + 25 - i`（因為 `'a'` 到 `'z'` 的差距為 25）。
2. **數字**：索引 `i` 從 0 到 4，對應的字元為 `'0' + i`，其對稱字元為 `'0' + 9 - i`。

將每一對頻率的絕對差值 (`abs`) 累加即可得到最終結果。

### 🛠️ 解題思路 (Approach)
1. **頻率統計**：
   - 宣告長度為 150 的整數陣列 `cnt` 並初始化為 0。
   - 走訪字串 `s` 中的每一個字元 `x`，將 `cnt[x]` 加 1。
2. **計算小寫字母對稱距離**：
   - 宣告結果變數 `rt = 0`。
   - 建立迴圈 `i` 從 0 到 12 (共 13 對)。
   - 計算 `cnt['a' + i]` 與 `cnt['a' + 25 - i]` 的絕對差值並累加至 `rt`。
3. **計算數字對稱距離**：
   - 建立迴圈 `i` 從 0 到 4 (共 5 對)。
   - 計算 `cnt['0' + i]` 與 `cnt['0' + 9 - i]` 的絕對差值並累加至 `rt`。
4. **回傳結果**：回傳總和 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + |\Sigma|)$。其中 $N$ 是字串長度，$|\Sigma| = 13 + 5 = 18$ 為對稱配對的數量。統計頻率需 $\mathcal{O}(N)$，計算差值為常數時間 $\mathcal{O}(1)$。整體為線性時間 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(1)$。宣告了一個固定大小 (150) 的陣列，空間開銷不受字串長度影響。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int mirrorFrequency(string s) {
        // 利用 ASCII 碼範圍，宣告大小為 150 的陣列統計字元頻率
        vector<int> cnt(150, 0);
        for(auto x : s) {
            ++cnt[x];
        }

        int rt = 0;
        
        // 1. 處理小寫字母 (共 26 個，分為 13 對，索引 0~12)
        // 對稱映射：'a'+i 對應 'z'-i，即 'a'+25-i
        for(int i = 0; i <= 12; ++i) {
            rt += abs(cnt['a' + i] - cnt['a' + 25 - i]);
        }
        
        // 2. 處理數字 (共 10 個，分為 5 對，索引 0~4)
        // 對稱映射：'0'+i 對應 '9'-i，即 '0'+9-i
        for(int i = 0; i <= 4; ++i) {
            rt += abs(cnt['0' + i] - cnt['0' + 9 - i]);
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