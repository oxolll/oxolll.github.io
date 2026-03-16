---
title: "LeetCode 1878. Get Biggest Three Rhombus Sums in a Grid"
date: 2026-03-16T12:00:00+08:00
lastmod: 2026-03-16T12:00:00+08:00
difficulty: 1897
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Matrix", "Breadth-First Search", "Math"]
keywords: ["LC1878", "1897", "Medium", "Rhombus Sums", "BFS", "Manhattan Distance"]
description: "LeetCode 第 1878 題：Get Biggest Three Rhombus Sums in a Grid。難度評分：1897。探討如何利用層次遍歷 (BFS) 結合曼哈頓距離特性，取代繁瑣的對角線座標推導，優雅地找出矩陣中前三大相異的菱形邊界和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1878](https://leetcode.com/problems/get-biggest-three-rhombus-sums-in-a-grid/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1897)</span>    
> **核心主題**：`Matrix` $\cdot$ `Breadth-First Search` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 $m \times n$ 的整數矩陣 `grid`。
一個「菱形和 (Rhombus Sum)」是指矩陣中一個正菱形**邊界**上所有元素的總和。菱形的面積必須大於 0（即不能只有邊界而沒有內部），但如果菱形退化成單一一個儲存格，其面積為 0，此時菱形和就是該儲存格的值。

請回傳一個陣列，包含矩陣中**前三大相異 (Distinct)** 的菱形和。如果相異的菱形和少於 3 個，則回傳所有相異的菱形和。
回傳的陣列必須按照**遞減順序**排列。

### 限制條件
- $m == grid.length$
- $n == grid[i].length$
- $1 \le m, n \le 50$
- $1 \le grid[i][j] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-16：初次提交 (中心擴展與層次 BFS 驗證法)](#2026-03-16-初次提交)

---

## 💡 2026-03-16 初次提交

### 🎯 直覺 (Intuition)
常規解法通常是枚舉菱形的頂點並向四個斜向方向尋找邊界，但實作細節極度繁瑣。
若從幾何角度思考二維網格：與中心點 $(i, j)$ 的**曼哈頓距離 (Manhattan Distance)** 正好為 $k$ 的所有點，連起來就是一個正菱形的邊界。
因此，我們可以將矩陣中的每一個點視為菱形的「中心」，並執行 **層次遍歷 (Level-order BFS)**。
- 距離 $k=0$ (中心點本身)：邊界周長為 **1** 個點。
- 距離 $k=1$ (上下左右)：邊界周長為 **4** 個點。
- 距離 $k=2$：邊界周長為 **8** 個點。
- 距離 $k=3$：邊界周長為 **12** 個點。

如果我們在 BFS 的過程中，發現某一整層的 Queue 大小不符合上述的數列 (即 `1, 4, 8, 12...`)，就代表這個菱形的擴展已經撞到了網格的物理邊界，無法形成完整的菱形，此時直接終止對該中心點的搜尋即可。

### 🛠️ 解題思路 (Approach)
1. **全域狀態準備**：
   - 建立一個 `cand` 陣列用來收集所有合法的菱形和。
   - 使用一個與 `grid` 大小相同的 `visited` 矩陣，並搭配一個遞增的 `token` 變數。這種做法可以在每次 BFS 時省去重新初始化 `visited` 陣列的 $\mathcal{O}(M \times N)$ 時間開銷。
2. **中心點枚舉與 BFS**：
   - 雙層迴圈遍歷矩陣的每一個座標 `(i, j)` 作為中心。
   - 將 `token` 加一，重置 `visited[i][j] = token`，並將起點推入 Queue。
   - 維護一個 `checksize` 變數，初始為 **1**。
   - 進入 BFS 迴圈：
     - 檢查當前層的節點數 `t` 是否等於 `checksize`。若不等於，代表菱形殘缺（撞到邊界），直接 `break` 結束該中心點的探索。
     - 遍歷當前層的所有節點，累加其數值得到 `sum`，同時將相鄰的四個合法節點推入下一層的 Queue 中。
     - 將 `sum` 加入 `cand` 陣列中。
     - 更新下一層的預期邊界大小：`checksize = (checksize == 1 ? 4 : checksize + 4)`。
3. **結果結算**：
   - 將 `cand` 陣列進行降冪排序 (`sort(cand.rbegin(), cand.rend())`)。
   - 遍歷排序後的 `cand`，過濾掉重複的數值，並收集前 **3** 大的元素回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}((M \times N)^2)$。網格大小最大為 $50 \times 50$。總共有 $M \times N$ 個中心點，每個中心點的 BFS 最多擴展整個網格。總運算次數約為 $(2500)^2 = 6.25 \times 10^6$，在 C++ 中可在數毫秒內執行完畢。
- **空間複雜度**: $\mathcal{O}(M \times N)$。`visited` 矩陣和 BFS Queue 皆需要與網格等同的空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> getBiggestThree(vector<vector<int>>& grid) {
        vector<int> cand;
        int m = grid.size(), n = grid[0].size();
        vector<pair<int, int>> dir = {{0,1}, {1,0}, {0,-1}, {-1,0}};
        
        // 利用 token 技巧避免每次 BFS 都需 memset 整個 visited 陣列
        vector<vector<int>> visited(m, vector<int>(n, 0));
        int token = 0;

        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                ++token;
                queue<pair<int, int>> q;
                visited[i][j] = token;
                int checksize = 1;
                q.push({i, j});
                
                while(!q.empty()) {
                    int t = q.size();
                    // 核心驗證：當前層的節點數必須嚴格等於曼哈頓距離下的周長
                    if(t != checksize) break;
                    
                    int sum = 0;
                    while(t--) {
                        auto [x, y] = q.front(); 
                        q.pop();
                        sum += grid[x][y];
                        
                        // 擴展至下一層 (曼哈頓距離 k+1)
                        for(auto [r, c] : dir) {
                            int nx = x + r, ny = y + c;
                            if(nx < 0 || nx >= m || ny < 0 || ny >= n || visited[nx][ny] == token)
                                continue;
                            visited[nx][ny] = token;
                            q.push({nx, ny});
                        }
                    }
                    
                    // 記錄完整的菱形和
                    cand.push_back(sum);
                    // 預期下一層的邊界大小：1 -> 4 -> 8 -> 12 ...
                    checksize = (checksize == 1 ? 4 : checksize + 4);
                }
            }
        }
        
        // 排序並取前三大相異的數值
        sort(cand.rbegin(), cand.rend());
        vector<int> rt;
        for(int i = 0; i < cand.size() && rt.size() != 3; ++i) {
            if(!rt.empty() && rt.back() == cand[i]) {
                continue;
            }
            rt.push_back(cand[i]);
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