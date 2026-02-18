---
title: "LeetCode 3713. Longest Balanced Substring I"
date: 2026-02-12T19:40:00+08:00
lastmod: 2026-02-12T20:00:00+08:00
difficulty: 1490
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Prefix Sum", "Greedy"]
keywords: ["LC3713", "1490", "平衡子字串", "Longest Balanced Substring"]
description: "LeetCode 第 3713 題：Longest Balanced Substring I。難度評分：1490。探討如何從字串中尋找所有字元出現頻率相同的最長子區間。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3713](https://leetcode.com/problems/longest-balanced-substring-i/)  
> **難度評分**：<span style="color: var(--diff-color); font-weight: bold;">1490 (Medium)</span>   
> **核心主題**：`Prefix Sum` $\cdot$ `Greedy` $\cdot$ `String`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個小寫英文字串 $s$，若一個子字串中**所有出現過的字元次數都相同**，則稱該子字串為「平衡子字串」。回傳最長平衡子字串的長度。

### 範例
- **Input**: `s = "abbac"` → **Output**: `4` ("abba" 中 'a' 與 'b' 各 2 次)
- **Input**: `s = "zzabccy"` → **Output**: `4` ("zabc" 每個字元各 1 次)

### 限制條件
- $1 \le s.\text{length} \le 1000$ (決定了 $O(N^2)$ 等級的算法可通行)
- $s$ 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-02-12：初次提交 (Prefix Sum + Greedy)](#2026-02-12-初次提交)

---

## 💡 2026-02-12 初次提交

### 直覺 (Intuition)
由於題目要求「最長」，我們採 **Greedy 策略** 從最大長度 $N$ 往回搜尋。只要找到第一個符合條件的，保證就是答案（Early Exit）。

### 解題思路 (Approach)
1. **前綴和優化**：建立 `count[n][26]` 陣列。
2. **區間查詢**：利用 `count[right] - count[left-1]` 在 $O(1)$ 時間取得子字串字母頻率。
3. **貪婪搜尋**：雙層迴圈檢查長度由 $N$ 到 $2$ 的所有子字串。

### 📊 複雜度分析
- **時間複雜度**: $O(N^2 \cdot 26)$。
- **空間複雜度**: $O(N \cdot 26)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int longestBalanced(string s) {
        int n = s.size();
        if(n == 1) return 1;
        
        // 1. 建立前綴和陣列
        vector<vector<int>> count(n, vector<int>(26, 0));
        for(int i = 0; i < n; ++i) {
            for(int j = 0; j < 26; ++j) {
                count[i][j] = (i > 0 ? count[i-1][j] : 0);
            }
            ++count[i][s[i]-'a'];
        }
        
        // 2. 由長至短迭代長度 (Greedy)
        for(int len = n; len > 1; --len) {
            for(int i = 0; i <= n - len; ++i) {
                int standardFreq = 0;
                bool isBalanced = true;
                
                // 檢查 26 個字母頻率
                for(int j = 0; j < 26; ++j) {
                    int total = count[i+len-1][j] - (i > 0 ? count[i-1][j] : 0);
                    
                    if(total == 0) continue;
                    
                    if(standardFreq == 0) {
                        standardFreq = total;
                    } else if(standardFreq != total) {
                        isBalanced = false;
                        break;
                    }
                }
                
                if(isBalanced) return len;
            }
        }
        return 1;
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