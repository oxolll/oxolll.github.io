---
title: "LeetCode 198. House Robber"
date: 2026-03-13T23:04:00+08:00
lastmod: 2026-03-13T23:04:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Dynamic Programming", "Array"]
keywords: ["LC198", "N/A", "Medium", "House Robber", "DP", "Space Optimization"]
description: "LeetCode 第 198 題：House Robber。難度評分：N/A (經典 DP 題)。探討如何定義動態規劃的狀態轉移方程來解決相鄰限制問題，並處理邊界條件。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 198](https://leetcode.com/problems/house-robber/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>   
> **核心主題**：`Dynamic Programming` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
你是一名專業的強盜，計畫沿街搶劫房屋。每間房屋都藏有一定數量的金錢。
唯一阻止你搶劫所有房屋的限制是：相鄰的房屋有連接防盜系統。如果**兩間相鄰的房屋在同一晚被闖入**，系統會自動報警。

給定一個整數陣列 `nums`，代表每間房屋存放的金額。請回傳在**不觸動警報裝置**的情況下，今晚你能夠搶劫到的最高金額。

### 範例
- **Input**: `nums = [1,2,3,1]`
  **Output**: `4`
  **Explanation**: 搶劫房屋 1 (金額 = 1) 然後搶劫房屋 3 (金額 = 3)。最高金額 = 1 + 3 = 4。

- **Input**: `nums = [2,7,9,3,1]`
  **Output**: `12`
  **Explanation**: 搶劫房屋 1 (金額 = 2), 房屋 3 (金額 = 9), 接著搶劫房屋 5 (金額 = 1)。最高金額 = 2 + 9 + 1 = 12。

### 限制條件
- $1 \le nums.length \le 100$
- $0 \le nums[i] \le 400$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-13：初次提交 (1D 動態規劃與邊界處理)](#2026-03-13-初次提交)

---

## 💡 2026-03-13 初次提交

### 🎯 直覺 (Intuition)
這是最經典的「選與不選」動態規劃問題。對於每一間房屋，我們只有兩種選擇：
1. **搶劫這間房屋**：那麼我們就絕對不能搶劫前一間房屋。所以我們能拿到的總金額會是「這間房屋的金額」加上「到前兩間房屋為止能搶到的最大金額」。
2. **不搶這間房屋**：那麼我們能拿到的總金額，就等於「到前一間房屋為止能搶到的最大金額」。
為了最大化收益，我們只要在這兩個選擇中取最大值即可。

### 🛠️ 解題思路 (Approach)
1. **狀態定義**：
   定義 `dp[i]` 為：考慮前 `i` 間房屋（索引從 `0` 到 `i`），在不觸發警報的情況下能搶到的**最大金額**。
2. **轉移方程**：
   $$dp[i] = \max(dp[i-2] + nums[i], dp[i-1])$$
3. **邊界條件處理**：
   - 在迴圈內部，利用三元運算子 `(i >= 2 ? dp[i-2] : 0)` 與 `(i >= 1 ? dp[i-1] : 0)` 來優雅地處理 `i=0` 和 `i=1` 時的越界問題。
   - 因為 `dp[i]` 的定義已經包含了「搶或不搶」的最佳解，所以 `dp` 陣列的最後一個元素 `dp.back()` 必定涵蓋了全局的最大收益。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需遍歷陣列一次。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了一個長度與 `nums` 相同的 `dp` 陣列來儲存狀態。*(註：由於轉移方程只依賴前兩項，空間可進一步優化至 $\mathcal{O}(1)$)*。

### 💻 程式碼實作 (C++)

#### 版本一：標準 1D DP (初始實作)
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        vector<int> dp(nums.size());
        
        for(int i = 0; i < nums.size(); ++i) {
            // dp[i] = max(搶這間 + 到 i-2 的最大值, 不搶這間取到 i-1 的最大值)
            // 巧妙利用三元運算子處理 i=0 和 i=1 的邊界越界問題
            dp[i] = max((i >= 2 ? dp[i-2] : 0) + nums[i], (i >= 1 ? dp[i-1] : 0));
        }

        // 由於 dp[i] 已涵蓋不搶 i 的情況，dp.back() 即為全局最大值
        return dp.back();
    }
};
```

<details>
<summary><b>點擊展開：版本二 (滾動變數空間優化 O(1))</b></summary>

因為 `dp[i]` 只跟 `dp[i-1]` (前一間) 和 `dp[i-2]` (前兩間) 有關，我們其實不需要維護整個陣列，只要兩個變數交替滾動即可。
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int prev2 = 0; // 對應 dp[i-2]
        int prev1 = 0; // 對應 dp[i-1]
        
        for(int num : nums) {
            int current = max(prev2 + num, prev1);
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
};
```
</details>

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