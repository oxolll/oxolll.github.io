---
title: "LeetCode 3612. Process String with Special Operations I"
date: 2026-06-16T13:01:00+08:00
lastmod: 2026-06-16T13:01:00+08:00
difficulty: 1185
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "simulation"]
keywords: ["LC3612", "Medium", "1185", "Process String", "字串處理", "模擬", "流程控制"]
description: "LeetCode 第 3612 題：Process String with Special Operations I。難度評分：1185。探討如何利用清晰的流程控制 (Switch Case) 與邊界防禦，安全且優雅地實作字串的刪除、複製與反轉等特殊運算模擬。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3612](https://leetcode.com/problems/process-string-with-special-operations-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1185)</span>  
> **核心主題**：`String` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串 `s`，其中包含小寫英文字母以及一些「特殊字元」。
你需要從左到右掃描並處理這個字串，規則如下：
1. **小寫字母 (`a` - `z`)**：直接將其附加到結果字串的尾端。
2. **星號 (`*`)**：刪除結果字串的最後一個字元（若結果字串為空，則忽略此操作）。
3. **井號 (`#`)**：將目前的結果字串複製一次並拼接在自己後方（即長度翻倍）。
4. **其他特殊字元 (預設)**：將目前的結果字串整個反轉 (Reverse)。

請你回傳經過所有操作處理後的最終字串。

### 限制條件
*(依據 Rating 推斷)*
- $1 \le s.length \le 10^5$
- 字串操作產生的最大長度保證不會超過記憶體限制 (MLE)。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-16：初次提交 (Switch-Case 流程控制模擬)](#2026-06-16-初次提交)

---

## 💡 2026-06-16 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的字串模擬題。主要考驗程式碼的組織能力與防禦性編程 (Defensive Programming)。
由於特殊字元會對「目前的結果字串」進行整體或尾端的操作，最直觀的資料結構就是直接使用一個動態字串 (`std::string`) 作為累積器 (`Accumulator`)，並利用內建函數來達成需求。

**核心破局點：清晰的邏輯分流**
1. 將常規字母與特殊操作脫鉤：遇到小寫字母直接 `push_back` 並 `continue`。
2. 利用 `switch-case` 處理特殊字元，提高可讀性與執行效率。
3. **極端狀況防禦**：當遇到刪除指令 `*` 時，結果字串可能已經是空的。強制執行 `pop_back()` 會導致未定義行為 (Undefined Behavior) 或崩潰，因此必須加上 `!rt.empty()` 的防禦機制。

### 🛠️ 解題思路 (Approach)
1. 宣告累積字串 `rt = ""`。
2. 遍歷原字串 `s` 的每一個字元 `c`。
3. 若 `c` 為小寫字母 `a-z`，直接附加到 `rt` 尾端，並進入下一輪迴圈。
4. 若不是小寫字母，進入 `switch` 判斷：
   - `case '*'`：若 `rt` 不為空，則移除最後一個字元。
   - `case '#'`：執行 `rt += rt` 讓字串翻倍。
   - `default`：遇到未定義的其他特殊字元，呼叫 `std::reverse(rt.begin(), rt.end())` 將當前字串反轉。
5. 掃描結束後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times L)$。其中 $N$ 是字串長度，$L$ 是動態字串 `rt` 在操作過程中的最大長度。字母附加和 `pop_back` 皆為 $\mathcal{O}(1)$；但字串翻倍 (`#`) 與反轉 (`reverse`) 皆需花費與當前字串長度成正比的 $\mathcal{O}(L)$ 時間。
- **空間複雜度**: $\mathcal{O}(L)$。宣告了字串 `rt` 儲存結果，記憶體消耗取決於操作後字串的最大長度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string processStr(string s) {
        string rt = "";
        int n = s.size();
        
        for(int i = 0; i < n; ++i) {
            char c = s[i];
            
            // 1. 常規字母處理：提早結束當前回合，邏輯清晰
            if('a' <= c && c <= 'z') {
                rt += c;
                continue;
            }

            // 2. 特殊操作分流
            switch(c) {
                case '*':
                    // 邊界防禦：確保字串不為空才進行刪除
                    if(!rt.empty()) {
                        rt.pop_back();
                    }
                    break;
                    
                case '#':
                    // 複製字串並拼接
                    rt += rt;
                    break;
                    
                default:
                    // 其他特殊字元：將當前結果反轉
                    reverse(rt.begin(), rt.end());
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