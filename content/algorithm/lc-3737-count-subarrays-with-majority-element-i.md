---
title: "LeetCode 3737. Count Subarrays With Majority Element I"
date: 2026-06-25T14:55:00+08:00
lastmod: 2026-06-25T14:55:00+08:00
difficulty: 1422
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum", "counting"]
keywords: ["LC3737", "Medium", "1422", "Count Subarrays", "Majority Element", "前綴和", "絕對多數"]
description: "LeetCode 第 3737 題：Count Subarrays With Majority Element I。難度評分：1422。探討如何利用前綴和 (Prefix Sum) 技巧，搭配無浮點數誤差的代數不等式，在 O(N^2) 時間內高效統計符合絕對多數條件的子陣列數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3737](https://leetcode.com/problems/count-subarrays-with-majority-element-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1422)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums` 和一個整數 `target`。
請你計算並回傳 `nums` 中，有多少個連續子陣列滿足：`target` 是該子陣列的 **多數元素 (Majority Element)**。

一個元素被稱為子陣列的「多數元素」，若且唯若它在該子陣列中出現的次數 **嚴格大於** 子陣列長度的一半 (即 `count > length / 2`)。

### 限制條件
*(依據 Range I 難度推斷)*
- $1 \le nums.length \le 1000$
- $1 \le nums[i], target \le 10^9$
- 允許 $\mathcal{O}(N^2)$ 的時間複雜度。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-25：初次提交 (O(N^2) 前綴和與區間枚舉)](#2026-06-25-初次提交)

---

## 💡 2026-06-25 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在一個較小測資範圍 (Range I) 下，我們可以放心地枚舉所有可能的子陣列。
為了能夠在 $\mathcal{O}(1)$ 時間內知道任意區間 $[i, i+len-1]$ 中 `target` 出現了幾次，我們需要預處理一個 **前綴和 (Prefix Sum)** 陣列。

**核心破局點 1：布林映射的前綴和**
我們建立一個大小為 $N+1$ 的 `pre` 陣列。遍歷 `nums` 時，若 `nums[i] == target`，則貢獻值為 $1$，否則為 $0$。
如此一來，任何長度為 `len`、起點為 `i` 的子陣列中，`target` 的出現次數就可以用 $\mathcal{O}(1)$ 時間算出：`count = pre[i + len] - pre[i]`。

**核心破局點 2：避開浮點數的代數轉換**
判斷多數元素的條件為 $count > \frac{len}{2}$。
如果在程式中直接寫 `count > len / 2.0`，可能會有效能或精確度問題；若寫 `count > len / 2` (整數除法)，則遇到奇數長度時會產生向下取整的錯誤判斷。
因此，我們將不等式兩邊同乘 2，轉換為：**$2 \times count > len$**。
在程式碼中反向寫法即為：`len < 2 * count`，安全且精準。

### 🛠️ 解題思路 (Approach)
1. 宣告陣列大小 $n$，並初始化前綴和陣列 `pre`，長度為 $n + 1$。
2. 遍歷 `nums`，若 `nums[i] == target`，則 `pre[i+1] = pre[i] + 1`；否則 `pre[i+1] = pre[i]`。
3. 初始化答案計數器 `rt = 0`。
4. 使用外層迴圈枚舉所有子陣列的長度 `len` (從 $1$ 到 $n$)。
5. 使用內層迴圈枚舉子陣列的起點 `i` (從 $0$ 到 $n - len$)。
6. 透過前綴和計算該區間的 `target` 數量，並利用 `len < 2 * (pre[i+len] - pre[i])` 判斷是否為多數元素。若成立則 `++rt`。
7. 回傳結果 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。其中 $N$ 是陣列長度。前綴和建立需要 $\mathcal{O}(N)$，雙層迴圈枚舉所有長度與起點需要 $\mathcal{O}(N^2)$。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了長度為 $N+1$ 的 `pre` 陣列以儲存前綴狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int countMajoritySubarrays(vector<int>& nums, int target) {
        int n = nums.size();
        
        // 1. 建構前綴和陣列，儲存 target 出現的累積次數
        // pre[i] 代表區間 [0, i-1] 中 target 的數量
        vector<int> pre(n + 1, 0);
        for(int i = 0; i < n; ++i) {
            pre[i + 1] = pre[i] + (nums[i] == target ? 1 : 0);
        }
        
        int rt = 0;
        
        // 2. 雙層迴圈枚舉所有可能的子陣列 (長度 len 與起點 i)
        for(int len = 1; len <= n; ++len) {
            for(int i = 0; i <= n - len; ++i) {
                // 計算區間 [i, i+len-1] 中 target 的數量
                // count = pre[i+len] - pre[i]
                // 判斷條件：count > len / 2  =>  2 * count > len
                if(len < 2 * (pre[i + len] - pre[i])) {
                    ++rt;
                }
            }
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