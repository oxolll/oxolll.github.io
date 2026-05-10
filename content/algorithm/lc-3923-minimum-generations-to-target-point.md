---
title: "LeetCode 3923. Minimum Generations to Target Point"
date: 2026-05-10T20:00:00+08:00
lastmod: 2026-05-10T20:00:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "simulation", "bfs", "geometry"]
keywords: ["LC3923", "Medium", "Midpoint", "Generation", "空間模擬"]
description: "LeetCode 第 3923 題：Minimum Generations to Target Point。探討如何在有限的三維整數空間中，利用中點生成規則進行狀態擴張，並計算達成目標點的最少世代數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3923](https://leetcode.com/problems/minimum-generations-to-target-point/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Simulation` $\cdot$ `BFS` $\cdot$ `Geometry`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一組初始的三維座標點 `points` 和一個目標點 `target`。
在每一「代」(Generation) 中，你可以從目前已有的所有點中任選兩個點 $P_i(x_i, y_i, z_i)$ 和 $P_j(x_j, y_j, z_j)$（$i$ 可以等於 $j$），生成它們的中點：
$$P_{new} = \left( \left\lfloor \frac{x_i+x_j}{2} \right\rfloor, \left\lfloor \frac{y_i+y_j}{2} \right\rfloor, \left\lfloor \frac{z_i+z_j}{2} \right\rfloor \right)$$

請計算最少需要經過幾代演化，目標點 `target` 才會出現在點集之中。如果永遠無法達成，回傳 `-1`。

### 限制條件
- 座標範圍通常極小（如 $0 \le x, y, z \le 6$）。
- `points` 的初始數量有限。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-10：初次提交 (三維空間擴張模擬法)](#2026-05-10-初次提交)

---

## 💡 2026-05-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的「世代」概念與傳統 BFS 的「步數」略有不同。在傳統 BFS 中，一次移動代表產生一個新狀態；而在本題中，一「代」代表**當前所有點集兩兩配對**後產生的所有新點的集合。

**核心破局點：**
1. **有限狀態空間**：由於座標範圍僅在 $0 \sim 6$，三維空間的總點數僅有 $7 \times 7 \times 7 = 343$ 個。這意味著演算法最多演化 343 次一定會停止。
2. **中點生成規律**：中點運算使用整數除法。如果選擇兩個相同的點，產生的中點就是其本身。
3. **收斂判定**：如果某一世代演化後，可達點的總數沒有增加，且仍未包含 `target`，則代表該目標點永遠無法抵達。

### 🛠️ 解題思路 (Approach)
1. **空間初始化**：使用一個三維布林陣列 `visited[7][7][7]` 記錄目前所有可達的點。
2. **零代檢查**：若起始點集已包含 `target`，直接回傳 `0`。
3. **循環演化**：
   - 將當前所有可達點存入列表 `p`。
   - 執行 $O(|p|^2)$ 的雙重迴圈，計算任意兩點的中點並在 `visited` 中標記。
   - 檢查 `target` 是否被標記。若有，回傳當前世代 `t`。
   - 更新點集列表 `p`。
   - **死路判定**：若新世代產生的點數與上一代完全相同（無新點產生），則回傳 `-1`。

### 📊 複雜度分析
- **時間複雜度**: $O(T \cdot N_{max}^2)$。其中 $T$ 是演化世代數，$N_{max}$ 是空間總點數（在本題為 343）。由於空間極小，計算量在百萬級別以內，執行效率極高。
- **空間複雜度**: $O(C^3)$。其中 $C$ 為座標範圍。建立 `visited` 陣列與緩存列表 `p` 需要與空間體積成正比的記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minGenerations(vector<vector<int>>& points, vector<int>& target) {
        // 使用三維陣列記錄可達點，空間範圍為 0-6
        bool visited[7][7][7];
        memset(visited, false, sizeof(visited));
        
        for(auto& p : points) {
            visited[p[0]][p[1]][p[2]] = true;
        }

        // 第 0 代檢查
        if(visited[target[0]][target[1]][target[2]]) return 0;

        // 收集初始點集
        vector<vector<int>> p;
        for(int i = 0; i < 7; ++i)
            for(int j = 0; j < 7; ++j)
                for(int k = 0; k < 7; ++k)
                    if(visited[i][j][k]) p.push_back({i, j, k});

        int l = p.size();
        int t = 1;

        while(true) {
            // 生成新一代的所有可能中點
            for(int i = 0; i < l; ++i) {
                for(int j = 0; j < l; ++j) {
                    int nx = (p[i][0] + p[j][0]) / 2;
                    int ny = (p[i][1] + p[j][1]) / 2;
                    int nz = (p[i][2] + p[j][2]) / 2;
                    visited[nx][ny][nz] = true;
                }
            }

            // 檢查目標點是否在這一代生成
            if(visited[target[0]][target[1]][target[2]]) return t;

            // 重新收集所有點，準備下一代演化
            p.clear();
            for(int i = 0; i < 7; ++i)
                for(int j = 0; j < 7; ++j)
                    for(int k = 0; k < 7; ++k)
                        if(visited[i][j][k]) p.push_back({i, j, k});
            
            // 如果點數沒有增加，代表已經達到飽和，無法再產生新點
            if(l == p.size()) return -1;
            
            l = p.size();
            ++t;
        }

        return -1;
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