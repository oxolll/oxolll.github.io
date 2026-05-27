---
title: "LeetCode 3121. Count the Number of Special Characters II"
date: 2026-05-27T13:00:00+08:00
lastmod: 2026-05-27T13:00:00+08:00
difficulty: 1411
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table"]
keywords: ["LC3121", "Medium", "1411", "Special Characters II", "字串處理", "位置追蹤", "邊界檢查"]
description: "LeetCode 第 3121 題：Count the Number of Special Characters II。難度評分：1411。探討如何將『所有小寫必須在大寫之前』的字串順序問題，巧妙轉化為『最後一個小寫位置與第一個大寫位置』的簡單大小比較，實現極速 O(N) 線性掃描。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3121](https://leetcode.com/problems/count-the-number-of-special-characters-ii/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1411)</span>  
> **核心主題**：`Hash Table` $\cdot$ `String`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串 `word`。如果某個字母在 `word` 中 **同時** 出現了小寫形式和大寫形式，並且該字母的 **所有小寫形式** 都出現在該字母的 **第一個大寫形式** 之前，我們就稱這個字母為 **特殊字母 (Special Character)**。

請你回傳 `word` 中特殊字母的總數量。

### 範例
- **Input**: `word = "aaAbcBC"`
- **Output**: `3`
- **Explanation**: 
  - `'a'` 的小寫均在第一個 `'A'` 之前。
  - `'b'` 的小寫均在第一個 `'B'` 之前。
  - `'c'` 的小寫均在第一個 `'C'` 之前。
  符合條件的字母有 3 個。

- **Input**: `word = "abc"`
- **Output**: `0`
- **Explanation**: 沒有任何字母同時以大小寫形式出現。

- **Input**: `word = "AbCBc"`
- **Output**: `0`
- **Explanation**: 
  - `'b'` 的大寫 `'B'` 出現在小寫 `'b'` 之前，不符合條件。
  - `'c'` 的大寫 `'C'` 出現在小寫 `'c'` 之前，不符合條件。

### 限制條件
- $1 \le word.length \le 2 \times 10^5$
- `word` 僅由小寫和大寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-27：初次提交 (首尾位置極值比較法)](#2026-05-27-初次提交)

---

## 💡 2026-05-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
與第一集 (LC 3120) 只需要判斷「有沒有出現」不同，這題加上了嚴格的「先後順序」限制。

如果我們用人類直覺去模擬，可能會在遇到大寫後，設定一個標記，如果之後又遇到小寫就取消資格。但更具數學直覺的**極值思維 (Extremum Thinking)** 可以讓代碼變得極度純粹：
要保證「所有」小寫都在「第一個」大寫之前，我們只需要關心兩個極端位置：
1. **最後一個小寫字母的位置** (Last occurrence of lowercase)
2. **第一個大寫字母的位置** (First occurrence of uppercase)

只要滿足 $\text{Last Lowercase Index} < \text{First Uppercase Index}$，就能從邏輯上保證該字母的所有小寫一定都在所有大寫之前。

### 🛠️ 解題思路 (Approach)
1. **初始化位置陣列**：
   宣告兩個長度為 26 的整數陣列 `firstU` 和 `l`，並將所有初始值設為 `-1`（代表尚未出現）。
2. **單次線性遍歷**：
   掃描字串 `word`，利用索引 `i`：
   - **遇到大寫字母 (`isupper`)**：如果 `firstU` 中該字母的位置還是 `-1`，代表這是**第一次**遇到，將其位置記錄為 `i`。如果不是 `-1` 就忽略，藉此鎖定「首次位置」。
   - **遇到小寫字母 (`islower`)**：直接將 `l` 中該字母的位置更新為 `i`。透過不斷覆寫，迴圈結束時自然會留下「最後一次出現的位置」。
3. **驗證與計數**：
   遍歷 a-z 這 26 個字母：
   - 檢查是否同時存在（即 `firstU[i] >= 0` 且 `l[i] >= 0`）。
   - 驗證相對位置是否合法：第一個大寫的索引必須大於最後一個小寫的索引 (`firstU[i] > l[i]`)。
   - 若皆滿足，計數器 `c` 加 1。
4. **回傳結果**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串的長度。我們進行了一趟 $\mathcal{O}(N)$ 的字串掃描，以及一趟 $\mathcal{O}(1)$ 的常數次數 (26次) 陣列比對，整體時間極為高效。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了兩個大小固定為 26 的輔助陣列，記憶體開銷與輸入字串長度無關。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numberOfSpecialChars(string word) {
        // firstU 記錄每個字母的「第一個」大寫位置
        vector<int> firstU(26, -1);
        // l 記錄每個字母的「最後一個」小寫位置
        vector<int> l(26, -1);
        
        for(int i = 0; i < word.size(); ++i) {
            if(isupper(word[i])) {
                // 如果是第一次遇到該大寫字母，才記錄其位置
                if(firstU[word[i] - 'A'] == -1) {
                    firstU[word[i] - 'A'] = i;
                }
            } else {
                // 不斷覆寫小寫字母的位置，最終會留下最後一次出現的索引
                l[word[i] - 'a'] = i;
            }
        }

        int c = 0;
        for(int i = 0; i < 26; ++i) {
            // 條件 1：大寫和小寫都必須出現過 (>= 0)
            // 條件 2：大寫的第一次出現，必須在小寫的最後一次出現之後
            if(firstU[i] >= 0 && l[i] >= 0 && firstU[i] > l[i]) {
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