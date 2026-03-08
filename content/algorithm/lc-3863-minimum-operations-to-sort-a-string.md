---
title: "LeetCode 3863. Minimum Operations to Sort a String"
date: 2026-03-09T13:10:00+08:00
lastmod: 2026-03-09T13:10:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Sorting", "Greedy"]
keywords: ["LC3863", "N/A", "Medium", "Sorting Substring", "Operations"]
description: "LeetCode 第 3863 題：Minimum Operations to Sort a String。難度評分：N/A。探討如何藉由貪心策略，以最少的操作次數(排序非真子字串)讓整個字串變為非遞減序列。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3863](https://leetcode.com/problems/minimum-operations-to-sort-a-string/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>   
> **核心主題**：`String` $\cdot$ `Sorting` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個由小寫英文字母組成的字串 `s`。
在一次操作中，你可以選擇 `s` 中**不是整個字串的任意子字串 (Proper Substring)**，並將其依字典序**非遞減排序**。
請回傳讓字串變為非遞減排序所需的**最少操作次數**。如果無法達成，則回傳 `-1`。

### 限制條件
- $1 \le s.length \le 10^5$
- `s` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-09：初次提交 (邊界判斷與前後綴貪心策略)](#2026-03-09-初次提交)

---

## 💡 2026-03-09 初次提交

### 🎯 直覺 (Intuition)
每次操作只能排序「非整體」的子字串，代表我們最多一次只能排序長度為 $N-1$ 的部分。
所以最佳的操作候選者只有兩種：排序前綴 `s[0...N-2]` 或排序後綴 `s[1...N-1]`。
- 如果原本就有序，答案是 0。
- 如果我們對前綴排序後，尾端剩下的那個字元剛好夠大，或是對後綴排序後，首端剩下的那個字元剛好夠小，答案就是 1。
- 否則，我們可以透過兩步（甚至三步）操作來橋接邊界。

### 🛠️ 解題思路 (Approach)
1. **提早判定無解/有序**：
   - 檢查整個字串是否已經是非遞減排序。如果是，回傳 `0`。
   - 特例：如果長度為 2 且逆序，因為只能選擇長度 $\le 1$ 的子字串排序（等於沒排），永遠無法還原，回傳 `-1`。
2. **模擬排序前綴 (`s[0...N-2]`)**：
   - 將除最後一個字元外的子字串排序。如果排序後的最大字元 `<= s.back()`，代表一次操作即可完成 (回傳 `1`)。
   - 否則，若 `x[0] > s.back()`，代表需要更多次操作。
3. **模擬排序後綴 (`s[1...N-1]`)**：
   - 將除第一個字元外的子字串排序。如果排序後的最小字元 `>= s[0]`，代表一次操作即可完成。
   - 利用 `min` 結合兩端策略的結果求出最小操作數。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。字串排序的操作佔據主要時間。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了 `x` 來儲存子字串。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minOperations(string s) {
        if(s.size() == 2 && s[0] > s[1]) return -1;
        
        // 檢查是否已經排序
        int a = 0;
        bool acd = true;
        for(int i = 0; i < s.size(); ++i) {
            if(a > s[i]) acd = false;
            else a = s[i];
        }
        if(acd) return 0;
        
        // 策略 1: 保留最後一個字元，排序前面的部分
        char tmp = s.back();
        string x = s.substr(0, int(s.size()) - 1);
        sort(x.begin(), x.end());

        int res = 1 + (x.back() <= tmp ? 0 : (x[0] > tmp ? 2 : 1));

        // 策略 2: 保留第一個字元，排序後面的部分
        tmp = s[0];
        x = s.substr(1, int(s.size() - 1));
        sort(x.begin(), x.end());
        
        res = min(res, 1 + (x[0] >= tmp ? 0 : (x.back() < tmp ? 2 : 1)));
        
        return res;
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