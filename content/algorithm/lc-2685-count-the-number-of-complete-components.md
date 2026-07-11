---
title: "LeetCode 2685. Count the Number of Complete Components"
date: 2026-07-11T13:45:00+08:00
lastmod: 2026-07-11T13:45:00+08:00
difficulty: 1769
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "graph", "union-find", "math"]
keywords: ["LC2685", "Medium", "1769", "Complete Components", "並查集", "完全圖", "度數統計", "圖論"]
description: "LeetCode 第 2685 題：Count the Number of Complete Components。難度評分：1769。探討如何利用並查集 (Union-Find) 高效劃分連通分量，並結合圖論的度數數學性質 (Sum of Degrees)，優雅判定完全圖。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2685](https://leetcode.com/problems/count-the-number-of-complete-components/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1769)</span>  
> **核心主題**：`Graph` $\cdot$ `Union-Find` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數 `n`，代表有一個包含 `n` 個節點的無向圖，節點編號為 `0` 到 `n - 1`。
同時給你一個二維整數陣列 `edges`，其中 $edges[i] = [a_i, b_i]$ 代表節點 $a_i$ 和 $b_i$ 之間有一條無向邊。

請你回傳該圖中 **完全連通分量 (Complete Connected Components)** 的數量。

一個 **完全連通分量** 必須滿足以下兩個條件：
1. 它是一個連通分量（子圖內的任意兩個節點都可以互相抵達，且不與子圖外的任何節點相連）。
2. 子圖內的 **任意兩個節點之間都有一條邊直接相連**（即這是一個完全圖 / Clique）。

### 範例
- **Input**: `n = 6, edges = [[0,1],[0,2],[1,2],[3,4]]`
- **Output**: `3`
- **Explanation**: 
  - 節點 {0, 1, 2} 構成一個完全連通分量（3個節點互相連接，共3條邊）。
  - 節點 {3, 4} 構成一個完全連通分量（2個節點互相連接，共1條邊）。
  - 節點 {5} 獨立構成一個完全連通分量（1個節點，0條邊）。
  總共有 3 個完全連通分量。

- **Input**: `n = 6, edges = [[0,1],[0,2],[1,2],[3,4],[3,5]]`
- **Output**: `1`
- **Explanation**:
  - 節點 {0, 1, 2} 是一個完全連通分量。
  - 節點 {3, 4, 5} 是一個連通分量，但 4 和 5 之間沒有邊相連，因此不是「完全」的。
  總共只有 1 個完全連通分量。

### 限制條件
- $1 \le n \le 50$
- $0 \le edges.length \le n \times (n - 1) / 2$
- $edges[i].length == 2$
- $0 \le a_i, b_i \le n - 1$
- $a_i \neq b_i$
- 兩個節點之間最多只有一條邊。
</details>

---

