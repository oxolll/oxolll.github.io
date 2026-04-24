---
title: "LeetCode 2833. Furthest Point From Origin"
date: 2026-04-24T13:00:00+08:00
lastmod: 2026-04-24T13:00:00+08:00
difficulty: 1294
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "math", "greedy"]
keywords: ["LC2833", "Easy", "1294", "Furthest Point From Origin", "Greedy", "Math"]
description: "LeetCode 第 2833 題：Furthest Point From Origin。難度評分：1294。探討如何利用貪婪策略 (Greedy)，透過統計固定方向與未定方向的數量，以 O(N) 線性時間複雜度極速算出距離原點的最遠距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2833](https://leetcode.com/problems/furthest-point-from-origin/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1294)</span>  
> **核心主題**：`String` $\cdot$ `Math` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串 `moves`，長度為 $n$，僅由字元 `'L'`、`'R'` 和 `'_'` 組成。該字串表示你在數線上從原點 $0$ 開始的一系列移動。
- `'L'` 代表向左移動一個單位。
- `'R'` 代表向右移動一個單位。
- `'_'` 代表可以選擇向左 **或** 向右移動一個單位。

請回傳執行完所有移動後，你能到達距離原點的**最遠距離**。

### 限制條件
- $1 \le moves.length \le 50$
- `moves` 僅包含字元 `'L'`、`'R'` 和 `'_'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-24：初次提交 (頻率統計與貪婪數學解)](#2026-04-24-初次提交)

---

## 💡 2026-04-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求距離原點的「最遠距離」。
根據**貪婪策略 (Greedy Strategy)**，為了讓最終位置盡可能遠離原點，我們應該將所有可自由選擇方向的 `'_'`，**全部集中朝同一個方向移動**。

具體來說：
1. 固定的移動 `'L'` 和 `'R'` 會互相抵銷。它們產生的「淨位移」絕對值為 $|L - R|$。
2. 自由的移動 `'_'` 應該全部加入這個佔優勢的方向，以擴大淨位移。
因此，最遠距離的數學公式極度簡潔：**固定方向的淨位移 + 自由步數**，即 $|L - R| + count(\text{'_'})$。

### 🛠️ 解題思路 (Approach)
1. 宣告三個變數 `l`、`r`、`a`，分別記錄 `'L'`、`'R'` 和 `'_'` 的出現次數。
2. 走訪字串 `moves`，根據字元累加對應的計數器。
3. 迴圈結束後，計算並回傳 `abs(l - r) + a`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串 `moves` 的長度。只需走訪字串一次進行計數。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個整數變數來儲存計數狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int furthestDistanceFromOrigin(string moves) {
        int r = 0, l = 0, a = 0;
        
        // 統計各個方向的步數
        for(auto c : moves) {
            if(c == 'R') ++r;
            else if(c == 'L') ++l;
            else ++a;
        }

        // 最遠距離 = 固定移動的淨位移絕對值 + 所有可自由選擇的步數
        return abs(l - r) + a;
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