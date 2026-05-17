---
title: "LeetCode 1306. Jump Game III"
date: 2026-05-18T01:15:00+08:00
lastmod: 2026-05-18T01:15:00+08:00
difficulty: 1396
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "breadth-first-search", "depth-first-search"]
keywords: ["LC1306", "Medium", "Jump Game III", "BFS", "圖論遍歷"]
description: "LeetCode 第 1306 題：Jump Game III。難度評分：1396。探討如何將陣列跳躍規則建模為隱式圖，並利用廣度優先搜尋 (BFS) 配合走訪標記，在線性時間內尋找目標值 0。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1306](https://leetcode.com/problems/jump-game-iii/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1396)</span>  
> **核心主題**：`Array` $\cdot$ `Breadth-First Search` $\cdot$ `Depth-First Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
這裡有一個非負整數陣列 `arr`，你最初位於陣列的起始索引 `start` 處。
當你位於下標 `i` 時，你可以跳躍到 `i + arr[i]` 或者 `i - arr[i]`。

請判斷你是否能跳躍到任意一個值為 `0` 的下標。
注意：在任何時候，你都不能跳出陣列的邊界。

### 範例
- **Input**: `arr = [4,2,3,0,3,1,2], start = 5`
- **Output**: `true`
- **Explanation**: 
  所有抵達下標 3 (值為 0) 的可能路徑如下：
  下標 5 -> 下標 4 -> 下標 1 -> 下標 3
  下標 5 -> 下標 6 -> 下標 4 -> 下標 1 -> 下標 3

- **Input**: `arr = [3,0,2,1,2], start = 2`
- **Output**: `false`

### 限制條件
- $1 \le arr.length \le 5 \times 10^4$
- $0 \le arr[i] < arr.length$
- $0 \le start < arr.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-18：初次提交 (標準 BFS 佇列解法)](#2026-05-18-初次提交)

---

## 💡 2026-05-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這道題的最佳破局思維是將陣列結構**抽象化為有向圖**。
- **節點 (Vertices)**：陣列的每個索引 $0 \le i < n$。
- **邊 (Edges)**：從索引 $i$ 連向 $i + arr[i]$ 以及 $i - arr[i]$ 的兩條有向邊。

因為問題是要找「存不存在一條路徑抵達目標點（值為 0）」，而且不需要求最短路徑長度，因此 **BFS (廣度優先搜尋)** 和 **DFS (深度優先搜尋)** 都是極佳的選擇。

**核心考量點：**
1. **防無窮迴圈**：圖中可能存在環（例如兩個點的值相同且互相指向對方）。必須引入 `visited` 機制，確保每個節點最多被丟入佇列一次。
2. **早停機制 (Early Exit)**：一旦在探測過程中發現 `arr[x] == 0`，代表目標已達成，立即中斷並回傳 `true`，節省後續不必要的搜尋開銷。

### 🛠️ 解題思路 (Approach)
1. **初始化**：配置長度為 $n$ 的布林陣列 `visited`，並將起點 `start` 標記為已訪問、推入 `queue`。
2. **層次遍歷**：當隊列不為空時，彈出隊首節點 `x`：
   - 檢查 `arr[x]` 是否為 `0`。若是，直接回傳 `true`。
   - 計算向前跳 `f = x + arr[x]` 與向後跳 `b = x - arr[x]`。
   - 分別驗證 `f` 與 `b` 是否在陣列範圍內且未被訪問。若通過驗證，更新 `visited` 狀態並將其加入佇列。
3. **無解判定**：若佇列已空仍未碰到 `0`，說明從該起點出發的連通元件中不包含目標值，回傳 `false`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。在最壞情況下，我們可能會遍歷陣列中的每一個元素。因為有 `visited` 的攔截，每個下標最多只會進出佇列一次。
- **空間複雜度**: $\mathcal{O}(N)$。主要開銷為大小為 $N$ 的 `visited` 陣列以及 BFS 佇列在最極端寬度下的記憶體佔用。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canReach(vector<int>& arr, int start) {
        int n = arr.size();
        // 1. 維護走訪紀錄標記，防止在陣列的環結構中無限打轉
        vector<bool> visited(n, false);
        queue<int> q;
        
        // 2. 初始化起點狀態
        q.push(start);
        visited[start] = true;
        
        // 3. 開始廣度優先搜尋
        while(!q.empty()) {
            int cur = q.front(); 
            q.pop();
            
            int step = arr[cur];
            // 早停機制：一旦找到值為 0 的節點，代表路徑可行
            if(step == 0) return true;

            int f = cur + step; // 向前跳躍目標
            int b = cur - step; // 向後跳躍目標

            // 檢查向前跳躍的邊界與走訪狀態
            if(f < n && !visited[f]) {
                q.push(f);
                visited[f] = true;
            }

            // 檢查向後跳躍的邊界與走訪狀態
            if(b >= 0 && !visited[b]) {
                q.push(b);
                visited[b] = true;
            }
        }

        return false;
    }
};
```

---

### 💡 延伸優化思維：$\mathcal{O}(1)$ 額外空間優化手法
如果面試官要求不使用額外的 `visited` 陣列來達成 $\mathcal{O}(1)$ 輔助空間，你可以利用**原陣列就地標記 (In-place Marking)** 的技巧。因為題目說明 `arr[i]` 皆為非負整數，我們可以把訪問過的節點數值**乘以 $-1$ 轉為負數**（若原本是 $0$ 則不用管，因為一踩到 $0$ 就退出了）。

```cpp
// 偽代碼思維展示：
if (arr[next_idx] >= 0) {
    arr[next_idx] = -arr[next_idx]; // 標記為負值代表已訪問
    q.push(next_idx);
}
```
*這是一個常見的陣列空間優化黑科技，在面試中提出能顯著提升對記憶體控制的加分度！*

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