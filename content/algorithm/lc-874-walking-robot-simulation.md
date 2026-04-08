---
title: "LeetCode 874. Walking Robot Simulation"
date: 2026-04-10T14:30:00+08:00
lastmod: 2026-04-10T14:30:00+08:00
difficulty: 1846
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "simulation", "hash-table"]
keywords: ["LC874", "1846", "Medium", "Walking Robot Simulation", "Simulation", "Coordinate Encoding"]
description: "LeetCode 第 874 題：Walking Robot Simulation。難度評分：1846。探討如何利用方向陣列模擬機器人移動，並透過座標壓縮編碼技巧結合 Hash Set 以 O(1) 時間複雜度高效偵測網格上的障礙物。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 874](https://leetcode.com/problems/walking-robot-simulation/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1846)</span>  
> **核心主題**：`Array` $\cdot$ `Simulation` $\cdot$ `Hash Table`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
在一個無限大的 XY 平面上，有一個機器人初始位置為原點 `(0, 0)`，並面向北方。
給定一個整數陣列 `commands` 代表機器人的移動指令，以及一個二維陣列 `obstacles` 代表平面上的障礙物座標。
指令包含三種：
- `-2`：向左轉 90 度
- `-1`：向右轉 90 度
- `1 <= k <= 9`：向前移動 `k` 個單位。

機器人每移動一個單位，都會檢查下一個位置是否為障礙物。如果是障礙物，機器人將停留在當前位置，並繼續執行下一個指令。
請回傳機器人在整個移動過程中，**距離原點的最大歐幾里得距離的平方** (即 $x^2 + y^2$ 的最大值)。

### 限制條件
- $1 \le commands.length \le 10^4$
- `commands[i]` 為 `-2`, `-1` 或介於 `1` 到 `9` 之間。
- $0 \le obstacles.length \le 10^4$
- $-3 \times 10^4 \le x_i, y_i \le 3 \times 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-10：初次提交 (方向陣列模擬與座標壓縮 Hash)](#2026-04-10-初次提交)

---

## 💡 2026-04-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道標準的二維網格模擬題。處理這類題目的兩大核心為：
1. **方向與轉向的數學化**：定義一個方向陣列 `dir`，依順時針方向存放北、東、南、西的單位位移。如此一來，向右轉即為 `(dir_idx + 1) % 4`，向左轉即為 `(dir_idx - 1 + 4) % 4`，可完全避免冗長的 `if-else`。
2. **高效的障礙物偵測**：為了在每一步 $\mathcal{O}(1)$ 時間內判斷前方是否有障礙物，我們必須使用 Hash Set (`unordered_set`) 儲存障礙物。由於 C++ 不預設支援 `pair` 的 Hash，觀察到座標範圍為 $[-30000, 30000]$ (跨度 60000)，我們可以將二維座標 $(X, Y)$ 壓縮為一維的 `long long` 純量：`X * 100000LL + Y`。由於乘數 $100000 > 60000$，這在數學上保證了絕對不會發生雜湊碰撞。

### 🛠️ 解題思路 (Approach)
1. **建立障礙物 Hash Set**：
   - 宣告 `unordered_set<long long> ob`。
   - 走訪 `obstacles`，將每個座標 `[x, y]` 轉換為 `x * 100000LL + y` 並加入集合中。
2. **初始化機器人狀態**：
   - 宣告位置變數 `x = 0, y = 0`。
   - 宣告方向變數 `d = 0` (0:北, 1:東, 2:南, 3:西)。
   - 宣告方向陣列 `dir = {{0,1}, {1,0}, {0,-1}, {-1,0}}`。
   - 宣告變數 `rt = 0` 記錄過程中的最大平方距離。
3. **執行指令模擬**：
   - 走訪每一個 `commands` 中的指令 `c`。
   - **若為轉向指令 (`c < 0`)**：利用模數運算更新方向 `d = (d + (c == -1 ? 1 : -1) + 4) % 4`。
   - **若為前進指令 (`c > 0`)**：
     - 利用 `while` 迴圈嘗試前進 `c` 步。
     - 每次前進前，計算下一步的預期座標 `nx = x + dir[d].first` 與 `ny = y + dir[d].second`。
     - 將預期座標編碼後在集合 `ob` 中查找，若存在則提早中斷 (`break`) 該次前進。
     - 若無障礙物，正式更新 `x` 與 `y`。
   - 每執行完一個指令（或被障礙物擋下後），計算並更新最大平方距離 `rt = max(rt, x*x + y*y)`。
4. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M \times K)$。其中 $N$ 為障礙物數量，$M$ 為移動指令的數量，$K$ 為單次最大移動步數 (本題限制 $K \le 9$)。建立 Set 需 $\mathcal{O}(N)$，處理指令需 $\mathcal{O}(M \times K)$，整體為線性時間複雜度。
- **空間複雜度**: $\mathcal{O}(N)$。Hash Set `ob` 中儲存了 $N$ 個障礙物的編碼。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int robotSim(vector<int>& commands, vector<vector<int>>& obstacles) {
        // 利用座標壓縮技巧將 (x, y) 轉換為單一 long long，避開 pair hash 的問題
        unordered_set<long long> ob;
        for(auto x : obstacles) {
            ob.insert(x[0] * 100000LL + x[1]);
        }

        int x = 0, y = 0; // 初始座標
        int d = 0;        // 初始方向：0 代表北方
        int rt = 0;       // 記錄最大歐氏距離的平方
        
        // 方向陣列，順時針：北, 東, 南, 西
        vector<pair<int, int>> dir = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
        
        for(auto c : commands) {
            // 處理轉向指令
            if(c < 0) {
                // c == -1 (右轉) -> d + 1
                // c == -2 (左轉) -> d - 1
                // 加上 4 以避免負數取模問題
                d = (d + (c == -1 ? 1 : -1) + 4) % 4;
                continue;
            }

            // 處理前進指令，逐步模擬以偵測障礙物
            int steps = c;
            while(steps--) {
                long long next_hash = (x + dir[d].first) * 100000LL + (y + dir[d].second);
                
                // 遇到障礙物，停止當前指令的剩餘步數
                if(ob.find(next_hash) != ob.end()) {
                    break;
                }

                // 更新座標
                x += dir[d].first;
                y += dir[d].second;
            }

            // 每次移動後，更新最大距離平方
            rt = max(rt, x * x + y * y);
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