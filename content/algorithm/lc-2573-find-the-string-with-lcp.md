---
title: "LeetCode 2573. Find the String with LCP"
date: 2026-03-28T16:00:00+08:00
lastmod: 2026-03-28T16:05:00+08:00
difficulty: 2681
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy", "matrix", "dynamic programming"]
keywords: ["LC2573", "2681", "Hard", "LCP", "Longest Common Prefix", "Greedy Construction"]
description: "LeetCode 第 2573 題：Find the String with LCP。難度評分：2681。探討如何利用貪婪策略構造字典序最小的字串，並利用動態規劃結合矩陣對稱性與對角線性質，以 O(N^2) 時間複雜度嚴謹驗證 LCP 矩陣的邏輯一致性。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2573](https://leetcode.com/problems/find-the-string-with-lcp/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2681)</span>  
> **核心主題**：`String` $\cdot$ `Greedy` $\cdot$ `Matrix` $\cdot$ `Dynamic Programming`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
對一個字串 `word` 而言，我們可以構造一個 $n \times n$ 的矩陣 `lcp`，其中 `lcp[i][j]` 代表子字串 `word[i...n-1]` 與 `word[j...n-1]` 的「最長共同前綴 (Longest Common Prefix)」長度。

現在，給定一個 $n \times n$ 的整數矩陣 `lcp`。請你找出一個長度為 $n$ 的字串 `word`，使得該字串構造出的 LCP 矩陣與給定的矩陣完全相同。
- 字串必須僅由小寫英文字母組成。
- 如果存在多個合法的字串，請回傳**字典序最小 (Lexicographically Smallest)** 的字串。
- 如果不存在任何合法的字串，請回傳空字串 `""`。

### 限制條件
- $1 \le n == lcp.length == lcp[i].length \le 1000$
- $0 \le lcp[i][j] \le n$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-28：修訂提交 (增加對角線與對稱性驗證，優化迴圈範圍)](#2026-03-28-修訂提交)

---

## 💡 2026-03-28 修訂提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求從 LCP 矩陣反推原始字串，並過濾掉所有不合法的輸入矩陣。合法的 LCP 矩陣必須嚴格滿足三大基本性質：
1. **對角線性質**：字串與自身的共同前綴長度等於剩餘長度，即 `lcp[i][i] == n - i`。
2. **對稱性質**：`lcp[i][j] == lcp[j][i]`。
3. **遞迴/動態規劃性質**：若 `word[i] == word[j]`，則 `lcp[i][j] == 1 + lcp[i+1][j+1]`；若不相等則為 0。

演算法分為兩個階段：先透過 `lcp[i][j] > 0` 的資訊，以貪婪策略 (Greedy Strategy) 將相同的字元分群並依序指派最小字母 `'a'` 到 `'z'` 以確保字典序最小。接著，由矩陣右下角往左上角進行反向掃描，嚴格驗證上述三大性質是否完全吻合。

### 🛠️ 解題思路 (Approach)
1. **字串初始化**：宣告長度為 $n$ 的字串 `rt`，全填充為空白字元 `' '`。宣告字元 `ch = 'a'` 作為當前可用字元。
2. **第一階段：字元貪婪賦值**
   - 遍歷 `rt` 的每一個索引 `i`。若 `rt[i]` 已有字母，則跳過。
   - 若 `ch > 'z'`，字元種類不足，直接回傳 `""`。
   - 開啟內層迴圈，索引 `j` 直接從 `i` 開始（優化：小於 `i` 的位置若有相同的字元，早在先前的迴圈就被處理過了）。
   - 若 `lcp[i][j] > 0`，則將 `rt[j]` 設為 `ch`。
   - 完成當前群組賦值後，將可用字元 `ch` 遞增。
3. **第二階段：性質與一致性驗證**
   - 從矩陣右下角倒序走訪至左上角 (`i` 與 `j` 皆從 `n-1` 遞減)。
   - **檢查對角線**：在列迴圈 `i` 剛開始時，檢查 `lcp[i][i] != n - i`，若不符則回傳 `""`。
   - **檢查對稱性**：在內層迴圈 `j`，檢查 `lcp[i][j] != lcp[j][i]`，若不符則回傳 `""`。
   - **動態規劃比對**：計算出理論上的 LCP 值 `lcpcnt`。若構造出的字元 `rt[i] == rt[j]`，則 `lcpcnt` 為後續狀態 `lcp[i+1][j+1] + 1`（邊界處理為 1）。若不相等則為 0。將其與 `lcp[i][j]` 比對，不符則回傳 `""`。
4. **回傳結果**：全部驗證通過後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。其中 $N$ 是矩陣的大小。第一階段分配字元與第二階段驗證分別需要遍歷矩陣一次，所有驗證操作皆為 $\mathcal{O}(1)$ 常數時間。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了長度為 $N$ 的字串 `rt` 儲存結果。驗證階段直接利用輸入的 `lcp` 矩陣作為動態規劃狀態來源，無額外配置二維陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string findTheString(vector<vector<int>>& lcp) {
        int n = lcp.size();
        string rt(n, ' ');
        char ch = 'a';
        
        // 1. 貪婪分配字元
        for(int i = 0; i < n; ++i) {
            if(rt[i] != ' ') continue;
            if(ch > 'z') return ""; // 所需相異字元超過 26 個，無解

            // j 從 i 開始遍歷即可，提升執行效率
            for(int j = i; j < n; ++j) {
                if(lcp[i][j] > 0) {
                    rt[j] = ch;
                }
            }
            ++ch;
        }

        // 2. 嚴格驗證 LCP 矩陣的三大基本性質
        for(int i = n - 1; i >= 0; --i) {
            // 驗證對角線性質
            if(lcp[i][i] != n - i) return "";
            
            for(int j = n - 1; j >= 0; --j) {
                // 驗證對稱性
                if(lcp[i][j] != lcp[j][i]) return "";
                
                int lcpcnt = 0;
                
                // 動態規劃狀態轉移：若字元相等，延續後方狀態
                if(rt[i] == rt[j]) {
                    lcpcnt = (i == n - 1 || j == n - 1) ? 1 : (lcp[i+1][j+1] + 1);
                }

                // 驗證動態規劃計算結果是否與輸入矩陣相符
                if(lcp[i][j] != lcpcnt) {
                    return "";
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