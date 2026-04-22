---
title: "LeetCode 2452. Words Within Two Edits of Dictionary"
date: 2026-04-22T14:45:00+08:00
lastmod: 2026-04-22T14:45:00+08:00
difficulty: 1459
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "string", "brute-force"]
keywords: ["LC2452", "1459", "Medium", "Words Within Two Edits", "String Matching"]
description: "LeetCode 第 2452 題：Words Within Two Edits of Dictionary。難度評分：1459。探討如何利用暴力搜尋法搭配容錯計數器，並結合提早剪枝 (Early Exit) 優化，以極低的常數時間找出編輯距離在 2 以內的字串。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2452](https://leetcode.com/problems/words-within-two-edits-of-dictionary/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1459)</span>  
> **核心主題**：`Array` $\cdot$ `String`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個字串陣列 `queries` 和 `dictionary`。所有字串的長度都相同。
對於每一次查詢 `queries[i]`，你可以嘗試對字串進行**最多兩次**的字元編輯（編輯定義為：將字串中的某個字元替換成任意其他英文字母）。
如果經過最多兩次編輯後，`queries[i]` 能夠與 `dictionary` 中的**任意一個**字串完全相同，我們就稱這個查詢是成功的。
請回傳所有查詢成功的 `queries[i]`。回傳結果的順序必須與原陣列保持一致。

### 限制條件
- $1 \le queries.length, dictionary.length \le 100$
- $1 \le queries[i].length == dictionary[j].length \le 100$
- 字串僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-22：初次提交 (暴力搜尋與提早剪枝)](#2026-04-22-初次提交)

---

## 💡 2026-04-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道標準的字串比對題。觀察測資限制，$Q \le 100, D \le 100, L \le 100$，最差情況下的運算次數約為 $100 \times 100 \times 100 = 10^6$，這個量級在 C++ 中可以輕鬆透過暴力搜尋法 (Brute Force) 解決。
針對暴力比對，我們可以加入一個核心的**提早剪枝 (Early Exit)** 優化：
因為題目要求「最多只能編輯 2 次」，所以我們在逐字元比對時，只要發現不同的字元數量超過 2，就立刻中斷當前字串的比對，跳到下一個字典單字。這能大幅降低實際執行的常數時間。

### 🛠️ 解題思路 (Approach)
1. **走訪查詢字串**：使用外層迴圈逐一取出 `queries` 中的每個字串 `q`。
2. **走訪字典字串**：對於每個 `q`，使用中層迴圈逐一取出 `dictionary` 中的每個字串 `d` 進行比對。
3. **容錯比對與剪枝**：
   - 初始化容錯計數器 `edit = 2`。
   - 使用內層迴圈逐一比對字元 `q[i]` 與 `d[i]`。
   - **剪枝條件**：將 `edit >= 0` 寫入 `for` 迴圈的執行條件中。只要 `q[i] != d[i]`，就將 `edit` 減 1；一旦 `edit` 變成負數，迴圈會立刻中斷，不再比對後續字元。
4. **判斷是否匹配成功**：
   - 如果內層迴圈結束（或是被剪枝中斷）後，`edit >= 0`，代表兩者差異在 2 個字元以內，匹配成功！
   - 將字串 `q` 推入結果陣列 `rt`，並使用 `break` 中斷中層迴圈（因為只要找到一個字典字串匹配即可，不需繼續找剩下的）。
5. **回傳結果**：走訪完所有查詢後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(Q \times D \times L)$。其中 $Q$ 是 `queries` 的長度，$D$ 是 `dictionary` 的長度，$L$ 是字串長度。因提早剪枝的存在，實際執行時間遠小於理論上限。
- **空間複雜度**: $\mathcal{O}(1)$。除儲存答案的陣列外，僅使用常數個變數進行計數與索引。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<string> twoEditWords(vector<string>& queries, vector<string>& dictionary) {
        vector<string> rt;
        
        for(auto& q : queries) {
            int edit;
            
            // 嘗試與字典中的每一個單字進行比對
            for(auto& d : dictionary) {
                edit = 2; // 最多允許 2 次修改
                
                // 逐字元比對，並加入 edit >= 0 的提早剪枝 (Early Exit)
                // 只要差異超過 2 次，立刻中斷比對
                for(int i = 0; i < d.size() && edit >= 0; ++i) {
                    if(q[i] != d[i]) {
                        --edit;
                    }
                }

                // 若比對結束且 edit 未低於 0，代表匹配成功
                if(edit >= 0) {
                    rt.push_back(q);
                    break; // 只要找到一個匹配的字典字串即可，跳出尋找下一個 query
                }
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