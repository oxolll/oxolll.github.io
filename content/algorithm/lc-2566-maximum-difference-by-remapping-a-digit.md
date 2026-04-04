---
title: "LeetCode 2566. Maximum Difference by Remapping a Digit"
date: 2026-04-06T10:00:00+08:00
lastmod: 2026-04-06T10:00:00+08:00
difficulty: 1396
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "greedy", "string"]
keywords: ["LC2566", "1396", "Easy", "Maximum Difference", "Remapping a Digit", "Greedy"]
description: "LeetCode 第 2566 題：Maximum Difference by Remapping a Digit。難度評分：1396。探討如何利用貪婪策略，藉由修改最高位數的特徵數字來構造可能的最大值與最小值，並以 O(log N) 的時間複雜度求得兩者差異。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2566](https://leetcode.com/problems/maximum-difference-by-remapping-a-digit/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1396)</span>  
> **核心主題**：`Math` $\cdot$ `Greedy` $\cdot$ `String`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `num`。你知道丹尼 (Danny) 曾經將這個數字中的**恰好一個數字**替換成另一個數字（範圍為 0 到 9），藉此構造出一個最大值和一個最小值。
- 在構造最大值時，選擇一個數字 `d1` 並將 `num` 中所有出現的 `d1` 都替換成另一個數字。
- 在構造最小值時，選擇一個數字 `d2` 並將 `num` 中所有出現的 `d2` 都替換成另一個數字。

這兩次替換是各自獨立的。請回傳可能的最大值與最小值之間的**差值**。

### 限制條件
- $1 \le num \le 10^8$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-06：初次提交 (數字拆解與貪婪替換)](#2026-04-06-初次提交)

---

## 💡 2026-04-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求透過替換單一數字（包含其所有出現的位置）來最大化兩次變換後的差異。
根據貪婪策略 (Greedy Strategy)：
- **構造最大值 (`mx`)**：為了讓數字變得最大，我們應該盡可能改變最高位數。我們從最高位開始向低位掃描，找到第一個**不是 `9`** 的數字，將其設為目標 `mxtar`，並把所有 `mxtar` 替換為 `9`。
- **構造最小值 (`mm`)**：為了讓數字變得最小，我們同樣從最高位開始，找到第一個**不是 `0`** 的數字，將其設為目標 `mmtar`，並把所有 `mmtar` 替換為 `0`。由於給定的數字不包含前導零，因此 `mmtar` 實際上永遠都會是該數字的最高位數字。

### 🛠️ 解題思路 (Approach)
1. **數字拆解**：
   - 建立陣列 `digits`。
   - 使用 `num % 10` 取出最低位，並 `num /= 10` 推進，將數字依序放入 `digits` 中。（注意：陣列中的排序是低位數在前面、最高位數在陣列尾端）。
2. **尋找替換目標**：
   - 宣告 `mxtar = -1` 與 `mmtar = -1`。
   - 陣列從後往前掃描（即從數字的最高位往最低位掃描）。
   - 若 `mxtar` 尚未設定且當前數字不為 `9`，將其記錄為 `mxtar`。
   - 若 `mmtar` 尚未設定且當前數字不為 `0`，將其記錄為 `mmtar`。
   - 當兩者皆已找到時，可提早中斷迴圈 (`break`)。
3. **重建數字並計算結果**：
   - 宣告變數 `mx = 0` 與 `mm = 0`。
   - 再一次從後往前遍歷 `digits`。
   - 每前進一位，將 `mx` 與 `mm` 分別乘以 10 進行進位。
   - 若當前數字等於 `mxtar`，則在 `mx` 加上 `9`；否則加上原數字。
   - 若當前數字等於 `mmtar`，則在 `mm` 加上 `0`；否則加上原數字。
4. **回傳結果**：回傳 `mx - mm`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log_{10} N)$。其中 $N$ 是給定的整數 `num`。我們只需對數字的位數進行三次遍歷，由於 $N \le 10^8$，迴圈最多執行 9 次，整體時間可視為 $\mathcal{O}(1)$ 常數時間。
- **空間複雜度**: $\mathcal{O}(\log_{10} N)$。需要一個陣列 `digits` 來儲存數字的各個位數，空間亦受限於常數級別，可視為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minMaxDifference(int num) {
        vector<int> digits;
        
        // 1. 將整數拆解成各位數 (存入陣列後，digits[0] 為個位數)
        while(num) {
            digits.push_back(num % 10);
            num /= 10;
        }
        
        int mxtar = -1, mmtar = -1;
        int n = digits.size();
        
        // 2. 從最高位數開始尋找替換目標
        for(int i = n - 1; i >= 0; --i) {
            if(mxtar == -1 && digits[i] != 9) {
                mxtar = digits[i];
            }
            if(mmtar == -1 && digits[i] != 0) {
                mmtar = digits[i];
            }

            if(mmtar != -1 && mxtar != -1) break;
        }

        int mx = 0, mm = 0;
        
        // 3. 重新建構出最大值與最小值
        for(int i = n - 1; i >= 0; --i) {
            mx *= 10;
            mx += (mxtar == digits[i] ? 9 : digits[i]);
            
            mm *= 10;
            mm += (mmtar == digits[i] ? 0 : digits[i]);
        }
        
        return mx - mm;
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