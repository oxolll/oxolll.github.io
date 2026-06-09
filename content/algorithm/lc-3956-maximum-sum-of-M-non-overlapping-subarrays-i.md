---
title: "LeetCode 3956. Maximum Sum of M Non-Overlapping Subarrays I"
date: 2026-06-09T16:45:00+08:00
lastmod: 2026-06-09T16:45:00+08:00
difficulty: 2200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "monotonic-queue", "prefix-sum", "sliding-window"]
keywords: ["LC3956", "Hard", "Maximum Sum of M Non-Overlapping Subarrays", "動態規劃", "單調佇列", "前綴和", "滑動視窗優化"]
description: "LeetCode 第 3956 題：Maximum Sum of M Non-Overlapping Subarrays I。探討如何利用前綴和建立 DP 狀態，並透過數學解耦將轉移方程式轉換為滑動視窗最大值問題，最後利用單調佇列 (Monotonic Queue) 達成 O(N * M) 的極致效能優化。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3956](https://leetcode.com/problems/maximum-sum-of-m-non-overlapping-subarrays-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Monotonic Queue` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums`，以及三個整數 `m`, `l` 和 `r`。
請你從 `nums` 中選出恰好 **`m` 個互不重疊的連續子陣列**，使得這些子陣列的元素總和達到最大。

對於每一個被選出的子陣列，其長度 `len` 必須滿足：
$l \le len \le r$

請回傳這 `m` 個子陣列的最大總和。

### 限制條件
- $1 \le nums.length \le 10^5$ (推斷值)
- $1 \le m \le 100$
- $1 \le l \le r \le nums.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (DP + 單調佇列滑動視窗優化)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是要求選取多個不重疊區間的最大和，標準解法是 **動態規劃 (Dynamic Programming)**。
定義狀態 $dp[c][i]$ 表示：在前 $i$ 個元素中，選出 $c$ 個符合長度限制的不重疊子陣列所能獲得的最大和。

**核心破局點 1：狀態轉移與前綴和**
對於第 $i$ 個元素，我們有兩種決策：
1. **不包含在最後一個子陣列中**：繼承前一個狀態 $dp[c][i-1]$。
2. **作為第 $c$ 個子陣列的結尾**：這意味著該子陣列從某個索引 $j$ 開始，長度為 $i - j$。
   - 長度限制：$l \le i - j \le r \implies i - r \le j \le i - l$。
   - 此時的總和為：$dp[c-1][j] + \sum_{k=j}^{i-1} nums[k]$。
   - 引入前綴和 $pre$，區間和可化簡為 $pre[i] - pre[j]$。
   
綜合起來，轉移方程式為：
$$dp[c][i] = \max \left( dp[c][i-1], \max_{i-r \le j \le i-l} (dp[c-1][j] + pre[i] - pre[j]) \right)$$

**核心破局點 2：單調佇列 (Monotonic Queue) 降維優化**
如果暴力尋找符合條件的 $j$，轉移的複雜度將是 $\mathcal{O}(r-l)$，整體複雜度會達到 $\mathcal{O}(N \cdot M \cdot (r-l))$，導致 TLE。
我們對方程式進行**數學解耦**：因為對固定的 $i$ 而言，$pre[i]$ 是常數，所以可以提出來：
$$dp[c][i] = \max \left( dp[c][i-1], pre[i] + \max_{i-r \le j \le i-l} (dp[c-1][j] - pre[j]) \right)$$
我們發現，目標變成了在一個隨 $i$ 向右滑動的視窗 $[i-r, i-l]$ 中，尋找 $dp[c-1][j] - pre[j]$ 的最大值。這正是經典的 **滑動視窗最大值問題**！利用雙向單調佇列 `deque`，我們可以在 $\mathcal{O}(1)$ 均攤時間內維護這個最大值，將整體複雜度完美壓制在 $\mathcal{O}(N \cdot M)$。

### 🛠️ 解題思路 (Approach)
1. **初始化與前綴和**：
   - 宣告 $dp$ 陣列，初始化為極小值 `-1e18`，並設定基礎狀態 $dp[0][i] = 0$。
   - 計算前綴和陣列 `pre`。
2. **外層迴圈**：針對要選出的子陣列數量 $c$ 從 $1$ 到 $m$ 進行遍歷。
3. **內層單調佇列優化**：
   - 宣告一個 `deque<pair<long long, int>> dq`，儲存數值 `upd` 與索引 `j`。
   - 遍歷結尾索引 $i$ 從 $l$ 到 $n$。對應的左端點 $j = i - l$。
   - 計算當前左端點的潛在價值：`upd = dp[c-1][j] - pre[j]`。
   - **淘汰過期者**：把佇列頭部中索引超出滑動視窗左邊界 ($i - j > r$) 的元素 `pop_front`。
   - **維護單調性**：把佇列尾部中小於等於當前 `upd` 的劣勢元素全部 `pop_back`。
   - 將當前的 `{upd, j}` 推進佇列。
   - **狀態轉移**：$dp[c][i]$ 即為 $\max(dp[c][i-1], pre[i] + dq.front().first)$。
4. **回傳極值**：每一輪算完，更新全局最大值 `rt = max(rt, dp[c][n])`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \cdot M)$。狀態總數為 $N \cdot M$。得益於單調佇列，每個 $j$ 在內層迴圈中最多進出佇列一次，狀態轉移時間為均攤 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N \cdot M)$。用於儲存 DP 陣列。*(註：因第 $c$ 層只依賴第 $c-1$ 層，此空間可進一步滾動優化至 $\mathcal{O}(N)$)*。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long maximumSum(vector<int>& nums, int m, int l, int r) {
        int n = nums.size();
        
        // dp[c][i] 表示前 i 個元素中，選出 c 個合法子陣列的最大總和
        vector<vector<long long>> dp(m + 1, vector<long long>(n + 1, -1e18));
        
        // 基礎狀態：0 個子陣列的和為 0
        for(int i = 0; i <= n; ++i) {
            dp[0][i] = 0;
        }
        
        // 計算前綴和，加速區間和查詢
        vector<long long> pre(n + 1, 0);
        for(int i = 0; i < n; ++i) {
            pre[i + 1] = pre[i] + nums[i];
        }

        long long rt = -1e18;
        
        // 依序計算選出 1 到 m 個子陣列的狀態
        for(int c = 1; c <= m; ++c) {
            // 單調遞減佇列，儲存 {dp[c-1][j] - pre[j], j}
            deque<pair<long long, int>> dq;
            
            // i 為當前子陣列的結束位置
            for(int i = l; i <= n; ++i) {
                int j = i - l; // 最新進入合法視窗 [i-r, i-l] 的左端點
                long long upd = dp[c - 1][j] - pre[j];
                
                // 1. 淘汰過期者：移出超出長度上限 r 的端點
                while(!dq.empty() && i - dq.front().second > r) {
                    dq.pop_front();
                }
                
                // 2. 維護單調性：剔除比當前值小的不具競爭力選項
                while(!dq.empty() && dq.back().first <= upd) {
                    dq.pop_back();
                }
                
                // 3. 加入新端點
                dq.push_back({upd, j});
                
                // 4. O(1) 狀態轉移：
                // 比較「不以 i 結尾」和「以 i 結尾搭配最佳左端點」
                dp[c][i] = max(dp[c][i - 1], pre[i] + (dq.empty() ? 0 : dq.front().first));
            }
            // 記錄所有選滿 c 個子陣列的全局最大值
            rt = max(rt, dp[c][n]);
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