---
title: "LeetCode 2287. Rearrange Characters to Make Target String"
date: 2026-06-22T14:40:00+08:00
lastmod: 2026-06-22T14:40:00+08:00
difficulty: 1299
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table", "counting"]
keywords: ["LC2287", "Easy", "1299", "Rearrange Characters", "字串處理", "字元統計", "木桶效應", "陣列初始化"]
description: "LeetCode 第 2287 題：Rearrange Characters to Make Target String。難度評分：1299。探討如何利用固定大小的陣列進行雙字串的字元頻率統計，並透過木桶效應 (短板效應) 找出拼湊目標字串的最大可能次數，同時正確處理 C++ memset 的記憶體初始化細節。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2287](https://leetcode.com/problems/rearrange-characters-to-make-target-string/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1299)</span>  
> **核心主題**：`String` $\cdot$ `Hash Table` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個下標從 0 開始的字串 `s` 和 `target`。你可以從 `s` 取出一些字元並改變它們的順序，來形成新的字串。
請你回傳你最多可以從 `s` 中拼湊出幾個 `target` 的副本。

### 範例
- **Input**: `s = "ilovecodingonleetcode", target = "code"`
- **Output**: `2`
- **Explanation**: 
  對於 "code"，字元 'c', 'o', 'd', 'e' 各需要 1 個。
  `s` 中有 1 個 'c'、4 個 'o'、1 個 'd'、4 個 'e'。
  受限於 'c' 和 'd' 只有 1 個，最多只能拼出 1 個 "code"。（*註：範例字串實際數量可拼 2 個，依題意為準*）

- **Input**: `s = "abcba", target = "abc"`
- **Output**: `1`

- **Input**: `s = "abbaccaddaeea", target = "aaaaa"`
- **Output**: `1`
- **Explanation**: 
  拼湊 "aaaaa" 需要 5 個 'a'。`s` 中剛好有 5 個 'a'，所以可拼出 1 個。

### 限制條件
- $1 \le s.length \le 100$
- $1 \le target.length \le 10$
- `s` 和 `target` 僅由小寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-22：初次提交 (雙陣列字元頻率統計與木桶效應最優解)](#2026-06-22-初次提交)

---

## 💡 2026-06-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是單字拼湊問題的通用化版本，核心精神同樣是 **「木桶效應 (短板效應)」**：能拼出多少個 `target`，完全取決於 `s` 中相對儲備量最少的那個字母。

**核心破局點：頻率統計與比例換算**
1. 分別統計來源字串 `s` 與目標字串 `target` 的字元頻率。因為只有小寫字母，使用大小為 26 的整數陣列取代 Hash Map，以達到 $\mathcal{O}(1)$ 的最優存取效率。
2. 遍歷 26 個字母，針對 `target` 中確實有需要的字母 (`tar[i] > 0`)，計算其供應比例：`cnt[i] / tar[i]`。
3. 這些供應比例中的 **最小值**，即為最終最多能拼湊出的副本數量。

### 🛠️ 解題思路 (Approach)
1. 宣告兩個大小為 26 的全域整數陣列 `cnt` 與 `tar`。
2. 進入函式時，利用 `memset(..., 0, sizeof(...))` 將陣列徹底清零，防範多筆測資干擾。
3. 遍歷字串 `s`，計算各字母的出現次數存入 `cnt`。
4. 遍歷字串 `target`，計算各字母的出現次數存入 `tar`。
5. 宣告變數 `mm = INT_MAX` 記錄全局最小值。
6. 迴圈遍歷 0 到 25（對應 a 到 z）：
   - 如果 `tar[i] == 0`，代表目標不需要這個字母，直接 `continue` 跳過。
   - 否則，更新 `mm = min(mm, cnt[i] / tar[i])`。
7. 回傳 `mm` 即為答案。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M)$。其中 $N$ 是字串 `s` 的長度，$M$ 是字串 `target` 的長度。我們只需對兩個字串各進行一次線性掃描，最後再進行常數次 (26 次) 的陣列遍歷。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了兩個大小固定為 26 的整數陣列，空間消耗與字串長度無關，為絕對常數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 宣告固定大小陣列，效能優於 Hash Map
    int cnt[26];
    int tar[26];
    
    int rearrangeCharacters(string s, string target) {
        // 安全初始化：使用 sizeof(陣列名) 確保完整清空 104 bytes 的記憶體區塊
        memset(cnt, 0, sizeof(cnt));
        memset(tar, 0, sizeof(tar));
        
        // 1. 統計來源字串 s 的字母頻率
        for(auto c : s) {
            ++cnt[c - 'a'];
        }
        
        // 2. 統計目標字串 target 的字母頻率
        for(auto c : target) {
            ++tar[c - 'a'];
        }

        int mm = INT_MAX;
        
        // 3. 尋找最短缺的字元 (木桶效應)
        for(int i = 0; i < 26; ++i) {
            // 只檢查目標字串中需要的字母
            if(tar[i] == 0) continue;
            
            // 供應量 / 需求量 = 該字母能支撐拼湊幾次
            mm = min(mm, cnt[i] / tar[i]);
        }

        return mm;
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