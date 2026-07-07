---
title: "LeetCode 3754. Concatenate Non-Zero Digits and Multiply by Sum I"
date: 2026-07-07T21:19:00+08:00
lastmod: 2026-07-07T21:19:00+08:00
difficulty: 1247
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "simulation"]
keywords: ["LC3754", "Easy", "1247", "Concatenate Non-Zero Digits", "字串與數字轉換", "數學模擬", "位值操作"]
description: "LeetCode 第 3754 題：Concatenate Non-Zero Digits and Multiply by Sum I。難度評分：1247。探討如何捨棄傳統的字串轉換，利用純數學的除法與模數運算，由左至右精準提取非零數字並完成數值重組與計算。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3754](https://leetcode.com/problems/concatenate-non-zero-digits-and-multiply-by-sum-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1247)</span>  
> **核心主題**：`Math` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `n`。
請你將 `n` 中的所有「非零 (Non-Zero)」數字依原本的順序提取出來，組合成一個新的數字。
接著，計算這些非零數字的「總和」。
最後，回傳「組合出的新數字」與「數字總和」的乘積。

### 範例
- **Input**: `n = 1052`
- **Output**: `1216`
- **Explanation**: 
  1. 忽略零之後，提取出的非零數字組合為 `152`。
  2. 這些非零數字的總和為 $1 + 5 + 2 = 8$。
  3. 最終結果為 $152 \times 8 = 1216$。

- **Input**: `n = 2000`
- **Output**: `4`
- **Explanation**:
  1. 非零數字組合為 `2`。
  2. 總和為 $2$。
  3. 結果為 $2 \times 2 = 4$。

### 限制條件
- $1 \le n \le 10^9$
</details>

---

## 📝 歷次打卡與演算法進化
- [2026-07-07：初次提交 (純數學位值萃取法)](#2026-07-07-初次提交)

---

## 💡 2026-07-07 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理數字提取問題，最直觀的作法通常是呼叫標準函式庫將其轉為字串 (`to_string`)，濾除 `'0'` 後再轉回數字。但這種作法會產生額外的字串記憶體配置與轉型開銷。

**核心破局點：由左至右的數學提取 (Left-to-Right Digit Extraction)**
為追求效能與程式碼的純粹性，我們可以直接使用數學運算。
考慮到 $n$ 的上限為 $10^9$，我們可以設定一個 `base = 10^9`。
透過 `n / base` 即可知道當前最高位數的值：
1. **過濾 0 的絕妙巧思**：
   如果目前的 `base` 碰上了原數字中的 `0`，或者 `base` 還比 `n` 大（前導零），那麼 `n / base` 的結果必定為 `0`。
   利用 `if(n / base)` 作為判斷條件，就能完美地在一個動作中同時過濾掉「前導零」與「內部零」。
2. **數值重組與剝離**：
   一旦確認 `cur = n / base` 不為零：
   - 將新數字 `rt` 往左推一位 (`rt *= 10`) 並加上 `cur`。
   - 累加總和 `s += cur`。
   - 將剛提取完的最高位數從原數字 `n` 中剝除 (`n -= cur * base`)。
3. 每回合結束後縮小除數 `base /= 10`，直到 `base` 歸零為止。

### 🛠️ 解題思路 (Approach)
1. 宣告 `base = 1e9` 準備進行最高位提取。
2. 宣告 `rt = 0` (組合出的新數字) 與 `s = 0` (非零數字總和)。
3. 使用 `while(base)` 進行迴圈掃描：
   - 若 `n / base` 大於 0（代表該位數為非零數字）：
     - 取出該位數 `cur = n / base`。
     - 將 `cur` 附加到 `rt` 的尾端。
     - 從 `n` 中扣除該最高位的值。
     - 將 `cur` 加入總和 `s`。
   - `base /= 10` 進入下一個較低的位數。
4. 迴圈結束後，回傳 `rt * s`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_{10} n)$。對於 $10^9$ 以內的數字，迴圈固定執行約 10 次，屬於 $\mathcal{O}(1)$ 的絕對常數時間，效能極高。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了少數幾個 `long long` 和 `int` 型別的變數，不依賴任何字串或陣列記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long sumAndMultiply(int n) {
        int base = 1e9; // 配合測資上限設定的初始基底
        long long rt = 0; // 重組後的新數字
        long long s = 0;  // 非零數字總和
        
        while(base) {
            // 這個 if 判斷完美過濾了「前導零」以及「數字中間的 0」
            if(n / base) {
                int cur = n / base;
                
                rt *= 10;
                rt += cur;
                
                // 剝除最高位數，例如 1052 - (1 * 1000) = 52
                n -= cur * base;
                
                s += cur;
            }
            base /= 10; // 基底降級，檢查下一位數
        }

        return rt * s;
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