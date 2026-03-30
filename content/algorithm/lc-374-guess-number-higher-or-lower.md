---
title: "LeetCode 374. Guess Number Higher or Lower"
date: 2026-03-30T16:50:00+08:00
lastmod: 2026-03-30T16:50:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "binary search", "interactive"]
keywords: ["LC374", "Easy", "N/A", "Guess Number", "Binary Search"]
description: "LeetCode 第 374 題：Guess Number Higher or Lower。難度評分：Easy (N/A)。探討如何透過呼叫互動式 API，利用二分搜尋法 (Binary Search) 以 O(log N) 時間複雜度快速猜中目標數字。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 374](https://leetcode.com/problems/guess-number-higher-or-lower/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Binary Search` $\cdot$ `Interactive`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
我們正在玩一個猜數字遊戲。遊戲規則如下：
我會從 `1` 到 `n` 之間挑選一個數字。你必須猜測我挑選了哪個數字。
每次你猜錯，我都會告訴你，我挑選的數字比你的猜測值大還是小。

你可以呼叫一個預先定義好的 API `int guess(int num)`，它會回傳三個可能的結果：
- `-1`：你猜的數字比我挑選的數字大 (即 `picked < num`)。
- `1`：你猜的數字比我挑選的數字小 (即 `picked > num`)。
- `0`：恭喜！你猜對了 (即 `picked == num`)。

請回傳我挑選的數字。

### 限制條件
- $1 \le n \le 2^{31} - 1$
- $1 \le picked \le n$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-30：初次提交 (標準二分搜尋法)](#2026-03-30-初次提交)

---

## 💡 2026-03-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道最基礎的二分搜尋 (Binary Search) 教科書題型。
因為數字範圍從 `1` 到 `n` 是嚴格遞增的序列，我們可以直接設定左右邊界，每次取中點 `mid` 作為猜測值。依據 API 回傳的結果，我們可以將搜尋範圍精準縮小一半，從而在對數時間內找到目標值。

### 🛠️ 解題思路 (Approach)
1. 宣告左邊界 `l = 1`，右邊界 `r = n`。
2. 使用 `while(l <= r)` 進行二分搜尋。
3. 計算中點 `mid = l + (r - l) / 2`。（使用減法形式可避免 `l + r` 產生整數溢位）。
4. 呼叫 `guess(mid)` 取得結果：
   - 若回傳 `0`，代表猜中，直接回傳 `mid`。
   - 若回傳 `-1`，代表猜測值太大，目標在左半部，更新 `r = mid - 1`。
   - 若回傳 `1`，代表猜測值太小，目標在右半部，更新 `l = mid + 1`。
5. 理論上迴圈必定會找到答案並回傳。若迴圈結束仍未找到，回傳 `-1` 作為防呆機制。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log N)$。每次猜測都會將搜索範圍縮小一半。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用常數變數維護左右邊界。

### 💻 程式碼實作 (C++)
```cpp
/** * Forward declaration of guess API.
 * @param  num   your guess
 * @return 	     -1 if num is higher than the picked number
 *			      1 if num is lower than the picked number
 * otherwise return 0
 * int guess(int num);
 */

class Solution {
public:
    int guessNumber(int n) {
        int l = 1, r = n;
        
        while(l <= r) {
            // 防止 (l + r) 產生 int 溢位
            int mid = l + (r - l) / 2;
            
            int res = guess(mid);
            if(res == 0) return mid;
            
            // 猜測值太大，目標在左側
            if(res == -1) {
                r = mid - 1;
            } 
            // 猜測值太小，目標在右側
            else {
                l = mid + 1;
            }
        }

        return -1;
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