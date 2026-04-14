---
title: "LeetCode 2463. Minimum Total Distance Traveled"
date: 2026-04-14T20:30:00+08:00
lastmod: 2026-04-14T20:30:00+08:00
difficulty: 2453
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "sorting", "array", "prefix-sum"]
keywords: ["LC2463", "2453", "Hard", "Minimum Total Distance", "Dynamic Programming", "Flattening", "Knapsack"]
description: "LeetCode 第 2463 題：Minimum Total Distance Traveled。難度評分：2453。完整收錄兩種高階動態規劃解法：利用「容量扁平化」降維的 O(M*N) 匹配 DP，以及利用「預處理距離和」與區間分配枚舉的 O(N*M^2) 正統背包變形 DP。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2463](https://leetcode.com/problems/minimum-total-distance-traveled/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2453)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Sorting` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
在一個一維的數線上，有 $m$ 個機器人和 $n$ 個工廠。給定一個整數陣列 `robot` 代表每個機器人的初始位置，以及一個二維整數陣列 `factory`，其中 `factory[j] = [position_j, limit_j]` 代表第 $j$ 個工廠的位置與其最多能維修的機器人數量限制。
每個機器人必須被分配到一個工廠進行維修。
請計算並回傳所有機器人移動到各自被分配的工廠的**最小總移動距離**。

### 限制條件
- $1 \le robot.length \le 100$
- $1 \le factory.length \le 100$
- $-10^9 \le robot[i], position_j \le 10^9$
- $0 \le limit_j \le 100$
</details>

---

## 📝 歷次打卡與解法演進
這道 Hard 題的核心在於「一個工廠可以收容多個機器人」，這破壞了傳統 1 對 1 DP 的結構。以下收錄兩種截然不同的破局思路：
- **[解法一：容量扁平化 (Flattening)](#解法一-容量扁平化-flattening)**：改變資料結構，將工廠拆解，降維成 1 對 1 匹配。
- **[解法二：區間分配 DP (Group Assignment DP)](#解法二-區間分配-dp-group-assignment-dp)**：保留原始結構，透過枚舉分配數量與預處理距離，完成標準的區間狀態轉移。

---

## 💡 解法一：容量扁平化 (Flattening)

### 🎯 演算法分析
- **不交叉原則**：為了使距離最小，機器人與工廠的分配連線不能交叉。因此先將兩者依座標遞增排序。
- **降維打擊**：將容量為 $K$ 的工廠拆解成 $K$ 個「容量為 1」的獨立虛擬工廠。問題瞬間退化成簡單的 1 對 1 匹配 DP (`dp[i][j]`：前 $i$ 個機器人分配給前 $j$ 個虛擬工廠)。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long minimumTotalDistance(vector<int>& robot, vector<vector<int>>& factory) {
        int m = robot.size();
        
        // 容量扁平化：將工廠按容量拆解為單一空位
        vector<int> f;
        for(auto& fa : factory) {
            for(int i = 0; i < fa[1]; ++i) f.push_back(fa[0]);
        }
        int n = f.size();
        
        sort(robot.begin(), robot.end());
        sort(f.begin(), f.end());
        
        vector<vector<long long>> dp(m + 1, vector<long long>(n + 1, LLONG_MAX / 2));
        for(int j = 0; j <= n; ++j) dp[0][j] = 0;
        
        for(int i = 1; i <= m; ++i) {
            for(int j = i; j <= n; ++j) {
                dp[i][j] = min(dp[i][j-1], dp[i-1][j-1] + abs(robot[i-1] - f[j-1]));
            }
        }
        
        return dp[m][n];
    }
};
```
- **時間複雜度**: $\mathcal{O}(M \times N_{limit})$。$N_{limit}$ 是所有工廠的容量總和。
- **空間複雜度**: $\mathcal{O}(M \times N_{limit})$。

---

## 💡 解法二：區間分配 DP (Group Assignment DP)

### 🎯 演算法分析
不改變原有的工廠結構，直接在 DP 中枚舉「第 $j$ 個工廠要吃下幾個機器人」。
- **狀態定義**：`dp[j][i]` 代表前 $j$ 個工廠負責覆蓋前 $i$ 個機器人的最小總成本。
- **狀態拆解視覺化**：
  ```text
  機器人序列: X X X | X X | | X X | X X i
  負責工廠:                        j 
  ```
- **狀態轉移方程式**：
  $$dp[j][i] = \min \begin{cases} dp[j-1][i] & (\text{工廠 } j \text{ 不收機器人}) \\ \min_{k=1}^{\min(limit_j, i)} \left( dp[j-1][i-k] + \text{dist}(j-1, i-k, i-1) \right) & (\text{收納 } k \text{ 個}) \end{cases}$$
- **初始狀態**：
  - `dp[0][0] = 0`, `dp[j][0] = 0` (0 個機器人成本為 0)。
  - `dp[0][i] = LLONG_MAX` (沒有工廠卻有機器人，為不合法狀態)。
- **預處理優化**：預先計算 `dist[j][i][k]`，提早算好第 $j$ 個工廠處理區間 $[i, k]$ 機器人的成本，讓核心 DP 迴圈保持 O(1) 狀態轉移。

### 💻 程式碼實作 (C++)
```cpp
// 全域宣告避免 Stack Overflow，dist[j][i][k] 記錄第 j 個工廠處理機器人 [i, k] 的成本
long long dist[105][105][105];

class Solution {
public:
    long long minimumTotalDistance(vector<int>& robot, vector<vector<int>>& factory) {
        int m = robot.size(), n = factory.size();
        
        sort(robot.begin(), robot.end());
        sort(factory.begin(), factory.end());
        
        // dp[j][i]：前 j 個工廠處理前 i 個機器人的最小成本
        vector<vector<long long>> dp(n + 1, vector<long long>(m + 1, LLONG_MAX / 2));
        
        // Base case
        for(int j = 0; j <= n; ++j) dp[j][0] = 0;

        // 1. 預處理所有區間的距離成本
        for(int j = 0; j < n; ++j) {
            for(int i = 0; i < m; ++i) {
                long long sum = 0;
                // 工廠 j 從機器人 i 開始收納，最多收 limit 個
                for(int k = i; k < min(m, i + factory[j][1]); ++k) {
                    sum += abs(factory[j][0] - robot[k]);
                    dist[j][i][k] = sum;
                }
            }
        }
        
        // 2. DP 狀態轉移
        for(int j = 1; j <= n; ++j) {
            for(int i = 1; i <= m; ++i) {
                // 預設當前工廠不收納任何機器人
                dp[j][i] = dp[j-1][i];
                
                // 枚舉當前工廠收納 k 個機器人
                for(int k = 1; k <= min(factory[j-1][1], i); ++k) {
                    dp[j][i] = min(dp[j][i], 
                                   dp[j-1][i-k] + dist[j-1][i-k][i-1]);
                }
            }
        }
        
        return dp[n][m];
    }
};
```
- **時間複雜度**: $\mathcal{O}(N \times M^2)$。預處理需 $\mathcal{O}(N \cdot M^2)$，DP 填表需 $\mathcal{O}(N \cdot M \cdot K_{limit})$。
- **空間複雜度**: $\mathcal{O}(N \times M^2)$。全域陣列 `dist` 的空間為主導。

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