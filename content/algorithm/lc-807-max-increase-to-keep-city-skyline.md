---
title: "LeetCode 807. Max Increase to Keep City Skyline"
date: 2026-03-26T16:05:00+08:00
lastmod: 2026-03-26T16:05:00+08:00
difficulty: 1376
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "matrix", "greedy"]
keywords: ["LC807", "1376", "Medium", "City Skyline", "Matrix", "Greedy"]
description: "LeetCode 第 807 題：Max Increase to Keep City Skyline。難度評分：1376。探討如何預先處理矩陣的行列極值，以 O(N^2) 時間複雜度計算在不改變城市天際線的條件下，所有建築物高度可增加的最大總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 807](https://leetcode.com/problems/max-increase-to-keep-city-skyline/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1376)</span>  
> **核心主題**：`Array` $\cdot$ `Matrix` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $n \times n$ 的整數矩陣 `grid`，其中 `grid[i][j]` 代表位於座標 `(i, j)` 的建築物高度。
從四個方向（北、南、東、西）觀看這座城市時，會看到由建築物形成的「天際線」。天際線是由該方向上每一行或每一列的最高建築物所組成的輪廓。
你可以增加任何建築物的高度，但增加後不能改變從任何方向觀看的天際線。

請回傳所有建築物高度可增加的最大總和。

### 限制條件
- $n == grid.length$
- $n == grid[i].length$
- $2 \le n \le 50$
- $0 \le grid[i][j] \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (預先計算行列極值)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
為了不改變任何方向的天際線，位於座標 `(i, j)` 的建築物，其增加後的高度絕對不能超過所在列 (Row `i`) 的最大高度，也不能超過所在行 (Column `j`) 的最大高度。
因此，座標 `(i, j)` 的建築物可達到的最大合法高度為 $\min(\max(\text{Row}_i), \max(\text{Col}_j))$。
為避免在計算每個座標時重複掃描行列，我們需要先進行一次全域走訪，將每一列與每一行的最大值記錄下來，隨後再進行第二次走訪結算高度差。

### 🛠️ 解題思路 (Approach)
1. **宣告記錄陣列**：宣告一個 $2 \times n$ 的二維陣列 `DIR`。`DIR[0][i]` 用於記錄第 $i$ 行 (Column) 的最大值，`DIR[1][i]` 用於記錄第 $i$ 列 (Row) 的最大值。
2. **預處理極值**：利用雙層迴圈走訪 `grid`。在座標 `(i, j)` 處，更新 `DIR[0][i] = max(DIR[0][i], grid[j][i])` 以及 `DIR[1][i] = max(DIR[1][i], grid[i][j])`。*(註：迴圈內的索引對應設計將行列處理合併於同一個迴圈層級中)*。
3. **計算可增加高度**：再次使用雙層迴圈走訪 `grid`。對於每個座標 `(i, j)`，其可增加的額度為 `min(DIR[0][j], DIR[1][i]) - grid[i][j]`，將此值累加至 `rt`。
4. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。矩陣的大小為 $N \times N$。總共執行兩次雙層迴圈的完整走訪。
- **空間複雜度**: $\mathcal{O}(N)$。使用了一個大小為 $2 \times N$ 的 `DIR` 陣列來儲存行列的最大值。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxIncreaseKeepingSkyline(vector<vector<int>>& grid) {
        int n = grid.size();
        
        // DIR[0] 儲存每一行的最大值 (Column Max)
        // DIR[1] 儲存每一列的最大值 (Row Max)
        vector<vector<int>> DIR(2, vector<int>(n, 0));
        
        for(int i = 0; i < n; ++i) {
            for(int j = 0; j < n; ++j) {
                DIR[0][i] = max(DIR[0][i], grid[j][i]);
                DIR[1][i] = max(DIR[1][i], grid[i][j]);
            }
        }

        int rt = 0;
        
        for(int i = 0; i < n; ++i) {
            for(int j = 0; j < n; ++j) {
                // 可增加的最大高度 = 該列極值與該行極值取最小值 - 原始高度
                rt += min(DIR[0][j], DIR[1][i]) - grid[i][j];
            }
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