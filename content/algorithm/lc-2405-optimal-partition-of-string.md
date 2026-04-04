---
title: "LeetCode 2405. Optimal Partition of String"
date: 2026-04-06T20:30:00+08:00
lastmod: 2026-04-06T20:30:00+08:00
difficulty: 1355
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table", "greedy"]
keywords: ["LC2405", "1355", "Medium", "Optimal Partition", "String", "Greedy", "Bitmask"]
description: "LeetCode 第 2405 題：Optimal Partition of String。難度評分：1355。探討如何利用貪婪策略與字元雜湊表 (Hash Table)，在 O(N) 時間複雜度內計算將字串分割成無重複字元子字串的最少分割次數，並補充位元遮罩 (Bitmask) 的進階優化寫法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2405](https://leetcode.com/problems/optimal-partition-of-string/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1355)</span>  
> **核心主題**：`String` $\cdot$ `Hash Table` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個字串 `s`，請將該字串分割成一個或多個子字串，使得每個子字串中的字元都是**唯一的**（也就是每個字元在單一子字串中最多只能出現一次）。
請回傳符合上述條件的**最少**分割子字串數量。

*(注意：分割後的所有子字串按照原順序串接起來，必須等於原字串 `s`。)*

### 限制條件
- $1 \le s.length \le 10^5$
- `s` 僅由小寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-06：初次提交 (貪婪掃描與陣列 Hash)](#2026-04-06-初次提交)

---

## 💡 2026-04-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題目標為求「最少」的分割數量。根據貪婪策略 (Greedy Strategy)：為了減少分割次數，我們應該**盡可能讓每一個子字串包含越多的字元**。
因此，我們從左至右掃描字串，並記錄當前子字串中已經出現過的字元。只要遇到的字元尚未出現過，就繼續將其納入當前子字串；一旦遇到一個**已經出現過**的字元，代表當前子字串無法再擴展，必須在該字元前方「切一刀」，並將該字元作為下一個新子字串的起點，同時重置字元記錄。

### 🛠️ 解題思路 (Approach)
1. **初始化**：
   - 取得字串長度 `n = s.size()`。
   - 宣告變數 `rt = 0`，記錄切割出的子字串數量。
   - 使用外層 `for(int i = 0; i < n;)` 迴圈遍歷字串。
2. **尋找最長子字串**：
   - 在外層迴圈內，宣告一個長度為 26 的整數陣列 `existed`，並初始化為全 0，用於充當 Hash Set 記錄字元出現狀態。
   - 使用內層 `while(i < n && existed[s[i] - 'a'] == 0)` 迴圈不斷向右擴展：
     - 若字元未出現過，將該字元標記為已出現 (`++existed[s[i] - 'a']`)。
     - 指標 `i` 前進一位 (`++i`)。
   - 當內層迴圈中斷時，代表遇到了重複字元或已達字串尾端，這意味著一個極大子字串已被切出，因此將子字串數量加一 (`++rt`)。
3. **回傳結果**：回傳總子字串數量 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串長度。雖然使用了雙層迴圈，但指標 `i` 只會從 0 嚴格單調遞增至 $N$，每個字元只會被存取一次。
- **空間複雜度**: $\mathcal{O}(1)$。每次迴圈皆宣告一個大小固定為 26 的整數陣列，不受輸入規模影響。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int partitionString(string s) {
        int rt = 0;
        int n = s.size();
        
        for(int i = 0; i < n;) {
            // 使用陣列作為 Hash Set 記錄字元是否出現過
            int existed[26] = {0};
            
            // 貪婪擴展：只要字元沒出現過，就繼續納入當前子字串
            while(i < n && existed[s[i] - 'a'] == 0) {
                ++existed[s[i] - 'a'];
                ++i;
            }
            // 遇到重複字元或字串結尾，完成一次切割
            ++rt;
        }

        return rt;
    }
};
```

---

### 💡 進階最佳化：位元遮罩 (Bitmask)
由於字元僅限小寫英文字母 (共 26 個)，完全可以使用一個 32-bit 的整數來取代長度為 26 的陣列。
這不僅將空間壓縮到極致的 4 bytes，更因為位元運算 (`|` 和 `&`) 的速度遠快於陣列初始化與存取，能在實務執行上達到更好的效能。當遇到重複字元時，只需將整數清零並計數加一即可。

<details>
<summary><b>點擊展開 Bitmask 實作程式碼</b></summary>

```cpp
class Solution {
public:
    int partitionString(string s) {
        int rt = 1; // 至少會有一個子字串
        int mask = 0;
        
        for(char c : s) {
            int bit = c - 'a';
            // 若該字元對應的位元已為 1，代表發生重複
            if(mask & (1 << bit)) {
                ++rt;       // 增加切割數
                mask = 0;   // 重置集合，開啟新子字串
            }
            // 將該字元加入集合
            mask |= (1 << bit);
        }
        
        return rt;
    }
};
```
</details>

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