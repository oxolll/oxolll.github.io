---
title: "LeetCode 1710. Maximum Units on a Truck"
date: 2026-04-05T10:00:00+08:00
lastmod: 2026-04-05T10:00:00+08:00
difficulty: 1309
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "sorting", "counting sort"]
keywords: ["LC1710", "1309", "Easy", "Maximum Units", "Truck", "Greedy", "Counting Sort"]
description: "LeetCode 第 1710 題：Maximum Units on a Truck。難度評分：1309。探討如何利用單位數量的值域上限限制，將傳統的 O(N log N) 排序優化為 O(N) 的計數排序，並結合貪婪策略快速求得卡車可裝載的最大單位總數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1710](https://leetcode.com/problems/maximum-units-on-a-truck/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1309)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
請你將一些箱子裝在一輛卡車上。給你一個二維陣列 `boxTypes`，其中 `boxTypes[i] = [numberOfBoxesi, numberOfUnitsPerBoxi]`：
- `numberOfBoxesi` 是類型 `i` 的箱子數量。
- `numberOfUnitsPerBoxi` 是類型 `i` 每個箱子可以裝載的單位數量。

另給你一個整數 `truckSize`，表示卡車最多可以裝載的**箱子總數**。只要箱子總數不超過 `truckSize`，你可以選擇任意的箱子裝上卡車。
請回傳卡車可以裝載的**最大單位總數**。

### 限制條件
- $1 \le boxTypes.length \le 1000$
- $1 \le numberOfBoxesi, numberOfUnitsPerBoxi \le 1000$
- $1 \le truckSize \le 10^6$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-05：初次提交 (計數排序與貪婪裝載)](#2026-04-05-初次提交)

---

## 💡 2026-04-05 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要讓卡車裝載最多的單位總數，根據貪婪策略 (Greedy Strategy)，我們應該**優先選擇單位數量最高 (Units Per Box) 的箱子**。
常見的做法是將 `boxTypes` 陣列依據單位數量進行降冪排序，時間複雜度為 $\mathcal{O}(N \log N)$。
然而，觀察題目限制：`numberOfUnitsPerBoxi` 最大值僅為 1000。這是一個非常強烈的提示，意味著我們可以使用**計數排序 (Counting Sort)** 來取代傳統排序。利用一個固定大小的陣列來統計各種單位數量的箱子總數，隨後從單位數量最大值 (1000) 開始向下遍歷並貪婪地裝箱，即可將時間複雜度壓縮至最佳的 $\mathcal{O}(N)$。

### 🛠️ 解題思路 (Approach)
1. **頻率統計 (建構計數陣列)**：
   - 宣告一個大小為 1005 的整數陣列 `cnt`，初始化為 0。
   - 遍歷 `boxTypes`，將單位數量 `box[1]` 作為索引，並累加該類型箱子的數量 `box[0]` 到 `cnt[box[1]]`。這巧妙地將不同類型但單位數量相同的箱子合併計算。
2. **貪婪裝載 (由大到小)**：
   - 宣告變數 `rt = 0` 記錄已裝載的單位總數。
   - 建立迴圈，單位數量 `i` 從 1000 反向遍歷至 1。迴圈條件額外加入 `truckSize > 0`，若卡車已滿則提前結束 (Early Exit)。
   - 若該單位數量的箱子存在 (`cnt[i] > 0`)：
     - 計算實際可裝載數量：取「卡車剩餘空間 `truckSize`」與「該單位箱子總數 `cnt[i]`」兩者之中的最小值。
     - 將裝載的箱子數乘以單位數量 `i`，累加至結果 `rt`。
     - 從卡車剩餘空間 `truckSize` 扣除 `cnt[i]`。*(註：即便卡車空間被扣成負數，下一輪的 `truckSize > 0` 判斷也會確保立即跳出迴圈，不影響最終結果)*。
3. **回傳結果**：回傳計算所得的最大單位總數 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M)$。其中 $N$ 是 `boxTypes` 的長度，$M$ 是最大的單位數量 (本題中為常數 1000)。走訪原始陣列需 $\mathcal{O}(N)$，反向遍歷計數陣列需 $\mathcal{O}(M)$。整體時間複雜度為線性 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(1)$。僅宣告了一個固定大小 (1005) 的計數陣列 `cnt`，不受輸入規模影響。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximumUnits(vector<vector<int>>& boxTypes, int truckSize) {
        // 利用單位數量最大值限制，使用計數排序概念
        vector<int> cnt(1005, 0);
        
        // 統計每種「單位數量」所擁有的「箱子總數」
        for(auto box : boxTypes) {
            cnt[box[1]] += box[0];
        }

        int rt = 0;
        
        // 從單位數量最大的箱子開始貪婪裝載，直到卡車裝滿
        for(int i = 1000; i >= 1 && truckSize > 0; --i) {
            if(cnt[i]) {
                // 取卡車剩餘空間與該類型箱子總數之較小值
                rt += min(truckSize, cnt[i]) * i;
                
                // 扣除已處理的箱子數量
                truckSize -= cnt[i];
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