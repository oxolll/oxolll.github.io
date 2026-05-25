---
title: "LeetCode 3939. Count Non Adjacent Subsets in a Rooted Tree"
date: 2026-05-25T20:00:00+08:00
lastmod: 2026-05-25T20:00:00+08:00
difficulty: 2000
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "tree-dp", "dynamic-programming", "math", "combinatorics"]
keywords: ["LC3939", "Hard", "Tree DP", "樹上 DP", "背包問題", "同餘狀態", "獨立集"]
description: "LeetCode 第 3939 題：Count Non Adjacent Subsets in a Rooted Tree。探討如何在樹狀結構中，結合「最大獨立集」的非相鄰限制與「同餘背包」狀態，利用樹上動態規劃 (Tree DP) 高效計算總和為 k 的倍數的合法子集數量。"
---

## 📊 題目資訊
> **核心主題**：`Dynamic Programming` $\cdot$ `Tree` $\cdot$ `Math`  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>

---

## 📖 題目描述
給定一棵有根樹，由 `n` 個節點組成，節點編號從 `0` 到 `n - 1`，根節點為 `0`。
樹的結構由一個陣列 `parent` 給出，其中 `parent[i]` 是節點 `i` 的父節點，`parent[0] = -1`。
每個節點都有一個對應的值，由陣列 `nums` 給出。

你需要從這棵樹中選擇一個 **非空子集 (Subset)**，必須滿足以下兩個條件：
1. **非相鄰限制**：子集中的任意兩個節點不能在樹中有直接的父子連接邊（這類似於樹的最大獨立集問題）。
2. **整除限制**：子集中所有節點的 `nums` 值總和，必須是給定整數 `k` 的倍數（即總和 $\pmod k == 0$）。

請計算滿足條件的合法子集總數。由於答案可能非常大，請回傳對 $10^9 + 7$ 取模後的結果。

---

## 🎯 演算法分析 (Algorithm Analysis)

這是一道標準的 **樹上動態規劃 (Tree DP)** 結合 **同餘背包問題 (Knapsack DP on Modulo)** 的複合題型。

### 核心破局點 1：樹上的非相鄰狀態轉移 (打家劫舍 III)
對於樹上的任何一個節點 `u`，我們只有兩種選擇：
* **選擇節點 `u`**：則 `u` 的所有直接子節點 `v` 都**絕對不能**被選擇。
* **不選擇節點 `u`**：則 `u` 的子節點 `v` **可以被選擇，也可以不被選擇**（取兩者之和）。

### 核心破局點 2：同餘背包的狀態合併
除了要管選不選，我們還要管「總和」。由於題目只關心是否為 $k$ 的倍數，我們不需要記錄龐大的實際總和，只需要記錄**總和除以 $k$ 的餘數**（共有 $0$ 到 $k-1$ 種狀態）。
因此，對於每個節點 `u`，我們需要定義一個二維 DP 狀態：
* `cnt[1][j]`：代表**選擇**了節點 `u`，且以 `u` 為根的子樹中，合法選法的總和 $\pmod k = j$ 的組合數。
* `cnt[0][j]`：代表**不選擇**節點 `u`，且以 `u` 為根的子樹中，合法選法的總和 $\pmod k = j$ 的組合數。

當節點 `u` 有多個子節點時，我們不能簡單地將子節點的 DP 陣列相加，而是必須做**類似多項式乘法（卷積）的動態規劃合併**。利用兩個巢狀迴圈：
$$ \text{NewDP}[(i + j) \pmod k] += \text{DP}[i] \times \text{ChildDP}[j] $$
以此將所有子樹的組合數依序合併到當前節點上。

---

## 💻 程式碼實作 (C++)

