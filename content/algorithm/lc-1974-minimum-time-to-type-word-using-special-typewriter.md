---
title: "LeetCode 1974. Minimum Time to Type Word Using Special Typewriter"
date: 2026-04-07T14:00:00+08:00
lastmod: 2026-04-07T14:00:00+08:00
difficulty: 1364
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "greedy", "math"]
keywords: ["LC1974", "1364", "Easy", "Special Typewriter", "Greedy", "Circular Distance"]
description: "LeetCode 第 1974 題：Minimum Time to Type Word Using Special Typewriter。難度評分：1364。探討如何利用貪婪策略與環形陣列最短距離公式，以 O(N) 時間複雜度計算在環形打字機上打出目標字串的最少時間。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1974](https://leetcode.com/problems/minimum-time-to-type-word-using-special-typewriter/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1364)</span>  
> **核心主題**：`String` $\cdot$ `Greedy` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
有一個特殊的打字機，上面有一個包含 26 個小寫英文字母的環形字盤。指標最初指向字元 `'a'`。
每一秒鐘，你可以執行以下三種操作之一：
1. 將指標**順時針**移動一個字元。
2. 將指標**逆時針**移動一個字元。
3. 鍵入指標當前指向的字元。

給定一個字串 `word`，請回傳使用該特殊打字機打出 `word` 所需要的**最少**時間（單位：秒）。

### 限制條件
- $1 \le word.length \le 100$
- `word` 僅由小寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-07：初次提交 (環形最短距離與貪婪法)](#2026-04-07-初次提交)

---

## 💡 2026-04-07 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在環形字盤上移動，為了使花費時間最少，這是一個標準的貪婪策略 (Greedy Strategy)：每次移動到下一個字元時，都選擇**順時針**與**逆時針**兩者中距離較短的那一條路徑。
在長度為 26 的環形結構中，假設指標當前位置為 $A$，目標位置為 $B$。兩點之間的絕對距離為 $D = |A - B|$。
- 直接移動的距離（不跨越 `'a'` 與 `'z'` 的邊界）為 $D$。
- 繞越邊界移動的距離為 $26 - D$。
因此，指標移動的最短步數必定為 $\min(D, 26 - D)$。

此外，無論移動距離為何，每一次「鍵入」字元皆需固定消耗 1 秒。因此總時間即為「所有字元的最短移動時間總和」加上「字串的總長度」。

### 🛠️ 解題思路 (Approach)
1. **初始化時間**：宣告變數 `rt = word.size()`。因為字串中有幾個字元，就必定需要幾秒鐘來執行「鍵入」動作，我們可以直接將其預先算入總時間。
2. **追蹤指標**：宣告變數 `last = 0`，代表指標初始指向 `'a'` (索引 0)。
3. **計算最短距離**：
   - 遍歷字串 `word` 中的每一個字元 `c`。
   - 計算目標字元的索引 `idx = c - 'a'`。
   - 為了計算絕對差值，分別取得當前位置與目標位置的較大值 `mx = max(idx, last)` 與較小值 `mm = min(idx, last)`。
   - 直接距離為 `mx - mm`，繞越邊界距離為 `mm + 26 - mx`。
   - 將兩者之中的最小值累加至總時間 `rt` 中。
4. **狀態更新**：將指標更新至目標位置 `last = idx`。
5. **回傳結果**：迴圈結束後，回傳總耗時 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串 `word` 的長度。只需對字串進行一次線性掃描，每次計算移動距離為 $\mathcal{O}(1)$ 常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個整數變數，無需額外配置記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minTimeToType(string word) {
        // 每個字元都需要 1 秒鐘來「鍵入」，預先將字串長度計入總時間
        int rt = word.size();
        
        // 指標初始停留在 'a'，其對應的 0-based 索引為 0
        int last = 0;
        
        for(auto c : word) {
            int idx = c - 'a';
            
            // 計算當前位置與目標位置的大小關係，以推導距離
            int mx = max(idx, last);
            int mm = min(idx, last);
            
            // 取「直接移動 (mx - mm)」與「繞越邊界移動 (mm + 26 - mx)」的最短距離
            rt += min(mx - mm, mm + 26 - mx);
            
            // 更新指標位置
            last = idx;
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