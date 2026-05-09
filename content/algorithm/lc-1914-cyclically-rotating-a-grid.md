---
title: "LeetCode 1914. Cyclically Rotating a Grid"
date: 2026-05-09T15:00:00+08:00
lastmod: 2026-05-09T15:00:00+08:00
difficulty: 1766
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "matrix", "simulation", "math"]
keywords: ["LC1914", "Medium", "1766", "Matrix Rotation", "殼層遍歷", "矩陣旋轉"]
description: "LeetCode 第 1914 題：Cyclically Rotating a Grid。難度評分：1766。探討如何利用層次分解 (Layer-by-layer) 的策略，結合周長取模優化，實現矩陣的逐層循環旋轉。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1914](https://leetcode.com/problems/cyclically-rotating-a-grid/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1766)</span>  
> **核心主題**：`Array` $\cdot$ `Simulation` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個大小為 $m \times n$ 的整數矩陣 `grid`（其中 $m$ 和 $n$ 均為偶數），以及一個整數 $k$。
你需要將矩陣中的每一層（Layer）都**逆時針**旋轉 $k$ 次。

「層」的定義是從最外圈開始，向內嵌套的矩形邊界。例如一個 $4 \times 4$ 矩陣有 2 層。

請回傳旋轉後的矩陣。

### 限制條件
- $m, n$ 為偶數。
- $2 \le m, n \le 50$
- $1 \le k \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-09：初次提交 (層次模擬與方向向量控制)](#2026-05-09-初次提交)

---

## 💡 2026-05-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理矩陣旋轉問題，特別是這種「分層旋轉」的題型，最有效率且邏輯最清晰的方式就是**將每一層「攤平」成一維陣列**。

**核心破局點：**
1. **層數計算**：一個 $m \times n$ 的矩陣，其總層數為 $\min(m, n) / 2$。
2. **周長取模 (Optimization)**：
   對於每一層，其周長 (Perimeter) $P$ 可以透過公式計算：$P = 2 \times L_1 + 2 \times L_2 - 4$。
   旋轉 $k$ 次與旋轉 $k \pmod P$ 次的結果是完全相同的。由於 $k$ 可高達 $10^9$，此步驟是避免逾時的關鍵。
3. **方向向量與邊界感應**：
   使用方向向量 `{0, 1}, {1, 0}, {0, -1}, {-1, 0}` 模擬逆時針路徑，並透過預判下一步是否超出當前層的邊界來決定何時「轉彎」。

### 🛠️ 解題思路 (Approach)
1. **初始化與層遍歷**：
   使用迴圈 `i` 從 $0$ 到 $\min(m, n)/2 - 1$ 遍歷每一層。
2. **提取層元素**：
   針對當前層，計算當前邊界 $l_1, l_2$。利用 `while` 迴圈配合方向控制，將該圈元素依序存入 `tmp` 陣列。
3. **計算旋轉後位置**：
   設定起始偏移量為 $st = k \pmod P$。
4. **回填元素**：
   再次走訪該層路徑，將 `tmp[st]` 填入當前座標，並更新 $st = (st + 1) \pmod P$，完成旋轉映射。
5. **收縮邊界**：
   每一層處理完後，內層的邊界會縮減 $l_1 -= 2, l_2 -= 2$。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \times N)$。每個格子被訪問兩次（一次提取，一次回填）。
- **空間複雜度**: $\mathcal{O}(M + N)$。主要開銷在於暫存每一層元素的一維陣列 `tmp`，其最大長度約為 $2(M+N)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> rotateGrid(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();

        int l1 = m, l2 = n;
        // 逆時針方向向量：右 -> 下 -> 左 -> 上
        vector<pair<int, int>> dir = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        
        for(int i = 0; i < min(m, n) / 2; ++i) {
            // 計算當前層的總元素個數 (周長)
            int mod = 2 * l1 + 2 * l2 - 4;
            int K = k % mod; // 取模優化
            
            vector<int> tmp;
            int x = i, y = i;
            int d = 0;
            int loop = mod;
            
            // 第一次遍歷：提取當前層所有元素存入一維陣列
            while(loop--) {
                tmp.push_back(grid[x][y]);
                int dx = dir[d].first, dy = dir[d].second;
                // 檢查下一步是否超出當前殼層的矩形邊界
                if(x + dx < i || x + dx >= i + l1 || y + dy >= i + l2 || y + dy < i) {
                    d++; // 轉向
                }
                x += dir[d].first;
                y += dir[d].second;
            }

            // 第二次遍歷：根據偏移量 K，將旋轉後的元素填回
            x = i, y = i;
            loop = mod;
            d = 0;
            int st = K; // 旋轉後的新起始索引
            while(loop--) {
                grid[x][y] = tmp[st];
                st = (st + 1) % mod;
                int dx = dir[d].first, dy = dir[d].second;
                if(x + dx < i || x + dx >= i + l1 || y + dy >= i + l2 || y + dy < i) {
                    d++;
                }
                x += dir[d].first;
                y += dir[d].second;
            }

            // 進入下一層，邊界各縮減 2
            l1 -= 2;
            l2 -= 2;
        }

        return grid;
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