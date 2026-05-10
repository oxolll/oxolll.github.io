---
title: "LeetCode 3921. Score Validator"
date: 2026-05-10T17:15:00+08:00
lastmod: 2026-05-10T17:15:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "simulation", "string", "array"]
keywords: ["LC3921", "Easy", "Score Validator", "板球計分", "模擬題"]
description: "LeetCode 第 3921 題：Score Validator。難度評分：Easy。探討如何利用單次線性掃描 (Linear Scan) 處理各類運動計分事件，並精確控制比賽結束的邊界條件。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3921](https://leetcode.com/problems/score-validator/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個字串陣列 `events`，代表一場比賽（如板球）中的一系列事件。請計算最終的總分 (`score`) 和出局數 (`wickets`)。

規則如下：
- **"WD" (Wide)** 或 **"NB" (No Ball)**：總分加 1。
- **"W" (Wicket)**：出局數加 1。
- **數字字串 (如 "1", "4", "6")**：將字串代表的數字加到總分。
- **結束條件**：當出局數達到 **10** 次時，比賽立即結束，不再處理後續事件。

### 範例
- **Input**: `events = ["1", "4", "WD", "W", "6"]`
- **Output**: `[12, 1]`
- **Explanation**: 1+4 + 1(WD) + 0 + 6 = 12 分；1 次出局。

### 限制條件
- $1 \le events.length \le 1000$
- `events[i]` 可能為 "W", "WD", "NB" 或 "0"~"6" 之間的數字。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-10：初次提交 (線性模擬解法)](#2026-05-10-初次提交)

---

## 💡 2026-05-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道基礎的模擬題。由於所有的得分與出局事件都是依序發生的，我們只需要維護兩個累加器：`sc` (分數) 與 `cnt` (出局數)。

**核心邏輯：**
1. **條件判斷順序**：
   - 特殊標記（"WD", "NB", "W"）需優先於數字轉換。
   - 板球規則中，出局 ("W") 不會獲得分數。
2. **提前終止 (Early Exit)**：
   - 當 `cnt` 達到 10 時，必須立刻跳出迴圈，因為後續的事件在規則上是不被採計的。
3. **字元數值轉換**：
   - 對於數字字串，利用 `e[0] - '0'` 快速獲取整數值。

### 🛠️ 解題思路 (Approach)
1. 初始化 `sc = 0`, `cnt = 0`。
2. 遍歷 `events` 中的每個字串 `e`：
   - 若 `e` 為 "WD" 或 "NB"，`sc` 增加 1。
   - 若 `e` 為 "W"：
     - `cnt` 增加 1。
     - 檢查如果 `cnt == 10`，則執行 `break`。
   - 若為數字字串：
     - 將該字元的 ASCII 值與 '0' 相減，累加至 `sc`。
3. 回傳包含 `{sc, cnt}` 的陣列。

### 📊 複雜度分析
- **時間複雜度**: $O(N)$。其中 $N$ 是事件陣列的長度，我們僅需線性掃描一次。
- **空間複雜度**: $O(1)$。除了用於回傳的空間外，僅使用了常數個變數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> scoreValidator(vector<string>& events) {
        int sc = 0;  // 總得分 (Score)
        int cnt = 0; // 出局數 (Wickets)
        
        for(auto& e : events) {
            // 處理 Wide 或 No Ball 違規加分
            if(e == "WD" || e == "NB") {
                ++sc;
            } 
            // 處理出局事件
            else if(e == "W") {
                ++cnt;
                // 出局數達到 10 時，局數結束
                if(cnt == 10) break;
            } 
            // 處理一般得分事件 (0, 1, 2, 4, 6 等)
            else {
                // 將字元轉換為整數並累加
                sc += (e[0] - '0');
            }
        }

        return {sc, cnt};
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