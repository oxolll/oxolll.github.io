---
title: "LeetCode 3120. Count the Number of Special Characters I"
date: 2026-05-26T13:40:00+08:00
lastmod: 2026-05-26T13:40:00+08:00
difficulty: 1205
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table", "bit-manipulation"]
keywords: ["LC3120", "Easy", "1205", "Special Characters", "字串處理", "位元運算", "狀態壓縮"]
description: "LeetCode 第 3120 題：Count the Number of Special Characters I。難度評分：1205。探討如何利用大小為 26 的陣列結合位元運算 (Bitmask)，以極低的記憶體與時間開銷，優雅判斷大小寫字母是否同時存在。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3120](https://leetcode.com/problems/count-the-number-of-special-characters-i/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1205)</span>  
> **核心主題**：`Hash Table` $\cdot$ `String` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串 `word`。如果某個字母在 `word` 中 **同時** 出現了小寫形式和大寫形式，我們就稱這個字母為 **特殊字母 (Special Character)**。

請你回傳 `word` 中特殊字母的總數量。

### 範例
- **Input**: `word = "aaAbcBC"`
- **Output**: `3`
- **Explanation**: 
  - `'a'` 的小寫 `'a'` 和大寫 `'A'` 都出現了。
  - `'b'` 的小寫 `'b'` 和大寫 `'B'` 都出現了。
  - `'c'` 的小寫 `'c'` 和大寫 `'C'` 都出現了。
  總共有 3 個特殊字母。

- **Input**: `word = "abc"`
- **Output**: `0`
- **Explanation**: 沒有任何字母同時以大小寫形式出現。

- **Input**: `word = "abBCab"`
- **Output**: `1`
- **Explanation**: 只有 `'b'` 同時出現了小寫和大寫形式。

### 限制條件
- $1 \le word.length \le 50$
- `word` 僅由小寫和大寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-26：初次提交 (位元狀態壓縮計數法)](#2026-05-26-初次提交)

---

## 💡 2026-05-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標是找出同時擁有大小寫的英文字母。由於英文字母不區分大小寫只有 26 個，我們可以使用一個長度為 26 的陣列來記錄每個字母的出現狀態。

**核心破局點：位元運算狀態壓縮 (Bitmask State Compression)**
為了解決「大小寫是否同時出現」的問題，我們不需要使用複雜的資料結構，只需賦予大寫和小寫不同的「位元權重」：
- **大寫字母出現**：我們給予狀態值 `1` (二進位標示為 `01`)。
- **小寫字母出現**：我們給予狀態值 `2` (二進位標示為 `10`)。

當我們遍歷字串時，使用位元或運算 `|=` 疊加狀態：
- 如果只有大寫，狀態是 `1`。
- 如果只有小寫，狀態是 `2`。
- 如果大寫跟小寫都有出現，`1 | 2` 的結果恰好會是 `3` (二進位標示為 `11`)。

最後，只要清點陣列中有多少個坑位的值為 `3`，即可精準得出「特殊字母」的數量。

### 🛠️ 解題思路 (Approach)
1. **初始化狀態陣列**：宣告一個長度為 26、初始值皆為 0 的整數陣列 `cnt`。
2. **遍歷字串進行標記**：
   - 取出字元 `c`，判斷其為大寫或小寫（可使用 `isupper(c)`）。
   - 若為大寫，算出它在 0~25 的索引（`c - 'A'`），並將該位置的值 `|= 1`。
   - 若為小寫，算出它在 0~25 的索引（`c - 'a'`），並將該位置的值 `|= 2`。
3. **統計結果**：遍歷 `cnt` 陣列，計算值等於 `3` 的元素數量，並存入 `rt`。
4. **回傳答案**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串的長度。我們只需遍歷字串一次，然後固定遍歷長度為 26 的陣列一次，總操作次數與字串長度成正比。
- **空間複雜度**: $\mathcal{O}(1)$。只依賴一個長度固定為 26 的整數陣列，不受字串長度影響。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numberOfSpecialChars(string word) {
        // 宣告長度為 26 的陣列，用來記錄 a-z 的出現狀態
        vector<int> cnt(26, 0);
        
        for(auto c : word) {
            if(isupper(c)) {
                // 如果是大寫，賦予二進位的第一個 bit (數值 1)
                cnt[c - 'A'] |= 1;
            } else {
                // 如果是小寫，賦予二進位的第二個 bit (數值 2)
                cnt[c - 'a'] |= 2;
            }
        }

        int rt = 0;
        // 遍歷 26 個字母的狀態
        for(int i = 0; i < 26; ++i) {
            // 當狀態值為 3 時，代表 1 (大寫) 和 2 (小寫) 的 bit 都被點亮了
            if(cnt[i] == 3) {
                ++rt;
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