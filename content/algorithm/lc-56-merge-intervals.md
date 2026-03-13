---
title: "LeetCode 56. Merge Intervals"
date: 2026-03-13T22:44:00+08:00
lastmod: 2026-03-13T22:44:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Sorting", "Greedy"]
keywords: ["LC56", "N/A", "Medium", "Merge Intervals", "Greedy Algorithm"]
description: "LeetCode 第 56 題：Merge Intervals。難度評分：N/A (經典面試題)。探討如何透過左邊界排序與貪心策略，以 O(N log N) 的時間複雜度高效合併所有重疊的區間。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 56](https://leetcode.com/problems/merge-intervals/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>    
> **核心主題**：`Array` $\cdot$ `Sorting` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個區間陣列 `intervals`，其中 `intervals[i] = [start_i, end_i]`。
請合併所有重疊的區間，並回傳一個涵蓋輸入中所有區間的**非重疊區間陣列**。

### 範例
- **Input**: `intervals = [[1,3],[2,6],[8,10],[15,18]]`
  **Output**: `[[1,6],[8,10],[15,18]]`
  **Explanation**: 區間 `[1,3]` 和 `[2,6]` 重疊，因此將它們合併為 `[1,6]`。

- **Input**: `intervals = [[1,4],[4,5]]`
  **Output**: `[[1,5]]`
  **Explanation**: 區間 `[1,4]` 和 `[4,5]` 被視為重疊區間。

### 限制條件
- $1 \le intervals.length \le 10^4$
- $intervals[i].length == 2$
- $0 \le start_i \le end_i \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-13：初次提交 (左邊界排序與貪心合併)](#2026-03-13-初次提交)

---

## 💡 2026-03-13 初次提交

### 🎯 直覺 (Intuition)
如果區間是亂序的，我們很難判斷哪些區間會互相交集。但如果我們**先將所有區間依照「左邊界」從小到大進行排序**，那麼任何有機會重疊的區間，在陣列中必然是相鄰的！
排序後，我們只需要維護一個「當前正在擴展的區間」，然後依序掃描後面的區間。如果遇到有交集的，就試著把「右邊界」往外擴張；如果遇到完全不相交的，就代表當前區間已經擴展到了極限，可以將其收納進答案中，並開啟一個新的區間繼續追蹤。

### 🛠️ 解題思路 (Approach)
1. **排序 (Sorting)**：
   使用自訂比較函式，將 `intervals` 陣列依照每個區間的左邊界 (`intv[0]`) 進行升冪排序。
2. **初始化狀態指標**：
   宣告 `L = -1` 與 `R = -1` 作為當前正在追蹤的合併區間左右邊界。由於題目限制 $start_i \ge 0$，使用 `-1` 作為初始哨兵值 (Sentinel) 可以完美避開邊界衝突。
3. **貪心掃描與合併 (Greedy Merge)**：
   遍歷排序後的每個區間 `[l, r]`：
   - **完全被包覆** (`r < R`)：目前的區間已經被包含在先前的合併範圍內，直接忽略 (`continue`)。
   - **完全無交集** (`l > R`)：目前的區間與先前的合併範圍斷開了。如果 `R != -1`（代表這不是第一筆資料），我們就將之前的合併結果 `[L, R]` 推入解答陣列 `rt` 中。接著，更新 `L = l` 與 `R = r`，開始追蹤新區間。
   - **部分重疊** (`l <= R` 且 `r >= R`)：兩區間有交集，我們將右邊界擴展至兩者的最大值：`R = max(R, r)`。
4. **收尾**：
   迴圈結束後，最後一個正在追蹤的區間不會在迴圈內被推入陣列，因此需在迴圈外補上 `rt.push_back({L, R})`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。主要的效能瓶頸在於對 `intervals` 陣列進行排序。後續的合併掃描只需 $\mathcal{O}(N)$ 的時間。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(N)$（取決於 C++ `std::sort` 的底層實作遞迴深度）。儲存解答的陣列 `rt` 不計入額外空間複雜度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        // 1. 依照左邊界由小到大排序
        sort(intervals.begin(), intervals.end(), [](const vector<int>& a, const vector<int>& b){
            return a[0] < b[0];
        });

        // 初始化哨兵值，題目範圍 >= 0，故用 -1 安全
        int L = -1;
        int R = -1;
        vector<vector<int>> rt; 
        rt.reserve(intervals.size()); // 預先配置記憶體，減少 reallocation

        for(auto intv : intervals) {
            int l = intv[0], r = intv[1];
            
            // 情況一：完全被現有區間包覆
            if(r < R) continue;
            
            // 情況二：與現有區間無交集，產生斷層
            if(l > R) {
                // 將前一個合併好的區間推入解答 (排除初始狀態)
                if(R != -1) {
                    rt.push_back({L, R});
                }
                // 開啟新區間的追蹤
                L = l;
                R = r;
            }
            // 情況三：有部分交集，向右擴展邊界
            else {
                R = max(R, r);
            }
        }

        // 將迴圈結束前最後一個追蹤的區間推入解答
        rt.push_back({L, R});
        
        return rt;
    }
};

/* 合併示意圖：
| ----- |
  | -------------|   
| ---------------|       -> 合併為 
         | ----|          -> 忽略 (r < R)
                    | ----- |  -> 無交集 (l > R)，結算前一個並開新區間
*/
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