---
title: "LeetCode 2515. Shortest Distance to Target String in a Circular Array"
date: 2026-04-15T18:00:00+08:00
lastmod: 2026-04-15T18:00:00+08:00
difficulty: 1367
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "string", "simulation"]
keywords: ["LC2515", "1367", "Easy", "Shortest Distance", "Circular Array", "Bidirectional Search"]
description: "LeetCode 第 2515 題：Shortest Distance to Target String in a Circular Array。難度評分：1367。探討如何利用雙向擴展搜尋 (Bidirectional Search) 與取模運算 (Modulo Arithmetic) 在環狀陣列中以 O(N) 時間複雜度快速尋找目標字串的最短距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2515](https://leetcode.com/problems/shortest-distance-to-target-string-in-a-circular-array/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1367)</span>  
> **核心主題**：`Array` $\cdot$ `String` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串陣列 `words` (索引從 0 開始) 和一個目標字串 `target`。這個陣列代表一個**環狀陣列**，也就是說，陣列的第一個元素連接在最後一個元素的後面，而最後一個元素連接在第一個元素的前面。
- 你可以從 `words[i]` 向右移動到 `words[(i + 1) % n]`。
- 你也可以從 `words[i]` 向左移動到 `words[(i - 1 + n) % n]`。

給定一個起始索引 `startIndex`，請回傳從 `startIndex` 移動到字串值等於 `target` 的元素所需的**最短距離**。如果 `words` 中不存在 `target`，請回傳 `-1`。

### 限制條件
- $1 \le words.length \le 100$
- $1 \le words[i].length \le 100$
- $words[i]$ 和 `target` 僅由小寫英文字母組成。
- $0 \le startIndex < words.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-15：初次提交 (雙向擴展與模數運算)](#2026-04-15-初次提交)

---

## 💡 2026-04-15 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在環狀陣列中尋找目標的最短距離，比起全域掃描再計算距離，**雙向擴展搜尋 (Bidirectional Search)** 是更直觀且平均效能更優的策略。
我們從 `startIndex` 開始，同時派出兩個指標：
- 指標 $j$ 負責向右走：利用 $(j + 1) \pmod n$ 處理向右越界繞回頭部。
- 指標 $i$ 負責向左走：利用 $(i - 1 + n) \pmod n$ 處理向左越界繞回尾部（加上 $n$ 是為了避免 C++ 中負數取模的問題）。
因為兩個指標是同步每次移動 1 步，所以**第一次**遇到 `target` 時，當下的移動步數就保證是最小的距離，可以直接提早結束迴圈。

### 🛠️ 解題思路 (Approach)
1. **初始化**：
   - 取得陣列長度 `n`。
   - 設定左右兩個指標 `i` 與 `j`，初始位置皆為 `startIndex`。
   - 設定迴圈控制變數 `loop = n + 1`。這是一個巧妙的設計，保證迴圈能走完陣列的所有可能距離，且利用它的遞減可以反推出當前步數。
2. **雙向搜尋**：
   - 使用 `while(loop--)` 進行迴圈控制，每次進入迴圈時 `loop` 已遞減 1。
   - 檢查當前的 `words[i]` 或 `words[j]` 是否等於 `target`。若是，代表找到了最近的目標，直接 `break` 中斷迴圈。
   - 若尚未找到，指標 $i$ 向左移動一格 `(i - 1 + n) % n`，指標 $j$ 向右移動一格 `(j + 1) % n`。
3. **結果判斷**：
   - 若迴圈正常結束沒有被 `break`（即 `loop < 0`），代表整個陣列都沒有 `target`，回傳 `-1`。
   - 若被 `break` 提早中斷，此時的距離恰好等於 `n - loop`。*(例如：初始 `loop = n + 1`，判斷第 0 步時 `loop` 變為 `n`，距離為 $n - n = 0$；判斷第 1 步時 `loop` 變為 `n - 1`，距離為 $n - (n - 1) = 1$)*。回傳該結果即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times L)$。其中 $N$ 為陣列的長度，$L$ 為字串的平均長度。最差情況下（目標不存在或位於對向最遠處），需要走訪 $\frac{N}{2}$ 次，每次比較字串需 $\mathcal{O}(L)$。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個指標與計數器變數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int closestTarget(vector<string>& words, string target, int startIndex) {
        int n = words.size();
        // i 負責向左擴展，j 負責向右擴展
        int i = startIndex, j = startIndex;
        
        // 迴圈次數設為 n + 1，保證能覆蓋所有距離
        int loop = n + 1;
        while(loop--) {
            // 只要任一方向遇到 target，即為最短距離，直接中斷
            if(words[i] == target || words[j] == target) {
                break;
            }
            // 利用模數運算處理環狀陣列的越界問題
            i = (i - 1 + n) % n; // 向左走
            j = (j + 1) % n;     // 向右走
        }

        // 若 loop < 0 代表找不到 target
        // 巧妙利用 n - loop 計算出實際移動的步數
        return loop < 0 ? -1 : n - loop;
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