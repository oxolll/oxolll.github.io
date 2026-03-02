---
title: "LeetCode 1536. Minimum Swaps to Arrange a Binary Grid"
date: 2026-03-03T05:44:00+08:00
lastmod: 2026-03-03T05:44:00+08:00
difficulty: 1880
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Greedy", "Array", "Matrix"]
keywords: ["LC1536", "1880", "Medium", "Trailing Zeros", "Bubble Swap", "貪心演算法"]
description: "LeetCode 第 1536 題：Minimum Swaps to Arrange a Binary Grid。難度評分：1880。探討如何將二維矩陣降維為一維的連續後綴零陣列，並運用貪心演算法與相鄰交換來滿足主對角線以上的全零條件。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1536](https://leetcode.com/problems/minimum-swaps-to-arrange-a-binary-grid/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1880)</span>   
> **核心主題**：`Greedy` $\cdot$ `Array` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $n \times n$ 的二進位矩陣 `grid`。
在一次操作中，你可以選擇矩陣中的**任意兩相鄰列 (Adjacent Rows)** 並交換它們。
如果矩陣中主對角線以上的所有儲存格都是 0，我們就稱這個矩陣是有效的 (Valid)。
請回傳讓矩陣變為有效所需的**最小交換次數**。如果無法達成，則回傳 -1。

### 範例
- **Input**: `grid = [[0,0,1],[1,1,0],[1,0,0]]` → **Output**: `3`
- **Explanation**: 
  1. 交換第 1 列和第 2 列 -> `[[0,0,1],[1,0,0],[1,1,0]]`
  2. 交換第 0 列和第 1 列 -> `[[1,0,0],[0,0,1],[1,1,0]]`
  3. 交換第 1 列和第 2 列 -> `[[1,0,0],[1,1,0],[0,0,1]]`
  此時主對角線上的元素皆為 0。最小交換次數為 3。

### 限制條件
- $n == grid.length == grid[i].length$
- $1 \le n \le 200$
- `grid[i][j]` 是 0 或 1。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-03：初次提交 (降維攤平與貪心相鄰交換)](#2026-03-03-初次提交)

---

## 💡 2026-03-03 初次提交

### 🎯 直覺 (Intuition)
要在主對角線以上全為 0，這句話翻譯成白話文就是：「第 0 列的結尾至少要有 $n-1$ 個 0，第 1 列的結尾至少要有 $n-2$ 個 0... 以此類推」。
既然只關心「結尾連續 0 的數量」，我們完全可以把這個二維矩陣**攤平 (Flatten)**，轉化成一個一維陣列 `arr`，其中 `arr[i]` 記錄著第 $i$ 列的後綴 0 數量。接下來的問題就變成了：如何用最少的相鄰交換，讓 `arr[i] >= n - 1 - i` 對所有 $i$ 成立？

### 🛠️ 解題思路 (Approach)
1. **降維與前處理**：
   - 掃描每一列，從右往左數連續的 0 的個數，存入一維陣列 `arr` 中。
2. **無解的 Early Exit (提早判定)**：
   - 將 `arr` 複製一份並由大到小排序得到 `sorted`。
   - 由於第 $i$ 列最少需要 $n-1-i$ 個 0，如果我們手中**最大的第 $i$ 個後綴 0 數量**，連這個最低門檻都無法滿足 (`sorted[i] < dec`)，那就代表絕對無解，直接回傳 `-1`。
3. **貪心尋找與氣泡交換 (Greedy & Bubble Swap)**：
   - 從第 0 列開始往下滿足需求。當前需要的 0 數量為 `dec` (初始為 $n-1$)。
   - **貪心策略**：從當前位置 $i$ 開始往下找，找到**第一個**滿足 `arr[j] >= dec` 的位置 $j$。
   - **貪心正確性**：因為當前位置 $i$ 對 0 的需求是**最嚴苛**的。在有解的前提下，拿最近的滿足值來用，絕對不會讓後面的位置變得無解。如果拿了最近的卻導致後面無解，就代表整個陣列原本就無法滿足所有條件，這與「有解」的前提矛盾。此外，拿最近的位置能保證將它往上交換的次數 (`j - i`) 是最少的。
   - 找到 $j$ 後，不斷與上面的元素進行相鄰交換 (`swap`)，並將交換次數 `j - i` 累加到結果中。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N^2)$。計算後綴 0 需 $\mathcal{O}(N^2)$；排序檢查需 $\mathcal{O}(N \log N)$；最差情況下的貪心尋找與交換，對於每個位置 $i$ 都要往下找並交換，這部分的極限也是 $\mathcal{O}(N^2)$。因 $N \le 200$，運算量遠低於時間限制。
- **空間複雜度**: $\mathcal{O}(N)$。用於儲存攤平後的一維陣列 `arr` 與排序用的 `sorted` 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minSwaps(vector<vector<int>>& grid) {
        int n = grid.size();
        vector<int> arr; 
        arr.reserve(n);
        
        // 1. 將二維矩陣降維，計算每一列的後綴 0 數量
        for(int i = 0; i < n; ++i) {
            int cnt = 0;
            for(int j = n - 1; j >= 0 && grid[i][j] == 0; --j) {
                ++cnt;
            }
            arr.push_back(cnt);
        }

        // 2. Early Exit：透過排序檢查是否必定無解
        vector<int> sorted(arr.begin(), arr.end());
        sort(sorted.begin(), sorted.end(), [&](const int a, const int b) {
            return a > b;
        });

        int rt = 0;
        int dec = n - 1; // 每一列所需的最少後綴 0 數量
        
        // 3. 貪心尋找最近的滿足列並進行相鄰交換
        for(int i = 0; i < n; ++i) {
            // 如果連手邊第 i 大的後綴 0 數量都無法滿足需求，必定無解
            if(sorted[i] < dec) return -1;

            int j = i;
            // 往下尋找第一個滿足條件的列
            while(dec > arr[j]) ++j;
            --dec; // 下一列的需求數量會減少 1

            // 將距離累加至答案
            rt += j - i;
            // 執行相鄰交換，模擬將該列「浮」上來 (Bubble up)
            for(; j > i; --j) {
                swap(arr[j - 1], arr[j]);
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