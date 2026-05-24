---
title: "LeetCode 3933. Largest Local Values in a Matrix II"
date: 2026-05-18T01:30:00+08:00
lastmod: 2026-05-18T01:30:00+08:00
difficulty: 2052
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "matrix", "precomputation", "rmq", "simulation"]
keywords: ["LC3933", "Medium", "Local Maximum", "Range Maximum", "矩陣模擬", "區間極值"]
description: "LeetCode 第 3933 題：Largest Local Values in a Matrix II。難度評分：Medium(2052)。探討如何利用行內區間最大值預處理 (RMQ)，高效判定變動半徑且去頂點正方形鄰域內的局部最大值數量。"
---

## 📊 題目資訊
> **核心主題**：`Matrix` $\cdot$ `Precomputation` $\cdot$ `Simulation`  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (2052)</span>

---

## 📖 題目描述
給定一個大小為 $m \times n$ 的整數矩陣 `matrix`。
對於矩陣中的每個單元格 $(i, j)$，如果其值 $matrix[i][j] = cur > 0$，則它擁有一個以其自身為中心、半徑為 $cur$ 的**特定幾何鄰域**。

該鄰域的範圍定義為：以 $(i, j)$ 為中心，邊長為 $2 \cdot cur + 1$ 的正方形，但**不包含該正方形的四個角落頂點**：
1. 左上角：$(i - cur, j - cur)$
2. 右上角：$(i - cur, j + cur)$
3. 左下角：$(i + cur, j - cur)$
4. 右下角：$(i + cur, j + cur)$

如果單元格 $(i, j)$ 的值 $cur$ **大於或等於**其特定幾何鄰域內所有其他單元格的值，則稱該點為一個「局部極大值點」。請計算並回傳矩陣中共有多少個這樣的點。

---

## 🎯 演算法分析 (Algorithm Analysis)

### 核心破局點：行內區間最大值預處理 (Row-wise RMQ)
如果對每個點都採取純暴力內圈搜尋，時間複雜度會達到 $\mathcal{O}(m \times n \times cur^2)$，容易引發逾時。

本解法引入了**區間極值預處理**的技巧：
1. **預處理階段**：利用三維陣列 `rangeMX[i][l][r]` 事先儲存第 $i$ 行中，從列索引 $l$ 到 $r$ 的最大值。
2. **幾何邊界過濾**：當我們檢查中心點 $(i, j)$ 的鄰域行 $k$ 時：
   * **邊界行**（$k = i - cur$ 或 $k = i + cur$）：題目要求扣除四個角落，因此合法的列區間需向內收縮為 $[j - cur + 1, j + cur - 1]$。
   * **內部行**（其餘夾在中間的行）：完整的列區間皆為合法鄰域，範圍為 $[j - cur, j + cur]$。

透過預處理快取，每一行的最大值比對均可在 $\mathcal{O}(1)$ 內完成，大幅提升動態半徑的搜尋效率。

---

## 💻 程式碼實作 (C++)

```cpp
class Solution {
public:
    int countLocalMaximums(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        int rangeMX[m][n][n];

        // 1. 預處理每一行任意區間 [l, r] 的最大值
        for(int i = 0; i < m; ++i)
        {
            for(int l = 0; l < n; ++l)
            {
                int mx = matrix[i][l];
                for(int r = l; r < n; ++r)
                {
                    mx = max(mx, matrix[i][r]);
                    rangeMX[i][l][r] = mx;
                }
            }
        }

        int rt = 0;
        // 2. 遍歷矩陣檢查每個點是否為局部最大值
        for(int i = 0; i < m; ++i)
        {
            for(int j = 0; j < n; ++j)
            {
                if(matrix[i][j] == 0) continue;
                int cur = matrix[i][j];

                bool flag = true;
                int l = max(0, j-cur), r = min(n-1, j+cur);
                
                // 按行掃描該點對應的半徑鄰域
                for(int k = max(0, i-cur); flag && k <= min(m-1, i+cur); ++k)
                {
                    // 情況 A：屬於最頂部或最底部的邊界行，需排除四個角落點
                    if(k == i-cur || k == i+cur)
                    {
                        int L = max(j-cur+1, 0), R = min(j+cur-1, n-1);
                        if(L <= R && rangeMX[k][L][R] > cur) flag = false;
                        continue;
                    }
                    
                    // 情況 B：屬於中間行，查詢完整的列區間 [l, r]
                    if(rangeMX[k][l][r] > cur) flag = false;
                }

                // 若鄰域內沒有任何元素大於當前值，則計數加 1
                if(flag)
                {
                    ++rt;
                }
            }
        }
        return rt;
    }
};
```

---

## 📊 複雜度分析

* **時間複雜度**: $\mathcal{O}(m \cdot n^2 + m^2 \cdot n)$
  * **預處理階段**：需要三層迴圈枚舉每行的左右端點，耗時 $\mathcal{O}(m \cdot n^2)$。
  * **驗證階段**：每個點最多檢查 $2 \cdot cur + 1$ 行，單行查詢為 $\mathcal{O}(1)$，全圖驗證耗時 $\mathcal{O}(m \cdot n \cdot m) = \mathcal{O}(m^2 \cdot n)$。
  * 對於 $m, n \le 300$ 的規模，總運算量在 $2.7 \times 10^7$ 次以內，效率極佳。
* **空間複雜度**: $\mathcal{O}(m \cdot n^2)$  
  * 需要一個三維陣列 `rangeMX[m][n][n]` 來儲存區間最值快取。

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