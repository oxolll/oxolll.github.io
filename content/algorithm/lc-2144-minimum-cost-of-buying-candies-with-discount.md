---
title: "LeetCode 2144. Minimum Cost of Buying Candies With Discount"
date: 2026-06-03T10:15:00+08:00
lastmod: 2026-06-03T10:15:00+08:00
difficulty: 1260
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "counting-sort"]
keywords: ["LC2144", "Easy", "1260", "貪心演算法", "計數排序", "買二送一"]
description: "LeetCode 第 2144 題：Minimum Cost of Buying Candies With Discount。難度評分：1260。探討如何洞察數值範圍限制，捨棄 O(N log N) 的常規排序，改用計數排序 (Counting Sort) 結合貪心策略，以 O(N) 的極致效能解決買二送一的最優價格問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2144](https://leetcode.com/problems/minimum-cost-of-buying-candies-with-discount/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1260)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
一家糖果店正在進行促銷活動：每購買 **兩個** 糖果，你可以 **免費** 獲得一個額外的糖果。
免費獲得的糖果價格必須 **小於或等於** 你購買的兩個糖果的價格。

給你一個整數陣列 `cost`，其中 `cost[i]` 是第 `i` 個糖果的價格。
請你回傳購買陣列中所有糖果的 **最小總成本**。

### 範例
- **Input**: `cost = [1,2,3]`
- **Output**: `5`
- **Explanation**: 購買價格為 2 和 3 的糖果，總成本為 5。你可以免費獲得價格為 1 的糖果。

- **Input**: `cost = [6,5,7,9,2,2]`
- **Output**: `23`
- **Explanation**: 
  - 購買價格 9 和 7 的糖果 (花費 16)，免費獲得價格 6 的糖果。
  - 購買價格 5 和 2 的糖果 (花費 7)，免費獲得價格 2 的糖果。
  總成本為 16 + 7 = 23。

- **Input**: `cost = [5,5]`
- **Output**: `10`
- **Explanation**: 只有兩個糖果，無法獲得免費糖果，需全部購買。

### 限制條件
- $1 \le cost.length \le 100$
- $1 \le cost[i] \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-03：初次提交 (計數排序與 O(N) 貪心優化)](#2026-06-03-初次提交)

---

## 💡 2026-06-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的本質是 **貪心演算法 (Greedy)**：為了省下最多的錢，我們應該盡可能讓「免費贈送」的額度扣抵在「最貴」的糖果上。
因此，最優策略必定是：**將所有糖果由貴到便宜排列，每拿三個糖果，前兩個付錢，第三個免費。**

**核心破局點：效能極致優化 (Counting Sort)**
一般的做法是直接呼叫 `std::sort` 進行降序排列，這需要 $\mathcal{O}(N \log N)$ 的時間。
但注意到題目有一個極為重要的限制：$cost[i] \le 100$。這意味著價格的範圍非常小！
我們完全可以捨棄基於比較的排序，改用 **計數排序 (Counting Sort / Bucket Sort)**：
建立一個大小為 105 的頻率陣列 `cnt`，統計每個價格出現的次數。然後從最高價 (100) 往下掃描，這樣不僅自動完成了降序排列，還將時間複雜度降到了極限的 $\mathcal{O}(N)$。

### 🛠️ 解題思路 (Approach)
1. **建立頻率陣列**：宣告一個大小為 105 的陣列 `cnt`，統計 `cost` 中每個價格的糖果數量。
2. **貪心挑選 (由貴到便宜)**：從 `i = 100` 往下遍歷到 `1`。
3. **分步消耗 (買 2 送 1)**：在當前的 `i` 尚未消耗完之前：
   - **買第一顆**：找尋目前存在的最貴糖果 `j`，將其價格加入 `sum`，並消耗一顆 (`--cnt[j]`)。
   - **買第二顆**：往下尋找下一個存在的糖果 `j`，若有，將其價格加入 `sum`，並消耗一顆。
   - **拿免費第三顆**：繼續往下尋找下一個存在的糖果 `j`，若有，**不加入總和**，僅消耗一顆。
4. **指標跳轉**：一輪「買 2 送 1」完成後，將外層的指標 `i` 更新為 `j + 1` (因為下一次迴圈開始時會執行 `--i`，這樣剛好會從剩餘的最高價 `j` 繼續處理)。
5. **回傳結果 `sum`**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + K)$。其中 $N$ 是糖果總數，$K$ 是價格上限 (100)。建立頻率陣列需 $\mathcal{O}(N)$，向下遍歷尋找時，每個糖果最多被造訪常數次，總耗時為 $\mathcal{O}(N)$。整體時間複雜度為嚴格的線性級別。
- **空間複雜度**: $\mathcal{O}(K)$。配置了長度為 105 的常數大小頻率陣列，空間消耗極小，可視為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumCost(vector<int>& cost) {
        // 1. 計數排序：因為價格最大只有 100，使用頻率陣列取代 O(N log N) 排序
        vector<int> cnt(105, 0);
        for(auto c : cost) ++cnt[c];

        int sum = 0;
        
        // 2. 從最高價 (100) 開始往下貪心挑選
        for(int i = 100; i > 0; --i) {
            // 如果當前價格沒有糖果，直接跳過
            if(cnt[i] == 0) continue;
            
            int j = i;
            
            // 動作 A：買最貴的第一顆糖果
            sum += j;
            --cnt[j];
            
            // 動作 B：買第二貴的糖果 (需向下尋找可用的價格)
            while(j > 0 && cnt[j] == 0) --j;
            if(j > 0 && cnt[j]) {
                sum += j;
                --cnt[j];
            }
            
            // 動作 C：免費帶走第三貴的糖果 (再次向下尋找)
            while(j > 0 && cnt[j] == 0) --j;
            if(j > 0 && cnt[j]) {
                --cnt[j]; // 不加總和，純粹扣除數量
            }
            
            // 更新外層指標，從下一個還有剩餘糖果的價格 j 繼續處理
            // 因為 for 迴圈結束時會 --i，所以這裡設為 j + 1
            i = j + 1; 
        }

        return sum;
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