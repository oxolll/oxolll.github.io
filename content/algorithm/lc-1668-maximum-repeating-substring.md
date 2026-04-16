---
title: "LeetCode 1668. Maximum Repeating Substring"
date: 2026-04-16T15:20:00+08:00
lastmod: 2026-04-16T15:20:00+08:00
difficulty: 1395
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "dynamic-programming", "pattern-matching"]
keywords: ["LC1668", "1395", "Easy", "Maximum Repeating Substring", "Dynamic Programming"]
description: "LeetCode 第 1668 題：Maximum Repeating Substring。難度評分：1395。探討如何揚棄低效的字串疊加比對，改以一維動態規劃 (1D DP) 結合字首剪枝技巧，以 O(N * M) 時間複雜度高效計算最大連續重複子字串的次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1668](https://leetcode.com/problems/maximum-repeating-substring/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1395)</span>  
> **核心主題**：`String` $\cdot$ `Dynamic Programming`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個字串 `sequence` 與一個字串 `word`。
如果字串 `word` 連續重複 `k` 次形成的字串是 `sequence` 的子字串，那麼我們稱 `word` 擁有一個 `k` 值的「最大連續重複次數 (Maximum Repeating)」。
也就是說，`word` 串接 `k` 次後形成的 `word * k` 依然能在 `sequence` 中找到。
請找出並回傳 `word` 在 `sequence` 中的最大連續重複次數 `k`。

### 限制條件
- $1 \le sequence.length \le 100$
- $1 \le word.length \le 100$
- `sequence` 和 `word` 僅由小寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-16：初次提交 (一維 DP 與字首剪枝)](#2026-04-16-初次提交)

---

## 💡 2026-04-16 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的直覺解法是利用 `while` 迴圈不斷將 `word` 疊加，並使用字串的 `find()` 函式尋找。但這種寫法會產生多餘的字串配置開銷。
最優雅的做法是使用 **一維動態規劃 (1D DP)**。
我們定義 `dp[k]` 代表：以原字串索引 `k-1` 為結尾時，能夠湊出的最大連續 `word` 數量。
當我們從頭開始掃描 `sequence`，如果在索引 `i` 發現了一段長度與 `word` 相同的字串且完美吻合，那麼：
$$dp[i + \text{word.length}] = dp[i] + 1$$
這代表當前片段完美銜接了前一個片段的連續狀態，我們只需在走訪過程中動態更新全域最大值 `mx` 即可。

### 🛠️ 解題思路 (Approach)
1. **宣告 DP 陣列**：宣告一個大小為 $N+1$ 的整數陣列 `dp`，並將初始值皆設為 0。宣告 `mx = 0` 記錄最大連續次數。
2. **走訪字串**：使用迴圈 `i` 從 `0` 走訪至 `n - word.size()`，確保剩餘長度足夠進行比對。
3. **字首剪枝與字串比對**：
   - 為了加速，先檢查字首字元：`sequence[i] == word[0]`。
   - 若字首相同，再呼叫 `substr(i, word.size()) == word` 進行完整比對。
4. **狀態轉移**：若比對成功，則更新結尾位置的狀態 `dp[i + word.size()] = dp[i] + 1`，並同步更新全域最大值 `mx`。
5. **回傳結果**：走訪結束後，回傳 `mx`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times M)$。其中 $N$ 是 `sequence` 的長度，$M$ 是 `word` 的長度。迴圈執行 $N - M + 1$ 次，每次字串比對 `substr` 最多耗時 $\mathcal{O}(M)$。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了長度為 $N+1$ 的 `dp` 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxRepeating(string sequence, string word) {
        int n = sequence.size();
        // dp[k] 代表以長度 k 為結尾時的最大連續重複次數
        vector<int> dp(n + 1, 0);
        int mx = 0;
        
        // 確保擷取子字串時不會越界
        for(int i = 0; i <= n - int(word.size()); ++i) {
            // 優化剪枝：先比對第一個字元，相符才進行耗時的 substr 完整比對
            if(sequence[i] == word[0] && sequence.substr(i, word.size()) == word) {
                // 狀態轉移：完美銜接前一段的連續次數
                dp[i + word.size()] = dp[i] + 1;
                // 更新全域最大值
                mx = max(mx, dp[i + word.size()]);
            }
        }

        return mx;
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