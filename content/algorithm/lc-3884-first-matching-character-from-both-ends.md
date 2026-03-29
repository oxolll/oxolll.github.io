---
title: "LeetCode 3884. First Matching Character From Both Ends"
date: 2026-03-29T13:10:00+08:00
lastmod: 2026-03-29T13:10:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "two pointers"]
keywords: ["LC3884", "Easy", "N/A", "Matching Character", "Two Pointers"]
description: "LeetCode 第 3884 題：First Matching Character From Both Ends。難度評分：Easy (N/A)。探討如何透過首尾雙指標向內聚攏，以 O(N) 時間複雜度與 O(1) 空間快速尋找第一組對稱相等的字元。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3884](https://leetcode.com/problems/first-matching-character-from-both-ends/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Two Pointers`

---

## 📝 歷次打卡與更新
- [2026-03-29：初次提交 (雙指標對稱比對)](#2026-03-29-初次提交)

---

## 💡 2026-03-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求尋找從字串兩端開始向內，第一組完全相等的對稱字元索引。
這是一道基礎的雙指標 (Two Pointers) 題型，只需要一個迴圈從字串的頭部向中間走訪，同時利用 `n - i - 1` 的索引映射去比對尾部的對應字元。一旦發現字元相等，立即回傳當前的索引即可。

### 🛠️ 解題思路 (Approach)
1. 取得字串長度 `n`。
2. 建立迴圈，索引 `i` 從 0 走訪至 `(n + 1) / 2`。
3. 檢查頭部字元 `s[i]` 是否等於尾部對稱字元 `s[n - i - 1]`。
4. 若相等，立即回傳索引 `i`。
5. 若走訪至字串中間仍未發現相等字元，代表不存在符合條件的配對，回傳 `-1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串的長度。最差情況下需要走訪半個字串，時間複雜度為線性。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用常數變數進行索引操作。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int firstMatchingIndex(string s) {
        int n = s.size();
        
        // 從外向內走訪至字串中點
        for(int i = 0; i < (n + 1) / 2; ++i) {
            // 檢查是否對稱相等
            if(s[i] == s[n - i - 1]) {
                return i;
            }
        }
        
        return -1;
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