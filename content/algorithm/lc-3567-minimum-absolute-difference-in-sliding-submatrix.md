---
title: "LeetCode 3567. Minimum Absolute Difference in Sliding Submatrix"
date: 2026-03-20T14:08:00+08:00
lastmod: 2026-03-20T14:08:00+08:00
difficulty: 1568
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Matrix", "Sliding Window", "Sorting"]
keywords: ["LC3567", "1568", "Medium", "Sliding Submatrix", "Absolute Difference", "Sorting"]
description: "LeetCode 第 3567 題：Minimum Absolute Difference in Sliding Submatrix。難度評分：1568。探討如何透過一維座標映射與記憶體重用技巧，在二維滑動視窗中利用排序高效求出相異元素的最小絕對差。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3567](https://leetcode.com/problems/minimum-absolute-difference-in-sliding-submatrix/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1568)</span>    
> **核心主題**：`Matrix` $\cdot$ `Sliding Window` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $m \times n$ 的整數矩陣 `grid` 以及一個整數 `k`。
對於矩陣中每一個大小為 $k \times k$ 的連續子矩陣，請計算該子矩陣內**任意兩個相異元素**之間的最小絕對差。
如果子矩陣內的所有元素都相同（即不存在兩個相異元素），則該子矩陣的結果為 `0`。

請回傳一個大小為 $(m - k + 1) \times (n - k + 1)$ 的二維矩陣 `rt`，其中 `rt[i][j]` 代表以 `grid[i][j]` 為左上角的 $k \times k$ 子矩陣的計算結果。

### 限制條件
- $m == grid.length$
- $n == grid[i].length$
- 測資範圍與 $k$ 值依據實際題目設定，保證暴力排序法可於時間限制內通過。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-20：初次提交 (子矩陣展開與局部排序法)](#2026-03-20-初次提交)

---

## 💡 2026-03-20 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在一個無序的數值集合中，要尋找任意兩元素的「最小絕對差」，最佳策略是先將集合進行排序，接著僅需計算相鄰元素之間的差值即可。
由於必須對每一個 $k \times k$ 的子矩陣獨立進行計算，最直接的做法是枚舉所有可能的左上角座標 `(i, j)`，將該子矩陣內的所有元素提取至一個一維陣列中，對其進行排序，並線性掃描求出相鄰且不相等元素的最小差值。

### 🛠️ 解題思路 (Approach)
1. **初始化與記憶體分配**：
   - 計算產生的結果矩陣大小為 $(m - k + 1) \times (n - k + 1)$，並將其初始化為 `0`。
   - 在外層宣告 `vector<int> tmp` 用於儲存子矩陣元素。此舉可避免在多層迴圈內頻繁觸發動態記憶體配置 (Memory Allocation)。
2. **滑動視窗遍歷**：
   - 使用雙層迴圈枚舉子矩陣的左上角起點 `i` 與 `j`。
   - 每次進入新的子矩陣前，呼叫 `tmp.clear()` 清空元素數量，但保留底層陣列容量。
3. **一維轉二維座標映射**：
   - 總共需要提取 `count = k * k` 個元素。
   - 透過單一迴圈變數 `K`，利用整數除法 `K / k` 與餘數 `K % k`，推導出該元素在原矩陣中的相對座標 `(i + K/k, j + K%k)`，將其推入 `tmp` 中。
4. **排序與差值計算**：
   - 對 `tmp` 進行降冪排序 (`sort(tmp.rbegin(), tmp.rend())`)。
   - 掃描排序後的陣列，若相鄰元素不相等 (`tmp[l] != tmp[l+1]`)，則計算差值並更新局部最小值 `mm`。
   - 若 `mm` 仍為 `INT_MAX`，代表陣列中所有元素皆相同，依題意紀錄為 `0`；否則紀錄求得的最小差值 `mm`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \cdot N \cdot k^2 \log k)$。給定子矩陣數量約為 $M \times N$，每個子矩陣需提取 $k^2$ 個元素並進行排序，排序成本為 $\mathcal{O}(k^2 \log(k^2)) = \mathcal{O}(k^2 \log k)$。
- **空間複雜度**: $\mathcal{O}(k^2)$。暫存陣列 `tmp` 所需的空間。結果矩陣不計入額外空間複雜度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> minAbsDiff(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        int count = k * k;
        
        // 將 tmp 宣告於外層以重複利用 Capacity，減少 reallocation 開銷
        vector<int> tmp;
        vector<vector<int>> rt(m - k + 1, vector<int>(n - k + 1, 0));
        
        for(int i = 0; i <= m - k; ++i) 
        {
            for(int j = 0; j <= n - k; ++j) 
            {
                tmp.clear();
                
                // 利用一維索引 K 映射至 k x k 子矩陣的二維相對座標
                for(int K = 0; K < count; ++K) {
                    int cur = grid[i + K / k][j + K % k];
                    tmp.push_back(cur);
                }
                
                // 排序以利後續線性尋找最小差值
                sort(tmp.rbegin(), tmp.rend());

                int mm = INT_MAX;
                // 線性掃描尋找相鄰且相異元素的最小差值
                for(int l = 0; l < tmp.size() - 1; ++l) 
                {
                    if(tmp[l] != tmp[l+1]) 
                        mm = min(mm, tmp[l] - tmp[l+1]);
                }
                        
                // 若 mm 未被更新，代表所有元素皆相同，依題意設為 0
                rt[i][j] = (mm == INT_MAX ? 0 : mm);
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