---
title: "LeetCode 3875. Construct Uniform Parity Array I"
date: 2026-03-22T16:00:00+08:00
lastmod: 2026-03-22T16:00:00+08:00
difficulty: 1199
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math"]
keywords: ["LC3875", "Easy", "1199", "Uniform Parity"]
description: "LeetCode 第 3875 題：Construct Uniform Parity Array I。難度評分：Easy (1199)。本題為系列題的第一部分，探討陣列元素奇偶性轉換的基本邏輯。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3875](https://leetcode.com/problems/construct-uniform-parity-array-i/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1199)</span>  
> **核心主題**：`Array` $\cdot$ `Math`   
> **進階挑戰**：🔗 [👉 點此查看本題的進階版本：3876. Construct Uniform Parity Array II](../lc-3876-construct-uniform-parity-array-ii/)

---

## 📝 歷次打卡與更新
- [2026-03-22：初次提交 (常數時間回傳)](#2026-03-22-初次提交)

---

## 💡 2026-03-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題為 "Construct Uniform Parity Array" 系列的第一題。由於測資限制與可用操作的定義，在第一部分的條件下，任何輸入陣列皆存在合法操作序列使其達到奇偶性一致的狀態。因此，函數直接回傳 `true` 即可滿足所有測試案例。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool uniformArray(vector<int>& nums1) {
        // 依據本題第一部分的條件限制，所有陣列皆可構造成功
        return true;
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