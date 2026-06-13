---
title: "LeetCode 1483. Kth Ancestor of a Tree Node"
date: 2026-06-13T03:00:00+08:00
lastmod: 2026-06-13T03:00:00+08:00
difficulty: 2115
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "tree", "binary-lifting", "dynamic-programming", "design"]
keywords: ["LC1483", "Hard", "2115", "Kth Ancestor", "Tree Node", "倍增法", "Binary Lifting", "祖先查詢"]
description: "LeetCode 第 1483 題：Kth Ancestor of a Tree Node。難度評分：2115。探討如何利用倍增法 (Binary Lifting) 預處理節點的 2^i 個祖先，將單次祖先查詢的時間複雜度從 O(K) 降至 O(log K)，達成極致效能的樹上跳躍。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1483](https://leetcode.com/problems/kth-ancestor-of-a-tree-node/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2115)</span>  
> **核心主題**：`Tree` $\cdot$ `Binary Lifting` $\cdot$ `Dynamic Programming` $\cdot$ `Design`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一棵樹，樹上有 `n` 個節點，編號為 `0` 到 `n - 1`。樹以父節點陣列 `parent` 的形式給出，其中 `parent[i]` 是節點 `i` 的父節點。樹的根節點為 `0`，因此 `parent[0] = -1`。

請你設計並實作 `TreeAncestor` 類別：
- `TreeAncestor(int n, int[] parent)`：初始化樹結構。
- `int getKthAncestor(int node, int k)`：回傳節點 `node` 的第 `k` 個祖先節點。如果不存在這樣的祖先，則回傳 `-1`。

### 範例
- **Input**: 
  `["TreeAncestor","getKthAncestor","getKthAncestor","getKthAncestor"]`
  `[[7,[-1,0,0,1,1,2,2]],[3,1],[5,2],[6,3]]`
- **Output**: `[null,1,0,-1]`
- **Explanation**: 
  ```text
        0
      /   \
     1     2
    / \   / \
   3   4 5   6
  ```
  `TreeAncestor treeAncestor = new TreeAncestor(7, [-1, 0, 0, 1, 1, 2, 2]);`
  `treeAncestor.getKthAncestor(3, 1);` // 回傳 1，因為 3 的第 1 個祖先是 1
  `treeAncestor.getKthAncestor(5, 2);` // 回傳 0，因為 5 的第 2 個祖先是 0
  `treeAncestor.getKthAncestor(6, 3);` // 回傳 -1，因為不存在第 3 個祖先

