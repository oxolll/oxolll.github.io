---
title: "LeetCode 1189. Maximum Number of Balloons"
date: 2026-06-22T14:20:00+08:00
lastmod: 2026-06-22T14:20:00+08:00
difficulty: 1181
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table", "counting"]
keywords: ["LC1189", "Easy", "1181", "Maximum Number of Balloons", "字串處理", "字元統計", "木桶效應"]
description: "LeetCode 第 1189 題：Maximum Number of Balloons。難度評分：1181。探討如何利用固定大小的陣列進行高效率的字元頻率統計，並透過木桶效應 (短板效應) 找出拼湊單字的最大可能次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1189](https://leetcode.com/problems/maximum-number-of-balloons/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1181)</span>  
> **核心主題**：`String` $\cdot$ `Hash Table` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串 `text`，你需要使用 `text` 中的字母來拼湊出盡可能多的單字 `"balloon"`。
字串 `text` 中的每個字母最多只能被使用一次。請你回傳最多可以拼湊出幾個 `"balloon"`。

### 範例
- **Input**: `text = "nlaebolko"`
- **Output**: `1`

- **Input**: `text = "loonbalxballpoon"`
- **Output**: `2`
- **Explanation**: 可以拼出兩個 "balloon"。

- **Input**: `text = "leetcode"`
- **Output**: `0`

### 限制條件
- $1 \le text.length \le 10^4$
- `text` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-22：初次提交 (字元頻率統計與木桶效應)](#2026-06-22-初次提交)

---

## 💡 2026-06-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的核心在於計算特定單字的組成極限，這是一個標準的 **「木桶效應」** 問題：一個水桶能裝多少水，取決於最短的那塊木板。能拼出多少個 `"balloon"`，取決於儲備量最少的那個必要字母。

**核心破局點：頻率統計與比例換算**
1. `"balloon"` 需要的字元為：`b` (1個), `a` (1個), `l` (2個), `o` (2個), `n` (1個)。
2. 由於題目限制只有小寫英文字母，我們不需要使用較為肥大且有額外常數時間開銷的 Hash Map，直接使用大小為 26 的整數陣列 `cnt[26]` 即可達成 $\mathcal{O}(1)$ 的完美查找與統計。
3. 統計完畢後，針對所需的 5 個字母，找出它們的「可供應次數」。對於需要 2 個的字母 (`l` 與 `o`)，其可供應次數即為總數量除以 2。這 5 個數值的 **最小值** 就是最終答案。

---

### 🛠️ 解題思路 (Approach)
1. 宣告一個大小為 26 的整數陣列 `cnt`，並使用 `memset` 初始化為 0。
2. 遍歷字串 `text` 中的每一個字元 `c`，利用 ASCII 碼相減 `c - 'a'` 將字母映射到 `0 ~ 25` 的索引，並遞增其計數器。
3. 宣告變數 `mm` 初始化為 `INT_MAX`。
4. 依序將 `mm` 與組成 `"balloon"` 的各個字元的極限供應量取 `min`：
   - `b`: `cnt['b' - 'a']`
   - `a`: `cnt['a' - 'a']`
   - `l`: `cnt['l' - 'a'] / 2` (需要 2 個)
   - `o`: `cnt['o' - 'a'] / 2` (需要 2 個)
   - `n`: `cnt['n' - 'a']`
5. 回傳最終的最小值 `mm`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串 `text` 的長度。只需對字串進行一次線性掃描，後續的最小值計算皆為 $\mathcal{O}(1)$ 常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。無論字串多長，都只使用了固定大小的 `cnt[26]` 陣列，空間消耗為絕對常數。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 宣告為類別成員，或者放置於函式內皆可
    int cnt[26];
    
    int maxNumberOfBalloons(string text) {
        // 初始化陣列為 0
        memset(cnt, 0, sizeof(cnt));
        
        // 1. 線性掃描，統計各個小寫字母的出現頻率
        for(auto c : text) {
            ++cnt[c - 'a'];
        }

        int mm = INT_MAX;
        
        // 2. 利用木桶效應 (短板效應) 尋找最短缺的字元數量
        // b, a, n 各需要 1 個
        // l, o 各需要 2 個，因此數量需除以 2
        mm = min(mm, cnt['b' - 'a']);
        mm = min(mm, cnt['a' - 'a']);
        mm = min(mm, cnt['l' - 'a'] / 2);
        mm = min(mm, cnt['o' - 'a'] / 2);
        mm = min(mm, cnt['n' - 'a']);
        
        return mm;
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