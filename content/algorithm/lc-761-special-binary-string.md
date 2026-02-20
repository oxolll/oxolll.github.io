---
title: "LeetCode 761. Special Binary String"
date: 2026-02-20T15:12:00+08:00
lastmod: 2026-02-20T15:12:00+08:00
difficulty: 2292
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Recursion", "Math"]
keywords: ["LC761", "2292", "Hard", "Valid Parentheses Sequence"]
description: "LeetCode 第 761 題：Special Binary String。難度評分：2292。探討如何利用合法括號序列 (VPS) 的同構性質，結合遞迴與貪心排序來求解最大字典序字串。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 761](https://leetcode.com/problems/special-binary-string/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(2292)</span>   
> **核心主題**：`Recursion` $\cdot$ `String` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
**特殊的二進位字串** 必須滿足以下兩個條件：
1. `0` 的數量與 `1` 的數量相等。
2. 對於字串的任何前綴 (Prefix)，`1` 的數量必須大於等於 `0` 的數量。

給定一個特殊二進位字串 `s`。你可以執行一種操作：選擇兩個**連續、非空且皆為特殊字串**的子字串，並交換它們的位置。
請回傳經過任意次數的操作後，能構成的**字典序最大 (Lexicographically largest)** 的字串。

### 範例
- **Input**: `s = "11011000"` → **Output**: `"11100100"`
- **Explanation**: 交換相鄰的特殊子字串 `"10"` (位於 index 1) 與 `"1100"` (位於 index 3)，可得到字典序最大的結果。

### 限制條件
- $1 \le s.length \le 50$
- `s[i]` 必定為 `'0'` 或 `'1'`
- `s` 保證是一個特殊的二進位字串。
</details>

---

## 📝 歷次打卡與更新
- [2026-02-20：初次提交 (VPS 同構映射與遞迴排序)](#2026-02-20-初次提交)

---

## 💡 2026-02-20 初次提交

### 🎯 直覺 (Intuition)
這題初見極度缺乏切入點，屬於典型的「構造型思維題」。
其突破口在於數學定義的轉換：題目給定的兩個條件（0與1等量、前綴1不小於0），在數學上**完全等價於「合法括號序列 (Valid Parentheses Sequence, VPS)」**（將 `1` 視為左括號，`0` 視為右括號）。
題目的「交換相鄰特殊子字串」操作，等同於在一個括號層級中，任意交換相鄰的兩個合法子括號對。根據冒泡排序 (Bubble Sort) 的特性，只要能交換相鄰元素，就代表我們可以將該層級的所有元素進行**全域排序**。

### 🛠️ 解題思路 (Approach)
1. **字串分割 (Decomposition)**：
   我們利用一個計數器 `cnt`（遇到 `1` 加一，遇到 `0` 減一），當 `cnt` 歸零時，就代表我們找到了一個「不可分割的最短合法特殊子字串」(Primitive Special String)。
2. **剝離外殼與遞迴 (Recursion)**：
   任何一個不可分割的特殊子字串，其形式必然為 `1 + [內部特殊子字串] + 0`。我們必須剝去外層的 `1` 和 `0`，將內部的字串丟入遞迴處理，確保每一層的括號內部都能達到字典序最大。
3. **降冪排序與重組 (Greedy Sorting)**：
   將當前層級解析出的所有合法特殊子字串（已遞迴處理完畢），依照字典序進行降冪排序 (`sort(rbegin, rend)`)，最後拼接起來回傳。這能保證較大的區塊會被排在前面，從而最大化整體的字典序。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2 \log N)$。在最壞情況下，每次遞迴都需要遍歷字串，且字串拼接與排序操作具有較高的時間常數。但考慮到測資限制 $N \le 50$，這種複雜度在執行時間上微乎其微（近乎 0ms）。
- **空間複雜度**: $\mathcal{O}(N)$。用於遞迴呼叫堆疊 (Call Stack) 的深度，以及儲存切割後字串 `subss` 的陣列空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string makeLargestSpecial(string s) {
        vector<string> subss;
        int cnt = 0;
        int left = 0;
        
        // 1. 將字串拆解為多個相鄰的不可分割特殊子字串
        for(int right = 0; right < s.size(); ++right) {
            cnt += (s[right] == '1' ? 1 : -1);
            
            // 當 cnt == 0，代表找到一個完整的特殊子字串 s[left..right]
            if(cnt == 0) {
                // 2. 剝除最外圍的 '1' 與 '0'，對內部字串進行遞迴處理
                string inner = s.substr(left + 1, right - left - 1);
                string sorted_sub = "1" + makeLargestSpecial(inner) + "0";
                
                subss.push_back(sorted_sub);
                left = right + 1; // 更新下一個子字串的起點
            }
        }

        // 3. 對所有處理完的特殊子字串進行降冪排序，以求得最大字典序
        sort(subss.rbegin(), subss.rend());
        
        // 4. 重組並回傳
        string rt = "";
        for(const auto& subs : subss) {
            rt += subs;
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