```cpp
class Solution {
public:
    // 封裝節點結構，讓樹狀結構與狀態管理更為具象
    class Node {
    public: 
        vector<Node*> childs;
        // cnt[0][j] = 不選當前節點，且子樹餘數為 j 的組合數
        // cnt[1][j] = 選當前節點，且子樹餘數為 j 的組合數
        vector<vector<long long>> cnt;
        int cur; // 節點的值
        Node(int cur, int k) : cur(cur) {
            cnt.assign(2, vector<long long>(k, 0));
        }
    };
    
    long long MOD = 1e9 + 7;
    
    void dfs(Node* cur, int k) {
        // 邊界條件：葉子節點
        if (cur->childs.empty()) {
            cur->cnt[1][cur->cur % k] = 1; // 選擇自己，餘數為自身的值 % k
            cur->cnt[0][0] = 1;            // 不選自己，總和為 0，餘數為 0
            return;
        }

        // 遞迴處理所有子樹 (Bottom-Up DP)
        for (auto next : cur->childs) {
            dfs(next, k);
        }

        // --- 狀態一：選擇當前節點 ---
        vector<long long> tmp(k, 0);
        tmp[cur->cur % k] = 1; // 初始狀態：只有自己
        
        for (auto next : cur->childs) {
            vector<long long> tmp2(k, 0);
            for (int i = 0; i < k; ++i) {
                if (tmp[i] == 0) continue;
                for (int j = 0; j < k; ++j) {
                    // 若選擇了當前節點，子節點【絕對不能選】 (next->cnt[0])
                    if (next->cnt[0][j] == 0) continue;

                    int nk = (i + j) % k;
                    long long val = (tmp[i] * next->cnt[0][j]) % MOD;
                    tmp2[nk] = (tmp2[nk] + val) % MOD;
                }
            }
            tmp = std::move(tmp2); // 滾動更新
        }
        for (int i = 0; i < k; ++i) {
            cur->cnt[1][i] = (cur->cnt[1][i] + tmp[i]) % MOD;
        }

        // --- 狀態二：不選擇當前節點 ---
        vector<long long> tmp3(k, 0);
        tmp3[0] = 1; // 初始狀態：總和為 0
        
        for (auto next : cur->childs) {
            vector<long long> tmp4(k, 0);
            for (int i = 0; i < k; ++i) {
                if (tmp3[i] == 0) continue;
                for (int j = 0; j < k; ++j) {
                    // 若不選當前節點，子節點【可以選，也可以不選】
                    if (next->cnt[1][j] == 0 && next->cnt[0][j] == 0) continue;

                    int nk = (i + j) % k;
                    long long ways = (next->cnt[1][j] + next->cnt[0][j]) % MOD;
                    long long val = (tmp3[i] * ways) % MOD;
                    tmp4[nk] = (tmp4[nk] + val) % MOD;
                }
            }
            tmp3 = std::move(tmp4); // 滾動更新
        }
        for (int i = 0; i < k; ++i) {
            cur->cnt[0][i] = (cur->cnt[0][i] + tmp3[i]) % MOD;
        }
    }

    int countValidSubsets(vector<int>& parent, vector<int>& nums, int k) {
        int n = parent.size();
        vector<Node*> nodes;
        for (int i = 0; i < n; ++i) {
            nodes.push_back(new Node(nums[i], k));
        }
        
        // 根據 parent 陣列建樹
        for (int i = 0; i < n; ++i) {
            if (parent[i] == -1) continue;
            nodes[parent[i]]->childs.push_back(nodes[i]);
        }

        // 從根節點 0 開始進行 DFS
        dfs(nodes[0], k);
        
        // 答案為「根節點選或不選，且餘數為 0 的總合法組合數」
        int ans = (nodes[0]->cnt[0][0] + nodes[0]->cnt[1][0]) % MOD;
        
        // 釋放動態記憶體 (Good Practice)
        for (int i = 0; i < n; ++i) {
            delete nodes[i];
        }
        
        // 題目要求「非空子集」，因此必須扣除全都不選的 1 種空集情況
        return (ans - 1 + MOD) % MOD; 
    }
};
```

---

## 📊 複雜度分析

* **時間複雜度**: $\mathcal{O}(N \cdot K^2)$
    * 這是一次標準的後序遍歷 (Bottom-Up DFS)。
    * 在每一個節點中，我們對其每一個子節點進行了狀態合併。每次合併需要執行雙重迴圈，耗時 $\mathcal{O}(K^2)$。
    * 由於每條邊 (Edge) 恰好對應一次子節點的合併，全樹共有 $N-1$ 條邊，因此整體的合併次數嚴格小於 $N$。
    * 總時間複雜度為 $\mathcal{O}(N \cdot K^2)$。
* **空間複雜度**: $\mathcal{O}(N \cdot K)$
    * 每個節點維護了大小為 $2 \times K$ 的狀態矩陣，消耗 $\mathcal{O}(N \cdot K)$ 的空間。
    * 系統 DFS 遞迴呼叫堆疊深度最大為 $\mathcal{O}(N)$。
    * 整體空間複雜度為 $\mathcal{O}(N \cdot K)$。