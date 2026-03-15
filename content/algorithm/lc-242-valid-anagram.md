---
title: "LeetCode 242. Valid Anagram"
date: 2026-03-16T00:16:00+08:00
lastmod: 2026-03-16T00:16:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Hash Table", "Sorting"]
keywords: ["LC242", "N/A", "Easy", "Valid Anagram", "Frequency Array", "Counting"]
description: "LeetCode 第 242 題：Valid Anagram。難度評分：N/A (經典面試題)。探討如何利用固定大小的陣列進行字元頻率統計，以 O(N) 時間與 O(1) 空間高效判斷兩個字串是否為字母異位詞。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 242](https://leetcode.com/problems/valid-anagram/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(N/A)</span>    
> **核心主題**：`String` $\cdot$ `Hash Table` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個字串 `s` 和 `t`，如果 `t` 是 `s` 的字母異位詞（Anagram），請回傳 `true`；否則回傳 `false`。

**字母異位詞** 是指由重新排列不同單字或片語的字母而形成的單字或片語，通常只使用所有原始字母恰好一次。

### 範例
- **Input**: `s = "anagram"`, `t = "nagaram"`
  **Output**: `true`

- **Input**: `s = "rat"`, `t = "car"`
  **Output**: `false`

### 限制條件
- $1 \le s.length, t.length \le 5 \times 10^4$
- `s` 和 `t` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-16：初次提交 (固定陣列頻率統計法)](#2026-03-16-初次提交)

---

## 💡 2026-03-16 初次提交

### 🎯 直覺 (Intuition)
要判斷兩個字串是否為字母異位詞，最直觀的定義就是「兩個字串中，每個字母出現的次數必須完全一模一樣」。
因為題目限制了只會出現小寫英文字母，字母的種類最多只有 26 種。與其使用較肥重的 Hash Map (`unordered_map`) 或是對兩個字串進行排序（需花費 $\mathcal{O}(N \log N)$），我們可以直接開一個長度為 26 的整數陣列來充當計數器。
將第一個字串的字母當作「存入」，第二個字串的字母當作「提領」。如果最後陣列裡的每一個元素都剛好歸零，就代表這兩個字串的字母組成完全相同。

### 🛠️ 解題思路 (Approach)
1. **長度快篩 (Early Exit)**：如果 `s` 和 `t` 的長度不相等，那它們絕對不可能是字母異位詞，可以直接回傳 `false`。
2. **頻率統計**：
   - 宣告一個大小為 26，初始值全為 0 的陣列 `cnt`。
   - 遍歷字串 `s`，將對應的字母計數加一 (`++cnt[c - 'a']`)。
   - 遍歷字串 `t`，將對應的字母計數減一 (`--cnt[c - 'a']`)。
3. **結算驗證**：
   - 遍歷 `cnt` 陣列。只要發現任何一個元素不為 `0`，代表某個字母在兩邊的數量不對等，回傳 `false`。
   - 若全部通過檢查，回傳 `true`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$，其中 $N$ 是字串的長度。遍歷了字串各一次，最後再遍歷一次長度常數為 26 的陣列，整體為線性時間。
- **空間複雜度**: $\mathcal{O}(1)$。無論字串多長，我們始終只宣告了一個大小為 26 的整數陣列，空間開銷為常數級別。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        // 優化：長度不同直接淘汰，連迴圈都不用進
        if (s.size() != t.size()) return false;
        
        vector<int> cnt(26, 0);
        
        // s 負責增加計數
        for(auto c : s) {
            ++cnt[c - 'a'];
        }
        
        // t 負責減少計數
        for(auto c : t) {
             --cnt[c - 'a'];
        }
        
        // 檢查是否完美抵銷
        for(auto c : cnt) {
            if(c != 0) return false;
        }
            
        return true;
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