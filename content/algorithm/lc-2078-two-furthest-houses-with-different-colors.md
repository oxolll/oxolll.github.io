---
title: "LeetCode 2078. Two Furthest Houses With Different Colors"
date: 2026-04-20T14:00:00+08:00
lastmod: 2026-04-20T14:00:00+08:00
difficulty: 1240
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy"]
keywords: ["LC2078", "Easy", "1240", "Two Furthest Houses", "Greedy"]
description: "LeetCode 第 2078 題：Two Furthest Houses With Different Colors。難度評分：1240。探討如何利用貪婪法 (Greedy) 的極值性質，將 O(N^2) 的暴力配對優化為 O(N) 的雙向單次走訪。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2078](https://leetcode.com/problems/two-furthest-houses-with-different-colors/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1240)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
一條街上有 $n$ 棟房子，排列在一個一維陣列中。給你一個長度為 $n$ 的整數陣列 `colors`，其中 `colors[i]` 代表第 $i$ 棟房子的顏色。
請回傳兩棟**顏色不同**房子之間的**最大距離**。
第 $i$ 棟與第 $j$ 棟房子之間的距離為 $|i - j|$。

### 限制條件
- $n == colors.length$
- $2 \le n \le 100$
- $0 \le colors[i] \le 100$
- 測資保證陣列中至少存在兩種不同的顏色。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-20：初次提交 (貪婪法雙向掃描)](#2026-04-20-初次提交)

---

## 💡 2026-04-20 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題若使用雙層迴圈兩兩比較，時間複雜度為 $\mathcal{O}(N^2)$。但我們可以利用**貪婪策略 (Greedy)** 進行降維打擊。
思考最大距離的極端情況：
要讓距離最大，構成這個最大距離的兩棟房子，**必然有其中一棟位於陣列的最邊緣**（也就是索引 $0$ 或索引 $n-1$）。
- 假設最左邊 `colors[0]` 與最右邊 `colors[n-1]` 顏色不同，那答案直接就是最大可能距離 $n-1$。
- 如果它們顏色相同，那麼最大距離要嘛是「從左端點 $0$ 到右側最遠的不同色房子」，要嘛是「從右端點 $n-1$ 到左側最遠的不同色房子」。

因此，我們只需要做兩次掃描，分別固定左端點與右端點，即可在 $\mathcal{O}(N)$ 的時間內找到全局最佳解！

### 🛠️ 解題思路 (Approach)
1. **固定左端點 (`colors[0]`)**：
   - 從陣列最右側 ($n-1$) 開始往左掃描。
   - 只要遇到顏色與 `colors[0]` 不同的房子，這就是固定左端點能得到的最大距離。計算距離並更新全域最大值 `mx`。（實務上此時可直接 `break` 中斷迴圈優化常數時間）。
2. **固定右端點 (`colors.back()`)**：
   - 從陣列最左側 ($0$) 開始往右掃描。
   - 只要遇到顏色與 `colors.back()` 不同的房子，計算距離並更新全域最大值 `mx`。
3. **回傳結果**：回傳兩次檢查後得到的最大距離 `mx`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。最差情況下需要走訪陣列兩次。若加上 `break` 優化，平均時間常數會非常小。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個變數進行距離運算。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxDistance(vector<int>& colors) {
        int left = colors[0];
        int mx = 0;
        
        // 1. 固定最左邊的房子，從右往左找第一個顏色不同的
        for(int i = colors.size() - 1; i > 0; --i) {
            if(left != colors[i]) {
                mx = max(mx, i);
                // 💡 優化：找到的第一個絕對是距離最遠的，可直接 break
                break; 
            }
        }

        // 2. 固定最右邊的房子，從左往右找第一個顏色不同的
        int right = colors.back();
        for(int i = 0; i < colors.size(); ++i) {
            if(right != colors[i]) {
                mx = max(mx, int(colors.size()) - 1 - i);
                // 💡 優化：找到的第一個絕對是距離最遠的，可直接 break
                break; 
            }
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