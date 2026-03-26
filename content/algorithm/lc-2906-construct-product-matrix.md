---
title: "LeetCode 2906. Construct Product Matrix"
date: 2026-03-24T17:30:00+08:00
lastmod: 2026-03-24T17:30:00+08:00
difficulty: 2074
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["Leetcode", "Array", "Matrix", "Prefix sum", "Math"]
keywords: ["LC2906", "2074", "Medium", "Construct Product Matrix", "Prefix Product", "Modulo"]
description: "LeetCode 第 2906 題：Construct Product Matrix。難度評分：2074。探討如何將 2D 矩陣降維思考，並利用前後綴乘積 (Prefix/Suffix Product) 配合模運算，以 O(N * M) 時間複雜度求出排除自身的元素乘積。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2906](https://leetcode.com/problems/construct-product-matrix/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (2074)</span>  
> **核心主題**：`Array` $\cdot$ `Matrix` $\cdot$ `Prefix Sum` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個以 0 為起始索引的 $n \times m$ 整數矩陣 `grid`。
請回傳一個大小相同的整數矩陣 `p`，其中 `p[i][j]` 的值等於 `grid` 中除了 `grid[i][j]` 之外所有元素的乘積。因為乘積可能非常大，請將每個 `p[i][j]` 的結果對 `12345` 取餘數 (Modulo) 後再回傳。

### 限制條件
- $1 \le n, m \le 10^5$
- $1 \le n \times m \le 10^5$
- $1 \le grid[i][j] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-24：初次提交 (一維化前後綴乘積與模運算)](#2026-03-24-初次提交)

---

## 💡 2026-03-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題本質上是經典題「238. Product of Array Except Self」的二維版本。
在處理「除了自己以外的乘積」時，不能使用總乘積除以自身的方法，原因有二：
1. 矩陣中可能包含 `0`，會導致除以零的錯誤。
2. 題目要求對 `12345` 取模。在模運算中，除法必須透過「模反元素 (Modular Multiplicative Inverse)」來實作，但 `12345` 並非質數，無法保證每個數字都存在模反元素。

因此，唯一的最佳解法就是拆解問題：**除了自己以外的乘積 = (左上到右下的前綴乘積) $\times$ (右下到左上的後綴乘積)**。
為了簡化二維座標的處理，我們可以將 $n \times m$ 的矩陣邏輯上「攤平」成一個長度為 $n \times m$ 的一維陣列來推導索引。

### 🛠️ 解題思路 (Approach)
1. **常數宣告**：設定模數 `MOD = 12345`。
2. **前後綴乘積預處理**：
   - 宣告兩個一維陣列 `prefix` 與 `suffix`，並利用 `reserve` 預先配置 $N \times M$ 的記憶體以加速執行。
   - 使用雙層迴圈同時計算前綴與後綴。
   - **前綴 (`pre`)**：從左上 `grid[0][0]` 遍歷至右下。
   - **後綴 (`post`)**：從右下 `grid[m-1][n-1]` 反向遍歷至左上。透過 `grid[m-1-i][n-1-j]` 精準取得對稱座標。
   - 每次乘法操作後立即對 `12345` 取模，防止 `long long` 溢位。
3. **組合最終矩陣**：
   - 宣告與原矩陣相同大小的結果矩陣 `rt`。
   - 再次使用雙層迴圈遍歷。將當前二維座標轉換為一維索引 `idx = i * n + j`。
   - 計算對應的後綴陣列索引 `idx2 = n * m - 1 - (idx + 1)`。
   - 結果 `rt[i][j]` 即為 `prefix[idx - 1]` 乘上 `suffix[idx2]`。
   - 注意邊界條件：若 `idx == 0`，前綴視為 `1`；若 `idx2 < 0`，後綴視為 `1`。相乘時加上 `1LL` 強制轉型，最後再取模。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times M)$。其中 $N$ 和 $M$ 為矩陣的列數與行數。預處理前後綴與組合結果皆只需遍歷矩陣兩次，時間複雜度為線性。
- **空間複雜度**: $\mathcal{O}(N \times M)$。使用了兩個長度為 $N \times M$ 的一維陣列 `prefix` 與 `suffix` 來儲存狀態。（註：與 238 題相同，此題也可進一步優化空間複雜度，將 `rt` 當作前綴陣列，並在反向遍歷時以常數變數維護後綴乘積，達到 $\mathcal{O}(1)$ 額外空間）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const long long MOD = 12345;
    
    vector<vector<int>> constructProductMatrix(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        
        // 攤平思維：宣告一維陣列儲存前後綴
        vector<int> prefix; prefix.reserve(m * n);
        vector<int> suffix; suffix.reserve(m * n);

        long long pre = 1;
        long long post = 1;
        
        // 同時建立由前向後的前綴乘積，以及由後向前的後綴乘積
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // 正向遍歷
                pre = (pre * grid[i][j]) % MOD;
                prefix.push_back(pre);
                
                // 反向遍歷
                post = (post * grid[m - 1 - i][n - 1 - j]) % MOD;
                suffix.push_back(post);
            }
        }

        vector<vector<int>> rt(m, vector<int>(n, 0));
        
        // 組合最終結果
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                // 轉換為 1D 索引
                int idx = i * n + j;
                // 計算對應的 suffix 索引 (扣除自身與前方元素)
                int idx2 = n * m - 1 - (idx + 1);
                
                // 左側前綴 * 右側後綴 (使用 1LL 確保乘法過程以 64-bit 進行)
                rt[i][j] = ((idx > 0 ? prefix[idx - 1] : 1) * 1LL * (idx2 >= 0 ? suffix[idx2] : 1)) % MOD;
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