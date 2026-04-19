---
title: "LeetCode 3891. Minimum Increase to Maximize Special Indices"
date: 2026-04-08T10:00:00+08:00
lastmod: 2026-04-08T10:00:00+08:00
difficulty: 1952
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic-programming", "greedy"]
keywords: ["LC3891", "Medium", "1952", "Special Indices", "Dynamic Programming", "Peak Elements"]
description: "LeetCode 第 3891 題：Minimum Increase to Maximize Special Indices。難度評分：Medium (1952)。探討如何依據陣列長度的奇偶性，利用貪婪策略與一維狀態機動態規劃 (1D DP)，以 O(N) 時間複雜度求得構造最多峰值所需的最少增加總量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3891](https://leetcode.com/problems/minimum-increase-to-maximize-special-indices/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1952)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Greedy`

---

## 📝 歷次打卡與更新
- [2026-04-08：初次提交 (奇偶分類與狀態機 DP)](#2026-04-08-初次提交)

---

## 💡 2026-04-08 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
題目的「特殊索引 (Special Indices)」本質上就是陣列中的「嚴格局部峰值 (Strict Peaks)」，即 `nums[i] > nums[i-1]` 且 `nums[i] > nums[i+1]`。我們只能增加元素的值，並希望以最小的總增加量，構造出**最多**的峰值。
任意兩個峰值之間至少必須間隔 1 個元素 (即最小索引差為 2)。依據陣列長度 `n` 的奇偶性，構造最多峰值的策略截然不同：

1. **奇數長度 (`n % 2 != 0`)**：
   - 最多可構造的峰值數量為 `(n - 1) / 2`。
   - 唯一的構造方式是將峰值固定在索引 `1, 3, 5, ..., n-2`。因為沒有任何位置的自由度，我們可以直接使用**貪婪策略**，對這些固定索引計算所需的最小增加量並加總。

2. **偶數長度 (`n % 2 == 0`)**：
   - 最多可構造的峰值數量為 `(n - 2) / 2`。
   - 在偶數長度下，峰值序列擁有「一次平移的自由度」。也就是說，相鄰峰值的間距可以有且僅有一次為 3（例如：`1, 4, 6` 或 `2, 4, 6`），其餘皆為 2。
   - 這非常適合使用 **動態規劃 (DP)** 解決。我們定義兩個狀態來記錄是否已經使用過這次平移的自由度。

### 🛠️ 解題思路 (Approach)
**奇數長度處理**：
- 直接遍歷 `i = 1` 到 `n - 1`，步長為 2。
- 若 `nums[i]` 已為峰值則跳過；否則將其增加至 `max(nums[i-1], nums[i+1]) + 1`，並累加成本。

**偶數長度處理 (DP)**：
- 宣告 DP 陣列 `dp[n+1][2]`，初始化為極大值 `1e18` (避免總成本過大造成 `long long` 的溢位誤判)。
- `dp[i][1]`：以索引 `i` 作為峰值，且**尚未發生平移**（即前面的峰值嚴格為 `i-2, i-4...`）的最小成本。
- `dp[i][0]`：以索引 `i` 作為峰值，且**已經發生過平移**（即前方出現過間距為 3 的峰值，或起點被平移至 2）的最小成本。
- **狀態轉移**：
  - `dp[i][1] = dp[i-2][1] + cost`
  - `dp[i][0] = min(dp[i-3][1], dp[i-2][0]) + cost`
- 最後，合法的最終峰值必然落在 `n-3` (無平移) 或 `n-2` (有平移)，回傳 `min(dp[n-3][1], dp[n-2][0])` 即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。無論奇偶，皆只對陣列進行一次線性掃描與常數次數的狀態轉移。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了大小為 $N \times 2$ 的 DP 陣列。*(註：由於狀態只依賴前三階，可利用滾動陣列將空間壓縮至 $\mathcal{O}(1)$)*。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long minIncrease(vector<int>& nums) {
        int n = nums.size();
        
        // 偶數長度：使用狀態機 DP 處理允許一次平移的自由度
        if(n % 2 == 0) {
            // 使用 1e18 作為無限大，避免真實成本超過 INT_MAX 時的取 min 誤判
            vector<vector<long long>> dp(n + 1, vector<long long>(2, 1e18));
            dp[0][1] = 0;
            
            for(int i = 1; i < n - 1; ++i) {
                // 判斷當前位置是否已為峰值
                bool ig = (nums[i] > nums[i-1] && nums[i] > nums[i+1]);
                // 計算成為峰值所需的增加量
                long long add = (max(nums[i-1], nums[i+1]) - nums[i] + 1);
                long long cost = ig ? 0 : add;
                
                // dp[i][1]: 尚未發生過間距為 3 的平移
                dp[i][1] = (i > 1 ? dp[i-2][1] : 0) + cost;
                
                // dp[i][0]: 已經發生過平移 (從前一個平移狀態繼承，或在此刻發生平移)
                dp[i][0] = min((i > 2 ? dp[i-3][1] : 0), (i > 1 ? dp[i-2][0] : 0)) + cost;
            }
            
            // 最大的最後一個峰值可能落在 n-3 或 n-2
            return min(dp[n-3][1], dp[n-2][0]);
        }
        
        // 奇數長度：位置固定，直接貪婪加總
        long long rt = 0;
        for(int i = 1; i < n; i += 2) {
            if(nums[i] > nums[i-1] && nums[i] > nums[i+1]) continue;
            rt += max(nums[i-1], nums[i+1]) - nums[i] + 1;
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