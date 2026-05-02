---
title: "LeetCode 673. Number of Longest Increasing Subsequence"
date: 2026-05-02T13:00:00+08:00
lastmod: 2026-05-02T13:00:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "segment-tree", "coordinate-compression"]
keywords: ["LC673", "Medium", "Number of LIS", "Segment Tree", "離散化", "線段樹"]
description: "LeetCode 第 673 題：Number of Longest Increasing Subsequence。探討如何突破傳統 O(N^2) 的雙 DP 陣列限制，利用「離散化」與「線段樹 (Segment Tree)」維護 (長度, 組合數) 的 Pair 結構，實現 O(N log N) 的極致優化。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 673](https://leetcode.com/problems/number-of-longest-increasing-subsequence/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Segment Tree` $\cdot$ `Binary Indexed Tree`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個未排序的整數陣列 `nums`，請回傳**最長遞增子序列 (Longest Increasing Subsequence, LIS) 的總數量**。
請注意：這個子序列必須是「嚴格」遞增的。

### 範例
- **Input**: `[1, 3, 5, 4, 7]`
- **Output**: `2`
- **Explanation**: 兩個最長遞增子序列分別是 `[1, 3, 4, 7]` 和 `[1, 3, 5, 7]`。

### 限制條件
- $1 \le nums.length \le 2000$
- $-10^6 \le nums[i] \le 10^6$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-02：初次提交 (離散化 + 線段樹維護 Pair 狀態)](#2026-05-02-初次提交)

---

## 💡 2026-05-02 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的傳統解法是使用 $\mathcal{O}(N^2)$ 的動態規劃，維護兩個陣列：`len[i]` (以 $nums[i]$ 結尾的 LIS 長度) 與 `cnt[i]` (對應的組合數)。
但我們可以直接將這題「降維打擊」，**將時間複雜度優化至 $\mathcal{O}(N \log N)$**！
**核心概念：值域線段樹 + Pair 狀態維護**
我們可以建立一棵以「數值大小」為底層區間的線段樹。由於 $nums[i]$ 的範圍包含負數且極大 ($[-10^6, 10^6]$)，我們必須先進行**離散化 (Coordinate Compression)**，將所有出現過的數字映射為 $1 \sim N$ 的密集排名。

線段樹的每個節點不只儲存單一數值，而是儲存一個 `pair<int, int>`，分別代表 `{該區間的最大 LIS 長度, 達成該長度的組合數}`。

### 🛠️ 解題思路 (Approach)
1. **離散化 (Coordinate Compression)**：
   - 複製 `nums` 到 `sorted`，並進行排序 (`sort`) 與去重 (`unique`)。
   - 記錄不重複元素的總數 $N$。
2. **線段樹設計 (Segment Tree)**：
   - 宣告大小為 $4N+5$ 的線段樹，節點型態為 `pair<int, int>`，初始值為 `{0, 0}`。
   - **Merge 邏輯**：當合併兩個子節點 $n1, n2$ 時：
     - 若 $n1.len > n2.len$，回傳 $n1$。
     - 若 $n1.len < n2.len$，回傳 $n2$。
     - 若 $n1.len == n2.len$，長度不變，組合數相加：回傳 `{n1.len, n1.cnt + n2.cnt}`。
3. **走訪與狀態轉移**：
   - 走訪原陣列 `nums[i]`，透過 `lower_bound` 找出其離散化後的排名 `cur` (1-based)。
   - **查詢 (Query)**：向線段樹查詢區間 `[1, cur - 1]` 的狀態，即找出所有比當前數字小的值域中，最大的 LIS 狀態 `p`。
   - **轉移**：
     - 如果 `p.first == 0`，代表左邊沒有比它小的數字，它自己就是一個長度為 $1$，組合數為 $1$ 的序列 `{1, 1}`。
     - 否則，將查詢到的最大長度 $+1$，組合數維持不變 (`p.first += 1`)。
   - **更新 (Update)**：將計算好的狀態 `p` 單點更新回線段樹的位置 `cur`。*(註：更新時也要呼叫 `merge`，因為同一個數值可能出現在不同位置，會有新的組合數疊加)*。
4. **回傳結果**：迴圈結束後，線段樹的根節點 `tree[1]` 包含了全域 `[1, N]` 的最大 LIS 狀態，直接回傳 `tree[1].second` 即可！

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。
  - 離散化：排序與去重耗時 $\mathcal{O}(N \log N)$。
  - 線段樹轉移：掃描長度為 $N$ 的陣列，每次查詢與更新耗時 $\mathcal{O}(\log N)$。
  - 整體時間優於傳統的 $\mathcal{O}(N^2)$ DP。
- **空間複雜度**: $\mathcal{O}(N)$。需要離散化陣列與長度為 $4N$ 的 `pair` 線段樹。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 線段樹節點儲存 {最大 LIS 長度, 達成該長度的組合數量}
    vector<pair<int, int>> tree; 
    int N;

    // 自訂的狀態合併邏輯
    pair<int, int> merge(pair<int, int>& n1, pair<int, int>& n2) {
        if(n1.first > n2.first) return n1;
        if(n1.first < n2.first) return n2;
        // 防呆：若長度皆為 0 (未初始化)，避免誤算
        if(n1.first == 0) return {1, 1}; 
        
        // 若最大長度相同，則將組合數量相加
        return {n1.first, n1.second + n2.second};
    }

    void update(int node, int l, int r, int idx, pair<int, int>& val) {
        if(l == r) {
            // 注意：同一個數字可能出現在不同索引，必須將新的組合數與舊的 merge
            tree[node] = merge(tree[node], val);
            return;
        }

        int mid = l + (r - l) / 2;
        if(idx <= mid) update(node << 1, l, mid, idx, val);
        else update((node << 1) | 1, mid + 1, r, idx, val);

        // Push Up
        tree[node] = merge(tree[node << 1], tree[(node << 1) | 1]);
    }

    pair<int, int> query(int node, int l, int r, int L, int R) {
        if(L > R || R < l || L > r) return {0, 0};

        if(L <= l && r <= R) return tree[node];

        int mid = l + (r - l) / 2;

        pair<int, int> left = query(node << 1, l, mid, L, R);
        pair<int, int> right = query((node << 1) | 1, mid + 1, r, L, R);

        // 手動 inline 處理 query 階段的合併 (避免誤觸 merge 函式中的防呆機制)
        if(left.first == right.first) return {left.first, left.second + right.second};
        return (left.first > right.first ? left : right);
    }
    
    int findNumberOfLIS(vector<int>& nums) {
        // 1. 離散化 (Coordinate Compression)
        vector<long long> sorted(nums.begin(), nums.end());
        sort(sorted.begin(), sorted.end());
        sorted.erase(unique(sorted.begin(), sorted.end()), sorted.end());

        N = sorted.size();
        tree = vector<pair<int, int>>(4 * N + 5, {0, 0});

        // 2. 走訪陣列，維護值域線段樹
        for(int i = 0; i < nums.size(); ++i) {
            // 透過二分搜取得 1-based 的排名
            int cur = lower_bound(sorted.begin(), sorted.end(), nums[i]) - sorted.begin() + 1;
            
            // 查詢左側 (嚴格小於當前數字) 的最佳 LIS 狀態
            auto p = query(1, 1, N, 1, cur - 1);
            
            if(p.first == 0) {
                // 左邊沒有數字比它小，自己成為長度為 1 的起點
                p = {1, 1};
            } else {
                // 將最大長度 + 1，組合數繼承
                p.first += 1;
            }
            
            // 將計算好的狀態更新回線段樹
            update(1, 1, N, cur, p);
        }

        // 3. 根節點 tree[1] 即為全域 [1, N] 的最佳狀態
        return tree[1].second;
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