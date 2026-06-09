---
title: "LeetCode 3938. Maximum Path Intersection Sum in a Grid"
date: 2026-05-24T15:45:00+08:00
lastmod: 2026-05-24T15:45:00+08:00
difficulty: 2251
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic-programming", "kadane"]
keywords: ["LC3938", "Medium", "Kadane's Algorithm", "Grid Path", "最大子陣列和", "一維動態規劃"]
description: "LeetCode 第 3938 題：Maximum Path Intersection Sum in a Grid。探討如何將 Kadane's Algorithm (最大子陣列和) 應用於二維矩陣的行列掃描中，並結合特定的幾何邊界條件進行極值過濾。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3938](https://leetcode.com/problems/maximum-path-intersection-sum-in-a-grid/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` (Kadane's Algorithm)

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個大小為 $m \times n$ 的二維整數矩陣 `grid`。
我們定義一條「合法路徑」為以下兩種情況之一：
1. 位於同一行或同一列上，且長度 **至少為 2** 的連續子陣列。
2. 長度為 **1** 的單一單元格，但該單元格必須 **嚴格位於矩陣內部**（即不能位於第一行、最後一行、第一列或最後一列）。

請計算並回傳所有「合法路徑」中的 **最大元素總和**。

### 限制條件
- $3 \le m, n \le 10^5$ (依據內部格子限制推斷)
- $-10^4 \le grid[i][j] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-24：初次提交 (行列分離掃描與邊界 Kadane 過濾)](#2026-05-24-初次提交)

---

## 💡 2026-05-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的本質是經典動態規劃題 **Maximum Subarray (Kadane's Algorithm)** 的變形與降維打擊。

因為路徑只能是單純的水平或垂直線段，我們完全可以把這個二維問題拆解為：
1. 對每一「行」跑一次一維的 Kadane's Algorithm。
2. 對每一「列」跑一次一維的 Kadane's Algorithm。

**核心破局點：精細的長度與邊界控制**
標準的 Kadane 演算法在每步會取 `prev = max(grid[i], prev + grid[i])`，並將 `prev` 納入全局最大值計算。但本題對長度有嚴格要求：
- 我們利用 `cur = prev + grid[i][j]` 來強迫路徑長度至少為 2，並用它來更新全局最大值 `mx`。
- 對於單點 `grid[i][j]`，我們只在它滿足 `i != 0 && i != m-1 && j != 0 && j != n-1` (嚴格內部單元格) 時，才允許它單獨去挑戰全局最大值 `mx`。
- 在狀態繼承時，`prev` 依然標準地接收 `max(cur, grid[i][j])`，確保下一輪的連續加總不會斷裂。

---

### 🛠️ 解題思路 (Approach)
1. **水平掃描 (Row-wise Scan)**：
   - 遍歷每一行 `i`，初始化 `prev = grid[i][0]`。
   - 從第二行開始掃描 (`j = 1` 到 `n-1`)。
   - 計算長度 $\ge 2$ 的連續和：`cur = prev + grid[i][j]`，並更新 `mx`。
   - 若當前格子位於嚴格內部，允許單點和挑戰 `mx`。
   - 更新 Kadane 狀態：`prev = max(cur, grid[i][j])`。
2. **垂直掃描 (Column-wise Scan)**：
   - 遍歷每一列 `j`，初始化 `prev = grid[0][j]`。
   - 從第二列開始掃描 (`i = 1` 到 `m-1`)。
   - 同樣計算長度 $\ge 2$ 的連續和 `cur` 並更新 `mx`。
   - *(註：內部單點挑戰已在水平掃描中完全覆蓋，垂直掃描不需重複檢查單點)*。
   - 更新 Kadane 狀態。
3. **回傳結果**：回傳全局最大值 `mx`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。每個格子在水平掃描中被訪問一次，在垂直掃描中被訪問一次。總操作次數與矩陣大小成正比。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了幾個常數級別的變數 (`prev`, `cur`, `mx`) 進行狀態滾動，無需額外配置陣列。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxScore(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        int mx = INT_MIN;

        // 1. 水平方向掃描 (Row-wise Kadane's)
        for(int i = 0; i < m; ++i) {
            int prev = grid[i][0];
            for(int j = 1; j < n; ++j) {
                // cur 強制代表長度 >= 2 的連續子陣列和
                int cur = prev + grid[i][j];
                mx = max(mx, cur);
                
                // 狀態滾動：決定當前點是要接上前面的路徑，還是自己自立門戶重新開始
                prev = max(cur, grid[i][j]);
                
                // 特殊條件過濾：只有嚴格位於內部的格子，才能以「長度 1」的姿態挑戰最大值
                if(i != 0 && i != m - 1 && j != 0 && j != n - 1) {
                    mx = max(mx, grid[i][j]);
                }
            }
        }

        // 2. 垂直方向掃描 (Column-wise Kadane's)
        for(int j = 0; j < n; ++j) {
            int prev = grid[0][j];
            for(int i = 1; i < m; ++i) {
                int cur = prev + grid[i][j];
                mx = max(mx, cur);
                
                prev = max(cur, grid[i][j]);
                // 內部格子的單點最大值已在上面水平掃描時涵蓋過，此處無需重複檢查
            }
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