### 限制條件
- $1 \le k \le n \le 5 \times 10^4$
- `parent[0] == -1` 且對所有 $i > 0$，都有 $0 \le parent[i] < n$。
- 最多會呼叫 $5 \times 10^4$ 次 `getKthAncestor`。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-13：初次提交 (倍增法 Binary Lifting 模板)](#2026-06-13-初次提交)

---

## 💡 2026-06-13 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
如果對於每次呼叫 `getKthAncestor(node, k)`，我們都沿著 `parent` 陣列一步一步往上找，最壞情況下單次查詢需要 $\mathcal{O}(K)$ 的時間。在 $5 \times 10^4$ 次查詢的極限測資下，絕對會 `Time Limit Exceeded`。

**核心破局點：倍增法 (Binary Lifting)**
為了加速查詢，我們可以預先算出每個節點往上跳「$2$ 的整數次方」步會到達哪裡。
定義 `k_thP[node][i]` 代表 `node` 的第 $2^i$ 個祖先。

**狀態轉移方程式 (動態規劃)：**
要找我的第 $2^i$ 個祖先，相當於「先往上跳 $2^{i-1}$ 步到達中繼節點，再從中繼節點往上跳 $2^{i-1}$ 步」。
因此：
`k_thP[node][i] = k_thP[ k_thP[node][i-1] ][ i-1 ]`

這讓我們能在 $\mathcal{O}(N \log N)$ 的時間內建好查詢表。

**快速查詢 (Query)：**
在查詢第 `k` 個祖先時，我們將 `k` 拆解為二進位。例如 $k = 5$ (二進位 `101`)，代表我們需要往上跳 4 步，再往上跳 1 步。
透過檢查二進位的每一個 bit，只要該位元為 1，我們就利用預建好的 `k_thP` 陣列進行 $\mathcal{O}(1)$ 的大步跳躍。這將單次查詢的時間壓縮到了極致的 $\mathcal{O}(\log K)$。

---

### 🛠️ 解題思路 (Approach)
1. **全域陣列宣告**：宣告 `k_thP[50005][31]` 儲存倍增結果。大小設為 31 足以涵蓋 $n \le 50000$ (因 $2^{15} > 50000$)。
2. **初始化 (`TreeAncestor`)**：
   - 使用 `memset` 將陣列全初始化為 `-1`。
   - `k_thP[i][0] = parent[i]`：第 $2^0 = 1$ 個祖先就是直接的父節點。
   - 雙層迴圈建表：外層遍歷步數 $i$ (1 ~ 31)，內層遍歷節點 $j$。若中繼節點 `k_thP[j][i-1]` 存在，則轉移算出 `k_thP[j][i]`。
3. **查詢 (`getKthAncestor`)**：
   - 迴圈檢查 `k` 的二進位表示 (從高位到低位)。
   - 若 `(k >> i) & 1` 為真，代表需要跳 $2^i$ 步，更新 `node = k_thP[node][i]`。
   - 若途中 `node` 變成 `-1`，代表已經跳出樹根，提早 `break`。
4. **回傳結果**。

---

### 📊 複雜度分析
- **時間複雜度**:
  - **初始化**: $\mathcal{O}(N \log N)$。雙層迴圈建立倍增表，$\log N$ 最大約為 16。
  - **單次查詢**: $\mathcal{O}(\log K)$。最多執行 31 次位元運算與陣列讀取。
- **空間複雜度**: $\mathcal{O}(N \log N)$。配置了大小為 $N \times 31$ 的倍增陣列 `k_thP`。

---

### 💻 程式碼實作 (C++)
```cpp
// 宣告全域陣列以避免重複分配記憶體開銷
int k_thP[50005][31];

class TreeAncestor {
public:
    TreeAncestor(int n, vector<int>& parent) {
        // 初始化為 -1，代表不存在該祖先
        memset(k_thP, -1, sizeof(k_thP));

        // Base case: 第 2^0 = 1 個祖先就是 parent 陣列
        for(int i = 0; i < n; ++i) {
            k_thP[i][0] = parent[i];
        }
            
        // 倍增法建表 (Binary Lifting)
        for(int i = 1; i < 32; ++i) {
            for(int j = 0; j < n; ++j) {
                // 如果我的「第 2^(i-1) 個祖先」存在
                if(k_thP[j][i - 1] != -1) {
                    // 我的「第 2^i 個祖先」 = (我的第 2^(i-1) 個祖先) 的「第 2^(i-1) 個祖先」
                    k_thP[j][i] = k_thP[k_thP[j][i - 1]][i - 1];
                }
            }
        }
    }
    
    int getKthAncestor(int node, int k) {
        // 將 k 拆解為二進位，利用倍增表快速跳躍
        for(int i = 31; i >= 0; --i) {
            // 如果 k 的第 i 個位元是 1，代表需要往上跳 2^i 步
            if((k >> i) & 1) {
                node = k_thP[node][i];
            }

            // 如果已經跳出樹的範圍，提早結束
            if(node == -1) break;
        }

        return node;
    }
};

/**
 * Your TreeAncestor object will be instantiated and called as such:
 * TreeAncestor* obj = new TreeAncestor(n, parent);
 * int param_1 = obj->getKthAncestor(node,k);
 */
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