---
title: "LeetCode 1967. Number of Strings That Appear as Substrings in Word"
date: 2026-06-29T15:21:00+08:00
lastmod: 2026-06-29T15:21:00+08:00
difficulty: 1231
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "string-matching"]
keywords: ["LC1967", "Easy", "1231", "Substrings", "字串匹配", "find", "npos"]
description: "LeetCode 第 1967 題：Number of Strings That Appear as Substrings in Word。難度評分：1231。探討如何利用 C++ 標準函式庫的 find 函式，高效且簡潔地完成陣列中多個字串的子字串比對任務。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1967](https://leetcode.com/problems/number-of-strings-that-appear-as-substrings-in-word/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1231)</span>  
> **核心主題**：`String` $\cdot$ `String Matching`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串陣列 `patterns` 和一個字串 `word`。
請你計算並回傳 `patterns` 中，有多少個字串是 `word` 的 **子字串 (Substring)**。

子字串的定義是：字串中一段連續的字元序列。

### 範例
- **Input**: `patterns = ["a","abc","bc","d"], word = "abc"`
- **Output**: `3`
- **Explanation**: 
  - "a" 是 "abc" 的子字串。
  - "abc" 是 "abc" 的子字串。
  - "bc" 是 "abc" 的子字串。
  - "d" 不是 "abc" 的子字串。
  共有 3 個符合條件的字串。

- **Input**: `patterns = ["a","b","c"], word = "aaaaabbbbb"`
- **Output**: `2`
- **Explanation**: "a" 和 "b" 是子字串，"c" 不是。

- **Input**: `patterns = ["a","a","a"], word = "ab"`
- **Output**: `3`
- **Explanation**: 每個 "a" 都被獨立計算。

### 限制條件
- $1 \le patterns.length \le 100$
- $1 \le patterns[i].length \le 100$
- $1 \le word.length \le 100$
- `patterns[i]` 和 `word` 僅由小寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-29：初次提交 (內建函式庫暴力比對)](#2026-06-29-初次提交)

---

## 💡 2026-06-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的測資範圍非常小（陣列長度、單字長度皆不超過 100），因此不需要動用 KMP (Knuth-Morris-Pratt) 或 Rabin-Karp 等高階字串匹配演算法。
我們只需直接遍歷 `patterns` 陣列，並利用 C++ 內建的 `std::string::find` 逐一檢查該單字是否為 `word` 的子字串即可。

**核心機制與邊界處理**：
當 `std::string::find` 找不到指定的子字串時，會回傳常數 `std::string::npos`。
在 C++ 底層中，`npos` 通常被定義為 `-1` 並轉型為無號整數 `size_t`，這意味著它會是 `size_t` 能夠表達的 **最大值**。
因此，判斷 `word.find(p) < word.size()` 是一種依賴此底層機制的巧妙寫法（若找不到，`npos` 必定大於 `word.size()`）。

### 🛠️ 解題思路 (Approach)
1. 宣告計數器 `c = 0`。
2. 遍歷 `patterns` 中的每一個字串 `p`。
3. 呼叫 `word.find(p)`：
   - 若回傳的索引值小於 `word.size()`，代表 `p` 確實存在於 `word` 中，則 `++c`。
4. 遍歷結束，回傳最終的計數結果 `c`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times L \times M)$。其中 $N$ 是 `patterns` 陣列的長度，$L$ 是 `word` 的長度，$M$ 是 `patterns` 中字串的最大長度。C++ `find` 的底層實作最壞情況下為 $\mathcal{O}(L \times M)$。在給定的極小測資下，執行時間幾乎可視為 $\mathcal{O}(N)$ 常數。
- **空間複雜度**: $\mathcal{O}(1)$。沒有使用額外的資料結構，僅使用常數級的記憶體空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numOfStrings(vector<string>& patterns, string word) {
        int c = 0;
        
        // 線性掃描每一個 pattern
        for(auto p : patterns) {
            // 利用 find 搜尋子字串，若找到的索引 < word.size() 代表存在
            // 實務上更建議寫成：if(word.find(p) != string::npos)
            if(word.find(p) < word.size()) {
                ++c;
            }
        }

        return c;
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