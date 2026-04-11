---
title: "LeetCode 3894. Traffic Signal Color"
date: 2026-04-12T10:00:00+08:00
lastmod: 2026-04-12T10:00:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "simulation"]
keywords: ["LC3894", "Easy", "N/A", "Traffic Signal Color"]
description: "LeetCode 第 3894 題：Traffic Signal Color。探討如何利用基本的條件判斷 (if-else) 模擬紅綠燈的時序變化。"
---

## 💡 解題思路與程式碼

這是一道基礎的條件控制題 (Control Flow)。
我們只需要依照題目定義的秒數區間，嚴格設定 `if-else` 的判斷條件即可。

```cpp
class Solution {
public:
    string trafficSignal(int timer) {
        // 精準命中特定秒數
        if(timer == 0) return "Green";
        if(timer == 30) return "Orange";
        
        // 區間判斷
        if(timer > 30 && timer <= 90) return "Red";
        
        // 例外處理
        return "Invalid";
    }
};
```
- **時間複雜度**: $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(1)$。

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