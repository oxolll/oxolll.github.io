---
title: "LeetCode 969. Pancake Sorting"
date: 2026-04-23T10:15:00+08:00
lastmod: 2026-04-23T10:15:00+08:00
difficulty: 1590
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting"]
keywords: ["LC969", "Medium", "1590", "Pancake Sorting", "Greedy", "Prefix Reversal"]
description: "LeetCode 第 969 題：Pancake Sorting。難度評分：1590。探討如何利用貪婪策略 (Greedy)，透過兩步翻轉法（先翻到頂部，再翻到對應的底部），以 O(N^2) 的時間複雜度完成鬆餅排序。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 969](https://leetcode.com/problems/pancake-sorting/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1590)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `arr`，請你對它進行一系列的「鬆餅翻轉 (Pancake Flips)」來對陣列進行排序。
在一次鬆餅翻轉中，你可以選擇一個整數 $k$ ($1 \le k \le arr.length$)，然後將子陣列 `arr[0...k-1]` 進行反轉。
請回傳一個陣列，陣列中包含一系列的 $k$ 值，這些 $k$ 值對應的操作能將 `arr` 排序。
任何能將陣列排序的有效操作序列皆可被接受（不要求操作次數最少，只要操作總數不超過 $10 \times arr.length$ 即可）。

### 限制條件
- $1 \le arr.length \le 100$
- $1 \le arr[i] \le arr.length$
- `arr` 中的所有整數皆為**唯一**（即 `arr` 是從 1 到 $n$ 的一個排列）。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-23：初次提交 (兩步翻轉構造法)](#2026-04-23-初次提交)

---

## 💡 2026-04-23 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題不要求操作次數最少，因此我們可以使用構造性的**貪婪演算法 (Greedy Algorithm)**。
既然只能從陣列開頭進行翻轉，我們無法輕易地操作中間的元素，但我們可以輕易地把最上面的元素翻到指定的底層。
因此，排序策略為**「由大到小，逐一歸位」**：
假設當前要處理的最大數字為 $i$（也就是它應該被放在索引 $i-1$ 的位置）：
1. 若它已經在正確位置，直接跳過。
2. 若它不在正確位置，我們先找出它目前的索引 $j$。
3. **第一步翻轉**：將前 $j+1$ 個元素翻轉，這樣數字 $i$ 就會跑到最頂端（索引 0）。
4. **第二步翻轉**：將前 $i$ 個元素翻轉，這時位於頂端的數字 $i$ 就會被翻到我們期望的底層位置（索引 $i-1$）。
重複上述過程直到數字 1，整個陣列就會呈現遞增排序。

### 🛠️ 解題思路 (Approach)
1. 宣告 `rt` 陣列儲存翻轉的 $k$ 值。
2. 外層迴圈 `i` 從最大值 $n$ 遞減到 $1$：
   - 檢查 `arr[i-1] == i`，如果當前最大值已經在對應位置，直接 `continue` 處理下一個。
   - 內層迴圈尋找數字 $i$ 在陣列中的實際位置，記錄為索引 `j`。
   - 呼叫 `reverse` 將前 $j+1$ 個元素反轉（將數字 $i$ 移至首位），並將 $j+1$ 記錄到 `rt`。
   - 呼叫 `reverse` 將前 $i$ 個元素反轉（將數字 $i$ 移至正確位置），並將 $i$ 記錄到 `rt`。
3. 迴圈結束後，回傳記錄所有翻轉長度的 `rt` 陣列。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。外層迴圈執行 $N$ 次，尋找元素位置耗時 $\mathcal{O}(N)$，執行兩次 `reverse` 也耗費 $\mathcal{O}(N)$。總體時間複雜度為 $\mathcal{O}(N^2)$。在 $N \le 100$ 的限制下非常快速。
- **空間複雜度**: $\mathcal{O}(N)$。除了回傳所需的 `rt` 陣列之外，翻轉操作皆為就地 (In-place) 進行，不耗費額外空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> pancakeSort(vector<int>& arr) {
        int n = arr.size();
        vector<int> rt;
        
        // 從最大的數字 n 開始，逐一把它們翻到正確的底部位置
        for(int i = n; i > 0; --i) {
            // 如果數字 i 已經在它該在的位置 (索引 i-1)，不需處理
            if(arr[i-1] == i) continue;
            
            // 尋找數字 i 目前在哪個位置 j
            int j = 0;
            while(arr[j] != i) ++j;

            // 1. 第一翻：把前 j+1 個鬆餅翻轉，讓數字 i 跑到最頂端 (索引 0)
            reverse(arr.begin(), arr.begin() + j + 1);
            rt.push_back(j + 1);
            
            // 2. 第二翻：把前 i 個鬆餅翻轉，讓頂端的數字 i 掉落到正確的位置 (索引 i-1)
            reverse(arr.begin(), arr.begin() + i);
            rt.push_back(i);
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