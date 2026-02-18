---
title: "LeetCode 67. Add Binary"
date: 2026-02-15T16:30:00+08:00
lastmod: 2026-02-15T16:30:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Math", "String", "Simulation"]
keywords: ["LC67", "Binary Addition", "Simulation", "Carry", "Fundamental"]
description: "LeetCode 第 67 題：Add Binary。經典的二進位字串相加模擬，練習處理進位 (Carry) 與字串操作的基礎題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 67](https://leetcode.com/problems/add-binary/)  
> **難度等級**：<span style="color: #00af9b; font-weight: bold;">N/A (Easy)</span>  
> **核心主題**：`Math` $\cdot$ `String` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個二進位字串 `a` 和 `b`，請計算它們的和，並以二進位字串的形式回傳。

### 範例
- **Input**: `a = "11"`, `b = "1"`
- **Output**: `"100"`
- **Input**: `a = "1010"`, `b = "1011"`
- **Output**: `"10101"`

### 限制條件
- $1 \le a.length, b.length \le 10^4$
- 字串僅包含字元 `'0'` 或 `'1'`。
- 除了數字 0 本身，字串不會以 0 開頭。
</details>

---

## 📝 歷次打卡與更新
- [2026-02-15：初次提交 (Simulation)](#2026-02-15-初次提交)

---

## 💡 2026-02-15 初次提交

### 直覺 (Intuition)
這題要求將兩個二進位字串相加，這其實跟我們小學學的「直式加法」完全一樣，只是進位制從 10 變成了 2。
1.  **對齊**：從最右邊（最低有效位 LSB）開始往左算。
2.  **相加**：對應位數相加，若有進位（Carry）則加到下一位。
3.  **進位**：若相加結果 $\ge 2$，則產生進位。

### 解題思路 (Approach)
我們可以模擬上述過程，利用指標從字串尾端向前移動，並維護一個 `carry` 變數。

1.  **初始化**：取得字串 `a`, `b` 的長度，初始化 `carry = 0` (程式碼中用 `x` 表示)。
2.  **處理重疊部分**：從尾端同時遍歷兩個字串，計算 `sum = digit_a + digit_b + carry`。
    * 更新進位：`x = sum / 2`
    * 紀錄當前位：`rt += (sum % 2)`
3.  **處理剩餘部分**：若兩字串長度不同，繼續處理較長字串的剩餘位數（同樣要加上 `carry`）。
4.  **最終進位**：若遍歷結束後 `carry` 仍不為 0，記得補上最後一個 `'1'`。
5.  **反轉結果**：因為我們是從 LSB (尾) 拼接到 MSB (頭)，所以目前的字串是反的，最後需要 `reverse` 修正。

### 📊 複雜度分析
- **時間複雜度**: $O(\max(N, M))$。
  我們只需遍歷較長的字串一次，其中 $N$ 和 $M$ 分別為 `a` 和 `b` 的長度。`reverse` 操作也是線性的。
- **空間複雜度**: $O(\max(N, M))$。
  使用了一個字串 `rt` 來儲存結果。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        int len1 = a.size();
        int len2 = b.size();
        int x = 0; // carry
        string rt = "";
        
        int i = 0;
        // 1. 處理兩個字串重疊的部分
        for(; i < min(len1, len2); ++i) {
            // 計算當前位的總和：a 的當前位 + b 的當前位 + 進位
            // 注意：字串索引是從 0 開始，所以倒數第 i 個位置是 len - 1 - i
            int sum = (a[len1 - 1 - i] - '0') + (b[len2 - 1 - i] - '0') + x;
            
            x = sum / 2;         // 更新進位
            rt += (sum % 2) + '0'; // 串接當前位的結果
        }

        // 2. 若 a 比較長，處理 a 剩餘的部分
        while(i < len1) {
            int sum = (a[len1 - 1 - i] - '0') + x;
            x = sum / 2;
            rt += (sum % 2) + '0';
            ++i;
        }
        
        // 3. 若 b 比較長，處理 b 剩餘的部分
        while(i < len2) {
            int sum = (b[len2 - 1 - i] - '0') + x;
            x = sum / 2;
            rt += (sum % 2) + '0';
            ++i;
        }
        
        // 4. 若最後還有進位，補上 '1'
        if(x != 0) rt += '1';
        
        // 5. 因為是從尾巴加到頭，所以結果要反轉
        reverse(rt.begin(), rt.end());

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