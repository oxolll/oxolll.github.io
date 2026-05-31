---
title: "LeetCode 3946. Maximum Number of Items From Sale I"
date: 2026-06-02T15:00:00+08:00
lastmod: 2026-06-02T15:00:00+08:00
difficulty: 1700
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "knapsack", "greedy", "array"]
keywords: ["LC3946", "Medium", "Maximum Number of Items From Sale I", "0/1 背包問題", "動態規劃", "貪心演算法"]
description: "LeetCode 第 3946 題：Maximum Number of Items From Sale I。探討如何將條件贈送規則轉化為 0/1 背包問題的權重，並結合貪心策略，利用剩餘預算無限購買最便宜物品，以求得最大物品總數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3946](https://leetcode.com/problems/maximum-number-of-items-from-sale-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Greedy` $\cdot$ `Knapsack`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個二維陣列 `items`，其中 `items[i] = [value_i, price_i]`，代表第 $i$ 個物品的值與價格。同時給你一個整數 `budget`，代表你擁有的總預算。

商店有一個特殊的「買一送多」促銷活動：
- 當你購買了第 $i$ 個物品時，對於任何其他的物品 $j$ ($i \neq j$)，只要 `items[j][0]` (即 $value_j$) 是 `items[i][0]` ($value_i$) 的 **倍數**，你就可以 **免費** 獲得物品 $j$。
- 你可以重複購買同一個物品（包含最便宜的物品）。

請你計算在不超過 `budget` 的情況下，最多可以獲得 **多少個** 物品。

### 限制條件
- $1 \le items.length \le 1000$ (依據 $\mathcal{O}(N^2)$ 推斷)
- $1 \le budget \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-02：初次提交 (0/1 背包 + 貪心填補)](#2026-06-02-初次提交)

---

## 💡 2026-06-02 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的決策分為兩個維度：
1. **觸發特殊促銷**：花費一筆錢買某個特定的物品，來獲得大量免費贈品。因為每個物品的贈送條件只能觸發一次，這是一個標準的 **0/1 背包問題 (0/1 Knapsack)**。
2. **消化剩餘零錢**：當預算不足以觸發更多有價值的促銷時，剩下的錢應該用來做什麼？當然是**全部拿去買單價最便宜的物品**！這是一個標準的 **貪心策略 (Greedy)**。

**核心破局點：狀態轉化**
我們不需要在 DP 裡面處理複雜的「倍數關係」。
先用 $\mathcal{O}(N^2)$ 的時間，把每個物品當成獨立的「福袋」。
對於福袋 $i$，它的成本是 `items[i][1]`，它裡面裝的物品總數是 `1 + free[i]`。
接著，我們只需對這 $N$ 個福袋跑一次標準的 0/1 背包 DP，並在每個狀態結算時，把剩下的預算 `budget - j` 除以全場最低價 `mm`，加到總數中挑戰極值即可。

### 🛠️ 解題思路 (Approach)
1. **前置排序**：將物品依照價格由小到大排序 (非強迫，但有助於快取與思維梳理)。
2. **預處理贈品陣列 (`free`)**：
   - 找出全場最便宜的物品價格，記為 `mm`。
   - 雙層迴圈檢查：若 `items[j][0] % items[i][0] == 0`，則 `free[i]` 加 1。
3. **0/1 背包 DP**：
   - 建立 `dp[j][i]` 陣列，代表「考慮前 $i$ 個福袋，耗費預算 $j$ 時，能獲得的最大物品數」。
   - 狀態轉移方程式：`dp[j][i] = max(不買福袋 i, 買福袋 i)`
     $\Rightarrow$ `dp[j][i] = max(dp[j][i-1], dp[j-p][i-1] + 1 + free[i-1])`
4. **貪心結算**：
   - 在計算每一個 `dp[j][i]` 時，嘗試用剩餘的錢買最便宜的物品：
     `mx = max(mx, dp[j][i] + (budget - j) / mm)`
5. **回傳全局最大值 `mx`**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2 + N \cdot B)$。其中 $N$ 是物品數量，$B$ 是預算 `budget`。
  預處理贈品需要 $\mathcal{O}(N^2)$，二維 DP 需要 $\mathcal{O}(N \cdot B)$。兩者相加在常見限制下（如 $1000$）運行速度極快。
- **空間複雜度**: $\mathcal{O}(N \cdot B)$。用於儲存 `dp` 二維陣列。*(註：可透過一維滾動陣列將空間壓縮至 $\mathcal{O}(B)$)*。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximumSaleItems(vector<vector<int>>& items, int budget) {
        // 1. 依照價格由小到大排序
        sort(items.begin(), items.end(), [](auto& a, auto& b){
            return a[1] < b[1];
        });

        int n = items.size();
        vector<int> free(n, 0); // 記錄每個物品能帶來的「免費贈品數量」
        int mm = INT_MAX;       // 記錄全場最便宜的價格
        
        // 2. 預處理：計算每個物品的附加價值，並找出最低價格 mm
        for(int i = 0; i < n; ++i) {
            int cur = items[i][0];
            mm = min(mm, items[i][1]); // 更新最低價格
            
            for(int j = 0; j < n; ++j) {
                // 如果 j 的 value 是 i 的 value 的倍數，則買 i 送 j
                if(i != j && (items[j][0] % cur) == 0) {
                    ++free[i];
                }
            }
        }
        
        // 3. 0/1 背包動態規劃
        // dp[j][i] 表示使用恰好/最多 j 的預算，在考慮前 i 個物品時的最大收益
        vector<vector<int>> dp(budget + 1, vector<int>(n + 1, 0));
        int mx = 0;
        
        for(int i = 1; i <= n; ++i) {
            int p = items[i-1][1]; // 當前福袋的價格
            
            for(int j = 1; j <= budget; ++j) {
                // 選擇 1：不買當前福袋
                dp[j][i] = dp[j][i-1];
                
                // 選擇 2：如果預算夠，嘗試購買當前福袋 (成本 p，價值 1 + free)
                if(j >= p) {
                    dp[j][i] = max(dp[j][i], dp[j-p][i-1] + 1 + free[i-1]);
                }
                
                // 4. 貪心策略：已透過 DP 花費 j 元，剩下的錢全買最便宜的物品
                // 挑戰刷新全局最大值
                mx = max(mx, dp[j][i] + (budget - j) / mm);
            }
        }
        
        return mx;
    }
};
```

---

### 💡 延伸優化 (Space Optimization)
由於 0/1 背包問題的狀態轉移 `dp[j][i]` 只依賴於上一層 `dp[...][i-1]`，我們可以將二維陣列壓縮成一維陣列，將空間複雜度從 $\mathcal{O}(N \cdot B)$ 降至 $\mathcal{O}(B)$：
```cpp
// 空間優化版的核心迴圈
vector<int> dp(budget + 1, 0);
int mx = 0;

for(int i = 0; i < n; ++i) {
    int p = items[i][1];
    int value = 1 + free[i];
    
    // 0/1 背包必須「倒序」遍歷預算，避免同一物品被重複選取
    for(int j = budget; j >= p; --j) {
        dp[j] = max(dp[j], dp[j - p] + value);
    }
    
    // 結算當前狀態的貪心極值 (正序或倒序皆可，因為依賴的是當前已更新的 dp)
    for(int j = 0; j <= budget; ++j) {
        mx = max(mx, dp[j] + (budget - j) / mm);
    }
}
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