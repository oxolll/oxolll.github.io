---
title: "LeetCode 2559. Count Vowel Strings in Ranges"
date: 2026-03-21T20:05:00+08:00
lastmod: 2026-03-21T20:05:00+08:00
difficulty: 1435
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "string", "prefix sum"]
keywords: ["LC2559", "1435", "Medium", "Count Vowel Strings", "Prefix Sum"]
description: "LeetCode 第 2559 題：Count Vowel Strings in Ranges。難度評分：1435。探討如何利用前綴和 (Prefix Sum) 預先處理字串陣列，以 O(1) 時間複雜度高效回答多次區間查詢。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2559](https://leetcode.com/problems/count-vowel-strings-in-ranges/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1435)</span>  
> **核心主題**：`Array` $\cdot$ `String` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個字串陣列 `words` 以及一個二維整數陣列 `queries`。
每個查詢 $queries[i] = [l_i, r_i]$ 要求計算在 `words` 陣列中，從索引 $l_i$ 到 $r_i$（包含首尾）的範圍內，**開頭和結尾都是母音字母**的字串數量。
母音字母為 `'a'`, `'e'`, `'i'`, `'o'`, `'u'`。

請回傳一個整數陣列 `ans`，其中 `ans[i]` 是第 $i$ 個查詢的答案。

### 限制條件
- $1 \le words.length \le 10^5$
- $1 \le words[i].length \le 40$
- `words[i]` 僅由小寫英文字母組成。
- $1 \le queries.length \le 10^5$
- $queries[i].length == 2$
- $0 \le l_i \le r_i < words.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (前綴和與 Lambda 判斷)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題包含大量的區間查詢操作。如果對於每個查詢都重新遍歷一次指定的區間，最差時間複雜度將會達到 $\mathcal{O}(N \times M)$，其中 $N$ 是字串陣列長度，$M$ 是查詢數量，這必定會導致 Time Limit Exceeded (TLE)。
為了解決這個問題，必須使用前綴和 (Prefix Sum)。預先計算出從陣列開頭到每個位置的「合法字串累積數量」。計算完成後，對於任何區間 $[l, r]$，只需利用公式 `pre[r] - pre[l-1]` 即可在 $\mathcal{O}(1)$ 的時間內得出答案。

### 🛠️ 解題思路 (Approach)
1. **設計判斷邏輯**：
   - 建立一個 Lambda 函式 `isOK(idx)`，檢查 `words[idx]` 的第一個字元與最後一個字元是否皆為母音。
   - 由於題目保證字串僅包含小寫字母，直接比對 `'a'`, `'e'`, `'i'`, `'o'`, `'u'` 即可。
2. **建構前綴和陣列**：
   - 宣告長度與 `words` 相同的陣列 `pre`。
   - 遍歷 `words`，如果當前字串符合條件，則累積值加 1。
   - 轉移方程式：`pre[i] = (i > 0 ? pre[i - 1] : 0) + (isOK(i) ? 1 : 0)`。
3. **處理區間查詢**：
   - 宣告結果陣列 `rt`，長度與 `queries` 相同。
   - 遍歷 `queries` 陣列，取得每個查詢的左右邊界 $l$ 與 $r$。
   - 計算該區間的合法字串數量：`pre[r] - (l > 0 ? pre[l - 1] : 0)`。利用三元運算子確保當 $l = 0$ 時不會發生陣列越界。
4. **回傳結果**：迴圈結束後，回傳 `rt` 陣列。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M)$。其中 $N$ 是 `words` 的長度，$M$ 是 `queries` 的長度。建立前綴和陣列需要 $\mathcal{O}(N)$ 的時間，處理所有查詢需要 $\mathcal{O}(M)$ 的時間。判斷單一字串是否符合條件的操作為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了一個長度為 $N$ 的 `pre` 陣列來儲存前綴和狀態。回傳結果的 `rt` 陣列不計入額外空間複雜度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> vowelStrings(vector<string>& words, vector<vector<int>>& queries) {
        int n = words.size();
        vector<int> pre(n, 0);

        // Lambda 函式：判斷字串的首尾是否皆為母音
        auto isOK = [&](int idx) {
            char a = words[idx][0];
            if(a != 'a' && a != 'e' && a != 'i' && a != 'o' && a != 'u') return false;
            
            a = words[idx].back();
            if(a != 'a' && a != 'e' && a != 'i' && a != 'o' && a != 'u') return false;
            
            return true;
        };
        
        // 建構前綴和陣列
        for(int i = 0; i < n; ++i) {
            pre[i] = (i > 0 ? pre[i - 1] : 0) + (isOK(i) ? 1 : 0);
        }

        int m = queries.size();
        vector<int> rt(m, 0);
        
        // 以 O(1) 時間處理每筆查詢
        for(int i = 0; i < m; ++i) {
            int l = queries[i][0], r = queries[i][1];
            rt[i] = (pre[r] - (l > 0 ? pre[l - 1] : 0));
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