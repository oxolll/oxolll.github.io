---
title: "LeetCode 3014. Minimum Number of Pushes to Type Word I"
date: 2026-04-22T22:54:00+08:00
lastmod: 2026-04-22T22:54:00+08:00
difficulty: 1324
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy", "sorting", "bit-manipulation"]
keywords: ["LC3014", "Easy", "1324", "Minimum Number of Pushes", "Greedy", "Bitwise"]
description: "LeetCode 第 3014 題：Minimum Number of Pushes to Type Word I。難度評分：1324。探討如何利用貪婪策略 (Greedy) 與位元運算 (i >> 3) 極速計算按鍵次數，並寫出能無縫過渡至 Part II 的通用最佳解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3014](https://leetcode.com/problems/minimum-number-of-pushes-to-type-word-i/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1324)</span>  
> **核心主題**：`String` $\cdot$ `Greedy` $\cdot$ `Sorting`
> **🔗 系列題連結**：[👉 點此查看 Part II (LeetCode 3016) 進階題解筆記](../3016-minimum-number-of-pushes-to-type-word-ii/)

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串 `word`，代表你要在一個傳統手機鍵盤（包含按鍵 2 到 9，共 **8** 個可用按鍵）上打出的文字。
你可以將 26 個小寫英文字母**任意重新對應**到這 8 個按鍵上。每個按鍵可以對應多個字母，但每個字母只能對應到一個按鍵。
當你按鍵盤時，輸入該按鍵上的第 1 個字母需要按 1 次，第 2 個字母需要按 2 次，依此類推。

請找出重新規劃按鍵對應後，打出整個 `word` 所需的**最少按鍵總次數**。
*(註：本題保證 `word` 中沒有重複字元。)*

### 限制條件
- $1 \le word.length \le 26$
- `word` 僅包含小寫英文字母，且沒有重複字元。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-22：初次提交 (頻率統計與貪婪位元運算)](#2026-04-22-初次提交)

---

## 💡 2026-04-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
雖然 Part I 保證了字串中沒有重複字元，可以直接依據長度依序填滿第一格、第二格。但為了讓程式碼具備最完美的擴展性（直接通殺 Part II），我們依然採用標準的**貪婪演算法 (Greedy Algorithm)** 模型。
可用按鍵總共有 8 個，意味著：
- 排名前 8 高的字母放在第一格，按鍵成本為 1。
- 排名 9 到 16 的字母放在第二格，按鍵成本為 2。
依此類推。

**極致優化 (Bitwise Magic)**：
在根據排名索引 `i` 計算成本時，利用 `(i >> 3) + 1` 取代 `(i / 8) + 1`，在底層組合語言層級上能省去 CPU 除法器的開銷，達成效能的最大化。

### 🛠️ 解題思路 (Approach)
1. **頻率統計**：走訪 `word` 計算每個字母的出現次數存入 `cnt` 陣列。
2. **貪婪排序**：將 `cnt` 陣列由大到小排序。
3. **計算最小按鍵數**：
   - 走訪排序好的陣列，若遇到頻率為 0 的字母，直接提早剪枝 `break`。
   - 該字母的按鍵次數為 `cnt[i] * ((i >> 3) + 1)`，累加至結果。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + |\Sigma| \log |\Sigma|)$。$N$ 是字串長度，字符集 $|\Sigma| = 26$ 為常數。整體時間複雜度為線性 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(|\Sigma|)$。宣告大小為 26 的 `cnt` 陣列，空間為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumPushes(string word) {
        vector<int> cnt(26, 0);
        for(auto c : word) {
            ++cnt[c - 'a'];
        }

        sort(cnt.rbegin(), cnt.rend());
        int rt = 0;
        
        for(int i = 0; i < 26; ++i) {
            if(cnt[i] == 0) break;
            // 位元運算優化：(i >> 3) 等價於 i / 8
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