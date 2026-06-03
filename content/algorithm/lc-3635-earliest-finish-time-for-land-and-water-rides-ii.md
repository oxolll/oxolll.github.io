---
title: "LeetCode 3635. Earliest Finish Time for Land and Water Rides II"
date: 2026-06-03T22:00:00+08:00
lastmod: 2026-06-03T22:00:00+08:00
difficulty: 1869
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "simulation"]
keywords: ["LC3635", "Medium", "1869", "Earliest Finish Time II", "貪心演算法", "排程問題", "時間軸重疊"]
description: "LeetCode 第 3635 題：Earliest Finish Time for Land and Water Rides II。探討如何利用貪心策略 (Greedy)，鎖定最早結束的設施作為基準點，以 O(N+M) 線性時間突破大資料量的限制，高效找出最優遊玩排程。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3635](https://leetcode.com/problems/earliest-finish-time-for-land-and-water-rides-ii/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1869)</span>  
> **核心主題**：`Greedy` $\cdot$ `Array` $\cdot$ `Simulation`

*(註：本題為 LC 3633 的進階版，主要差異在於輸入陣列的大小限制被大幅提高，強迫要求線性時間複雜度的最優解。)*

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
你正在遊樂園中，想要搭乘 **一個陸地設施 (Land Ride)** 和 **一個水上設施 (Water Ride)**。
給定四個整數陣列：
- `landStartTime` 與 `landDuration`：代表各個陸地設施的開放時間與遊玩所需時長。
- `waterStartTime` 與 `waterDuration`：代表各個水上設施的開放時間與遊玩所需時長。

你必須一前一後地遊玩這兩個設施（可以先陸後水，也可以先水後陸）。
如果在遊玩設施 A 結束後，設施 B 已經開放，你可以選擇立刻排隊並花費 B 的 duration 來遊玩。
請計算出遊玩完這兩個設施的 **最早結束時間**。

### 限制條件
- $1 \le N, M \le 10^5$ (嚴格要求 $\mathcal{O}(N+M)$ 時間複雜度)
</details>

---

## 📝 歷次打卡與更新
- [2026-06-03：初次提交 (雙向貪心極值探索法)](#2026-06-03-初次提交)

---

## 💡 2026-06-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
如果嘗試將所有的陸地設施與所有的水上設施兩兩配對，時間複雜度將會是 $\mathcal{O}(N \times M)$，在本題必定會 `Time Limit Exceeded`。
但根據 **貪心演算法 (Greedy)** 的直覺：為了讓「總結束時間」盡可能早，我們挑選的兩個設施中，**至少有一個必須是該類別中「最早結束」的設施**。

**核心破局點 1：尋找最佳基準點**
我們先分別找出陸地和水上設施中，結束時間最早的設施（`end1` 和 `end2`）。
*細節優化*：如果有多個設施同時最早結束，我們應該選擇 **開始時間最晚 (即時長最短)** 的設施 (`st = max(st, startTime)`)。因為這能為「安排在它之前的另一個設施」騰出最大的時間空檔。

**核心破局點 2：交叉匹配與時間軸組合**
有了「最早結束的陸地設施 A」後，我們去遍歷所有的水上設施 B，嘗試組合：
1. **B 在 A 之前 (無縫接軌)**：若 B 的結束時間 $\le$ A 的開始時間 (`tmpst + tmpd <= st`)，總結束時間取決於 A 的結束時間 (`end1`)。
2. **A 在 B 之前 (有空檔)**：若 B 的開始時間 $\ge$ A 的結束時間 (`tmpst >= end1`)，總結束時間為 B 的結束時間 (`tmpst + tmpd`)。
3. **強制重疊 (延遲等待)**：若兩者時間衝突，我們只能先玩 A，A 結束後立刻接著玩 B，總結束時間為 `end1 + tmpd`。

反之，拿「最早結束的水上設施」去匹配所有陸地設施，重複上述邏輯，最後取全局最小值。

### 🛠️ 解題思路 (Approach)
1. **尋找最優陸地設施**：遍歷 `land` 陣列，找出最早結束時間 `end1`。若平手，保留較晚開始的 `st`。
2. **尋找最優水上設施**：遍歷 `water` 陣列，找出最早結束時間 `end2`。若平手，保留較晚開始的 `st2`。
3. **策略 1 (固定陸地，遍歷水上)**：遍歷 `water` 陣列，以 `end1` 和 `st` 為基準，根據三種時間軸重疊情況計算結束時間，更新最小值 `rt`。
4. **策略 2 (固定水上，遍歷陸地)**：遍歷 `land` 陣列，以 `end2` 和 `st2` 為基準，重複計算邏輯，更新最小值 `rt`。
5. **回傳結果 `rt`**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M)$。其中 $N$ 是陸地設施數量，$M$ 是水上設施數量。我們僅需要對陣列進行幾次獨立的線性掃描，完全避免了雙層迴圈的交叉組合，完美通過大資料量測試。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個變數來記錄極值，完全不佔用額外記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int earliestFinishTime(vector<int>& landStartTime, vector<int>& landDuration, vector<int>& waterStartTime, vector<int>& waterDuration) {
        
        // 1. 找出結束時間最早的陸地設施
        int end1 = INT_MAX;
        int st = 0, ed = 0;
        int n = landStartTime.size();
        for(int i = 0; i < n; ++i) {
            int cal = landStartTime[i] + landDuration[i];
            if(end1 >= cal) {
                // 貪心細節：如果結束時間一樣早，選開始時間晚的，留更多空檔給對方
                if(end1 == cal)
                    st = max(st, landStartTime[i]);
                else
                    st = landStartTime[i];
                end1 = cal;
            }
        }

        // 2. 找出結束時間最早的水上設施
        int end2 = INT_MAX;
        int st2 = 0, ed2 = 0;
        int m = waterStartTime.size();
        for(int i = 0; i < m; ++i) {
            int cal = waterStartTime[i] + waterDuration[i];
            if(end2 >= cal) {
                if(end2 == cal)
                    st2 = max(st2, waterStartTime[i]);
                else 
                    st2 = waterStartTime[i];
                end2 = cal;
            }
        }

        int rt = INT_MAX;
        
        // 3. 策略 A：以最早結束的陸地設施為基礎，嘗試搭配所有水上設施
        for(int i = 0; i < m; ++i) {
            int tmpst = waterStartTime[i], tmpd = waterDuration[i];
            
            // 情況 1：先玩水上，再玩陸地 (無重疊)
            if(tmpst + tmpd <= st) 
                rt = min(rt, end1);
            // 情況 2：先玩陸地，再玩水上 (中間有空檔)
            else if(tmpst >= end1) 
                rt = min(rt, tmpst + tmpd);
            // 情況 3：兩者時間重疊，先玩陸地，一結束立刻無縫接軌水上
            else 
                rt = min(rt, end1 + tmpd);
        }

        // 4. 策略 B：以最早結束的水上設施為基礎，嘗試搭配所有陸地設施
        for(int i = 0; i < n; ++i) {
            int tmpst = landStartTime[i], tmpd = landDuration[i];
            
            if(tmpst + tmpd <= st2) 
                rt = min(rt, end2);
            else if(tmpst >= end2) 
                rt = min(rt, tmpst + tmpd);
            else 
                rt = min(rt, end2 + tmpd);
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