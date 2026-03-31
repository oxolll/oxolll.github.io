---
title: "LeetCode 3474. Lexicographically Smallest Generated String"
date: 2026-03-31T20:10:00+08:00
lastmod: 2026-03-31T20:10:00+08:00
difficulty: 2605
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy"]
keywords: ["LC3474", "2605", "Hard", "Lexicographically Smallest", "Generated String", "Greedy"]
description: "LeetCode 第 3474 題：Lexicographically Smallest Generated String。難度評分：2605。探討如何利用貪婪策略，分階段處理字串的強制匹配與互斥條件，並透過延遲較大字元的填充位置，以 O(N * M) 時間複雜度構造出字典序最小的合法字串。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3474](https://leetcode.com/problems/lexicographically-smallest-generated-string/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2605)</span>  
> **核心主題**：`String` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個字串 `str1` 和 `str2`，長度分別為 $n$ 和 $m$。
請構造一個長度為 $n + m - 1$ 的字串 `rt`，使得它滿足以下條件：
- 對於 $0 \le i < n$ 的每一個索引 `i`：
  - 如果 `str1[i] == 'T'`，則 `rt` 從索引 `i` 開始長度為 $m$ 的子字串必須**等於** `str2`。
  - 如果 `str1[i] == 'F'`，則 `rt` 從索引 `i` 開始長度為 $m$ 的子字串必須**不等於** `str2`。

如果存在多個合法的字串，請回傳其中**字典序最小 (Lexicographically Smallest)** 的字串。
如果不存在任何合法的字串，請回傳空字串 `""`。

### 限制條件
- $1 \le str1.length \le 10^4$
- $1 \le str2.length \le 500$
- `str1` 僅包含字元 `'T'` 和 `'F'`。
- `str2` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-31：初次提交 (貪婪分段構造法)](#2026-03-31-初次提交)

---

## 💡 2026-03-31 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
構造字典序最小的字串且帶有嚴格限制時，必須遵循「先滿足強制條件，再貪婪填補剩餘空間」的原則。
演算法分為三個階段：
1. **處理強制匹配 (`T`)**：遍歷 `str1`，遇到 `'T'` 時，將 `str2` 覆寫到目標字串 `rt` 的對應位置。若發現該位置已被其他強制匹配寫入了不同的字元，代表條件互斥，直接判定無解。
2. **處理強制不匹配 (`F`)**：遍歷 `str1`，遇到 `'F'` 時，必須確保對應的子字串不等於 `str2`。
   - 為了保持字典序最小，我們優先檢查是否能填入 `'a'` 來製造不匹配。若 `str2` 在該位置不為 `'a'`，我們填入 `'a'` 即可完美達成目標。
   - 若已存在的字元本身就與 `str2` 不匹配，則該條件已自動滿足。
   - 最棘手的情況是：目前填入的字元全等於 `str2`，且所有空格對應到 `str2` 的字元剛好都是 `'a'`。此時，我們被迫必須將某個空格填入 `'b'` 來破壞匹配。
   - **貪婪決策**：為了讓字典序的增量最小化，這個被迫填入的 `'b'` 必須放置在**盡可能靠右**的可用空格。因此我們需記錄最後一個可用空格的索引 `last`。
3. **填補剩餘空格**：歷經上述兩階段後，所有未被指定的字元（仍為空格）皆可安全地填入 `'a'`，以確保整體字典序最小。

### 🛠️ 解題思路 (Approach)
1. 宣告長度為 $n + m - 1$ 的字串 `rt`，並初始化為空白字元 `' '`。
2. **階段一：執行強制匹配**
   - 走訪 `str1`，若 `str1[i] == 'T'`，建立內層迴圈 `j` 遍歷 `str2`。
   - 若 `rt[i+j]` 不為空且與 `str2[j]` 不相等，回傳 `""`。否則，寫入 `rt[i+j] = str2[j]`。
3. **階段二：執行強制不匹配**
   - 走訪 `str1`，若 `str1[i] == 'F'`，設定 `last = -1` 與 `needed = true`。
   - 建立內層迴圈 `j` 遍歷 `str2`：
     - 若 `rt[i+j]` 為空：檢查 `str2[j]` 是否不為 `'a'`。若不為 `'a'`，我們大膽填入 `'a'` 製造不匹配，設定 `needed = false` 並 `break`。若為 `'a'`，記錄此空格位置 `last = j`。
     - 若 `rt[i+j]` 不為空：檢查是否已與 `str2[j]` 不相等，若不相等，設定 `needed = false` 並 `break`。
   - 內層迴圈結束後，若 `needed` 仍為 `true`：
     - 若 `last == -1`，代表沒有任何空格可供修改，且既有字元已完全匹配 `str2`，發生矛盾，回傳 `""`。
     - 若 `last != -1`，將最右側的可用空格修改為 `'b'` (因 `str2[last]` 必為 `'a'`)，即 `rt[i + last] = 'b'`。
4. **階段三：填補未定義字元**
   - 遍歷字串 `rt`，將所有剩餘的 `' '` 替換為 `'a'`。
5. **回傳結果**：回傳字串 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times M)$。其中 $N$ 是 `str1` 的長度，$M$ 是 `str2` 的長度。階段一與階段二皆包含雙層迴圈，最差情況下每次都會遍歷長度為 $M$ 的子字串。
- **空間複雜度**: $\mathcal{O}(N + M)$。宣告了長度為 $N + M - 1$ 的字串 `rt` 來儲存結果。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string generateString(string str1, string str2) {
        int n = str1.size(), m = str2.size();
        string rt(n + m - 1, ' ');

        // 1. 處理強制匹配條件 'T'
        for(int i = 0; i < n; ++i) {
            if(str1[i] == 'T') {
                for(int j = 0; j < m; ++j) {
                    // 發生衝突：同一個位置被要求填入不同的字元
                    if(rt[i+j] != ' ' && rt[i+j] != str2[j]) return "";
                    rt[i+j] = str2[j];
                }
            }
        }

        // 2. 處理強制不匹配條件 'F'
        for(int i = 0; i < n; ++i) {
            if(str1[i] == 'F') {
                int last = -1;
                bool needed = true;
                
                for(int j = 0; j < m; ++j) {
                    if(rt[i+j] == ' ') {
                        // 若 str2[j] 不為 'a'，填入 'a' 即可產生不匹配且維持字典序最小
                        if(str2[j] != 'a') {
                            rt[i + j] = 'a';
                            needed = false;
                            break;
                        }
                        // 記錄最後一個可用空格 (此時 str2[j] 必定為 'a')
                        last = j;
                    } else {
                        // 既有字元已產生不匹配，自動滿足條件
                        if(rt[i+j] != str2[j]) {
                            needed = false;
                            break;
                        }
                    }
                }

                // 若仍需製造不匹配
                if(needed) {
                    // 無空格可改，必定匹配，產生矛盾
                    if(last == -1) return "";
                    // 將最右側的可用空格改為 'b' 以破壞匹配，且將字典序衝擊降至最低
                    rt[i + last] = ('a' == str2[last] ? 'b' : 'a');
                }
            }
        }

        // 3. 將剩餘未受限制的空格全數填入 'a'
        for(int i = 0; i < n + m - 1; ++i) {
            if(rt[i] == ' ') {
                rt[i] = 'a';
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