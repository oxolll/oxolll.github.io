---
title: "LeetCode 3873. Maximum Points Activated with One Addition"
date: 2026-03-15T16:08:00+08:00
lastmod: 2026-03-15T16:08:00+08:00
difficulty: 2198
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Graph", "Union Find", "Greedy"]
keywords: ["LC3873", "2198", "Hard", "DSU", "Connected Components", "Maximum Activated"]
description: "LeetCode 第 3873 題：Maximum Points Activated with One Addition。難度評分：2198。探討如何利用併查集 (DSU) 找出共享座標的連通塊，並透過貪心策略計算新增一個點所能啟動的最大點數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3873](https://leetcode.com/problems/maximum-points-activated-with-one-addition/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(2198)</span>   
> **核心主題**：`Graph` $\cdot$ `Union Find (DSU)` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個二維平面上的點集合 `points`，其中 `points[i] = [x_i, y_i]`。
兩個點如果共享相同的 X 座標或相同的 Y 座標，則視為相連（啟動狀態可以互相傳遞）。
你現在可以選擇在平面上的**任意位置**新增**恰好一個點**。
請回傳在新增一個點之後，最多可以讓多少個點相互連通（啟動）。

### 限制條件
- $1 \le points.length \le 10^5$
- $points[i].length == 2$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (併查集 DSU 與連通塊貪心合併)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
將點的啟動傳遞視為圖論中的「連通塊 (Connected Components)」。只要兩個點的 X 或 Y 座標相同，它們就屬於同一個 Group。
當我們在平面上新增一個點 $(x_{new}, y_{new})$ 時，這個點的 $X$ 座標可以對齊某一個 Group，$Y$ 座標可以對齊另一個 Group。也就是說，**新增一個點最多可以將兩個不相連的 Group 合併為一個超大 Group**。
為了讓連通的點數最大化，我們只需要：
1. 把所有相連的點分好 Group。
2. 找出點數最多的「第一大 Group」與「第二大 Group」。
3. 把這兩個 Group 的數量相加，再加上新增的這 1 個點，即為全局最佳解！

*(註：不用擔心無法對齊的問題。若 Group A 的點與 Group B 的點的 X、Y 組合已經存在於圖中，那它們早就屬於同一個 Group 了。正因為它們是分離的，所以一定存在一個空白座標 $(X_A, Y_B)$ 可以讓我們放置新點。)*

### 🛠️ 解題思路 (Approach)
1. **初始化併查集 (DSU)**：建立 `parent` 陣列，長度為 `points.size()`，初始時每個點的 parent 都是自己。
2. **座標分群**：
   - 使用兩個 Hash Map `x` 與 `y`，將相同 X 座標或 Y 座標的「點的索引 (Index)」收集在一起。
3. **同座標合併 (Union)**：
   - 遍歷 Hash Map `x`，將共享同一個 X 座標的所有點進行 `Union`。
   - 遍歷 Hash Map `y`，將共享同一個 Y 座標的所有點進行 `Union`。
4. **統計連通塊大小**：
   - 宣告 `cnt` 陣列統計每個連通塊的點數。遍歷每個點，先執行 `find(i)` 確保路徑壓縮到最終的 Root，然後將 `cnt[Root]` 加 1。
5. **貪心結算**：
   - 將 `cnt` 陣列從小到大排序。
   - 陣列的最後一個元素 `cnt.back()` 即為最大 Group。
   - 如果存在的 Group 數量大於 1，則倒數第二個元素 `cnt[cnt.size()-2]` 即為次大 Group；否則為 0。
   - 回傳 `最大 + 次大 + 1` 即為答案。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。將座標加入 Map 與 DSU 的操作平均為 $\mathcal{O}(N \alpha(N))$。最後對 `cnt` 陣列進行排序花費 $\mathcal{O}(N \log N)$ 時間。
- **空間複雜度**: $\mathcal{O}(N)$。使用了 `parent` 陣列、`cnt` 陣列以及記錄索引的 Hash Map。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> parent;
    
    // 併查集：合併兩個集合
    void Union(int a, int b) {
        int x = find(a);
        int y = find(b);
        if(x <= y)
           parent[y] = x;
        else 
           parent[x] = y;
    }
    
    // 併查集：尋找根節點 (帶路徑壓縮)
    int find(int a) {
        if(a == parent[a]) return a;
        return parent[a] = find(parent[a]);
    }
    
    int maxActivated(vector<vector<int>>& points) {
        int n = points.size();
        parent = vector<int>(n, 0);
        for(int i = 0; i < n; ++i) {
             parent[i] = i;   
        }

        unordered_map<int, vector<int>> x;
        unordered_map<int, vector<int>> y;
        
        // 1. 將相同座標的點索引收集起來
        for(int i = 0; i < n; ++i) {
            x[points[i][0]].push_back(i);
            y[points[i][1]].push_back(i);
        }

        // 2. 合併相同 X 座標的點
        for(auto [k, idxs] : x) {
            int a = idxs[0];
            for(auto idx : idxs)
                if(find(a) != find(idx))
                    Union(a, idx);
        }

        // 3. 合併相同 Y 座標的點
        for(auto [k, idxs] : y) {
            int a = idxs[0];
            for(auto idx : idxs)
                if(find(a) != find(idx))
                    Union(a, idx);
        }

        // 4. 統計每個連通塊的點數量
        vector<int> cnt(n, 0);
        for(int i = 0; i < n; ++i) {
            find(i); // 確保壓縮到底
            ++cnt[parent[i]];
        }

        // 5. 排序找出最大的兩個連通塊
        sort(cnt.begin(), cnt.end());

        // 最大 Group + 次大 Group (若存在) + 新增的 1 個點
        return cnt.back() + (cnt.size() > 1 ? cnt[cnt.size() - 2] : 0) + 1;
    }
};
```