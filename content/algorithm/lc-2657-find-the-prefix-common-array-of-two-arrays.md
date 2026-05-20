---
title: "LeetCode 2657. Find the Prefix Common Array of Two Arrays"
date: 2026-05-20T14:00:00+08:00
lastmod: 2026-05-20T14:00:00+08:00
difficulty: 1304
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash-table", "counting"]
keywords: ["LC2657", "Medium", "1304", "Prefix Common Array", "陣列", "頻率計數"]
description: "LeetCode 第 2657 題：Find the Prefix Common Array of Two Arrays。難度評分：1304。探討如何利用排列 (Permutation) 的特性，透過線性掃描與頻率計數陣列，在 O(N) 時間內求得兩個陣列的前綴公共元素數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2657](https://leetcode.com/problems/find-the-prefix-common-array-of-two-arrays/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1304)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個長度為 $n$ 的整數排列 $A$ 和 $B$。
一個陣列的 **前綴公共陣列 (Prefix Common Array)** 定義為一個長度為 $n$ 的陣列 $C$，其中 $C[i]$ 是在 $A$ 的前綴 $A[0..i]$ 和 $B$ 的前綴 $B[0..i]$ 中均出現過的數字數量。

請回傳 $A$ 和 $B$ 的前綴公共陣列。
*(註：排列是指一個長度為 $n$ 的陣列，包含 $1$ 到 $n$ 的所有整數，每個數字恰好出現一次。)*

### 範例
- **Input**: `A = [1,3,2,4], B = [3,1,2,4]`
- **Output**: `[0,2,3,4]`
- **Explanation**: 
  - $i = 0$: $A[0]=1, B[0]=3$。沒有公共元素，所以 $C[0] = 0$。
  - $i = 1$: $A[0..1]=[1,3], B[0..1]=[3,1]$。公共元素有 $1$ 和 $3$，所以 $C[1] = 2$。
  - $i = 2$: $A[0..2]=[1,3,2], B[0..2]=[3,1,2]$。公共元素有 $1, 2, 3$，所以 $C[2] = 3$。
  - $i = 3$: 公共元素有 $1, 2, 3, 4$，所以 $C[3] = 4$。

### 限制條件
- $1 \le A.length == B.length == n \le 50$
- $1 \le A[i], B[i] \le n$
- $A$ 和 $B$ 保證是包含 $1$ 到 $n$ 的排列。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-20：初次提交 (線性計數法優化)](#2026-05-20-初次提交)

---

## 💡 2026-05-20 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
如果對每個索引 $i$ 都去計算兩個子陣列的交集，最壞情況下的時間複雜度會高達 $\mathcal{O}(N^2)$。這題的核心突破點在於善用「**陣列是 $1$ 到 $n$ 的排列**」這個強烈條件。

因為是排列，任何一個數字 $x \in [1, n]$ 在 $A$ 中只會出現一次，在 $B$ 中也只會出現一次。也就是說，隨著雙指標同步向右掃描，當某個數字累積被我們「看見」**第二次**時，它就一定同時存在於當前 $A$ 的前綴與 $B$ 的前綴中，成為了一個新的「公共元素」。

### 🛠️ 解題思路 (Approach)
1. **初始化頻率陣列**：建立一個大小為 $n+1$ 的計數陣列 `x`。這裡採用了一個獨特的技巧：將初始值設為 `-1`。
2. **同步遍歷**：用一個指標 `i` 同時遍歷 $A$ 和 $B$。
   - 分別將 $A[i]$ 和 $B[i]$ 的出現頻率加 $1$（即 `++x[A[i]]` 與 `++x[B[i]]`）。
3. **配對判定**：
   - 如果 `x[A[i]] == 1`，代表原本是 `0`（已看見一次），現在變成 `1`（看見第二次），這意味著配對成功！公共計數器 `c` 加 $1$。
   - 對 `B[i]` 進行相同的檢查。
   - *(註：為避免後續重複觸發，可在判定成功後將值歸回 `-1`，但在排列題型中，每個數字最多就出現兩次，所以這一步是防禦性編程。)*
4. **結果收集**：在每次迴圈的最後，將當前的公共元素總數 `c` 推入結果陣列 `rt` 中。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需對長度為 $N$ 的陣列進行一次同步線性掃描，內部運算皆為 $\mathcal{O}(1)$ 的陣列存取。
- **空間複雜度**: $\mathcal{O}(N)$。使用了一個大小為 $N+1$ 的計數陣列 `x` 來記錄每個數字出現的頻率。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> findThePrefixCommonArray(vector<int>& A, vector<int>& B) {
        int n = A.size();
        
        // 計數陣列，長度 n+1 以涵蓋數字 1~n。初始值設為 -1。
        vector<int> x(n + 1, -1);
        vector<int> rt; 
        rt.reserve(n); // 預先分配記憶體以提升效能
        
        int c = 0; // 記錄當前的公共元素數量
        for(int i = 0; i < n; ++i) {
            // 記錄當前遍歷到的數字
            ++x[A[i]];
            ++x[B[i]];
            
            // 若頻率達到 1 (即 -1 -> 0 -> 1，代表出現了兩次)
            if(x[A[i]] == 1) {
                ++c;
                x[A[i]] = -1; // 復位防重複
            }
            // 檢查 B[i] 是否也湊齊了兩次
            if(x[B[i]] == 1) {
                ++c;
                x[B[i]] = -1; // 復位防重複
            }
            
            // 寫入當前前綴的公共數量
            rt.push_back(c);
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