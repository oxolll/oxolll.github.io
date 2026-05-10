---
title: "LeetCode 3925. Concatenate Array With Reverse"
date: 2026-05-10T19:55:00+08:00
lastmod: 2026-05-10T19:55:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "simulation", "two-pointers"]
keywords: ["LC3925", "Easy", "Concatenate", "Reverse", "對稱填充", "陣列操作"]
description: "LeetCode 第 3925 題：Concatenate Array With Reverse。難度評分：Easy。探討如何利用陣列對稱索引的物理特性，在 $O(n)$ 時間內完成原序與反序的無縫拼接。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3925](https://leetcode.com/problems/concatenate-array-with-reverse/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的整數陣列 `nums`。
你需要建立一個長度為 $2n$ 的新陣列 `ans`，其內容定義如下：
- `ans` 的前 $n$ 個元素是 `nums` 的原始順序。
- `ans` 的後 $n$ 個元素是 `nums` 的反轉順序。

例如，若 `nums = [1, 2, 3]`，則回傳 `[1, 2, 3, 3, 2, 1]`。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-10：初次提交 (對稱索引一次遍歷法)](#2026-05-10-初次提交)

---

## 💡 2026-05-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標是建構一個「回文拼接」形式的陣列。最直觀的思考是將陣列複製兩份，並對後半段進行 `reverse`，但這會涉及多次記憶體掃描。

**核心破局點：一次遍歷完成對稱賦值**
對於結果陣列 `rt`，總長度為 $2n$：
- 正向索引 $i$ 處應填入 `nums[i]`。
- 反向索引（從尾部起算）$2n - 1 - i$ 處同樣應填入 `nums[i]`。

藉由這個物理特性，我們可以用一個 $0 \sim n-1$ 的迴圈，同時解決兩個位置的資料填寫，將執行效率拉到最高。

### 🛠️ 解題思路 (Approach)
1. **初始化**：預先配置好大小為 $2n$ 的結果向量 `rt`，避免動態擴張 (`push_back`) 造成的性能耗損。
2. **對稱填充**：
   - 遍歷 `nums` 陣列，索引為 `i`。
   - `rt[i] = nums[i]`：填充前半段原序部分。
   - `rt[2*n - 1 - i] = nums[i]`：填充後半段反序部分。
3. **回傳結果**。

### 📊 複雜度分析
- **時間複雜度**: $O(n)$。雖然結果陣列長度是 $2n$，但我們只需掃描原始陣列 `nums` 一次，執行 $n$ 次迭代。
- **空間複雜度**: $O(n)$。若不計入結果陣列所需的 $2n$ 空間，額外使用的輔助空間為 $O(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> concatWithReverse(vector<int>& nums) {
        int n = nums.size();
        // 1. 建立 2n 大小的結果陣列，預設填充 0
        vector<int> rt(2 * n, 0);
        
        // 2. 利用對稱位置進行一次性填充
        for(int i = 0; i < n; ++i) {
            // 正向：填充 ans[0] 到 ans[n-1]
            rt[i] = nums[i];
            
            // 反向：填充 ans[2n-1] 倒退回 ans[n]
            // 當 i = 0 時，填入最後一個位置 (2n-1)
            // 當 i = n-1 時，填入第 n 個位置 (2n-1-(n-1) = n)
            rt[2 * n - 1 - i] = nums[i];
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