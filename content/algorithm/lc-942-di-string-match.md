---
title: "LeetCode 942. DI String Match"
date: 2026-03-27T13:10:00+08:00
lastmod: 2026-03-27T13:10:00+08:00
difficulty: 1444
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "greedy", "two pointers"]
keywords: ["LC942", "1444", "Easy", "DI String Match", "Greedy", "Two Pointers"]
description: "LeetCode 第 942 題：DI String Match。難度評分：1444。探討如何利用雙指標與貪婪策略，以 O(N) 時間複雜度構造滿足給定遞增與遞減規則的排列數列。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 942](https://leetcode.com/problems/di-string-match/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1444)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Greedy` $\cdot$ `Two Pointers`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
由範圍 `[0, n]` 內的所有整數組成的排列 `perm` 可以表示為長度為 `n` 的字串 `s`，其中：
- 若 `perm[i] < perm[i + 1]`，則 `s[i] == 'I'`
- 若 `perm[i] > perm[i + 1]`，則 `s[i] == 'D'`

給定一個字串 `s`，請重新構造並回傳任何滿足上述條件的排列 `perm`。

### 限制條件
- $1 \le s.length \le 10^5$
- `s[i]` 僅包含字元 `'I'` 或 `'D'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (雙指標貪婪構造法)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
構造符合特定升降關係的數列時，利用貪婪策略 (Greedy Strategy) 搭配雙指標是最直接有效的方法。
由於我們擁有的數字範圍是固定的連續整數 `[0, n]`：
- 當遇到 `'I'` (遞增) 時，為了保證下一個數字絕對大於當前數字，我們應將「目前可用的最小數字」放入排列中。
- 當遇到 `'D'` (遞減) 時，為了保證下一個數字絕對小於當前數字，我們應將「目前可用的最大數字」放入排列中。

透過維護可用數字的上下界，每次操作後縮小範圍，即可無衝突地構造出合法數列。

### 🛠️ 解題思路 (Approach)
1. 取得字串長度 `n`。宣告結果陣列 `rt` 並預先配置 `n + 1` 的空間 (`rt.reserve(n)` 實際上可優化為 `reserve(n + 1)`)。
2. 宣告雙指標：左指標 `l = 0` 代表當前可用最小值，右指標 `r = n` 代表當前可用最大值。
3. 走訪字串 `s`：
   - 若字元為 `'I'`，將 `l` 推入結果陣列，並將 `l` 遞增 (`l++`)。
   - 若字元為 `'D'`，將 `r` 推入結果陣列，並將 `r` 遞減 (`r--`)。
4. 字串走訪結束後，`l` 與 `r` 必然相等，將最後剩餘的這個數字推入結果陣列中。
5. 回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串的長度。僅需進行一次線性走訪，操作皆為常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。除儲存結果的回傳陣列外，僅使用了兩個整數指標。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> diStringMatch(string s) {
        int n = s.size();
        vector<int> rt; 
        rt.reserve(n + 1); // 預先配置 n+1 的空間以防重新分配
        
        int l = 0, r = n;
        
        for(auto c : s) {
            if(c == 'I') {
                rt.push_back(l++); // 遇到遞增，放入當前最小值
            } else {
                rt.push_back(r--); // 遇到遞減，放入當前最大值
            }
        }
        
        // 放入最後剩餘的唯一數字
        rt.push_back(l);
        
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