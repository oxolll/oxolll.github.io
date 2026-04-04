---
title: "LeetCode 1402. Reducing Dishes"
date: 2026-04-06T15:30:00+08:00
lastmod: 2026-04-06T15:30:00+08:00
difficulty: 1679
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting", "math", "prefix-sum"]
keywords: ["LC1402", "1679", "Hard", "Reducing Dishes", "Greedy", "Prefix Sum"]
description: "LeetCode 第 1402 題：Reducing Dishes。難度評分：1679。探討如何利用貪婪策略與數學前綴和的規律，透過反向掃描將原本 O(N^2) 的問題降維至 O(N log N) 時間複雜度求解最大滿意度總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1402](https://leetcode.com/problems/reducing-dishes/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (1679)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
一個廚師收集了他能夠製作的 `n` 道菜的滿意度程度 `satisfaction`。
廚師可以在 1 單位時間內做完一道菜。
一道菜的「Like-time coefficient (喜愛時間係數)」定義為：`時間[i] * 滿意度[i]`，其中 `時間[i]` 是從 1 開始計算的烹飪順序。
廚師可以按照**任意順序**做菜。他也可以選擇**放棄某些菜**，以最大化總和。

請回傳廚師可以獲得的**最大喜愛時間係數總和**。

### 限制條件
- $n == satisfaction.length$
- $1 \le n \le 500$
- $-10^3 \le satisfaction[i] \le 10^3$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-06：初次提交 (排序後反向貪婪與前綴和推導)](#2026-04-06-初次提交)

---

## 💡 2026-04-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題目標為求最大化 $\sum (time_i \times satisfaction_i)$。
首先，為使總和最大化，滿意度越高的菜，其乘上的時間係數應該越大；也就是說，**滿意度越高的菜應該越晚煮**。這暗示了第一步必須對陣列進行遞增排序。

接著，我們考慮貪婪策略 (Greedy Strategy)。最優解一定包含陣列最尾端（滿意度最高）的若干道菜。當我們決定從後往前多納入一道新菜時，這道新菜會被排在第 1 單位時間烹煮，而原本已經在清單內的菜，其烹煮時間都會自動往後延 1 單位。
數學規律轉換：
假設舊清單得分為 $S_{prev}$，舊清單的滿意度總和為 $Sum$。
當我們在最前面加入一道滿意度為 $x$ 的新菜時，新清單的得分變為：
$$S_{new} = x \times 1 + (舊清單各項滿意度 \times (原有時間 + 1))$$
$$S_{new} = x + S_{prev} + Sum$$
因此，只要 $x + Sum > 0$，代表加入這道新菜能使總得分上升（即使 $x$ 本身是負數，只要現有清單的 $Sum$ 夠大，能帶來的時間延後紅利仍會抵銷 $x$ 的負面影響）。
我們只需反向遍歷陣列，持續維護 $S_{prev}$ 與 $Sum$，即可在線性時間內找出最大值。

### 🛠️ 解題思路 (Approach)
1. **排序**：呼叫 `sort` 將 `satisfaction` 陣列進行遞增排序。
2. **變數初始化**：
   - 取得陣列長度 `n`。
   - `mx = 0`：記錄全域最大得分，預設為 0（因為最差情況是什麼都不煮，得分為 0）。
   - `sum = 0`：記錄當前已納入清單的菜色滿意度總和。
   - `prev = 0`：記錄當前清單的總得分。
3. **反向貪婪掃描**：
   - 建立迴圈 `i`，從 `n-1` 遍歷至 `0`。
   - 計算若加入當前菜色 `satisfaction[i]` 後的新得分：`prev + sum + satisfaction[i]`。
   - 使用 `max` 嘗試更新全域最大值 `mx`。
   - 推進狀態：
     - 將當前菜色的滿意度加入總和：`sum += satisfaction[i]`。
     - 更新當前清單的總得分：`prev += sum`。
4. **回傳結果**：回傳 `mx`。

*(技術註記：迴圈即使遇到加入新菜後總分開始下降的情況仍會繼續執行，但因 `mx` 只取最大值，後續的衰退不會影響最終答案。若要極致優化，當發現 `sum + satisfaction[i] <= 0` 時即可 `break` 提早結束迴圈。)*

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是陣列的長度。主要效能瓶頸在於排序，後續的反向掃描只需 $\mathcal{O}(N)$ 時間。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(1)$。取決於 C++ 內部排序的遞迴深度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxSatisfaction(vector<int>& satisfaction) {
        // 1. 遞增排序，確保滿意度越高的菜排在越後面
        sort(satisfaction.begin(), satisfaction.end());
        
        int mx = 0; // 記錄全域最大得分 (最差為全不煮，得 0 分)
        int n = satisfaction.size();
        
        int sum = 0;  // 當前已選菜色的滿意度總和
        int prev = 0; // 當前已選菜色的喜愛時間係數總和
        
        // 2. 從滿意度最高的菜開始反向推導
        for(int i = n - 1; i >= 0; --i) {
            // 計算若多煮這道菜的新總分，並嘗試更新最大值
            mx = max(mx, prev + sum + satisfaction[i]);
            
            // 將這道菜納入清單，更新總和狀態
            sum += satisfaction[i];
            
            // 根據數學規律：新總分 = 原總分 + 新菜分數(被排在第一位) + 舊菜滿意度總和(因為時間順序全面+1)
            // 等價於 prev = prev + satisfaction[i] + (sum - satisfaction[i]) = prev + sum
            prev += sum; 
        }

        return mx;
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