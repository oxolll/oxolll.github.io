---
title: "LeetCode 2571. Minimum Operations to Reduce an Integer to 0"
date: 2026-05-18T23:55:00+08:00
lastmod: 2026-05-18T23:55:00+08:00
difficulty: 1649
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "bit-manipulation", "greedy", "math"]
keywords: ["LC2571", "Medium", "1649", "Bit Manipulation", "Greedy", "Non-Adjacent Form", "最小操作次數"]
description: "LeetCode 第 2571 題：Minimum Operations to Reduce an Integer to 0。難度評分：1649。探討如何利用二進位低位元特徵（n & 3），結合貪心策略與 NAF（非相鄰形式）概念，在對數時間內求得將整數消減至 0 的最少加減次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2571](https://leetcode.com/problems/minimum-operations-to-reduce-an-integer-to-0/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1649)</span>  
> **核心主題**：`比特位操作 (Bit Manipulation)` $\cdot$ `貪心演算法 (Greedy)`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個正整數 `n`，你可以執行以下操作任意次數：
- 將 `n` 加上或減去一個 $2$ 的冪次（即 $2^x$，其中 $x$ 為非負整數）。

請回傳將 `n` 減少到 `0` 所需的 **最少操作次數**。

### 範例
- **Input**: `n = 39`
- **Output**: `3`
- **Explanation**: 
  - $39 + 2^0 = 40$
  - $40 - 2^3 = 32$
  - $32 - 2^5 = 0$
  共需 3 次操作。

- **Input**: `n = 54`
- **Output**: `3`
- **Explanation**: 
  - $54 + 2^1 = 56$
  - $56 - 2^3 = 48$
  - $48 - 2^4 = 32$ (或直接 $48 - 2^5 = 16$)
  - 另一種最優：$54 - 2^1 = 52 \to 52 - 2^2 = 48 \dots$ 總之最少 3 次。

### 限制條件
- $1 \le n \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-18：初次提交 (位元特徵探測貪心法)](#2026-05-18-初次提交)

---

## 💡 2026-05-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這道題本質上是在尋找一個整數的 **最小權重符號數位表示 (Minimum Weight Signed-Digit Representation)**，也就是數學上著名的 **非相鄰形式 (Non-Adjacent Form, NAF)**。我們的目標是用最少數量的 $\pm 2^x$ 項把 $n$ 湊出來。

你的解法非常精準地利用了二進位低位元的特徵來實作貪心策略。當遇到奇數時，我們只需要看二進位的最後兩位（即 $n \pmod 4$ 的值，透過 `n & 3` 取得）：

1.  **當 `n` 為偶數時 (`(n & 1) == 0`)**：
    低位元是 `0`，這代表我們不需要在這個位置進行任何加減。直接右移 `n >>= 1`，這等同於在更高位元上繼續看相對結構，不需要花費任何操作成本。
2.  **當 `n` 為奇數且結尾為 `01` 時 (`n & 3 == 1`)**：
    此時若執行加 1 (`++n`)，`01` 會變成 `10`，我們依然留下了一個 `1` 需要處理。
    但若執行減 1 (`--n`)，`01` 會直接變成 `00`！這可以一舉**清空連續的低位元**，顯然是更優的選擇。
3.  **當 `n` 為奇數且結尾為 `11` 時 (`n & 3 == 3`)**：
    此時若執行減 1 (`--n`)，`11` 會變成 `10`，留下一個 `1`。
    但若執行加 1 (`++n`)，`11` 會產生進位變成 `100`！
    **核心精髓在於進位：** 如果高位元原本也是連續的 `1`（例如 `111`），這一次加法產生的進位會引發連鎖反應，把這一整串 `1` 全部洗成 `0`（例如 `0111 + 1 = 1000`）。這就是經典的「用一次加法，把多個連續的 1 合併成一個高位的 1」，隨後再用一次減法就能全部消掉。

---

### 🛠️ 解題思路 (Approach)
1.  **外層循環**：當 `n > 0` 時持續進行處理。
2.  **偶數分流**：若最低位是 `0`，不耗費步數，直接右移 `n >>= 1`。
3.  **奇數貪心決策**：
    - 取出最後兩位元 `x = n & 3`。
    - 若 `x == 1`（二進位尾數 `01`），選擇減法 `--n`。
    - 若 `x == 3`（二進位尾數 `11`），選擇加法 `++n`。
    - 由於執行了一次加減法，操作次數 `rt` 加 1。
4.  **回傳結果**：當 `n` 被削減至 0 時，回傳 `rt`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log n)$。在每次迴圈中，如果是偶數會直接右移 1 位；如果是奇數，經過加減後必然會變成偶數，並在下一次迭代中被右移。因此，迴圈執行的次數與 `n` 的二進位位元數成正比，最大約為 17 次左右，屬於極速的 $\mathcal{O}(1)$ 常數感官時間。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了兩個整數暫存變數，不需要任何額外空間。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minOperations(int n) {
        int rt = 0; // 記錄最少加減操作次數
        
        while (n > 0) {
            // 1. 偶數情況：尾數為 0，直接右移，不計入操作次數
            if ((n & 1) == 0) {
                n >>= 1;
            } 
            // 2. 奇數情況：需要進行貪心決策
            else {
                // 檢查最後兩位元 (相當於 n % 4)
                int x = n & 3;
                
                if (x == 1) {
                    // 尾數為 ...01，減 1 可以直接清空兩位 (變成 ...00)
                    --n;
                } else {
                    // 尾數為 ...11，加 1 可以利用進位效應將連續的 1 合併
                    ++n;
                }
                // 計入一次加法或減法操作
                ++rt;
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