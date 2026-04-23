---
title: "LeetCode 3016. Minimum Number of Pushes to Type Word II"
date: 2026-04-22T22:54:00+08:00
lastmod: 2026-04-22T22:54:00+08:00
difficulty: 1533
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy", "sorting", "bit-manipulation", "hash-table"]
keywords: ["LC3016", "Medium", "1533", "Minimum Number of Pushes", "Greedy", "Bitwise"]
description: "LeetCode 第 3016 題：Minimum Number of Pushes to Type Word II。難度評分：1533。探討在允許重複字元的情況下，如何利用貪婪策略 (Greedy) 根據字母頻率排序，並結合位元運算極速計算最佳按鍵配置。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3016](https://leetcode.com/problems/minimum-number-of-pushes-to-type-word-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1533)</span>  
> **核心主題**：`String` $\cdot$ `Greedy` $\cdot$ `Sorting` $\cdot$ `Hash Table`
> **🔗 系列題連結**：[👉 點此查看 Part I (LeetCode 3014) 基礎題解筆記](../3014-minimum-number-of-pushes-to-type-word-i/)

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
*(此為 Part I 的進階版)*
給你一個字串 `word`，代表你要在一個傳統手機鍵盤（包含按鍵 2 到 9，共 **8** 個可用按鍵）上打出的文字。
你可以將 26 個小寫英文字母**任意重新對應**到這 8 個按鍵上。每個按鍵可以對應多個字母，但每個字母只能對應到一個按鍵。
當你按鍵盤時，輸入該按鍵上的第 1 個字母需要按 1 次，第 2 個字母需要按 2 次，依此類推。

請找出重新規劃按鍵對應後，打出整個 `word` 所需的**最少按鍵總次數**。
*(註：本題允許 `word` 中有重複字元，因此頻率越高的字元越需要被優先配置。)*

### 限制條件
- $1 \le word.length \le 10^5$
- `word` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-22：初次提交 (頻率統計與貪婪位元運算)](#2026-04-22-初次提交)

---

## 💡 2026-04-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題允許字元重複，因此核心解法必須建立在**貪婪演算法 (Greedy Algorithm)** 之上。
策略非常直觀：**「出現頻率越高的字母，越應該放在按鍵的第 1 個位置（只需按 1 次）」**。
我們一共有 8 個按鍵，因此可以劃分為幾個「成本階級」：
- 頻率最高的前 8 個字母，分配在第一格（成本為 1）。
- 頻率排名的第 9 到 16 個字母，分配在第二格（成本為 2），依此類推。

我們透過統計字元頻率並排序後，利用索引來推算每個字母所屬的成本區間。
特別是在計算階層時，使用位元右移 `(i >> 3) + 1` 取代 `(i / 8) + 1` 來精簡除法運算，是競技程式實作上的極佳優化。

### 🛠️ 解題思路 (Approach)
1. **頻率統計**：宣告大小為 26 的 `cnt` 陣列，走訪 `word` 並累加字元頻率。
2. **貪婪排序**：使用 `sort(cnt.rbegin(), cnt.rend())` 將頻率由高到低進行遞減排序。
3. **結算按鍵成本**：
   - 宣告 `rt = 0` 記錄總和。
   - 遍歷排序後的頻率陣列（索引 `i` 從 0 到 25）。
   - 若遇到頻率為 0 的字母，代表字元已處理完畢，提早剪枝 `break`。
   - 累加成本：頻率 `cnt[i]` 乘上按鍵權重 `((i >> 3) + 1)`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + |\Sigma| \log |\Sigma|)$。其中 $N$ 是字串長度。頻率統計耗費 $\mathcal{O}(N)$，常數長度的陣列排序時間可視為 $\mathcal{O}(1)$，整體時間複雜度為 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(|\Sigma|)$。宣告了一個大小為 26 的計數陣列，空間為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumPushes(string word) {
        // 1. 統計字母出現頻率
        vector<int> cnt(26, 0);
        for(auto& c : word) {
            ++cnt[c - 'a'];
        }

        // 2. 貪婪策略：頻率由大到小排序
        sort(cnt.rbegin(), cnt.rend());
        
        int rt = 0;
        
        // 3. 依序計算按鍵次數
        for(int i = 0; i < 26; ++i) {
            // 提早剪枝：後面沒出現過的字母不需計算
            if(cnt[i] == 0) break;
            
            // 位元運算優化：(i >> 3) 等價於 i / 8
            // 索引 0~7 的成本為 1，8~15 的成本為 2，依此類推。
            rt += cnt[i] * ((i >> 3) + 1);
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