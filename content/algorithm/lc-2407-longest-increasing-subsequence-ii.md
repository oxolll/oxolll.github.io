---
title: "LeetCode 2407. Longest Increasing Subsequence II"
date: 2026-04-30T20:40:00+08:00
lastmod: 2026-04-30T20:40:00+08:00
difficulty: 2280
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "segment-tree", "math"]
keywords: ["LC2407", "Hard", "2280", "LIS", "Longest Increasing Subsequence", "Segment Tree", "線段樹"]
description: "LeetCode 第 2407 題：Longest Increasing Subsequence II。難度評分：2280。探討當傳統 LIS 遇到相鄰元素差值限制時，如何利用以「值域」為底層的線段樹 (Segment Tree) 來優化 DP 狀態轉移，實現 O(N log M) 的極致效能。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2407](https://leetcode.com/problems/longest-increasing-subsequence-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2280)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Segment Tree`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums` 和一個整數 `k`。
請找出 `nums` 中最長遞增子序列 (Longest Increasing Subsequence, LIS) 的長度，且必須滿足以下額外條件：
對於子序列中任意兩個相鄰的元素 `seq[i]` 與 `seq[i+1]`，必須滿足：
- `seq[i] < seq[i+1]` (嚴格遞增)
- `seq[i+1] - seq[i] <= k` (差值不超過 k)

請回傳滿足條件的最長子序列的長度。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i], k \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-30：初次提交 (值域線段樹 + DP 狀態轉移優化)](#2026-04-30-初次提交)

---

## 💡 2026-04-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
傳統的 LIS 問題若要求 $O(N \log N)$ 的解法，通常會採用「二分搜尋 (Patience Sorting)」。然而，該演算法只能保證嚴格遞增，完全無法處理「差值不大於 $k$」這種局部限制條件。
若回到基礎的 DP 定義：$dp[i]$ 代表以 $nums[i]$ 為結尾的最長 LIS 長度。
狀態轉移方程式為：
$$dp[i] = 1 + \max_{j < i}(dp[j])$$
其中必須滿足 $nums[i] - k \le nums[j] \le nums[i] - 1$。

如果直接暴力往前找 $j$，時間複雜度會退化為 $\mathcal{O}(N^2)$ 必定 TLE。
**破局關鍵：將查詢維度從「索引」轉換為「值域」！**
我們建立一棵以「數值大小 (1 到 $10^5$)」為區間的**線段樹 (Segment Tree)**，線段樹維護的內容是「以該數值結尾的 LIS 最大長度」。
當我們遍歷到 $nums[i]$ 時：
1. **區間查詢**：在線段樹中查詢區間 `[max(1, nums[i] - k), nums[i] - 1]` 的最大值。這個最大值就是接在合法範圍內的最優解。
2. **單點更新**：算出 $dp[i]$ 後，將線段樹中位置為 $nums[i]$ 的值更新為 $dp[i]$。

*(註：這題無法使用 Binary Indexed Tree (BIT)，因為 BIT 只能查詢前綴極值，而本題的查詢區間有一個下限 $nums[i] - k$，這正是線段樹區間查詢的強項。)*

### 🛠️ 解題思路 (Approach)
1. **建立值域線段樹**：找出陣列中的最大值 $N$，宣告大小為 $4N+5$ 的線段樹，用來維護區間最大值 (`Range Max`)。
2. **走訪與轉移**：
   - 宣告 `dp` 陣列記錄每個位置的答案。
   - 走訪原陣列 `nums`，對於每個元素 $x$：
     - 利用線段樹查詢區間 `[max(1, x-k), x-1]` 內的最大長度。
     - 狀態轉移：`dp[i] = 1 + query(1, 1, N, max(1, x-k), x-1)`。
     - 將算出的 `dp[i]` 單點更新回線段樹的位置 $x$。
3. **尋找全域最大值**：最後掃描一次 `dp` 陣列（或是直接拿維護好的 `rt` 變數），回傳最大值。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log M)$。其中 $N$ 是陣列長度，$M$ 是陣列中的最大數值 (約 $10^5$)。走訪陣列耗時 $\mathcal{O}(N)$，每次向線段樹查詢與更新皆耗時 $\mathcal{O}(\log M)$。
- **空間複雜度**: $\mathcal{O}(M)$。線段樹需配置 $4 \times M$ 的陣列空間，加上長度為 $N$ 的 DP 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<long long> tree;
    int N;
    
    // 線段樹單點更新：將數值為 idx 的節點長度更新為 val，並維護區間最大值
    void update(int node, int l, int r, int idx, long long val) {
        if(l == r) {
            tree[node] = val; // 此處因為 LIS 只增不減，直接覆蓋即可
            return;
        }

        int mid = l + (r - l) / 2;
        if(idx <= mid) update(node << 1, l, mid, idx, val);
        else update((node << 1) | 1, mid + 1, r, idx, val);

        // Push Up: 維護區間最大值
        tree[node] = max(tree[node << 1], tree[(node << 1) | 1]);
    }

    // 線段樹區間查詢：取得區間 [L, R] 內的最大 LIS 長度
    long long query(int node, int l, int r, int L, int R) {
        // 安全邊界防護
        if(L > R || r < L || R < l) return 0;

        if(L <= l && r <= R) return tree[node];

        int mid = l + (r - l) / 2;
        long long left = query(node << 1, l, mid, L, R);
        long long right = query((node << 1) | 1, mid + 1, r, L, R);
        
        return max(left, right);
    }
    
    int lengthOfLIS(vector<int>& nums, int k) {
        // 以數值域作為線段樹的底層
        N = *max_element(nums.begin(), nums.end());
        tree = vector<long long>(4 * N + 5, 0);
        
        int n = nums.size();
        vector<int> dp(n, 0);

        for(int i = 0; i < n; ++i) {
            int x = nums[i];
            
            // 查詢符合條件的值域區間 [x-k, x-1] 內的最大長度
            // 注意下界不能小於 1
            dp[i] = 1 + query(1, 1, N, max(1, x - k), x - 1);
            
            // 將當前計算出的最長長度，更新到值域為 x 的位置
            update(1, 1, N, x, dp[i]);
        }

        // 找尋所有以不同元素結尾的最大 LIS 長度
        int rt = 0;
        for(int i = 0; i < n; ++i) {
            rt = max(rt, dp[i]);
        }
        return rt;
    }
};

/*
User Comments & Notes:
dp[i] := the longest subsequence with the last element nums[i]

dp[i] = max{ dp[j] + 1 if nums[j] + k <= nums[i] } j < i, nums[i] != nums[j]
        1 otherwise
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