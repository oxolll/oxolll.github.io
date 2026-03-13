---
title: "LeetCode 22. Generate Parentheses"
date: 2026-03-14T01:12:00+08:00
lastmod: 2026-03-14T01:12:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Backtracking"]
keywords: ["LC22", "Medium", "Generate Parentheses", "DFS", "Catalan Number", "Memory Optimization"]
description: "LeetCode 第 22 題：Generate Parentheses。難度評分：N/A (經典面試題)。探討如何透過嚴格條件限制的回溯法 (Backtracking) 高效生成所有合法的括號排列組合，並將常數時間與記憶體優化至極致。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 22](https://leetcode.com/problems/generate-parentheses/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>    
> **核心主題**：`String` $\cdot$ `Backtracking`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數 `n`，代表有 `n` 對括號。請撰寫一個函式，生成並回傳所有可能且**合法 (Well-formed)** 的括號組合。

### 範例
- **Input**: `n = 3`
  **Output**: `["((()))","(()())","(())()","()(())","()()()"]`

- **Input**: `n = 1`
  **Output**: `["()"]`

### 限制條件
- $1 \le n \le 8$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-14：初次提交 (傳參考 Backtracking 與記憶體預分配優化)](#2026-03-14-初次提交)

---

## 💡 2026-03-14 初次提交

### 🎯 直覺 (Intuition)
生成所有可能的組合通常意味著要使用深度優先搜尋 (DFS) 或回溯法 (Backtracking)。
為了保證生成的括號是「合法」的，我們在遞迴過程中不需要盲目地加入字元再事後檢查，而是可以透過追蹤「還剩下幾個左括號」與「還剩下幾個右括號」來嚴格控制分支：
1. **只要還有左括號**，我們隨時可以加入左括號 `(`。
2. **只有當剩餘的右括號數量 > 剩餘的左括號數量時**，我們才可以加入右括號 `)`。這保證了每一個右括號前面必定有一個對應的左括號。

因為這個構造邏輯本身就是互斥且嚴格遞增的，它自然能保證生成出來的結果**必定唯一且不重複**，完全不需要使用 Hash Set 進行去重。

### 🛠️ 解題思路 (Approach)
1. **記憶體預分配優化**：
   - 宣告 `vector<string> rt` 並 `reserve(512)` 減少陣列擴張時的 reallocation。
   - 宣告暫存字串 `tmp` 並 `reserve(2*n)`，保證每次遞迴組合字串時，底層絕對不會發生記憶體重新配置。
2. **回溯法核心 (`dfs` 函式)**：
   - 傳入剩餘的左括號 `l`、右括號 `r`，以及**字串參考 `string& s`**。
   - **終止條件**：當 `l == 0` 且 `r == 0` 時，將當前字串推入結果集 `rt` 並回傳。
   - **分支一 (加左括號)**：如果 `l > 0`，我們執行 `s.push_back('(')`，向下遞迴 `dfs(l-1, r, s)`，出來後執行 `s.pop_back()` 撤銷選擇 (Backtrack)。
   - **分支二 (加右括號)**：如果 `r > l` (確保合法閉合)，我們執行 `s.push_back(')')`，向下遞迴 `dfs(l, r-1, s)`，出來後同樣執行 `s.pop_back()` 撤銷選擇。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}\left(\frac{4^n}{\sqrt{n}}\right)$。這是著名的**卡塔蘭數 (Catalan Number)**。由於我們嚴格只生成合法的組合，並且使用了傳參考與 `push/pop` 操作，時間複雜度嚴格貼合合法解的數量，且常數極低。
- **空間複雜度**: $\mathcal{O}(N)$。遞迴堆疊的最大深度為 $2n$，字串 `s` 的最大長度也為 $2n$。除了儲存結果的陣列外，額外空間開銷為線性級別。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<string> rt;
    int N;
    
    vector<string> generateParenthesis(int n) {
        N = n;
        // 預分配空間，減少動態擴容開銷
        rt.reserve(512); 
        string tmp = ""; 
        tmp.reserve(2 * n); // 每個合法字串的長度固定為 2*n
        
        dfs(n, n, tmp);
        return rt;
    }
    
private:
    void dfs(int l, int r, string& s) {
        // 終止條件：括號全部用完
        if(l == 0 && r == 0) {
            rt.push_back(s);
            return;
        }

        // 還有左括號可用
        if(l) {
            s.push_back('(');
            dfs(l - 1, r, s);
            s.pop_back(); // 回溯 (Backtrack)
        }

        // 剩餘的右括號比左括號多，才能確保閉合合法
        if(r > l && r > 0) {
            s.push_back(')');
            dfs(l, r - 1, s);
            s.pop_back(); // 回溯 (Backtrack)
        }
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