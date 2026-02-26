---
title: "LeetCode 1404. Number of Steps to Reduce a Number in Binary Representation to One"
date: 2026-02-26T18:25:00+08:00
lastmod: 2026-02-26T18:25:00+08:00
difficulty: 1396
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Bit Manipulation", "Simulation"]
keywords: ["LC1404", "1396", "Medium", "Binary String", "Carry"]
description: "LeetCode 第 1404 題：Number of Steps to Reduce a Number in Binary Representation to One。難度評分：1396。探討如何利用字串操作直接模擬二進位的進位與平移，避免大數溢位。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1404](https://leetcode.com/problems/number-of-steps-to-reduce-a-number-in-binary-representation-to-one/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1396)</span>   
> **核心主題**：`String` $\cdot$ `Bit Manipulation` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個以字串 `s` 表示的二進位數字，請回傳將其化簡為 `1` 所需要的步驟數。化簡的規則如下：
- 如果當前數字為**偶數**，則將其除以 2。
- 如果當前數字為**奇數**，則將其加上 1。

題目保證最終一定能將數字化簡為 1。

### 範例
- **Input**: `s = "1101"` → **Output**: `6`
- **Explanation**: 
  "1101" 是奇數 13。
  步驟 1: 13 + 1 = 14 (二進位 "1110")
  步驟 2: 14 / 2 = 7 (二進位 "111")
  步驟 3: 7 + 1 = 8 (二進位 "1000")
  步驟 4: 8 / 2 = 4 (二進位 "100")
  步驟 5: 4 / 2 = 2 (二進位 "10")
  步驟 6: 2 / 2 = 1 (二進位 "1")

### 限制條件
- $1 \le s.length \le 500$
- `s` 只包含字元 `'0'` 或 `'1'`
- `s` 的第一個字元必定為 `'1'`
</details>

---

## 📝 歷次打卡與更新
- [2026-02-26：初次提交 (直接透過字串模擬進位與位移)](#2026-02-26-初次提交)

---

## 💡 2026-02-26 初次提交

### 🎯 直覺 (Intuition)
由於字串長度最高達 500，直接轉換成整數型態（即便是 `unsigned long long`）也絕對會溢位。
因此，我們必須直接在「二進位字串」上操作：
- **除以 2**：等同於二進位向右平移一位，也就是直接把字串最後一個 `'0'` 砍掉。
- **加 1**：這會觸發「進位 (Carry)」。進位會把結尾所有的連續 `'1'` 都變成 `'0'`，然後進到下一個位數，直到遇到第一個 `'0'` 並將其翻轉為 `'1'`。

### 🛠️ 解題思路 (Approach)
利用字串模擬整個過程，每次迴圈處理結尾的字元，直到字串長度只剩 1：
1. 每執行一次動作，步驟計數 `rt` 就 `+1`。
2. **遇到 `'0'`**：代表是偶數，直接 `pop_back()` 移除它，繼續下一輪。
3. **遇到 `'1'`**：代表是奇數，執行加 1 操作。
   - 此時會產生進位，結尾的連續 `'1'` 都會變成 `'0'`，緊接著這些 `'0'` 都要被除以 2（也就是被移除）。
   - 所以我們直接用內層的 `while` 迴圈，把所有連續的 `'1'` 移除，並且每移除一個就增加一次操作步數（代表將其除以 2 移除的步驟）。
   - 當所有連續的 `'1'` 被移出後，進位會遇到一個 `'0'`，我們將其改為 `'1'`。如果字串已經空了（代表原本全是 `'1'`），則直接令字串為 `"1"`（代表進位溢出形成新的最高位）。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$，其中 $N$ 為字串的長度。每個字元最多被檢查與 `pop_back` 一次，整體操作是線性的。
- **空間複雜度**: $\mathcal{O}(1)$。因為我們直接在原字串 `s` 上進行 `pop_back()` 與字元修改，不需要額外配置陣列空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numSteps(string s) {
        int rt = 0;
        
        // 直到字串化簡到只剩 "1"
        while(s.size() != 1) {
            ++rt;
            
            // 偶數情況：直接除以 2 (砍掉結尾的 0)
            if(s.back() == '0') {
                s.pop_back();
                continue;
            }

            // 奇數情況：加 1 觸發進位
            // 連續的 1 會因為進位變成 0，接著就會被除以 2 砍掉
            while(!s.empty() && s.back() == '1') {
                ++rt;
                s.pop_back();
            }

            // 進位遇到第一個 0，將其變成 1；若字串清空代表需新增最高位 1
            if(!s.empty()) {
                s.back() = '1';
            } else {
                s = "1";
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