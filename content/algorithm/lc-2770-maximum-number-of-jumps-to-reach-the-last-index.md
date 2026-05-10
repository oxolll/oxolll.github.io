---
title: "LeetCode 2770. Maximum Number of Jumps to Reach the Last Index"
date: 2026-05-10T17:00:00+08:00
lastmod: 2026-05-10T17:00:00+08:00
difficulty: 1533
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "array"]
keywords: ["LC2770", "Medium", "1533", "Maximum Jumps", "LIS-like", "動態規劃"]
description: "LeetCode 第 2770 題：Maximum Number of Jumps to Reach the Last Index。難度評分：1533。探討如何利用線性動態規劃尋找滿足絕對值差值限制下的最長路徑步數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2770](https://leetcode.com/problems/maximum-number-of-jumps-to-reach-the-last-index/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1533)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個下標從 $0$ 開始、包含 $n$ 個整數的陣列 `nums` 和一個整數 `target`。
你最初位於下標 `0` 。在一步操作中，你可以從下標 $i$ 跳躍到下標 $j$ ，前提是：
1. $0 \le i < j < n$
2. $-target \le nums[j] - nums[i] \le target$ (即 $|nums[j] - nums[i]| \le target$)

請你回傳從下標 `0` 跳躍到下標 `n - 1` 的**最大跳躍次數**。如果無法抵達，回傳 `-1` 。

### 限制條件
- $2 \le n \le 1000$
- $-10^9 \le nums[i] \le 10^9$
- $0 \le target \le 2 \times 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-10：初次提交 (線性 DP 解法)](#2026-05-10-初次提交)

---

## 💡 2026-05-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標是求「最大」跳躍步數，這與最短路徑（通常用 BFS）不同，更適合使用**動態規劃 (DP)**。本題的邏輯與「最長遞增子序列 (LIS)」高度重疊。

**狀態定義：**
- $dp[i]$：代表抵達下標 $i$ 所能獲得的**最大跳躍次數**。
- 初始值：$dp[0] = 0$，其餘 $dp[1 \dots n-1] = -1$（代表目前尚未發現可抵達該點的路徑）。

**轉移方程式：**
對於每一對滿足 $i < j$ 的點，如果從 $i$ 可以跳到 $j$（滿足 $|nums[i] - nums[j]| \le target$）：
$$dp[j] = \max(dp[j], dp[i] + 1)$$
前提是 $dp[i] \ne -1$（即出發點必須是可達的）。

### 🛠️ 解題思路 (Approach)
1. **初始化**：
   建立一個大小為 $n$ 的 `dp` 陣列，填充為 `-1`。將起始點 `dp[0]` 設為 `0`。
2. **雙層迴圈掃描**：
   - 外層迴圈 $i$ 遍歷每一個潛在的「出發點」。
   - 如果 $dp[i]$ 為 `-1`，跳過該點。
   - 內層迴圈 $j$ 從 $i+1$ 開始遍歷每一個潛在的「落腳點」。
   - 檢查是否滿足絕對值差小於等於 `target` 的條件。
   - 若滿足，更新 `dp[j]` 為當前值與 `dp[i] + 1` 之間的較大者。
3. **回傳結果**：
   回傳 `dp[n-1]`，即最後一個位置的最大跳躍次數。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。其中 $N$ 為陣列長度。我們使用了兩層巢狀迴圈遍歷所有可能的 $(i, j)$ 對。
- **空間複雜度**: $\mathcal{O}(N)$。需要一個大小為 $N$ 的 $dp$ 陣列來儲存中間狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximumJumps(vector<int>& nums, int target) {
        int n = nums.size();

        // dp[i] 表示抵達索引 i 的最大跳躍次數，初始化為 -1 代表不可達
        vector<int> dp(n, -1);
        dp[0] = 0; // 起始點跳躍次數為 0

        for(int i = 0; i < n - 1; ++i) {
            // 如果當前點 i 本身不可達，則無法從此處向後跳
            if(dp[i] == -1) continue;

            for(int j = i + 1; j < n; ++j) {
                // 檢查跳躍條件：|nums[j] - nums[i]| <= target
                // 使用 long long 防止兩數相減溢位，或直接使用 abs 處理
                if(abs((long long)nums[i] - nums[j]) <= target) {
                    // 更新抵達索引 j 的最大跳躍次數
                    dp[j] = max(dp[j], dp[i] + 1);
                }
            }
        }

        // 回傳抵達最後一個索引的最大次數，若為 -1 則代表無法抵達
        return dp.back();
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