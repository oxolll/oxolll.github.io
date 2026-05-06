---
title: "LeetCode 1861. Rotating the Box"
date: 2026-05-06T09:00:00+08:00
lastmod: 2026-05-06T09:00:00+08:00
difficulty: 1536
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "matrix", "two-pointers"]
keywords: ["LC1861", "Medium", "Rotating the Box", "矩陣旋轉", "重力模擬"]
description: "LeetCode 第 1861 題：Rotating the Box。難度評分：1536。探討如何將重力感應與矩陣 90 度旋轉結合，利用「計數填入法」在一次遍歷中完成石頭堆疊與座標轉換。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1861](https://leetcode.com/problems/rotating-the-box/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1536)</span>  
> **核心主題**：`Array` $\cdot$ `Two Pointers` $\cdot$ `Matrix`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個 $m \times n$ 的字元矩陣 `box`，代表一個盒子的側面圖。矩陣中的字元如下：
- `'#'` 代表一個石頭。
- `'*'` 代表一個固定的障礙物。
- `'.'` 代表空位。

盒子將會**順時針旋轉 90 度**。旋轉後，由於重力作用，所有的石頭都會向下掉落，直到它們碰到障礙物、另一個石頭或盒子的底部。障礙物的位置不會改變。

請回傳旋轉並處理重力後的新矩陣。

### 限制條件
- $m == box.length$
- $n == box[i].length$
- $1 \le m, n \le 500$
- `box[i][j]` 僅包含 `'#'`, `'*'`, `'.'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-06：初次提交 (計數填入法：旋轉與重力同步處理)](#2026-05-06-初次提交)

---

## 💡 2026-05-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理這類題目通常有兩種策略：
1. **先重力再旋轉**：在原圖 $m \times n$ 中，讓石頭向右（$j$ 增加的方向）滾動，處理完後再進行 90 度旋轉。
2. **同步處理**：這就是你採用的高階做法。利用矩陣旋轉公式 $(i, j) \to (j, m-1-i)$，邊掃描原圖的每一行，邊計算石頭數量，遇到障礙物時直接計算出石頭在旋轉後應該「堆疊」在哪個垂直位置。

**座標映射邏輯：**
- 原圖 row $i$ $\to$ 旋轉圖 col $m-1-i$。
- 原圖 col $j$ $\to$ 旋轉圖 row $j$。
- 石頭在原圖向右滾動，對應到旋轉圖就是向下滾動（$j$ 增加的方向）。

### 🛠️ 解題思路 (Approach)
1. **初始化結果矩陣**：建立一個 $n \times m$ 的矩陣 `rt`，預設填入 `'.'`。
2. **逐行掃描原圖**：對於原圖的每一行 $i$：
   - 定義 `ni = m - 1 - i`，這是該行旋轉後對應的垂直列索引。
   - 使用 `cnt` 記錄目前遇到的石頭數量。
3. **處理障礙物與石頭**：
   - 遍歷 $j$ 從 $0$ 到 $n-1$：
     - 若為 `'#'`：`cnt++`。
     - 若為 `'*'`：
       - 在旋轉後的對應位置 `rt[j][ni]` 填入 `'*'`。
       - 將累積的 `cnt` 顆石頭，由下往上（即 $j$ 遞減的方向）填入 `rt[k][ni]`。
       - 清空 `cnt`。
4. **邊界處理**：當一行結束後，若 `cnt > 0`，代表還有石頭沒碰到障礙物，直接讓它們堆疊在旋轉後該列的最底部（即 $j$ 最大的位置）。

### 📊 複雜度分析
- **時間複雜度**: $O(M \times N)$。每個格子在原圖被掃描一次，在結果矩陣被填入一次，總運算次數與矩陣大小成正比。
- **空間複雜度**: $O(M \times N)$。需要一個額外的矩陣來儲存旋轉後的結果。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<char>> rotateTheBox(vector<vector<char>>& boxGrid) {
        int m = boxGrid.size(), n = boxGrid[0].size();
        // 建立旋轉後的結果矩陣 (n x m)，預設全為空位 '.'
        vector<vector<char>> rt(n, vector<char>(m, '.'));
        
        for(int i = 0; i < m; ++i) {
            int ni = m - 1 - i; // 旋轉後的行座標會轉換為新矩陣的列座標
            int cnt = 0;        // 記錄當前區間內的石頭數量
            
            for(int j = 0; j < n; ++j) {
                if(boxGrid[i][j] == '.') continue;
                
                if(boxGrid[i][j] == '#') {
                    ++cnt; // 收集石頭
                } else {
                    // 遇到障礙物 '*'
                    rt[j][ni] = '*'; // 障礙物位置轉換
                    
                    // 將障礙物後方（原圖左方）堆積的石頭填入
                    int k = j - 1;
                    while(k >= 0 && cnt--) {
                        rt[k--][ni] = '#';
                    }
                    cnt = 0; // 重置計數器
                }   
            }

            // 處理該行最末尾（原圖最右方）掉落到底部的石頭
            int k = n - 1;
            while(k >= 0 && cnt--) {
                rt[k--][ni] = '#';
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