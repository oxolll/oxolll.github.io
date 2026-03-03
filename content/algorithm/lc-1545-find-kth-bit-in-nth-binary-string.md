---
title: "LeetCode 1545. Find Kth Bit in Nth Binary String"
date: 2026-03-03T19:21:00+08:00
lastmod: 2026-03-03T19:21:00+08:00
difficulty: 1479
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Simulation"]
keywords: ["LC1545", "1479", "Medium", "Simulation", "Reverse", "Invert"]
description: "LeetCode 第 1545 題：Find Kth Bit in Nth Binary String。難度評分：1479。探討如何利用字串模擬 (Simulation) 構造反轉與翻轉的二進位字串，快速尋找第 K 個位元。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1545](https://leetcode.com/problems/find-kth-bit-in-nth-binary-string/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1479)</span>   
> **核心主題**：`String` $\cdot$ `Simulation` $\cdot$ `Divide and Conquer`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個正整數 $n$ 和 $k$，二進位字串 $S_n$ 的構造規則如下：
- $S_1 = \text{"0"}$
- $S_i = S_{i-1} + \text{"1"} + \text{reverse}(\text{invert}(S_{i-1}))$ 針對所有 $i > 1$

其中，`+` 代表字串串接，$\text{reverse}(x)$ 會將字串反轉，$\text{invert}(x)$ 會將字串中的 `'0'` 變成 `'1'`、`'1'` 變成 `'0'`。

例如，前四個字串為：
- $S_1 = \text{"0"}$
- $S_2 = \text{"011"}$
- $S_3 = \text{"0111001"}$
- $S_4 = \text{"011100110110001"}$

請回傳 $S_n$ 中的第 $k$ 個位元（$k$ 從 1 開始計算）。

### 範例
- **Input**: `n = 3, k = 1` → **Output**: `"0"`
- **Explanation**: $S_3$ 為 `"0111001"`。第 1 個位元為 `"0"`。

- **Input**: `n = 4, k = 11` → **Output**: `"1"`
- **Explanation**: $S_4$ 為 `"011100110110001"`。第 11 個位元為 `"1"`。

### 限制條件
- $1 \le n \le 20$
- $1 \le k \le 2^n - 1$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-03：初次提交 (字串模擬與動態建構)](#2026-03-03-初次提交)

---

## 💡 2026-03-03 初次提交

### 🎯 直覺 (Intuition)
看到這種序列生成的題目，第一步是評估**資料規模**。題目給定 $n \le 20$，字串長度會以 $2^n - 1$ 的幅度指數成長，但就算到了極限的 $S_{20}$，長度也不過是 $2^{20} - 1 \approx 10^6$。
在 C++ 中，配置並操作一個百萬長度的字串只需要幾毫秒，記憶體開銷也僅約 1 MB。與其花費心力去推導 $\mathcal{O}(N)$ 的分治法 (Divide & Conquer) 遞迴公式，直接實作「暴力模擬 (Simulation)」絕對是最穩妥、開發速度最快的解法。

### 🛠️ 解題思路 (Approach)
1. **初始化**：宣告初始字串 `cur = "0"`。
2. **提早結束的模擬條件**：
   我們不需要真的把整個 $S_n$ 都建構出來。只要目前的字串長度大於或等於 $k$ (`k > cur.size()`)，我們就可以停止迴圈，這能大幅減少無謂的字串操作。
3. **字串構造**：
   在迴圈內，依循題目給定的規則：
   - 先將當前字串 `cur` 進行翻轉 (Invert)：`0` 換 `1`，`1` 換 `0`，儲存為 `back`。
   - 將 `back` 進行反轉 (Reverse)。
   - 更新字串：`cur += "1" + back`。
4. **回傳解答**：當字串長度滿足要求後，直接回傳對應的索引 `cur[k - 1]` 即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(K)$。雖然字串最大可達 $2^n - 1$，但我們利用 `cur.size() < k` 進行了 Early Exit。每次迴圈字串長度會翻倍，字串拷貝與反轉的時間正比於當前長度，總和為幾何級數，最終時間複雜度與 $k$ 呈線性關係。
- **空間複雜度**: $\mathcal{O}(K)$。用於儲存長度至少為 $k$ 的字串 `cur` 與其輔助字串 `back`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    char findKthBit(int n, int k) {
        string cur = "0";
        
        // 只要長度還不到 k，就繼續構造字串
        while(k > int(cur.size())) {
            // 1. 翻轉 (Invert)
            auto back = flip(cur);
            // 2. 反轉 (Reverse)
            reverse(back.begin(), back.end());
            // 3. 串接
            cur += "1" + back;
        }
        
        // k 是 1-indexed，轉為 0-indexed 回傳
        return cur[k - 1];
    }
    
private:
    // 將 '0' 與 '1' 互換的輔助函式
    string flip(string& s) {
        string rt = "";
        for(auto c : s) {
            rt += (c == '0' ? '1' : '0');
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