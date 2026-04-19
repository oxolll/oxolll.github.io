---
title: "LeetCode 3894. Traffic Signal Color"
date: 2026-04-12T10:00:00+08:00
lastmod: 2026-04-12T10:00:00+08:00
difficulty: 1222
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "simulation"]
keywords: ["LC3894", "Easy", "1222", "Traffic Signal Color", "Control Flow"]
description: "LeetCode 第 3894 題：Traffic Signal Color。難度評分：Easy (1222)。探討如何利用基礎的條件控制 (Control Flow) 結構，嚴謹地處理紅綠燈時序的點狀與區間邊界判斷。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3894](https://leetcode.com/problems/traffic-signal-color/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1222)</span>  
> **核心主題**：`Math` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `timer`，表示交通信號燈上的剩餘時間（以秒為單位）。
信號燈遵循以下規則：
- 如果 `timer == 0`，信號燈為 `"Green"`
- 如果 `timer == 30`，信號燈為 `"Orange"`
- 如果 `30 < timer <= 90`，信號燈為 `"Red"`

請返回信號燈的當前狀態。如果上述條件均不滿足，請返回 `"Invalid"`。

### 限制條件
- $0 \le timer \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-12：初次提交 (基礎條件判斷與區間模擬)](#2026-04-12-初次提交)

---

## 💡 2026-04-12 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道測驗基礎 **控制流程 (Control Flow)** 與 **邊界條件 (Boundary Conditions)** 判斷的入門模擬題。
雖然邏輯極度簡單，但在實務開發與競賽中，這類題目要求的是對 `if` 條件式互斥性的掌握，以及確保不遺漏任何邊界狀態的嚴謹性。
題目明確劃分了三種合法狀態（兩種「點狀命中」、一種「區間涵蓋」），我們只需依序進行條件篩選，並將所有未命中的情況統一拋入預設的例外處理 (Fallback) 即可。

### 🛠️ 解題思路 (Approach)
1. **點狀命中 (Exact Match)**：
   - 優先處理單一秒數的絕對判斷：檢查 `timer == 0` 與 `timer == 30`。
2. **區間涵蓋 (Range Match)**：
   - 使用邏輯 AND 運算子 `&&` 確保數值嚴格落在區間內：`timer > 30 && timer <= 90`。
3. **例外防禦 (Fallback / Exception Handling)**：
   - 如果代碼執行到最後仍未觸發任何 `return`，代表輸入數值（例如 $5$ 或 $100$）不屬於任何合法的燈號邏輯，此時直接回傳 `"Invalid"` 作為防禦機制。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。僅執行常數次數的條件判斷，執行時間不隨任何變數增長。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數記憶體空間回傳字串。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string trafficSignal(int timer) {
        // 1. 精準命中特定秒數 (點狀判斷)
        if(timer == 0) return "Green";
        if(timer == 30) return "Orange";
        
        // 2. 數值區間涵蓋判斷
        if(timer > 30 && timer <= 90) return "Red";
        
        // 3. 例外處理：未落入上述任何條件的剩餘時間
        return "Invalid";
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