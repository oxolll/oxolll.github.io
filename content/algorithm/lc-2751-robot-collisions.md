---
title: "LeetCode 2751. Robot Collisions"
date: 2026-04-01T20:30:00+08:00
lastmod: 2026-04-01T20:30:00+08:00
difficulty: 2091
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "stack", "sorting", "simulation"]
keywords: ["LC2751", "2091", "Hard", "Robot Collisions", "Stack", "Simulation"]
description: "LeetCode 第 2751 題：Robot Collisions。難度評分：2091。探討如何結合排序與堆疊 (Stack) 模擬 1D 空間中的碰撞事件，並在時間複雜度 O(N log N) 內精準追蹤並還原機器人的原始順序。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2751](https://leetcode.com/problems/robot-collisions/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2091)</span>  
> **核心主題**：`Array` $\cdot$ `Stack` $\cdot$ `Sorting` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
在一個一維數線上，有 `n` 個機器人。給定三個長度為 `n` 的陣列：`positions`、`healths` 和 `directions`（僅包含 `'L'` 和 `'R'`）。
所有機器人同時以相同的速度開始移動。如果兩個機器人相撞：
- 健康度**較低**的機器人將被摧毀。
- 健康度**較高**的機器人將存活，但其健康度會**減少 1**。
- 如果兩個機器人的健康度**相同**，則兩者皆被摧毀。

請回傳一個陣列，包含所有碰撞結束後**存活下來的機器人的健康度**，並且必須**依照機器人在原始輸入中的順序**排列。

### 限制條件
- $1 \le positions.length == healths.length == directions.length == n \le 10^5$
- $1 \le positions[i], healths[i] \le 10^9$
- `directions[i]` 為 `'L'` 或 `'R'`。
- `positions` 中的所有值皆互不相同。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (索引綁定排序與單調堆疊模擬)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是「碰撞模擬」的經典題型。在 1D 空間中，碰撞只會發生在「向右移動 (`R`) 的機器人」與「其右側向左移動 (`L`) 的機器人」之間。
因此，我們必須**由左至右**掃描機器人。由於輸入陣列並未依照位置排序，第一步必須將機器人依據 `positions` 進行遞增排序。
為了滿足「輸出必須符合原始輸入順序」的嚴格要求，排序時不能只移動位置，必須將「位置」與「原始索引 (Original Index)」綁定。後續的健康度增減與存活判定，皆透過原始索引來操作獨立的陣列。

掃描過程中，我們利用一個 **堆疊 (Stack)** 來暫存所有向右移動 (`R`) 的機器人。當遇到向左移動 (`L`) 的機器人時，它必然會與堆疊頂端（最靠近它）的 `R` 機器人發生碰撞。透過一個 `while` 迴圈持續結算碰撞，直到該 `L` 機器人被摧毀，或是堆疊被清空為止。

### 🛠️ 解題思路 (Approach)
1. **資料前處理與綁定**：
   - 取得機器人數量 `n`。
   - 宣告 `p_idx` 陣列儲存 `{positions[i], i}`。
   - 宣告 `idx_h` 與 `idx_d` 陣列，直接複製 `healths` 與 `directions`。
   - 對 `p_idx` 進行遞增排序，使得機器人由左至右排列。
2. **堆疊碰撞模擬**：
   - 宣告堆疊 `move_r`，用於儲存向右移動的機器人**原始索引**。
   - 遍歷排序後的 `p_idx`，取得當前機器人的原始索引 `idx`。
   - **向右移動 (`R`)**：直接將 `idx` 推入堆疊 `move_r`。
   - **向左移動 (`L`)**：開啟 `while(!move_r.empty())` 迴圈進行碰撞結算：
     - 取出棧頂機器人索引 `idx2`。
     - **情況 A：棧頂機器人血量 $\ge$ 當前機器人血量**
       - 若血量相等，棧頂機器人亦死亡 (`idx_h[idx2] = 0`，並 `pop()`)。
       - 若棧頂機器人血量較多，棧頂機器人血量減 1。
       - 當前機器人必定死亡 (`idx_h[idx] = 0`)，跳出 `while` 迴圈 (`break`)。
     - **情況 B：棧頂機器人血量 $<$ 當前機器人血量**
       - 棧頂機器人死亡 (`idx_h[idx2] = 0`，並 `pop()`)。
       - 當前機器人血量減 1 (`--idx_h[idx]`)。
       - 繼續 `while` 迴圈，尋找下一個碰撞目標。
3. **收集存活結果**：
   - 宣告結果陣列 `rt`。
   - 依序遍歷原始索引 `0` 到 `n-1`，檢查 `idx_h[i]`。若大於 0 代表存活，推入 `rt` 中。
   - 回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是機器人的數量。排序 `p_idx` 陣列需要 $\mathcal{O}(N \log N)$ 時間。後續的碰撞模擬中，每個機器人最多被推入與彈出堆疊一次，耗時 $\mathcal{O}(N)$。整體由排序主導。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了數個長度為 $N$ 的陣列 (`p_idx`, `idx_h`, `idx_d`) 以及一個最差情況下深度為 $N$ 的堆疊。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> survivedRobotsHealths(vector<int>& positions, vector<int>& healths, string directions) {
        int n = positions.size();
        
        // 綁定位置與原始索引，以便排序後仍能追蹤狀態
        vector<pair<int, int>> p_idx;
        vector<int> idx_h(n, 0);
        vector<int> idx_d(n, 0);
        
        for(int i = 0; i < n; ++i) {
            p_idx.push_back({positions[i], i});
            idx_h[i] = healths[i];
            idx_d[i] = directions[i];
        }

        // 依照位置由左至右排序
        sort(p_idx.begin(), p_idx.end());

        // 堆疊儲存向右移動 ('R') 的機器人原始索引
        stack<int> move_r;
        
        for(int i = 0; i < n; ++i) {
            int idx = p_idx[i].second;
            
            if(idx_d[idx] == 'R') {
                move_r.push(idx);
                continue;
            } else { // 當前機器人向左 ('L') 移動
                while(!move_r.empty()) {
                    int idx2 = move_r.top(); 
                    
                    if(idx_h[idx2] >= idx_h[idx]) {
                        // 棧頂機器人血量大於等於當前機器人
                        if(idx_h[idx2] == idx_h[idx]) {
                            idx_h[idx2] = 0; // 同歸於盡
                            move_r.pop();
                        } else {
                            --idx_h[idx2]; // 棧頂機器人扣血存活
                        }
                        
                        idx_h[idx] = 0; // 當前機器人必定死亡
                        break;
                    }
                    
                    // 棧頂機器人血量小於當前機器人，被摧毀
                    idx_h[idx2] = 0;
                    move_r.pop();
                    // 當前機器人扣血，繼續與下一個棧頂機器人碰撞
                    --idx_h[idx];
                }
            }
        }
        
        vector<int> rt;
        // 依照原始順序收集存活的機器人健康度
        for(int i = 0; i < n; ++i) {
            if(idx_h[i] == 0) continue;
            rt.push_back(idx_h[i]);
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