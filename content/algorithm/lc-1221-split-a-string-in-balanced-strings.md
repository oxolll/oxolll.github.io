---
title: "LeetCode 1221. Split a String in Balanced Strings"
date: 2026-03-26T15:42:00+08:00
lastmod: 2026-03-26T15:42:00+08:00
difficulty: 1219
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy", "counting"]
keywords: ["LC1221", "1219", "Easy", "Balanced Strings", "Greedy"]
description: "LeetCode 第 1221 題：Split a String in Balanced Strings。難度評分：1219。探討如何利用貪婪演算法與計數器，在 O(N) 時間複雜度內計算字串可被分割為平衡字串的最大數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1221](https://leetcode.com/problems/split-a-string-in-balanced-strings/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1219)</span>  
> **核心主題**：`String` $\cdot$ `Greedy` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
平衡字串是指字元 `'L'` 和 `'R'` 數量相同的字串。
給定一個平衡字串 `s`，請將其分割成盡可能多的子字串，且滿足：
1. 每個子字串都是平衡字串。
2. 分割後的子字串串接起來必須等於原字串 `s`。

請回傳可以分割出的最大平衡字串數量。

### 限制條件
- $2 \le s.length \le 1000$
- `s[i]` 不是 `'L'` 就是 `'R'`。
- `s` 本身保證是一個平衡字串。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (單一計數器貪婪法)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
題目要求將字串分割為「盡可能多」的平衡子字串。此問題適用於貪婪演算法 (Greedy Algorithm)。
當我們從左向右掃描字串時，只要累積的 `'L'` 與 `'R'` 數量達到相等，我們就應立即進行分割。因為任何一個合法的平衡字串，若其內部還包含更小的平衡字串前綴，將其盡早切分必然能增加總分割數量，且不會影響後續字串的平衡狀態（剩餘的後綴依然會保持平衡）。

### 🛠️ 解題思路 (Approach)
1. 宣告計數器 `cnt` 初始化為 0，用於追蹤字元數量的淨值。宣告 `rt` 初始化為 0，用於記錄成功分割的次數。
2. 走訪字串 `s`：
   - 若遇到 `'L'`，計數器 `cnt` 加 1。
   - 若遇到 `'R'`，計數器 `cnt` 減 1。
   - 在每次更新後，檢查 `cnt` 是否為 0。若為 0，代表當前截斷的子字串達到了 `'L'` 與 `'R'` 數量相等，將 `rt` 加 1。
3. 走訪結束後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串 `s` 的長度。僅需對字串進行一次線性走訪。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了 `cnt` 與 `rt` 兩個整數變數，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int balancedStringSplit(string s) {
        int cnt = 0;
        int rt = 0;
        
        for(auto c : s) {
            if(c == 'L') {
                ++cnt;
            } else {
                --cnt;
            }
            
            // 當 L 與 R 數量抵銷為 0 時，進行一次貪婪分割
            if(cnt == 0) {
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