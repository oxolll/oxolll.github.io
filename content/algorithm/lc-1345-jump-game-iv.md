---
title: "LeetCode 1345. Jump Game IV"
date: 2026-05-18T14:00:00+08:00
lastmod: 2026-05-18T14:00:00+08:00
difficulty: 1809
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "breadth-first-search", "hash-table"]
keywords: ["LC1345", "Hard", "1809", "Jump Game IV", "BFS", "圖論最短路徑", "雜湊表優化"]
description: "LeetCode 第 1345 題：Jump Game IV。難度評分：1809。探討如何將相同數值的跳躍規則轉化為隱式圖的最短路徑問題，並利用雜湊表及時清空（Clear）的剪枝策略，將最壞複雜度從 O(N^2) 完美壓縮至 O(N)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1345](https://leetcode.com/problems/jump-game-iv/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (1809)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Breadth-First Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `arr`，你最初位於第一個元素的索引 `0` 處。
每一步中，如果你當前位於索引 `i`，你可以跳躍到以下任一索引：
1. `i + 1` （需滿足 $i + 1 < arr.length$）
2. `i - 1` （需滿足 $i - 1 \ge 0$）
3. `j` （需滿足 $arr[i] == arr[j]$ 且 $i \neq j$）

請回傳抵達陣列最後一個索引 `arr.length - 1` 所需的 **最少跳躍次數**。

### 範例
- **Input**: `arr = [100,-23,-23,404,100,23,23,23,3,404]`
- **Output**: `3`
- **Explanation**: 
  一種最優的跳躍路徑為：索引 0 -> 索引 4 -> 索引 3 -> 索引 9。總共跳躍 3 次。

- **Input**: `arr = [7]`
- **Output**: `0`

### 限制條件
- $1 \le arr.length \le 5 \times 10^4$
- $-10^8 \le arr[i] \le 10^8$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-18：初次提交 (層次 BFS + 雜湊表及時清空優化)](#2026-05-18-初次提交)

---

## 💡 2026-05-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題要求尋找從起點到終點的「最少跳躍次數」，是一道極具代表性的**無權重圖最短路徑問題**，因此使用 **廣度優先搜尋 (BFS)** 是最直接且正確的方向。

然而，本題被評為 Hard (1809) 的核心原因在於隱藏的 **TLE (Time Limit Exceeded) 陷阱**。
如果陣列中存在大量完全相同的元素（例如：$[7, 7, 7, \dots, 7]$），那麼相同數值對應的索引清單長度會達到 $\mathcal{O}(N)$ 等級。若在 BFS 過程中，每次遇到該數值都去遍歷一次長度為 $N$ 的清單，整體的複雜度將會退化至 $\mathcal{O}(N^2)$。

**核心破局點：同值邊的一次性清除**
你的代碼中使用了一個極其關鍵且標準的 Hard 題解法優化：
```cpp
mp[arr[cur]].clear();
```
當我們第一次從佇列中取出數值為 `arr[cur]` 的節點時，我們已經將所有與它「數值相同」的相鄰下標通通加入佇列並標記為 `visited` 了。這意味著**這個數值對應的傳送門已經被完全榨乾，失去了再次走訪的價值**。及時將該鍵值對清空（或從 map 中抹除），可以確保後續遇到同值節點時直接跳過，成功將時間複雜度壓回線性。

---

### 🛠️ 解題思路 (Approach)
1. **逆向索引建圖**：使用 `unordered_map<int, vector<int>> mp` 預先統計每個數值對應的所有索引位置。
2. **層次 BFS 初始化**：配置長度為 $n$ 的布林陣列 `visited` 防止重複走訪。將起點 `0` 推入佇列，並標記 `visited[0] = true`。
3. **逐層擴張（按步數統計）**：
   - 採用典型的層次 BFS 結構（利用 `sz = q.size()` 控制當前步數 `t` 的擴張邊界）。
   - 彈出當前節點 `cur`，若 `cur == n - 1` 則代表首次抵達終點，直接回傳步數 `t`。
   - **擴張方式一（同值跳躍）**：檢查雜湊表中是否存在 `arr[cur]`，若存在則遍歷對應的所有索引，未訪問的推入佇列，**遍歷結束後立即執行 `clear()`**。
   - **擴張方式二（向後走）**：若 `cur + 1 < n` 且未訪問，加入佇列。
   - **擴張方式三（向前走）**：若 `cur - 1 >= 0` 且未訪問，加入佇列。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。雖然加入了同值陣列的遍歷，但多虧了 `mp[arr[cur]].clear()` 的早停剪枝，使得陣列中的每個索引最多只會被塞入佇列一次、被同值迴圈檢索一次。相鄰節點（`+1`, `-1`）的檢查也是常數級，因此總時間與陣列長度 $N$ 成完美線性關係。
- **空間複雜度**: $\mathcal{O}(N)$。主要記憶體開銷來自於儲存所有索引位置的 `unordered_map`、大小為 $N$ 的 `visited` 陣列，以及 BFS 佇列所需的動態配置空間。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minJumps(vector<int>& arr) {
        int n = arr.size();
        if (n == 1) return 0; // 邊界情況：長度為 1 不需要跳躍

        // 1. 建立 數值 -> 索引清單 的映射表
        unordered_map<int, vector<int>> mp;
        for(int i = 0; i < n; ++i) {
            mp[arr[i]].push_back(i);
        }

        vector<bool> visited(n, false);
        queue<int> q;
        
        // 初始化起點
        q.push(0);
        visited[0] = true;
        int t = -1; // 記錄當前跳躍步數

        // 2. 開始標準層次 BFS
        while(!q.empty()) {
            ++t; // 每進入新的一層，步數加 1
            int sz = q.size();
            
            while(sz--) {
                int cur = q.front(); 
                q.pop();

                // 早停機制：最先抵達最後一個位置的一定是最短路徑
                if(cur == n - 1) return t;

                // 擴張方向一：跳躍到所有數值相同的其他索引
                if(mp.find(arr[cur]) != mp.end()) {
                    for(auto& nx : mp[arr[cur]]) {
                        if(visited[nx]) continue;
                        visited[nx] = true;
                        q.push(nx);
                    }
                    // 關鍵剪枝優化：該數值對應的所有邊都已走訪完畢，直接清空防止未來重複遍歷導致 TLE
                    mp.erase(arr[cur]); 
                }

                // 擴張方向二：向前移動一格 (cur + 1)
                // 註：因為前面有 if(cur == n-1) 攔截，此處 cur + 1 必定不超過 n-1
                if(!visited[cur + 1]) {
                    visited[cur + 1] = true;
                    q.push(cur + 1);
                }

                // 擴張方向三：向後移動一格 (cur - 1)
                if(cur >= 1 && !visited[cur - 1]) {
                    visited[cur - 1] = true;
                    q.push(cur - 1);
                }
            }
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