---
title: "LeetCode 3354. Make Array Elements Equal to Zero"
date: 2026-03-21T19:30:00+08:00
lastmod: 2026-03-21T19:30:00+08:00
difficulty: 1397
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix sum", "math"]
keywords: ["LC3354", "1397", "Easy", "Prefix Sum", "Math"]
description: "LeetCode 第 3354 題：Make Array Elements Equal to Zero。難度評分：1397。探討如何略過繁瑣的步進模擬，直接利用前綴和計算左右總和的絕對差，以 O(N) 時間複雜度運用數學特性秒殺求出所有合法起點。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3354](https://leetcode.com/problems/make-array-elements-equal-to-zero/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1397)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。你可以選擇任何一個滿足 `nums[i] == 0` 的起始索引 `i`，並選擇一個初始移動方向（向左或向右）。
移動規則如下：
1. 如果當前位置 `curr` 越界，則結束移動。
2. 如果 `nums[curr] == 0`，則保持當前方向繼續前進。
3. 如果 `nums[curr] > 0`，則將 `nums[curr]` 減 1，然後**反轉**移動方向並前進一步。

請回傳有多少種起點與初始方向的組合，能夠在移動結束時讓陣列中所有的元素都變成 `0`。

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (前綴和與左右總和差值判斷)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
如果依照題目要求去模擬指標的來回移動，最差時間複雜度可能會退化，且實作容易出錯。
仔細觀察移動規則：每次撞到大於 0 的數字，該數字減 1 並回頭。這意味著一個起點 `i` 能否將全圖清空，完全取決於它**左邊元素的總和**與**右邊元素的總和**之間的關係。
當我們從 `nums[i] == 0` 出發：
- 如果左側總和與右側總和**完全相等**：無論我們一開始向左還是向右，每次折返都會精準扣除兩邊各 1 的總和，最終完美清空。這提供了 **2** 種合法選擇。
- 如果左側總和與右側總和**相差恰好 1**：我們必須優先朝向總和**較大**的那一邊出發，這樣在來回折返抵銷後，多出來的那 1 剛好會被最後一次撞擊扣除並順勢滑出邊界。這提供了 **1** 種合法選擇。
- 如果兩側總和相差大於 1：不管怎麼走，數量少的那一邊一定會先被扣完變成 0，指標會直接穿透出界，留下另一邊未清空的數字。合法選擇數為 **0**。

### 🛠️ 解題思路 (Approach)
1. **建立前綴和陣列**：宣告 `pre` 陣列，計算 `nums` 的前綴和，以便後續能在 $\mathcal{O}(1)$ 時間內取得任意區間的總和。
2. **尋找起點與驗證**：
   - 宣告 `rt = 0` 記錄合法組合數。
   - 遍歷原陣列，如果 `nums[i] != 0`，不符合起點資格，直接 `continue`。
   - 針對合法的起點 `i`，分別計算左側總和 (`i > 0 ? pre[i-1] : 0`) 與右側總和 (`pre.back() - pre[i]`)，並求出兩者的絕對差 `diff`。
   - 若 `diff > 1`，無解，`continue`。
   - 若 `diff == 1`，貢獻 1 種解 (`rt += 1`)。
   - 若 `diff == 0`，貢獻 2 種解 (`rt += 2`)。
3. **回傳結果**：迴圈結束後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。陣列走訪兩次：一次計算前綴和，一次驗證每個 `0` 作為起點的合法性，每次驗證皆為常數時間操作。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了一個長度為 $N$ 的 `pre` 陣列來儲存前綴和。（註：若先算出全域總和，再用單一變數維護左側前綴和，可將空間複雜度優化至 $\mathcal{O}(1)$。）

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int countValidSelections(vector<int>& nums) {
        int n = nums.size();
        vector<int> pre(n, 0);
        
        // 1. 計算前綴和
        for(int i = 0; i < n; ++i) {
            pre[i] += (i > 0 ? pre[i-1] : 0) + nums[i];
        }
            
        int rt = 0;
        
        // 2. 遍歷尋找合法起點並計算解的數量
        for(int i = 0; i < n; ++i) {
            // 起點必須為 0
            if(nums[i] != 0) continue;
            
            // 計算左半部總和與右半部總和的絕對差
            int diff = abs((i > 0 ? pre[i-1] : 0) - (pre.back() - pre[i]));
            
            // 相差大於 1 必定無法清空
            if(diff > 1) continue;
            
            // 差值為 1 提供 1 種解，差值為 0 提供 2 種解
            rt += (diff == 1 ? 1 : 2);
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