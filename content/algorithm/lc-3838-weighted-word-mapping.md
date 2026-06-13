---
title: "LeetCode 3838. Weighted Word Mapping"
date: 2026-06-13T13:30:00+08:00
lastmod: 2026-06-13T13:30:00+08:00
difficulty: 1240
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "math", "simulation"]
keywords: ["LC3838", "Easy", "1240", "Weighted Word Mapping", "字串處理", "ASCII", "模除運算", "反向映射"]
description: "LeetCode 第 3838 題：Weighted Word Mapping。難度評分：1240。探討如何利用 ASCII 編碼特性與模除運算 (Modulo Arithmetic)，優雅地處理字元權重加總與字母表的反向推移映射，達成 O(N * L) 的最佳化實作。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3838](https://leetcode.com/problems/weighted-word-mapping/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1240)</span>  
> **核心主題**：`String` $\cdot$ `Math` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串陣列 `words` 和一個長度為 26 的整數陣列 `weights`。`weights[i]` 代表英文字母表中第 `i` 個小寫字母的權重（例如 `weights[0]` 代表 `'a'` 的權重）。

對於 `words` 中的每一個單字，你需要將其轉換為一個單一字元，轉換規則如下：
1. 計算該單字中所有字元的權重總和 `sum`。
2. 將總和對 26 取模，得到餘數 `rem = sum % 26`。
3. 將該單字映射到字母表中的「反向」對應字母。具體來說，餘數為 0 映射到 `'z'`，餘數為 1 映射到 `'y'`，以此類推，直到餘數為 25 映射到 `'a'`。

請你將所有單字映射後得到的字元串接起來，並回傳最終的字串。

### 限制條件
- $1 \le words.length \le 100$
- $1 \le words[i].length \le 100$
- `weights.length == 26`
- $0 \le weights[i] \le 100$
- `words[i]` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-13：初次提交 (ASCII 反向映射與模除運算)](#2026-06-13-初次提交)

---

## 💡 2026-06-13 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是一道標準的字串處理與模擬題。核心在於如何優雅地處理字元與數字之間的轉換。

**核心破局點 1：字母到索引的映射**
在 C++ 中，字元底層是 ASCII 碼。小寫字母 `'a'` 到 `'z'` 是連續分佈的。因此，計算字元 `c` 的權重陣列索引時，直接使用 `c - 'a'` 即可完美對應到 `0` ~ `25`。

**核心破局點 2：反向字母推移 (Inverted Modulo Mapping)**
計算完總和 `sum` 後，題目要求進行反向映射（0 $\to$ 'z', 1 $\to$ 'y' ... 25 $\to$ 'a'）。
我們可以先用 `sum % 26` 確保數值落在 $0$ 到 $25$ 之間。
接著，利用 `25 - (sum % 26)` 可以將數值反轉（0 變 25，25 變 0）。
最後再加上基準值 `'a'`，即可自動轉換回正確的 ASCII 字元。這一行簡單的數學運算省去了冗長的 `if-else` 或 `switch` 判斷。

### 🛠️ 解題思路 (Approach)
1. 宣告空字串 `rt` 準備儲存結果。
2. 使用 `for` 迴圈逐一處理 `words` 中的每個單字 `w`。
3. 針對每個單字，宣告 `sum = 0`。
4. 遍歷單字中的每個字元 `c`，利用 `weights[c - 'a']` 查表取得權重並累加至 `sum`。
5. 運用反向映射公式 `25 - (sum % 26) + 'a'` 算出對應字元，並附加至 `rt`。
6. 所有單字處理完畢後，回傳結果 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times L)$。其中 $N$ 是單字的數量，$L$ 是單字的平均長度。我們需要遍歷每一個單字中的每一個字元進行查表與加總。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了 `rt` 字串來儲存結果，其長度等於 `words` 陣列的長度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string mapWordWeights(vector<string>& words, vector<int>& weights) {
        string rt = "";
        
        // 逐一處理每個單字
        for(auto& w : words) {
            int sum = 0;
            
            // 1. 計算該單字的權重總和
            for(auto c : w) {
                sum += weights[c - 'a']; // 利用 ASCII 相減取得 0~25 的正確索引
            }

            // 2. 進行反向映射
            // sum % 26 取得 0~25 的餘數
            // 25 - 餘數 達成反向效果
            // 最後加上 'a' 轉回 ASCII 字元
            rt += (25 - (sum % 26) + 'a');
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