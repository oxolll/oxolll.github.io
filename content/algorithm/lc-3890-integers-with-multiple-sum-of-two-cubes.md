---
title: "LeetCode 3890. Integers With Multiple Sum of Two Cubes"
date: 2026-04-09T10:00:00+08:00
lastmod: 2026-04-09T10:00:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "hash-table", "enumeration", "sorting"]
keywords: ["LC3890", "Medium", "N/A", "Sum of Two Cubes", "Taxicab Number", "Ramanujan", "Hash Table"]
description: "LeetCode 第 3890 題：Integers With Multiple Sum of Two Cubes。難度評分：Medium (N/A)。探討如何利用數學上界分析 (x <= 1000) 配合雙層迴圈枚舉與雜湊表計數，快速找出所有能以多種方式表示為兩立方數之和的計程車數 (Taxicab numbers)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3890](https://leetcode.com/problems/integers-with-multiple-sum-of-two-cubes/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Hash Table` $\cdot$ `Enumeration` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `n`。
請你找出所有小於或等於 `n`，且可以被表示為**兩個正整數立方和**至少**兩種不同方式**的「好整數 (Good Integers)」。
（即存在正整數 $a, b, c, d$ 使得 $a^3 + b^3 = c^3 + d^3 = x$，其中 $\{a, b\} \neq \{c, d\}$）。

請回傳一個包含所有好整數的陣列，並將陣列**遞增排序**。

*(數學科普：這些數字被稱為計程車數 Taxicab numbers，最小的計程車數為 1729。)*

### 限制條件
- $1 \le n \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-09：初次提交 (枚舉法與 Hash Map 計數)](#2026-04-09-初次提交)

---

## 💡 2026-04-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題如果單純針對每個數字去拆解立方根會非常耗時，正確的做法是**主動枚舉並建表**。
我們需要決定枚舉的邊界。因為 $n \le 10^9$，假設數字 $x$ 由 $i^3 + j^3$ 組成，那麼 $i$ 或 $j$ 的最大極限值就是 $\lfloor\sqrt[3]{10^9}\rfloor = 1000$。
此外，$1000^3 = 10^9$，剛好落在 32-bit signed integer 的最大值 ($2 \times 10^9$) 以內，因此在計算過程中完全不需要擔心整數溢位 (Integer Overflow) 的問題。
我們只需使用雙層迴圈枚舉所有可能的 $i$ 與 $j$ ($i \le j$)，將計算出的立方和記錄在 Hash Map 中統計頻率。只要該和出現次數大於等於 2，即為題目所求的「好整數」。

### 🛠️ 解題思路 (Approach)
1. **頻率統計**：
   - 宣告 `unordered_map<int, int> mp` 用於記錄每一個立方和的出現次數。
   - 外層迴圈 `i` 從 1 枚舉至 1000。先計算 `cnt = i * i * i`。如果 `cnt > n`，代表後續的組合必定超過 $n$，提早中斷外層迴圈。
   - 內層迴圈 `j` 從 `i` 開始枚舉至 1000（確保不重複計算如 $\{1, 12\}$ 與 $\{12, 1\}$ 的排列）。計算 `cnt2 = j * j * j`。
   - 若 `cnt + cnt2 > n`，代表 $j$ 再變大也沒有意義，提早中斷內層迴圈。
   - 將 `cnt + cnt2` 的頻率加一：`++mp[cnt + cnt2]`。
2. **收集結果**：
   - 宣告結果陣列 `rt`。
   - 遍歷 `mp`，若某個和的出現次數 `cnt >= 2`，將該和推入 `rt`。
3. **排序回傳**：將 `rt` 進行遞增排序後回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(K^2 + R \log R)$。其中 $K = \lfloor\sqrt[3]{N}\rfloor \le 1000$，$R$ 是最終找到的「好整數」數量。雙層迴圈最多執行約 $\frac{1000 \times 1000}{2} = 5 \times 10^5$ 次，Hash Map 操作為 $\mathcal{O}(1)$。最終排序數量極少，整體時間常數極小，執行速度極快。
- **空間複雜度**: $\mathcal{O}(K^2)$。Hash Map `mp` 最多需要儲存約 $5 \times 10^5$ 種不同的和的狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> findGoodIntegers(int n) {
        unordered_map<int, int> mp;
        
        // 1000^3 = 10^9，保證不會有變數超過 1000
        for(int i = 1; i <= 1000; ++i) {
            int cnt = i * i * i;
            if(cnt > n) break; // 提早剪枝
            
            // j 從 i 開始，避免重複統計相反順序的組合
            for(int j = i; j <= 1000; ++j) {
                int cnt2 = j * j * j;
                if(cnt + cnt2 > n) break; // 超過 n 提早剪枝
                
                ++mp[cnt + cnt2];
            }
        }
        
        vector<int> rt;
        // 收集組合次數 >= 2 的計程車數
        for(auto [val, cnt] : mp) {
            if(cnt >= 2) {
                rt.push_back(val);
            }
        }
        
        // 題目要求遞增排序
        sort(rt.begin(), rt.end());
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