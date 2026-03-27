---
title: "LeetCode 2656. Maximum Sum With Exactly K Elements "
date: 2026-03-27T12:15:00+08:00
lastmod: 2026-03-27T12:15:00+08:00
difficulty: 1213
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "greedy"]
keywords: ["LC2656", "1213", "Easy", "Maximum Sum", "Math", "Arithmetic Progression"]
description: "LeetCode 第 2656 題：Maximum Sum With Exactly K Elements。難度評分：1213。探討如何利用貪婪策略尋找陣列最大值，並結合等差數列求和公式，以 O(N) 時間複雜度直接計算出 k 次操作後的最大總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2656](https://leetcode.com/problems/maximum-sum-with-exactly-k-elements/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1213)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個以 0 為起始索引的整數陣列 `nums` 和一個整數 `k`。你需要執行以下操作**恰好** `k` 次，以最大化你的得分：
1. 從 `nums` 中選擇一個元素 `m`。
2. 將 `m` 從陣列中移除。
3. 將 `m` 加到你的得分中。
4. 將 `m + 1` 加入陣列中。

請回傳執行恰好 `k` 次操作後的最大得分。

### 限制條件
- $1 \le nums.length \le 100$
- $1 \le nums[i] \le 100$
- $1 \le k \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (尋找極值與等差數列公式)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
依據貪婪策略 (Greedy Strategy)，為了使每次操作獲得的分數最大化，我們必須在每一次選擇時都挑選陣列中的**最大值**。
當我們選擇了最大值 $mx$，將其移除並加入 $mx + 1$ 後，陣列中的新最大值必然是 $mx + 1$。
因此，這 $k$ 次操作所選出的數字序列必定為：$mx, mx+1, mx+2, \dots, mx+k-1$。
這是一個首項為 $mx$、公差為 $1$、項數為 $k$ 的等差數列。與其使用迴圈模擬這 $k$ 次加法，我們可以直接套用等差數列求和公式，將時間複雜度從 $\mathcal{O}(N + K)$ 降至 $\mathcal{O}(N)$。

### 🛠️ 解題思路 (Approach)
1. **尋找最大值**：使用 STL 的 `max_element` 遍歷陣列 `nums`，找出全域最大值 `mx`。
2. **數學公式計算**：
   - 總和公式推導：$\sum_{i=0}^{k-1} (mx + i) = k \cdot mx + \frac{k(k-1)}{2}$。
   - 為了避免整數除法造成的無條件捨去誤差，透過判斷 $k$ 的奇偶性來決定計算順序：
     - 若 $k$ 為奇數 (`k % 2 == 1`)，$k-1$ 必為偶數，可先執行 $(k-1)/2$，公式重組為 $k \cdot (mx + (k-1)/2)$。
     - 若 $k$ 為偶數，則直接使用展開公式 $k \cdot mx + k \cdot (k-1) / 2$（此時 $k/2$ 必能整除）。
3. **回傳結果**：回傳計算出的數學總和。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。呼叫 `max_element` 需要遍歷陣列一次。後續的數學計算為 $\mathcal{O}(1)$ 時間。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用常數變數儲存最大值，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximizeSum(vector<int>& nums, int k) {
        // 1. 取得陣列中的最大值
        int mx = *max_element(nums.begin(), nums.end());

        // 2. 利用等差數列求和公式計算 k 次操作的總和
        // 公式：k * mx + k * (k - 1) / 2
        // 透過判斷奇偶性確保整數除法的精確度
        return k % 2 == 1 ? k * (mx + (k - 1) / 2) : k * mx + k * (k - 1) / 2;
    }
};

/* 數學推導註記：
   r + (r+1) + (r+2) + ... + (r+k-1)
   => k*r + (1+k-1)*(k-1)/2
   => k*r + k*(k-1)/2
   => k(r+(k-1)/2)
*/
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