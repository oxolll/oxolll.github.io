---
title: "LeetCode 1722. Minimize Hamming Distance After Swap Operations"
date: 2026-04-22T02:15:00+08:00
lastmod: 2026-04-22T02:15:00+08:00
difficulty: 1892
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "bfs", "hash-table", "greedy"]
keywords: ["LC1722", "1892", "Medium", "Minimize Hamming Distance", "Connected Components", "BFS"]
description: "LeetCode 第 1722 題：Minimize Hamming Distance After Swap Operations。難度評分：1892。探討如何將無限次交換操作抽象化為無向圖的連通分塊 (Connected Components)，並利用廣度優先搜尋 (BFS) 與極致的精準清空技巧，以 O(N) 時間複雜度計算最小漢明距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1722](https://leetcode.com/problems/minimize-hamming-distance-after-swap-operations/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1892)</span>  
> **核心主題**：`Graph` $\cdot$ `BFS` $\cdot$ `Hash Table` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個長度皆為 $n$ 的整數陣列 `source` 和 `target`。
同時給定一個二維陣列 `allowedSwaps`，其中 `allowedSwaps[i] = [a_i, b_i]` 代表你可以交換 `source` 陣列中索引為 $a_i$ 和 $b_i$ 的元素。
你可以以**任意順序、執行任意次數**的交換操作。

兩個陣列的「漢明距離 (Hamming Distance)」定義為：滿足 `source[i] != target[i]` 的索引 $i$ 的數量。
請在執行任意次數交換後，回傳 `source` 與 `target` 之間可能的**最小漢明距離**。

### 限制條件
- $n == source.length == target.length$
- $1 \le n \le 10^5$
- $1 \le source[i], target[i] \le 10^5$
- $0 \le allowedSwaps.length \le 10^5$
- $allowedSwaps[i].length == 2$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-22：初次提交 (圖論連通分塊與 BFS 精準計數)](#2026-04-22-初次提交)

---

## 💡 2026-04-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的核心破局點在於**「交換的傳遞性」**。
如果我們可以交換索引 $(A, B)$，也可以交換 $(B, C)$，那麼透過傳遞，我們實際上可以將位於 $A, B, C$ 這三個位置的元素進行**任意排列**。
這完美的契合了**圖論 (Graph Theory)** 的概念：
- 將每個陣列索引視為圖上的一個「節點 (Node)」。
- 將 `allowedSwaps` 視為無向圖中的「邊 (Edge)」。
所有透過邊連接在一起的節點，會構成一個 **連通分塊 (Connected Component)**。在同一個分塊內，`source` 的元素可以隨意移動來盡可能去匹配 `target` 對應位置上的元素。

因此，演算法的目標轉化為：
1. 找出圖中所有的連通分塊。
2. 對於每個分塊，統計 `source` 內元素的出現頻率，減去 `target` 內元素的出現頻率。
3. 頻率差異的絕對值總和，除以 2，即為該分塊中「無法成功匹配」的數量。

### 🛠️ 解題思路 (Approach)
1. **建構相鄰串列 (Adjacency List)**：
   - 根據 `allowedSwaps` 建立無向圖 `adj`。
2. **初始化計數器與走訪狀態**：
   - 宣告 `cnt` 陣列記錄頻率差值，大小開至測資上限 $10^5 + 5$。
   - 宣告 `used` 布林陣列記錄節點是否已走訪過。
3. **BFS 搜尋連通分塊**：
   - 走訪所有節點 $i \in [0, n-1]$，若未走訪過則以此為起點發起 BFS。
   - 準備一個 `tmp` 陣列，專門用來記錄**在這個分塊中出現過的數值**。
   - 進行 BFS 時，對於每個節點 `cur`：
     - `++cnt[source[cur]]` 增加來源頻率。
     - `--cnt[target[cur]]` 扣除目標頻率。
     - 將這兩個數值推入 `tmp` 陣列保存。
     - 將相鄰且未走訪的節點加入 Queue 繼續擴散。
4. **結算與精準清空 (Garbage Collection)**：
   - 分塊 BFS 結束後，走訪 `tmp` 陣列。
   - 將 `cnt[t]` 的絕對值累加至 `rt` 中（即該數值在 `source` 與 `target` 間無法匹配的數量）。
   - **關鍵優化**：讀取完畢後立刻執行 `cnt[t] = 0`。這樣可以避免每次都需要 $\mathcal{O}(\max\_val)$ 掃描整個 `cnt` 陣列，完美防堵 TLE。
5. **回傳結果**：因為一對無法匹配的元素（例如 `source` 多一個 A，`target` 多一個 B）會在計數器中產生 $1$ 與 $-1$ 兩次絕對值貢獻，因此最終總和必須 `rt / 2` 才是真實的漢明距離。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + E)$。其中 $N$ 是陣列長度，$E$ 是 `allowedSwaps` 的數量。建圖耗時 $\mathcal{O}(E)$，BFS 遍歷所有節點與邊各一次耗時 $\mathcal{O}(N + E)$。精準清空技巧確保了計數與重置時間嚴格限制在 $\mathcal{O}(N)$。整體為線性時間。
- **空間複雜度**: $\mathcal{O}(N + E + \max\_val)$。建圖佔用 $\mathcal{O}(N + E)$，`cnt` 陣列佔用 $\mathcal{O}(\max\_val)$ 空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumHammingDistance(vector<int>& source, vector<int>& target, vector<vector<int>>& allowedSwaps) {
        int n = source.size();
        
        // 1. 建構無向圖的相鄰串列
        vector<vector<int>> adj(n, vector<int>());
        for(auto a : allowedSwaps) {
            int u = a[0], v = a[1];
            adj[u].push_back(v);
            adj[v].push_back(u);
        }

        int rt = 0;
        // cnt 用於記錄同一個連通分塊內，各數值在 source 與 target 的頻率差異
        vector<int> cnt(1e5 + 5, 0);
        vector<bool> used(n, false);
        queue<int> q;
        
        // 2. 尋找並處理所有的連通分塊
        for(int i = 0; i < n; ++i) {
            if(used[i]) continue;
            
            used[i] = true;
            q.push(i);
            
            // 💡 神級優化：利用 tmp 記錄當前分塊出現過的數字，避免清空時引發 TLE
            vector<int> tmp;
            
            // BFS 擴散收集整個分塊
            while(!q.empty()) {
                auto cur = q.front(); 
                q.pop();
                
                // 來源陣列的數字頻率 +1，目標陣列的數字頻率 -1
                ++cnt[source[cur]];
                --cnt[target[cur]];
                
                // 記錄出現過的數字
                tmp.push_back(source[cur]);
                tmp.push_back(target[cur]);
                
                // 繼續走訪相鄰節點
                for(auto next : adj[cur]) {
                    if(used[next]) continue;
                    used[next] = true;
                    q.push(next);
                }
            }
            
            // 3. 結算當前分塊的差異，並進行精準清空 (Garbage Collection)
            for(auto& t : tmp) {
                if(cnt[t] == 0) continue;
                // 取頻率差的絕對值累加
                rt += (cnt[t] >= 0 ? cnt[t] : -cnt[t]);
                // 精準清零，讓下一個分塊可以安全使用 cnt 陣列
                cnt[t] = 0;
            }
        }

        // 由於每一個不匹配會造成一正一負的貢獻 (被重複算兩次)，因此最後要除以 2
        return rt / 2;
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