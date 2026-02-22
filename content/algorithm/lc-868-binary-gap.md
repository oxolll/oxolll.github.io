---
title: "LeetCode 868. Binary Gap"
date: 2026-02-22T11:45:00+08:00
lastmod: 2026-02-22T11:45:00+08:00
difficulty: 1307
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Bit Manipulation", "Math"]
keywords: ["LC868", "1307", "Easy", "Binary Gap", "位元運算"]
description: "LeetCode 第 868 題：Binary Gap。難度評分：1307。探討如何利用位元運算與右移操作來尋找二進位表示中相鄰 1 的最大距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 868](https://leetcode.com/problems/binary-gap/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1307)</span>   
> **核心主題**：`Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個正整數 `n`，請找出並回傳其二進位表示中，任何兩個**相鄰的 1** 之間的**最大距離**。如果沒有任何兩個相鄰的 1，則回傳 0。
如果兩個 1 之間只有 0 將它們隔開（也可能沒有 0），我們就稱這兩個 1 是相鄰的。兩個 1 之間的距離，是指它們位元位置的絕對差。例如，`"1001"` 中的兩個 1 距離為 3。

### 範例
- **Input**: `n = 22` → **Output**: `2`
- **Explanation**: 
  22 的二進位是 `"10110"`。
  第一對相鄰的 1 在 `"10110"` 中的距離為 2。
  第二對相鄰的 1 在 `"10110"` 中的距離為 1。
  答案取最大值，即為 2。

### 限制條件
- $1 \le n \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-02-22：初次提交 (位元右移與距離追蹤)](#2026-02-22-初次提交)

---

## 💡 2026-02-22 初次提交

### 🎯 直覺 (Intuition)
要在二進位中找兩個 `1` 的距離，最直覺的方式就是把數字不斷「向右位移 (`>> 1`)」。我們只需要先過濾掉最右邊那些沒用的 `0`，找到第一個 `1` 當作起點，接著繼續右移，同時用一個計數器計算走過的步數，每當再次遇到 `1` 時，就結算一次最大距離，並將計數器歸零重新開始。

### 🛠️ 解題思路 (Approach)
1. **濾除尾部的 0**：
   使用 `while` 迴圈不斷將 `n` 右移，直到 `n` 的最末位數為 `1`（即 `(n & 1) == 1`），確立計算起點。
2. **計算相鄰距離**：
   準備變數 `dis` 記錄距離（起點設為 0），與變數 `rt` 記錄最大值。
   開始迴圈，每次讓 `n` 右移 1 位，同時 `++dis`。
   若發現最末位又出現 `1`，代表找到了一對相鄰的 `1`，此時更新最大距離 `rt = max(rt, dis)`，並將 `dis` 重置為 0。
3. **⚠️ C++ 優先級陷阱**：
   實作時要特別注意 `n & 1 == 1` 在 C++ 中會被優先判斷 `1 == 1`，變成 `n & 1`。雖然邏輯上恰好成立，但若改成其他數字將會產生 Bug，應養成加上括號 `(n & 1) == 1` 或是直接寫 `n & 1` 的好習慣。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log n)$。$n$ 最大為 $10^9$，也就是最多只需右移約 30 次。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int binaryGap(int n) {
        int rt = 0;
        int dis = 0; // 統一從 0 開始計數
        
        // 1. 濾除右側連續的 0，直到找到第一個 1
        // ⚠️ 注意 C++ 優先級：== 高於 &，需使用括號保護
        while(n > 0 && (n & 1) != 1) {
            n >>= 1;
        }

        // 2. 開始計算距離
        while(n > 0) {
            if((n & 1) == 1) {
                rt = max(rt, dis);
                dis = 0; // 遇到 1，重新開始計算距離
            }
            ++dis; // 距離遞增 (包含當前這位數)
            n >>= 1;
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