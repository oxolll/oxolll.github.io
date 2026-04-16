---
title: "LeetCode 397. Integer Replacement"
date: 2026-04-16T16:10:00+08:00
lastmod: 2026-04-16T16:10:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "dfs", "recursion", "bit-manipulation", "memoization"]
keywords: ["LC397", "Medium", "Integer Replacement", "DFS", "Memoization", "Bit Manipulation"]
description: "LeetCode 第 397 題：Integer Replacement。完整收錄兩種 DFS 解法：利用基本遞迴模擬並透過 long long 防溢位，以及利用 Hash Map 記憶化搜尋 (Memoization) 結合位元運算路徑壓縮，將時間複雜度嚴格優化至 O(log N)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 397](https://leetcode.com/problems/integer-replacement/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `DFS` $\cdot$ `Recursion` $\cdot$ `Bit Manipulation` $\cdot$ `Memoization`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個正整數 $n$，你可以執行以下兩種操作：
1. 如果 $n$ 是偶數，將它替換為 $n / 2$。
2. 如果 $n$ 是奇數，你可以將它替換為 $n + 1$ 或是 $n - 1$。

請問最少需要多少次操作才能將 $n$ 變成 $1$？

### 限制條件
- $1 \le n \le 2^{31} - 1$
</details>

---

## 📝 歷次打卡與解法演進
這道題目的難點在於處理 $n = 2^{31} - 1$ 時的整數溢位陷阱，以及如何控制遞迴展開的規模。
- **[解法一：DFS 模擬與防溢位](#解法一-dfs-模擬與防溢位)**：最直觀的狀態展開，強調型別安全。
- **[解法二：記憶化搜尋與路徑壓縮](#解法二-記憶化搜尋-memoization--路徑壓縮)**：利用 Hash Map 去除重複計算，並透過位元運算將兩步操作合併，達成最優的 $\mathcal{O}(\log N)$ 效能。

---

## 💡 解法一：DFS 模擬與防溢位

### 🎯 演算法分析
這題要求尋找最短操作次數，使用深度優先搜尋 (DFS) 模擬狀態轉移。
**⚠️ 核心陷阱 (Integer Overflow)**：題目的 $n$ 最大可達 $2^{31} - 1$ (`INT_MAX`)。如果 $n$ 為 `INT_MAX` (奇數)，演算法會嘗試計算 $n + 1$。在 32-bit signed integer 中，`INT_MAX + 1` 會發生溢位變成負數。因此，在進行遞迴前，必須將當前數值轉型為 64 位元的 `long long`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int integerReplacement(int n) {
        int mm = INT_MAX;
        dfs(n, 0, mm);
        return mm;
    }
    
private:
    // 使用 long long cur 來防止 n = INT_MAX 時，執行 cur+1 產生 Integer Overflow
    void dfs(long long cur, int cnt, int& mm) {
        if(cur == 1) {
            mm = min(mm, cnt);
            return;
        }

        ++cnt;
        
        // cur & 1 為真代表是奇數
        if(cur & 1) {
            dfs(cur + 1, cnt, mm);
            dfs(cur - 1, cnt, mm);
        } else {
            dfs(cur / 2, cnt, mm);
        }
    }
};
```
- **時間複雜度**: $\mathcal{O}(2^{\log N})$ (無記憶化)。由於偶數必除以 2，分支增長受限，實測可通過。
- **空間複雜度**: $\mathcal{O}(\log N)$。取決於遞迴深度。

---

## 💡 解法二：記憶化搜尋 (Memoization) + 路徑壓縮

### 🎯 演算法分析
為避免重複計算相同的數字狀態（例如 $10 \to 5$ 與 $9 \to 10 \to 5$ 都會遇到 $5$），引入 `unordered_map` 作為快取 (Cache)。
更巧妙的是**「兩步合併 (Path Compression)」**：
當 `cur` 為奇數時，加一或減一後必定為偶數，意味著下一步絕對是除以 2。與其遞迴兩層，不如直接算好除以 2 的結果，並將步數一次加 2 (`step = 2 + min(...)`)。
- **分支一 (減 1 後除 2)**：$\frac{cur - 1}{2}$ 在整數運算中完全等價於 `cur >> 1`。
- **分支二 (加 1 後除 2)**：$\frac{cur + 1}{2}$ 在二進位位移中，可巧妙利用 `(cur + 2) >> 1` 等價求出。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 記憶化快取：記錄每個數字所需的最少步數
    unordered_map<int, int> mp;
    
    int integerReplacement(int n) {
        return dfs(n);
    }
    
private:
    // dfs 直接回傳化簡至 1 所需的最小步數
    int dfs(long long cur) {
        if(cur == 1) return 0;

        // 若已計算過，直接查表回傳 O(1)
        if(mp.find(cur) != mp.end()) return mp[cur];

        int step;
        if(cur & 1) {
            // 奇數：加減 1 後必為偶數，直接預判下一步的除以 2
            // 步數一次 +2。
            // cur >> 1 等價於 (cur - 1) / 2
            // (cur + 2) >> 1 等價於 (cur + 1) / 2
            step = 2 + min(dfs(cur >> 1), dfs((cur + 2) >> 1));
        } else {
            // 偶數：步數 +1，數值右移 1 位 (除以 2)
            step = 1 + dfs(cur >> 1);
        }

        // 存入快取並回傳
        return mp[cur] = step;
    }
};
```
- **時間複雜度**: $\mathcal{O}(\log N)$。有了記憶化搜尋，每個數字狀態只會被計算一次。
- **空間複雜度**: $\mathcal{O}(\log N)$。Hash Map 儲存的狀態數量與遞迴深度皆為對數級別。

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