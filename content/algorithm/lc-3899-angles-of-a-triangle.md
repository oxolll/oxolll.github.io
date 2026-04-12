---
title: "LeetCode 3899. Angles of a Triangle"
date: 2026-04-12T14:30:00+08:00
lastmod: 2026-04-12T14:30:00+08:00
difficulty: 15000
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "geometry"]
keywords: ["LC3899", "Medium", "N/A", "Angles of a Triangle", "Law of Cosines", "Geometry"]
description: "LeetCode 第 3899 題：Angles of a Triangle。難度評分：Medium (N/A)。探討如何利用餘弦定理 (Law of Cosines) 計算三角形內角，並透過浮點數箝制 (Clamping) 技巧避免 acos 產生 NaN 錯誤，最後以 O(1) 時間複雜度求得所有內角。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3899](https://leetcode.com/problems/angles-of-a-triangle/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Geometry`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 3 的整數陣列 `sides`，代表三條線段的長度。
請判斷這三條線段是否能構成一個有效的三角形。
- 如果無法構成有效三角形，請回傳一個空陣列 `[]`。
- 如果可以構成，請計算該三角形的三個內角角度（單位為**度 Degree**），並將這三個角度以**遞增排序**後的回傳。

### 限制條件
- $sides.length == 3$
- $1 \le sides[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-12：初次提交 (餘弦定理與浮點數精確度保護)](#2026-04-12-初次提交)

---

## 💡 2026-04-12 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的平面幾何運算。解題分為兩個階段：
1. **驗證三角形合法性**：利用「三角不等式 (Triangle Inequality)」：任意兩邊之和必須大於第三邊。我們將邊長遞增排序後，只需檢查最短的兩邊之和是否大於最長邊 (`sides[0] + sides[1] > sides[2]`) 即可。
2. **計算內角**：利用**餘弦定理 (Law of Cosines)** 推導角度。
   公式為：$c^2 = a^2 + b^2 - 2ab \cos(C)$
   移項後可得：$\cos(C) = \frac{a^2 + b^2 - c^2}{2ab}$
   因此，內角 $C = \arccos\left(\frac{a^2 + b^2 - c^2}{2ab}\right)$。
   計算出的結果為弧度 (Radian)，需乘上 $\frac{180}{\pi}$ 轉換為度數 (Degree)。

### 🛠️ 解題思路 (Approach)
1. **排序與驗證**：
   - 將 `sides` 進行遞增排序。
   - 若 `sides[0] + sides[1] <= sides[2]`，代表無法構成三角形，直接回傳 `{}`。
2. **實作角度計算函式 `cal`**：
   - 定義 $\pi$ 為 `acos(-1)`。
   - 帶入餘弦定理公式求出 `val`。
   - **關鍵防呆**：由於浮點數計算存在極微小的誤差，`val` 的結果可能略大於 $1.0$ 或略小於 $-1.0$（如 $1.0000000002$）。這超出了 $\arccos$ 的定義域區間 $[-1, 1]$，會導致 `acos` 拋出 `NaN`。因此必須手動將 `val` 箝制 (Clamp) 在 $[-1.0, 1.0]$ 之間。
   - 回傳轉換為度數的角度：`180.0 * acos(val) / pi`。
3. **計算三個角並優化**：
   - 宣告陣列 `rt`。
   - 呼叫 `cal` 計算其中兩個角（例如對應 `sides[2]` 與 `sides[1]` 的對角）。
   - **數學優化**：第三個角無需再呼叫昂貴的三角函數，直接利用三角形內角和為 180 度的特性，計算 `180.0 - rt[0] - rt[1]`。這同時確保了三個角的總和絕對精準等於 180.0。
4. **排序回傳**：將 `rt` 遞增排序後回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。陣列長度固定為 3，排序與三角函數計算皆為 $\mathcal{O}(1)$ 常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。只宣告了固定大小的陣列與常數變數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const double pi = acos(-1);
    
    // 利用餘弦定理計算對應邊 c 的內角角度
    double cal(double a, double b, double c){
        double val = (a * a + b * b - c * c) / (2.0 * a * b);
        
        // 浮點數精確度保護：確保 val 落在 acos 的定義域 [-1.0, 1.0] 內
        if(val > 1.0) val = 1.0;
        if(val < -1.0) val = -1.0;
        
        // 將弧度 (Radian) 轉換為度數 (Degree)
        return 180.0 * acos(val) / pi;
    }
    
    vector<double> internalAngles(vector<int>& sides) {
        // 1. 遞增排序邊長
        sort(sides.begin(), sides.end());
        
        // 2. 三角不等式檢驗：兩短邊之和必須大於最長邊
        if(sides[0] + sides[1] <= sides[2]) return {};
        
        vector<double> rt;
        
        // 3. 計算前兩個內角
        rt.push_back(cal(sides[0], sides[1], sides[2]));
        rt.push_back(cal(sides[0], sides[2], sides[1]));

        // 4. 優化：第三個角直接用 180 度相減，保證內角和精準等於 180
        rt.push_back(180.0 - rt[0] - rt[1]);
        
        // 5. 題目要求遞增排序後回傳
        sort(rt.begin(), rt.end());
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