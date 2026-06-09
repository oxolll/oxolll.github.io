---
title: "LeetCode 3951. Minimum Energy to Maintain Brightness"
date: 2026-06-09T14:30:00+08:00
lastmod: 2026-06-09T14:30:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "sorting", "intervals"]
keywords: ["LC3951", "Medium", "Merge Intervals", "區間合併", "無條件進位", "貪心演算法"]
description: "LeetCode 第 3951 題：Minimum Energy to Maintain Brightness。探討如何利用排序與區間合併 (Merge Intervals) 技巧，配合巧妙的初始狀態設定 (st=-1, ed=-2) 無縫結算離散區間長度，並利用整數運算達成無條件進位的能量計算。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3951](https://leetcode.com/problems/minimum-energy-to-maintain-brightness/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Sorting` $\cdot$ `Intervals`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `n`，一個整數 `brightness`，以及一個二維陣列 `intervals`，其中 `intervals[i] = [start_i, end_i]` 代表需要維持亮度的時間區間（包含頭尾，為離散時間點）。

系統在這些區間內運作時，每個時間單位的能量消耗為 `ceil(brightness / 3)`（即 `brightness` 除以 3 向上取整）。
如果區間發生重疊，重疊的時間點 **不會** 導致能量消耗疊加，而是視為同一個時間點的維持。

請你計算並回傳維持這些區間亮度所需的 **最小總能量 (Minimum Energy)**。

### 限制條件
- $1 \le n \le 10^9$
- $1 \le brightness \le 10^5$
- $1 \le intervals.length \le 10^5$
- $0 \le start_i \le end_i \le n$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (區間合併與數學計算)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是極度經典的 **區間合併 (Merge Intervals)** 變體題。
由於區間重疊不疊加能量，我們的首要任務就是求出所有區間「聯集」後的總長度。

**核心破局點 1：離散區間合併**
1. 將所有區間依照 `start` 升冪排序。
2. 準備一個當前合併區間 `[st, ed]`。
3. 依序掃描，如果下一個區間的 `start` $\le$ 當前的 `ed`，代表有重疊，將 `ed` 延伸到兩者的最大值。
4. 如果沒有重疊，代表前一個區間已經徹底斷開，將其長度 `(ed - st) + 1` 結算加入總和，並開啟新的區間。

**核心破局點 2：優雅的邊界處理**
利用 `st = -1` 與 `ed = -2` 作為虛擬初始區間。當迴圈第一次遇到真正的區間而觸發結算時，長度恰好為 $(-2 - (-1)) + 1 = 0$，不會產生任何多餘數值，這讓我們可以免去繁瑣的 `if (i == 0)` 特判邏輯。

**核心破局點 3：整數運算取代浮點數 Ceiling**
能量係數為 `brightness` 除以 3 向上取整。
使用浮點數 `ceil((double)brightness / 3.0)` 可能會有效能或精確度問題。
透過純整數運算：`brightness / 3 + ((brightness % 3) >= 1 ? 1 : 0)` (或數學上等價的 `(brightness + 2) / 3`)，可以完美且安全地得出所需係數。

### 🛠️ 解題思路 (Approach)
1. **排序**：對 `intervals` 進行排序，優先比較 `start`，再比較 `end`。
2. **計算能量係數 `c`**：利用整數模除運算算出單位時間的消耗量。
3. **區間合併掃描**：
   - 設立虛擬初始區間 `st = -1, ed = -2`。
   - 遍歷排序後的 `intervals`，判斷重疊則更新 `ed = max(ed, current_end)`。
   - 若未重疊，結算前一段區間的長度加到總長度 `rt`，並更新 `st` 與 `ed` 為當前區間。
4. **尾盤結算**：迴圈結束後，將最後一段區間的長度加到 `rt` 中。
5. **回傳結果**：將總長度 `rt` 乘上單位能量 `c` 回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是 `intervals` 的數量。時間瓶頸在於排序演算法，後續的區間合併掃描僅需 $\mathcal{O}(N)$ 的線性時間。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(N)$ (取決於語言實作的排序演算法空間配置)。演算法本體只使用了常數變數，屬於 In-place 計算。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long minEnergy(int n, int brightness, vector<vector<int>>& intervals) {
        // 1. 區間合併標準起手式：依照起點排序，起點相同則依照終點排序
        sort(intervals.begin(), intervals.end(), [](auto& a, auto& b){
            if(a[0] != b[0]) return a[0] < b[0];
            return a[1] < b[1];
        });

        // 2. 利用純整數運算達成 ceil(brightness / 3) 的效果
        long long c = brightness / 3 + ((brightness % 3) >= 1 ? 1 : 0);
        
        // 3. 虛擬初始狀態，巧妙避開第一次結算的長度 (ed - st + 1 = 0)
        int st = -1;
        int ed = -2;
        long long rt = 0;
        
        // 4. 線性掃描進行區間合併
        for(int i = 0; i < intervals.size(); ++i) {
            // 如果當前區間與上一個區間有重疊或相連
            if(intervals[i][0] <= ed) {
                // 擴張區間的右邊界
                ed = max(ed, intervals[i][1]);
            } 
            else {
                // 區間斷開，結算上一個區間的長度 (離散時間點故 +1)
                rt += (ed - st) + 1;
                // 開啟新的區間
                st = intervals[i][0];
                ed = intervals[i][1];
            }
        }
        
        // 5. 將最後一輪仍在手上的區間結算掉
        rt += (ed - st) + 1;
        
        // 總時長 × 單位消耗 = 總能量
        return rt * c;
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