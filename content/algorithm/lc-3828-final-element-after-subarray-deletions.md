---
title: "LeetCode 3828. Final Element After Subarray Deletions"
date: 2026-03-03T20:00:00+08:00
lastmod: 2026-03-03T20:00:00+08:00
difficulty: 1591
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Game Theory", "Brainteaser", "Math"]
keywords: ["LC3828", "1591", "Medium", "Game Theory", "Brain Teaser"]
description: "LeetCode 第 3828 題：Final Element After Subarray Deletions。難度評分：1591。探討在子陣列刪除遊戲中，如何利用賽局理論與邊界必勝策略，以 O(1) 時間複雜度破解腦筋急轉彎。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3828](https://leetcode.com/problems/final-element-after-subarray-deletions/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1591)</span>   
> **核心主題**：`Game Theory` $\cdot$ `Brainteaser` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
有兩名玩家 Alice 和 Bob 輪流進行遊戲，由 Alice 先手。
- 在每一輪中，當前玩家可以選擇任意一個子陣列 `nums[l..r]`，其長度必須小於當前陣列的長度 $m$ (即 $r - l + 1 < m$)。
- 被選中的子陣列將被移除，剩餘的元素會連接起來形成新的陣列。
- 遊戲持續進行，直到陣列中只剩下 1 個元素為止。

Alice 的目標是**最大化**最終剩下的元素，而 Bob 的目標是**最小化**它。
假設雙方都採取最佳策略，請回傳最終剩下的元素值。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-03：初次提交 (賽局理論與 O(1) 邊界推導)](#2026-03-03-初次提交)

---

## 💡 2026-03-03 初次提交

### 🎯 直覺 (Intuition)
這是一道標準的「賽局理論 (Game Theory)」腦筋急轉彎。
因為遊戲規則允許玩家刪除長度小於 $m$ 的子陣列，這意味著玩家「有能力在一步之內刪除 $m-1$ 個元素，直接結束遊戲」。
Alice 身為先手，她握有絕對的主動權去強制保留陣列的最左端或最右端。而中間那些看起來很大的誘人數字，Alice 其實是「看得到吃不到」的，因為只要她不一波帶走，Bob 就會反過來利用相同的規則把它們消滅掉。

### 🛠️ 解題思路 (Approach)
我們可以透過以下兩點來嚴謹證明：

1. **Alice 的必勝保底策略 (下界)**：
   - 規則允許刪除長度為 $m-1$ 的連續子陣列。
   - 因此，Alice 在第一回合就可以選擇刪除 `nums[1...n-1]`（留下 `nums[0]`），或是刪除 `nums[0...n-2]`（留下 `nums[n-1]`）。
   - 由於只剩 1 個元素，遊戲立刻結束，Bob 連出手的機會都沒有！這保證了 Alice **至少**能拿到 $\max(nums[0], nums[n-1])$。

2. **為什麼 Alice 無法拿到中間更大的元素？ (Bob 的反制)**：
   - 假設陣列中間有一個極大的值 $nums[i]$，Alice 想要把它留到最後。
   - 因為 Alice 每次只能刪除「連續」的子陣列，她無法在第一回合同時刪除 $nums[i]$ 左右兩邊的元素（這需要刪除兩個不連續的區塊）。所以，如果 Alice 想保留中間元素，她第一回合結束後，陣列必定還剩下 $\ge 2$ 個元素。
   - 這時輪到 Bob 回合。Bob 發現陣列裡有一個對他極度不利的巨大數字，他就會利用跟 Alice 一樣的「一波帶走」特權，直接刪除長度為 $m'-1$ 的子陣列，強迫留下對他最有利（最小）的邊界元素。
   - 因為 Bob 的目標是最小化結果，他絕對會把 Alice 想要的中間大數字給抹除。

**結論**：Alice 既然無法保護中間的巨大元素，她唯一也是最優的策略，就是開局直接「掀桌」，拿走兩端的最大值結束遊戲。這就是為什麼答案永遠是 `max(nums[0], nums.back())`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$。只需要判斷陣列頭尾元素的大小即可。
- **空間複雜度**: $\mathcal{O}(1)$。完全不需要額外配置記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int finalElement(vector<int>& nums) {
        // 直接取頭尾元素的最大值
        return max(nums[0], nums.back());
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