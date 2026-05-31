---
title: "LeetCode 2126. Destroying Asteroids"
date: 2026-06-01T12:18:00+08:00
lastmod: 2026-06-01T12:18:00+08:00
difficulty: 1334
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting"]
keywords: ["LC2126", "Medium", "1334", "Destroying Asteroids", "貪心演算法", "排序", "整數溢位"]
description: "LeetCode 第 2126 題：Destroying Asteroids。難度評分：1334。探討如何利用貪心策略與排序，確保以最優順序吸收小行星質量，並透過型別轉換與提早結束 (Early Exit) 迴避整數溢位陷阱。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2126](https://leetcode.com/problems/destroying-asteroids/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1334)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `mass`，代表一顆行星的初始質量。同時給你一個整數陣列 `asteroids`，其中 `asteroids[i]` 是第 `i` 顆小行星的質量。

你可以任意安排行星與小行星相撞的順序。
如果行星的質量 **大於或等於** 小行星的質量，那麼小行星會被摧毀，且行星的質量會 **增加** 該小行星的質量。
否則，行星會被摧毀。

如果你可以摧毀 **所有** 的小行星，請回傳 `true`，否則回傳 `false`。

### 範例
- **Input**: `mass = 10, asteroids = [3,9,19,5,21]`
- **Output**: `true`
- **Explanation**: 
  1. 質量 10 撞擊質量 3，新質量 = 10 + 3 = 13。
  2. 質量 13 撞擊質量 5，新質量 = 13 + 5 = 18。
  3. 質量 18 撞擊質量 9，新質量 = 18 + 9 = 27。
  4. 質量 27 撞擊質量 19，新質量 = 27 + 19 = 46。
  5. 質量 46 撞擊質量 21，新質量 = 46 + 21 = 67。
  所有小行星皆被摧毀。

- **Input**: `mass = 5, asteroids = [4,9,23,4]`
- **Output**: `false`
- **Explanation**:
  - 行星可以撞擊質量 4，變成 9。
  - 接著可以撞擊另一個 4，變成 13，或撞擊 9 變成 18。
  - 但無論如何，質量最高只能達到 18，遇到 23 時必然會被摧毀。

### 限制條件
- $1 \le mass \le 10^5$
- $1 \le asteroids.length \le 10^5$
- $1 \le asteroids[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-01：初次提交 (貪心策略與溢位防禦)](#2026-06-01-初次提交)

---

## 💡 2026-06-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的 **貪心演算法 (Greedy Algorithm)**。
為了最大化我們生存的機率，最優的決策永遠是 **「優先挑戰自己打得過的對手」**。透過先吸收較小的小行星來壯大自身質量，我們就能在面對大型小行星時擁有更多的籌碼。

**核心破局點 1：排序**
既然要挑小的打，直接對 `asteroids` 陣列進行升冪排序，由小到大依序比對即可。

**核心破局點 2：隱藏的整數溢位 (Integer Overflow)**
題目的限制條件看似不大，`mass` 和 `asteroids[i]` 最大都只有 $10^5$。但如果 $10^5$ 顆小行星的質量全是 $10^5$，總質量會達到 $10^{10}$！
這超過了 32-bit 有號整數 (`int`) 的上限 (約 $2 \times 10^9$)。如果不將質量變數宣告為 `long long`，在相加的過程中會發生溢位變成負數，導致誤判。

**核心破局點 3：提早結束 (Early Exit)**
既然小行星的最大質量不會超過 $10^5$（或 `INT_MAX`），一旦我們行星的質量突破了這個天花板，後面的小行星絕對不可能對我們造成威脅，直接回傳 `true` 即可省下後續的運算。

---

### 🛠️ 解題思路 (Approach)
1. **排序**：將 `asteroids` 陣列由小到大排序。
2. **初始化**：宣告一個 `long long` 型別的變數 `M = mass` 以防溢位。
3. **貪心掃描**：遍歷排序後的小行星 `m`：
   - 若 `M < m`，代表連當前最小的小行星都打不贏，直接宣告失敗，回傳 `false`。
   - 否則，打敗它並吸收質量：`M += m`。
   - *(優化)*：若 `M` 超過一個極大值（如 `INT_MAX`，或題目的極限 $10^5$），代表已經處於無敵狀態，提早回傳 `true`。
4. 若順利走完迴圈，代表全部摧毀，回傳 `true`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。主要的時間瓶頸在於對長度為 $N$ 的陣列進行排序。後續的線性掃描只需 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(\log N)$。為 C++ `std::sort` 遞迴呼叫所需的堆疊空間。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool asteroidsDestroyed(int mass, vector<int>& asteroids) {
        // 1. 貪心策略：先從小顆的開始吸收
        sort(asteroids.begin(), asteroids.end());
        
        // 2. 防禦整數溢位：總和可能高達 10^10，超過 int 極限
        long long M = mass;
        
        for(auto& m : asteroids) {
            // 打不贏當前的小行星，宣告失敗
            if(M < m) return false;
            
            // 吸收質量
            M += m;
            
            // 3. 提早結束優化：當質量超過極限，後續絕對無敵
            if(M > INT_MAX) return true; 
        }

        return true;
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