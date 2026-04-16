---
title: "LeetCode 396. Rotate Function"
date: 2026-04-16T15:40:00+08:00
lastmod: 2026-04-16T15:40:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "dynamic-programming", "sliding-window"]
keywords: ["LC396", "Medium", "Rotate Function", "Math", "Dynamic Programming"]
description: "LeetCode 第 396 題：Rotate Function。探討如何利用數學錯位相減法推導狀態轉移方程式，將 O(N^2) 的暴力旋轉計算降維至 O(N) 的線性時間複雜度。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 396](https://leetcode.com/problems/rotate-function/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Dynamic Programming`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的整數陣列 `nums`。
定義一個旋轉函數 $F(k)$，其值為將 `nums` 向右旋轉 $k$ 個位置後，元素與其索引的乘積總和。
具體來說，如果旋轉後的陣列為 $B$，則：
$$F(k) = 0 \times B[0] + 1 \times B[1] + \dots + (n-1) \times B[n-1]$$
請計算並回傳 $F(0), F(1), \dots, F(n-1)$ 中的**最大值**。

### 限制條件
- $n == nums.length$
- $1 \le n \le 10^5$
- $-100 \le nums[i] \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-16：初次提交 (數學推導狀態轉移)](#2026-04-16-初次提交)

---

## 💡 2026-04-16 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
如果每次旋轉都重新計算乘積和，時間複雜度將高達 $\mathcal{O}(N^2)$。破局的關鍵在於尋找相鄰狀態 $F(k)$ 與 $F(k-1)$ 之間的數學規律。

根據題目定義，觀察未旋轉 $F(i)$ 與向右旋轉一次 $F(i+1)$ 的差異：
$$F(i) = 0 \cdot A_0 + 1 \cdot A_1 + \dots + (n-1) \cdot A_{n-1}$$
當陣列向右旋轉一次後，原本在索引 $n-1$ 的元素會跑到索引 $0$，其餘元素索引皆 $+1$：
$$F(i+1) = 0 \cdot A_{n-1} + 1 \cdot A_0 + 2 \cdot A_1 + \dots + (n-1) \cdot A_{n-2}$$

我們將兩式相減 $F(i+1) - F(i)$：
$$F(i+1) - F(i) = 1 \cdot A_0 + 1 \cdot A_1 + \dots + 1 \cdot A_{n-2} - (n-1) \cdot A_{n-1}$$
此時我們發現，除了最後一個元素外，前面所有的元素都剛好加了 $1$ 倍。為了湊出完整的陣列總和 $Sum$，我們可以把公式改寫為：
$$F(i+1) - F(i) = (A_0 + A_1 + \dots + A_{n-1}) - n \cdot A_{n-1}$$
也就是說：
$$F(i+1) = F(i) + Sum - n \cdot A_{n-1}$$
有了這個 $\mathcal{O}(1)$ 的狀態轉移方程式，我們只需先算出 $F(0)$ 與全陣列總和 $Sum$，接著就能以線性時間推導出所有的旋轉結果！

### 🛠️ 解題思路 (Approach)
1. **初始化與基本計算**：
   - 取得陣列長度 $n$。
   - 宣告 `dp` 陣列儲存各次旋轉的結果，以及變數 `sum` 記錄陣列總和。
   - 走訪陣列，計算 `sum`，並同時計算出初始狀態 $F(0)$ 存入 `dp[0]`。
2. **狀態轉移**：
   - 宣告 `mx = dp[0]` 記錄全域最大值。
   - 迴圈 $i$ 從 $1$ 執行到 $n$。
   - 根據推導的公式計算下一個狀態：`dp[i] = dp[i-1] + sum - n * nums[n - i]`。
     *(註：當 $i=1$ 時，前一次最後一個元素為 `nums[n-1]`；$i=2$ 時為 `nums[n-2]`，依此類推)*。
   - 同步更新最大值 `mx = max(mx, dp[i])`。
3. **回傳結果**：迴圈結束後，回傳 `mx`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需走訪陣列兩次：一次計算初始和與 $F(0)$，一次進行狀態推導，效率極高。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了長度為 $N+1$ 的 `dp` 陣列。*(可透過只保留前一個狀態 `curr_val` 優化至 $\mathcal{O}(1)$)*。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxRotateFunction(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n + 1, 0);
        int sum = 0;
        
        // 1. 計算陣列總和 (sum) 與初始未旋轉的結果 F(0)
        for(int i = 0; i < n; ++i) {
            sum += nums[i];
            dp[0] += i * nums[i];
        }

        int mx = dp[0];
        
        // 2. 利用錯位相減法推導的公式，以 O(1) 時間轉移狀態
        // 轉移公式：dp[i] = dp[i-1] + sum - n * (當前被移到最前面的元素)
        for(int i = 1; i <= n; ++i) {
            dp[i] = dp[i-1] + sum - n * nums[n - i];
            mx = max(mx, dp[i]);
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