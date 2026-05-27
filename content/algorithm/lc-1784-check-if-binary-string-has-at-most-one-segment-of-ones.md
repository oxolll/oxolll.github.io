---
title: "LeetCode 1784. Check if Binary String Has at Most One Segment of Ones"
date: 2026-05-27T14:15:00+08:00
lastmod: 2026-05-27T14:15:00+08:00
difficulty: 1206
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "simulation"]
keywords: ["LC1784", "Easy", "1206", "Binary String", "字串模擬", "區段計數"]
description: "LeetCode 第 1784 題：Check if Binary String Has at Most One Segment of Ones。難度評分：1206。探討如何利用狀態變數精準統計字串中的連續區段數量，以及如何透過特徵子串觀察法達成一行極簡解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1784](https://leetcode.com/problems/check-if-binary-string-has-at-most-one-segment-of-ones/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1206)</span>  
> **核心主題**：`String` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個二進位字串 `s`，該字串 **不含前導零**（即字串必定以 `'1'` 開頭）。
如果字串中 **最多只有一個** 由連續的 `'1'` 組成的區段，請回傳 `true`；否則回傳 `false`。

### 範例
- **Input**: `s = "1001"`
- **Output**: `false`
- **Explanation**: 字串中包含兩個由連續 `'1'` 組成的區段。

- **Input**: `s = "110"`
- **Output**: `true`

### 限制條件
- $1 \le s.length \le 100$
- `s[i]` 僅為 `'0'` 或 `'1'`
- `s[0]` 為 `'1'`
</details>

---

## 📝 歷次打卡與更新
- [2026-05-27：初次提交 (連續區段計數法)](#2026-05-27-初次提交)

---

## 💡 2026-05-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道標準的字串遍歷模擬題。我們的目標是計算由 `'1'` 組成的「孤島」或「區段」數量，並驗證該數量是否 $\le 1$。

**核心破局點：狀態切換與邊界結算**
我們可以透過一個變數 `cnt1` 來記錄當前是否正在累積 `'1'`。
當我們遇到 `'0'` 時，這代表一個潛在的區段被切斷了。此時如果 `cnt1 > 0`，就代表我們剛走完一個完整的 `'1'` 區段，將總區段數 `seg` 加 1，並把 `cnt1` 歸零。

> **⚠️ 邊界防禦：** 最容易出錯的地方在於，如果字串的結尾剛好是 `'1'`（例如 `"1011"`），那麼最後一個區段在迴圈內是不會遇到 `'0'` 來觸發結算的。因此，在迴圈結束後，必須補上一句 `if(cnt1) ++seg;` 來收尾。

### 🛠️ 解題思路 (Approach)
1. 初始化 `cnt1 = 0` (記錄當前連續的 1) 與 `seg = 0` (記錄總區段數)。
2. 遍歷字串 `s` 中的每一個字元 `c`：
   - 若 `c == '1'`：增加 `cnt1`。
   - 若 `c == '0'`：判斷 `cnt1` 是否大於 0。若是，代表剛結束一個區段，`++seg` 並重置 `cnt1 = 0`。
3. 迴圈結束後，檢查是否有殘留的 `cnt1`，若有則 `++seg`。
4. 回傳 `seg <= 1` 的布林值。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 為字串的長度，我們僅對字串進行了一次線性掃描。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了兩個整數變數，無需額外空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool checkOnesSegment(string s) {
        int cnt1 = 0; // 記錄當前連續 1 的數量 (作為狀態旗標)
        int seg = 0;  // 記錄 1 的連續區段總數
        
        for(auto c : s) {
            if(c == '1') {
                ++cnt1;
            } else {
                // 當遇到 0 且前面有累積的 1 時，進行區段結算
                if(cnt1) {
                    ++seg;
                }
                cnt1 = 0; // 狀態重置
            }
        }
        
        // 迴圈結束後，處理尾部可能殘留的 1 區段
        if(cnt1) ++seg;

        return seg <= 1;
    }
};
```

---

## 💡 延伸思維：極簡特徵觀察法 (One-Liner)
面試時，面試官可能會問：「能不能把程式碼寫得更短？」
這題其實藏著一個非常有趣的數學特徵：題目保證字串開頭一定是 `'1'`，也就是說，**最少一定會有一個 `'1'` 的區段**。

既然如此，什麼情況下會出現「超過一個區段」？
答案是：**只要我們在遇到 `'0'` 之後，又看到了 `'1'`。**
換句話說，如果字串中出現了子字串 `"01"`，就代表必定發生了區段的斷裂與重啟，區段數必然 $\ge 2$。

因此，整題可以壓縮成一行：判斷字串中是否**不包含** `"01"`！

```cpp
class Solution {
public:
    bool checkOnesSegment(string s) {
        // 如果找不到 "01"，代表 1 的區段從未斷裂過
        return s.find("01") == string::npos;
    }
};
```
*這個解法的時間與空間複雜度同樣為 $\mathcal{O}(N)$ 與 $\mathcal{O}(1)$，但在程式碼簡潔度上達到了極致。*

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