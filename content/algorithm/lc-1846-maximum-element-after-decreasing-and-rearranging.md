---
title: "LeetCode 1846. Maximum Element After Decreasing and Rearranging"
date: 2026-06-28T15:13:00+08:00
lastmod: 2026-06-28T15:13:00+08:00
difficulty: 1454
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "counting-sort", "math"]
keywords: ["LC1846", "Medium", "1454", "Maximum Element", "Decreasing and Rearranging", "貪婪演算法", "計數排序", "極致優化"]
description: "LeetCode 第 1846 題：Maximum Element After Decreasing and Rearranging。難度評分：1454。探討如何突破 O(N log N) 的常規排序思維，利用天花板限制與計數排序 (Counting Sort) 的貪婪策略，在 O(N) 線性時間內精算重新排列後的陣列最大值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1846](https://leetcode.com/problems/maximum-element-after-decreasing-and-rearranging/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1454)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Counting Sort`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個正整數陣列 `arr`。請你透過對這個陣列執行一些操作，使它滿足以下條件：
1. `arr` 中 **第一個** 元素必須為 `1`。
2. 任意 **相鄰** 兩個元素的絕對差必須小於或等於 `1`。也就是說，對於所有的 $1 \le i < arr.length$（索引從 1 開始），都必須滿足 $|arr[i] - arr[i - 1]| \le 1$。

你可以執行以下兩種操作任意次數：
- **減小 (Decrease)** 陣列中任意元素的值，使其變為一個更小的正整數。
- **重新排列 (Rearrange)** 陣列中的元素，你可以將它們排成任何順序。

請你回傳執行完這些操作後，可能得到的陣列中的 **最大元素** 是多少。

### 範例
- **Input**: `arr = [2,2,1,2,1]`
- **Output**: `2`
- **Explanation**: 
  我們可以重新排列陣列得到 `[1,2,2,2,1]`，此時符合所有條件，最大值為 2。

- **Input**: `arr = [100,1,1000]`
- **Output**: `3`
- **Explanation**: 
  一種可能的做法是：
  1. 重新排列為 `[1,100,1000]`
  2. 將 100 減小為 2，得到 `[1,2,1000]`
  3. 將 1000 減小為 3，得到 `[1,2,3]`
  符合條件，最大元素為 3。

- **Input**: `arr = [1,2,3,4,5]`
- **Output**: `5`
- **Explanation**: 陣列已經符合條件，無需操作。

### 限制條件
- $1 \le arr.length \le 10^5$
- $1 \le arr[i] \le 10^9$
</details>

---

## 📝 歷次打卡與演算法進化
- [2026-06-28：初次提交 (計數排序與 O(N) 貪婪爬坡最優解)](#2026-06-28-初次提交)

---

## 💡 2026-06-28 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題最直觀的解法是「排序法」：將陣列由小到大排序後，強制第一項為 1，接著由左至右掃描，若 `arr[i] > arr[i-1] + 1` 則將其削平至 `arr[i-1] + 1`，最終最後一項即為答案。這種作法需要 $\mathcal{O}(N \log N)$ 的時間。

但為了追求極致效能，我們可以採用 **計數排序 (Counting Sort)** 的思維將複雜度降至 $\mathcal{O}(N)$。

**核心破局點 1：陣列長度即為天花板極限**
根據題意，第一項必須為 1，且相鄰元素差最多為 1。這意味著即使一個陣列長度為 $N$，它能一路 $+1$ 堆疊上去的最大理論值就是 $N$ (例如 $1, 2, 3 \dots N$)。
因此，原陣列中任何大於 $N$ 的數字，對我們來說都沒有區別，它最終必然會被「削平」到 $N$ 或以下。
我們可以大膽地將所有 $\ge N$ 的數字視為 $N$，這讓我們能建立一個大小僅為 $N+1$ 的 `freq` 陣列來統計頻率，徹底免疫 $10^9$ 巨大數值的干擾。

**核心破局點 2：貪婪的累積與自我設限**
統計完頻率後，我們從數字 `1` 到 `N` 進行掃描。
維護一個變數 `rt` 代表「目前為止，陣列能構造出的最大合法連續值」。
當遇到一個頻率不為 0 的數字 `i` 時，有幾顆這種數字，我們的 `rt` 理論上就能再往上墊高幾層 (`rt + freq[i]`)。
但是！這些數字本身的值是 `i`，它們不能違反「不能變大」的規則。因此，這批數字能達到的最終高度絕對不能超過它們自己的數值天花板 `i`。
這衍生出了最優美的狀態轉移方程式：`rt = min(rt + freq[i], i)`。

### 🛠️ 解題思路 (Approach)
1. 取得陣列長度 `n`。
2. 宣告長度為 `n + 1` 的頻率陣列 `freq` 並初始化為 0。
3. **頻率統計 (防護越界)**：遍歷 `arr`，若數字 $x \ge n$，則將其歸類至 `freq[n]`，否則歸類至 `freq[x]`。
4. 宣告答案變數 `rt = 0`。
5. **貪婪爬坡計算**：使用迴圈從 $i = 1$ 到 $n$：
   - 若 `freq[i] == 0`，代表沒有這個數字可以當作墊腳石，跳過。
   - 更新最大值：`rt = min(rt + freq[i], i)`（累積數量與自身數值取最小）。
6. 回傳最終的 `rt` 即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列 `arr` 的長度。遍歷陣列統計頻率耗時 $\mathcal{O}(N)$，後續掃描 `freq` 陣列耗時 $\mathcal{O}(N)$，無任何排序操作，達成完美的線性時間。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了一個長度為 $N+1$ 的 `freq` 陣列用作計數排序的桶子。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximumElementAfterDecrementingAndRearranging(vector<int>& arr) {
        int n = arr.size();
        
        // 1. 建立頻率桶，大小只需開到 n+1
        // 因為長度為 n 的陣列，最終極限最大值絕對不可能超過 n
        vector<int> freq(n + 1, 0);
        for(auto x : arr) {
            if(x >= n) {
                ++freq[n]; // 大於等於 n 的巨大數字，一律視為 n 存放
            } else {
                ++freq[x];
            }
        }

        int rt = 0; // 記錄目前能構造出的合法最大值
        
        // 2. 貪婪爬坡結算
        for(int i = 1; i <= n; ++i) {
            // 若該數字不存在，無法提供墊高階梯的資源
            if(freq[i] == 0) continue;
            
            // 核心邏輯：
            // rt + freq[i] 代表：將這些數字疊加上去後，能達到的「理想」高度
            // i 代表：這些數字本身的數值，它們「不能變大」，這是絕對的物理限制
            // 兩者取 min 即為當前安全的極限高度
            rt = min(rt + freq[i], i);
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