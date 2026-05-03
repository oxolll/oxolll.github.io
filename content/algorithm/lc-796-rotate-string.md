---
title: "LeetCode 796. Rotate String"
date: 2026-05-03T13:10:00+08:00
lastmod: 2026-05-03T13:10:00+08:00
difficulty: 1167
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "string-matching"]
keywords: ["LC796", "Easy", "1167", "Rotate String", "字串旋轉", "拼接法"]
description: "LeetCode 第 796 題：Rotate String。難度評分：1167。探討如何利用極度經典的「字串拼接法 (s + s)」，將字串旋轉驗證問題，優雅地轉化為單純的子字串搜尋問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 796](https://leetcode.com/problems/rotate-string/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1167)</span>  
> **核心主題**：`String` $\cdot$ `String Matching`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個字串 `s` 和 `goal`。
如果在對 `s` 進行若干次「旋轉操作」後，`s` 能夠變成 `goal`，請回傳 `true`，否則回傳 `false`。

一次「旋轉操作」定義為：將 `s` 最左邊的字元移動到最右邊。
- 例如，若 `s = "abcde"`，旋轉一次後會變成 `"bcdea"`。

### 範例
- **Input**: `s = "abcde", goal = "cdeab"`
- **Output**: `true`
- **Explanation**: "abcde" -> "bcdea" -> "cdeab" (旋轉兩次)

### 限制條件
- $1 \le s.length, goal.length \le 100$
- `s` 和 `goal` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-03：初次提交 (經典字串拼接法)](#2026-05-03-初次提交)

---

## 💡 2026-05-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道標準的面試熱身題。如果暴力模擬旋轉操作，每次搬移字元並進行比對，程式碼會顯得冗長。
**破局關鍵：**
無論字串 `s` 怎麼旋轉，它的有效旋轉結果必定會被完整包覆在**將自己複製拼接一次的字串 `s + s`** 之中！
例如：`s = "abcde"`，拼接後 `ss = "abcdeabcde"`。
你可以觀察到 `ss` 內部包含了所有旋轉可能：
- `abcde` (轉 0 次)
- `bcdea` (轉 1 次)
- `cdeab` (轉 2 次) ...以此類推。

因此，判斷 `s` 能否旋轉成 `goal`，就等價於判斷 **`goal` 是否為 `s + s` 的子字串 (Substring)**！

### 🛠️ 解題思路 (Approach)
1. **長度防呆**：如果 `s` 和 `goal` 的長度不相等，那絕對不可能透過旋轉得到，直接回傳 `false`。
2. **拼接母字串**：建立一個新字串 `ss = s + s`。
3. **子字串搜尋**：使用 C++ 內建的 `ss.find(goal)` 來尋找 `goal`。
   - 如果有找到，回傳的索引必定小於 $N$ (原字串長度)。
   - 如果沒找到，`find` 會回傳 `string::npos` (一個極大的無號整數)，它轉換比較時會大於 $N$。
   - 因此，直接回傳 `ss.find(goal) < n` 是一個非常精簡且優雅的寫法。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。字串拼接需要 $\mathcal{O}(N)$。字串搜尋 `std::find` 底層通常實作良好，平均時間複雜度為 $\mathcal{O}(N)$ (最壞情況為 $\mathcal{O}(N^2)$，但在 LeetCode 測資中幾乎可視為線性)。
- **空間複雜度**: $\mathcal{O}(N)$。需要額外配置空間來儲存長度為 $2N$ 的拼接字串 `ss`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool rotateString(string s, string goal) {
        int m = s.size(), n = goal.size();
        
        // 1. 長度不同，絕不可能匹配
        if(m != n) return false;

        // 2. 經典拼接技巧：s + s 包含了所有可能的旋轉組合
        string ss = s + s;

        // 3. 判斷 goal 是否為 ss 的子字串
        // 若有找到，索引必定小於 n；若沒找到，會回傳 string::npos (極大值)，判斷式為 false
        return ss.find(goal) < n;
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