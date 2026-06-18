---
title: "LeetCode 1344. Angle Between Hands of a Clock"
date: 2026-06-18T13:50:00+08:00
lastmod: 2026-06-18T13:50:00+08:00
difficulty: 1324
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "geometry"]
keywords: ["LC1344", "Medium", "1324", "Angle Between Hands", "時鐘夾角", "數學", "幾何"]
description: "LeetCode 第 1344 題：Angle Between Hands of a Clock。難度評分：1324。探討如何利用基礎幾何與比例運算，精準計算時針與分針的動態偏移角度，並以 O(1) 的極致時間複雜度求出兩指針間的最小夾角。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1344](https://leetcode.com/problems/angle-between-hands-of-a-clock/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1324)</span>  
> **核心主題**：`Math` $\cdot$ `Geometry`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個整數 `hour` 和 `minutes`，請你計算並回傳時鐘盤面上，時針與分針之間形成的 **較小夾角**。
答案的誤差範圍在 $10^{-5}$ 以內均視為正確。

### 範例
- **Input**: `hour = 12, minutes = 30`
- **Output**: `165.0`
- **Explanation**: 
  分針指在 30 分 (180度)。時針指在 12 和 1 之間的一半 (15度)。夾角為 165度。

- **Input**: `hour = 3, minutes = 30`
- **Output**: `75.0`

- **Input**: `hour = 3, minutes = 15`
- **Output**: `7.5`

### 限制條件
- $1 \le hour \le 12$
- $0 \le minutes \le 59$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-18：初次提交 (數學幾何比例計算)](#2026-06-18-初次提交)

---

## 💡 2026-06-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道純粹的數學計算題，核心在於正確釐清時針與分針的「移動速率」。
時鐘盤面總共 360 度。

**1. 分針的絕對角度**
- 盤面有 60 分鐘，因此分針每分鐘移動 $360 / 60 = 6$ 度。
- 分針角度公式：$M_{angle} = minutes \times 6$。

**2. 時針的絕對角度**
- 盤面有 12 小時，因此時針每小時移動 $360 / 12 = 30$ 度。
- **關鍵點 (偏移量)**：時針會隨著分鐘的增加而緩慢前進。經過的分鐘佔了一小時的比例為 $\frac{minutes}{60}$，因此時針會在當前小時的基礎上，額外偏移 $\frac{minutes}{60} \times 30$ 度。
- 此外，12 點鐘方向等同於 0 度起始點，因此我們需要用 `hour % 12` 進行歸零處理。
- 時針角度公式：$H_{angle} = (hour \bmod 12) \times 30 + \left( \frac{minutes}{60.0} \right) \times 30$。

**3. 求最小夾角**
- 求出兩針的絕對角度差值 `diff = abs(H_angle - M_angle)`。
- 時鐘上的夾角有內角與外角之分。題目要求「較小」的夾角，因此結果即為 `min(diff, 360 - diff)`。

### 🛠️ 解題思路 (Approach)
1. 宣告常數 `d = 360` 方便閱讀，計算出時針每小時度數 `h = 360 / 12` 與分針每分鐘度數 `m = 360 / 60`。
2. 利用 `hour % 12` 將時針基底歸零，乘上 `h` 得到基礎角度。
3. 利用 `minutes / 60.0 * h` 計算時針的微小偏移角度（注意除以 `60.0` 確保浮點數除法不丟失精度）。
4. 減去分針角度 `minutes * m`，並取絕對值得到 `diff`。
5. 回傳 `diff` 與 `360 - diff` 中的最小值。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。僅進行數次常數級別的基礎數學運算。
- **空間複雜度**: $\mathcal{O}(1)$。只宣告了少數幾個區域浮點數變數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    double angleClock(int hour, int minutes) {
        double d = 360;
        double h = d / 12; // 時針每小時走 30 度
        double m = d / 60; // 分針每分鐘走 6 度

        // 計算絕對角度差值
        // (hour % 12) * h       : 時針基礎角度 (12 點視為 0)
        // minutes / 60.0 * h    : 時針隨分鐘推進的額外偏移量
        // minutes * m           : 分針的絕對角度
        double diff = abs((hour % 12) * h + minutes / 60.0 * h - minutes * m);
        
        // 取內角與外角中較小的值
        return min(diff, 360 - diff);
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