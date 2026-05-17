---
title: "LeetCode 3932. Count K-th Roots in a Range"
date: 2026-05-18T01:25:00+08:00
lastmod: 2026-05-18T01:25:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "binary-search", "enumeration"]
keywords: ["LC3932", "Medium", "K-th Roots", "二分搜尋", "完美冪次", "溢位處理"]
description: "LeetCode 第 3932 題：Count K-th Roots in a Range。難度評分：Medium。探討如何利用數學上高次冪快速增長的特性，配合邊界特判與二分搜尋，高效計算區間內的完美 $k$ 次方數數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3932](https://leetcode.com/problems/count-k-th-roots-in-a-range/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Binary Search` $\cdot$ `Enumeration`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定三個正整數 `l`、`r` 和 `k`。
請你計算在閉區間 $[l, r]$ 內，有多少個整數 $x$ 滿足：存在一個非負整數 $b$，使得：
$$x = b^k$$
換句話說，需要統計區間內有多少個數字是完整的「完美 $k$ 次方數」。

### 範例
- **Input**: `l = 4, r = 25, k = 2`
- **Output**: `4`
- **Explanation**: 在 $[4, 25]$ 之間的完美平方數（$k=2$）有：$4 (2^2)$, $9 (3^2)$, $16 (4^2)$, $25 (5^2)$，共 4 個。

- **Input**: `l = 1, r = 10, k = 3`
- **Output**: `2`
- **Explanation**: 在 $[1, 10]$ 之間的完美立方數（$k=3$）有：$1 (1^3)$ 和 $8 (2^3)$，共 2 個。

### 限制條件
- $1 \le l \le r \le 10^9$
- $1 \le k \le 30$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-18：初次提交 (特判優化與主動生成二分法)](#2026-05-18-初次提交)

---

## 💡 2026-05-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題如果直接對區間內的每個數字去開 $k$ 次根號，會遇到浮點數精度誤差的問題。因此，更好的逆向思維是：**主動生成所有的完美 $k$ 次方數，再統計落入區間的個數。**

**核心破局點：**
1. **高次冪的極速增長**：
   當 $k \ge 2$ 時，底數 $b$ 的上限為 $\sqrt{r}$。在本題最大值 $r = 10^9$ 的情況下：
   - 當 $k = 2$ 時，$b \le \sqrt{10^9} \approx 31622$。
   - 當 $k = 3$ 時，$b \le \sqrt[3]{10^9} = 1000$。
   - 當 $k \ge 30$ 時，$b$ 只需要列舉 $0, 1, 2$（因為 $2^{30} > 10^9$）。
   這意味著，**需要生成的完美 $k$ 次方數數量其實非常少**，可以直接暴力生成。

2. **邊界特判 ($k = 1$)**：
   當 $k = 1$ 時，任何整數 $x$ 都可以表示為 $x^1$。此時若走生成邏輯，會試圖生成 $10^9$ 個數字導致 TLE/MLE。因此必須直接特判回傳區間長度 $r - l + 1$。

3. **二分搜尋定位**：
   將所有生成的合法數存入單調遞增陣列 `tmp` 中，利用 `std::lower_bound` 尋找第一個大於等於 $l$ 的位置，剩餘到末尾的元素數量即為答案。

---

### 🛠️ 解題思路 (Approach)
1. **攔截特判**：若 `k == 1`，直接回傳 `r - l + 1`。
2. **安全冪次計算**：實作 `cal(base, t)`，在內部用 `long long` 累乘。若中途數值 $\ge 10^9+1$，為防整數溢位並加快速度，立即截斷回傳固定大值。
3. **動態生成**：從底數 `i = 0` 開始不斷計算 `i^k`，若結果大於 `r` 則切斷循環，否則推入陣列 `tmp`。
4. **區間過濾**：在 `tmp` 中二分搜尋第一個 $\ge l$ 的索引 $L$，最終答案即為 `tmp.size() - L`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\sqrt[k]{r})$。
  - 當 $k = 1$ 時，時間為 $\mathcal{O}(1)$。
  - 當 $k \ge 2$ 時，生成陣列長度最大為 $\sqrt{10^9} \approx 31622$。主要耗時在於生成迴圈與二分搜尋，總執行時間不超過數萬次運算，極速通過。
- **空間複雜度**: $\mathcal{O}(\sqrt[k]{r})$。需要一個動態陣列儲存生成的開根號底數對應值，最大空間開銷約為 30KB。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 安全計算 base^t，引入防溢位截斷機制
    int cal(int base, int t) {
        long long b = 1;
        while (t--) {
            b *= base;
            // 超出題目極限範圍值時，立刻早停返回，避免 long long 發生溢位
            if (b >= 1e9 + 1) return 1e9 + 1;
        }
        return b;
    }

    int countKthRoots(int l, int r, int k) {
        // 關鍵核心特判：當 k = 1 時，所有整數皆符合條件，直接計算區間內整數個數
        if (k == 1) return r - l + 1;

        vector<int> tmp;
        // 從 0 開始枚舉所有可能的底數
        for (int i = 0; ; ++i) {
            int x = cal(i, k);
            // 當產生的完美 k 次方數超過右邊界 r，代表後續底數皆不合法，退出迴圈
            if (x > r) break;
            tmp.push_back(x);
        }
        
        // 使用 lower_bound 在有序陣列中尋找第一個大於等於左邊界 l 的位置
        auto L = lower_bound(tmp.begin(), tmp.end(), l) - tmp.begin();
        
        // 總生成數減去小於 l 的個數，即為落在 [l, r] 區間內的合格總數
        return tmp.size() - L;
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