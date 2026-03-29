---
title: "LeetCode 2839. Check if Strings Can be Made Equal With Operations I"
date: 2026-03-29T13:30:00+08:00
lastmod: 2026-03-29T13:30:00+08:00
difficulty: 1285
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "simulation"]
keywords: ["LC2839", "1285", "Easy", "String Operations", "Simulation"]
description: "LeetCode 第 2839 題：Check if Strings Can be Made Equal With Operations I。難度評分：1285。探討在固定字串長度為 4 的限制下，如何透過窮舉所有可能的交換狀態，以 O(1) 時間複雜度判定兩字串是否能透過特定操作變得相等。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2839](https://leetcode.com/problems/check-if-strings-can-be-made-equal-with-operations-i/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1285)</span>  
> **核心主題**：`String` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個長度皆為 4 的字串 `s1` 和 `s2`，字串僅包含小寫英文字母。
你可以對 `s1` 執行以下操作任意次數：
- 選擇兩個索引 `i` 和 `j`，使得 `j - i = 2`，然後交換 `s1[i]` 和 `s1[j]` 的字元。

如果可以透過上述操作讓 `s1` 等於 `s2`，請回傳 `true`，否則回傳 `false`。

### 限制條件
- `s1.length == s2.length == 4`
- `s1` 和 `s2` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-29：初次提交 (全狀態窮舉模擬)](#2026-03-29-初次提交)

---

## 💡 2026-03-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的關鍵在於字串長度被嚴格限制為 4。
根據題目允許的操作 `j - i = 2`，我們只能將索引 0 與索引 2 的字元互換，或是將索引 1 與索引 3 的字元互換。
這意味著字元的交換是獨立的：
- 偶數索引 `(0, 2)` 之間有 2 種排列可能（交換或不交換）。
- 奇數索引 `(1, 3)` 之間也有 2 種排列可能（交換或不交換）。
因此，`s1` 經過任意次數操作後，最多只能產生 $2 \times 2 = 4$ 種不同的字串狀態。我們只需窮舉這 4 種狀態並與 `s2` 進行比對，即可在常數時間內得出正確答案。

### 🛠️ 解題思路 (Approach)
1. **初始狀態檢查**：首先檢查未作任何修改的 `s1` 是否等於 `s2`。若是，回傳 `true`。
2. **狀態一 (交換偶數索引)**：執行 `swap(s1[0], s1[2])`。此時字串狀態為「僅偶數索引交換」。比對是否等於 `s2`。
3. **狀態二 (交換奇數與偶數索引)**：接著執行 `swap(s1[1], s1[3])`。此時字串狀態為「偶數與奇數索引皆已交換」。比對是否等於 `s2`。
4. **狀態三 (僅交換奇數索引)**：再次執行 `swap(s1[0], s1[2])` 將偶數索引復原。此時字串狀態為「僅奇數索引交換」。比對是否等於 `s2`。
5. **回傳結果**：若歷經所有可能的 4 種狀態皆無法與 `s2` 匹配，代表無法透過合法操作達成相等，回傳 `false`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。字串長度固定為 4，最多只執行 3 次交換與 4 次字串比對，執行時間為極小的常數。
- **空間複雜度**: $\mathcal{O}(1)$。直接在原字串 `s1` 上進行原地 (In-place) 交換，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canBeEqual(string s1, string s2) {
        // 狀態 0: 原字串
        if(s1 == s2) return true;
        
        // 狀態 1: 僅交換 (0, 2)
        swap(s1[0], s1[2]);
        if(s1 == s2) return true;
        
        // 狀態 2: 同時交換 (0, 2) 與 (1, 3)
        swap(s1[1], s1[3]);
        if(s1 == s2) return true;
        
        // 狀態 3: 將 (0, 2) 復原，僅保留交換 (1, 3)
        swap(s1[0], s1[2]);
        if(s1 == s2) return true;
        
        // 窮舉完所有可能狀態皆不相等
        return false;
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