## 📝 歷次打卡與演算法進化
- [2026-07-11：初次提交 (並查集分組 + 數學度數驗證)](#2026-07-11-初次提交)

---

## 💡 2026-07-11 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要解決這道題，我們需要完成兩個步驟：一是「將圖劃分成獨立的連通分量」，二是「驗證每個連通分量是否為完全圖」。

**核心破局點 1：並查集 (Union-Find) 家族劃分**
我們可以使用並查集來將所有有邊相連的節點歸類到同一個家族 (連通分量) 中。
為了讓後續的分類更加直觀，在所有的 `Union` 操作結束後，我們額外執行一次迴圈 `find(i)`。這一步會觸發「路徑壓縮 (Path Compression)」，讓所有節點的 `parent` 直接指向該家族的絕對根節點，徹底扁平化樹狀結構。

**核心破局點 2：完全圖的度數數學性質**
如何快速驗證一個家族是否為完全圖？
在圖論中，一個包含 $V$ 個節點的完全圖，每個節點都必須與其他 $V-1$ 個節點相連，因此每個節點的度數 (Degree) 必定為 $V-1$。
這意味著，**整個完全圖的「度數總和」必定等於 $V \times (V - 1)$**。
- 我們在建立圖的階段，先用一個 `deg` 陣列記錄每個節點的度數。
- 在結算家族時，統計該家族的節點總數 `cnt`，並將該家族所有節點的度數加總為 `ecnt`。
- 最後只需驗證 `(cnt - 1) * cnt == ecnt` 即可！這個數學判定式 $\mathcal{O}(1)$ 且無懈可擊（包含 $cnt = 1$ 的邊界情況也能完美處理）。

### 🛠️ 解題思路 (Approach)
1. **初始化**：
   - 準備並查集的 `parent` 陣列，長度為 `n`，初始化為自己。
   - 準備 `deg` 陣列記錄每個節點的連線數（度數）。
2. **建構圖與並查集**：
   - 遍歷 `edges`，累加 `u` 和 `v` 的度數 `deg[u]` 與 `deg[v]`。
   - 執行 `Union(u, v)` 將兩者合併為同一家族。
3. **路徑壓縮扁平化**：
   - 遍歷 `0` 到 `n-1`，執行 `find(i)`，確保所有節點的 `parent` 都是最終的根節點。
4. **家族統計與驗證**：
   - 使用雙層迴圈。外層枚舉所有可能的根節點 `i`。
   - 內層遍歷所有節點 `j`，若 `parent[j] == i`，代表 `j` 屬於家族 `i`。
     - 累加節點數 `++cnt`。
     - 累加家族總度數 `ecnt += deg[j]`。
     - **去重標記**：將 `parent[j] = -1`，確保該節點之後不會被外層迴圈重複計算。
   - 如果該家族確實存在 (`cnt > 0`)，且符合完全圖的數學性質 `(cnt - 1) * cnt == ecnt`，則答案 `rt` 加 1。
5. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(V^2 + E \cdot \alpha(V))$。其中 $V$ 是節點數 ($n$)，$E$ 是邊數。並查集的合併耗時 $\mathcal{O}(E \cdot \alpha(V))$。最後的雙層迴圈掃描耗時 $\mathcal{O}(V^2)$。由於題目限制 $N \le 50$，$V^2 \le 2500$，這樣的複雜度在執行時間上微乎其微，效能極佳。
- **空間複雜度**: $\mathcal{O}(V)$。宣告了長度為 $n$ 的 `parent` 陣列與 `deg` 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> parent;
    
    // 尋找根節點，並進行路徑壓縮
    int find(int x) {
        if(parent[x] == x) return x;
        return parent[x] = find(parent[x]);
    }
    
    // 合併兩個連通分量
    void Union(int x, int y) {
        int a = find(x), b = find(y);
        if(a < b) {
            parent[b] = a;
        } else {
            parent[a] = b;
        }
    }
    
    int countCompleteComponents(int n, vector<vector<int>>& edges) {
        parent.resize(n);
        for(int i = 0; i < n; ++i) {
            parent[i] = i;
        }

        vector<int> deg(n, 0); // 記錄每個節點的度數
        
        for(auto& e : edges) {
            int u = e[0], v = e[1];
            ++deg[u];
            ++deg[v];
            Union(u, v);
        }
        
        // 核心細節：強力路徑壓縮，確保所有節點的 parent 都是絕對根節點
        for(int i = 0; i < n; ++i) {
            find(i);
        }
            
        int rt = 0;
        
        // 枚舉所有可能的家族根節點
        for(int i = 0; i < n; ++i) {
            int cnt = 0;   // 家族中的節點數量
            int ecnt = 0;  // 家族中的總度數
            
            for(int j = 0; j < n; ++j) {
                // 若節點 j 屬於以 i 為根的家族
                if(i == parent[j]) {
                    ++cnt;
                    ecnt += deg[j];
                    // 去重標記：處理過後設為 -1，防止外層迴圈輪到 j 時重複計算
                    parent[j] = -1;
                }
            }

            // 完全圖的數學判定：若家族非空，且總度數 == 節點數 * (節點數 - 1)
            if(cnt && (cnt - 1) * cnt == ecnt) {
                ++rt;
            }
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