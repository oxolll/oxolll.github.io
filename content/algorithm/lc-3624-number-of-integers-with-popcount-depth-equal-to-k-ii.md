---
title: "LeetCode 3624. Number of Integers With Popcount-Depth Equal to K II"
date: 2026-02-22T01:07:00+08:00
lastmod: 2026-02-22T01:07:00+08:00
difficulty: 2085
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Binary Indexed Tree (Fenwick Tree)", "Bit Manipulation"]
keywords: ["LC3624", "2085", "Hard", "BIT", "Fenwick Tree", "popcount", "__builtin_popcountll"]
description: "LeetCode 第 3624 題：Number of Integers With Popcount-Depth Equal to K II。難度評分：2085。探討如何利用多棵 Binary Indexed Tree 維護特定 popcount-depth 的區間統計，以及 C++ 中 __builtin_popcountll 的使用細節。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3624](https://leetcode.com/problems/number-of-integers-with-popcount-depth-equal-to-k-ii/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(2085)</span>   
> **核心主題**：`Binary Indexed Tree` $\cdot$ `Bit Manipulation` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。對於任何正整數 $x$，定義以下數列：
- $p_0 = x$
- $p_{i+1} = \text{popcount}(p_i)$ 對於所有 $i \ge 0$，其中 $\text{popcount}(y)$ 是 $y$ 二進位表示中 1 的個數。

這個數列最終會達到值 1。我們定義 $x$ 的 **popcount-depth** 為滿足 $p_d = 1$ 的最小非負整數 $d$。
例如，$x = 7$ (二進位 `"111"`) 的數列為：$7 \to 3 \to 2 \to 1$，因此 7 的 popcount-depth 為 3。

給定一個 2D 整數陣列 `queries`，每個查詢屬於以下兩種之一：
- `[1, l, r, k]`：計算在索引範圍 `[l, r]` 中，有多少個數字的 popcount-depth 等於 $k$。
- `[2, idx, val]`：將 `nums[idx]` 更新為 `val`。

請回傳一個整數陣列 `answer`，其中 `answer[i]` 是第 $i$ 個類型為 `[1, l, r, k]` 查詢的結果。

### 限制條件
- $1 \le n = nums.length \le 10^5$
- $1 \le nums[i] \le 10^{15}$
- $1 \le queries.length \le 10^5$
- $0 \le l \le r \le n - 1$
- $0 \le k \le 5$
- $0 \le idx \le n - 1$
- $1 \le val \le 10^{15}$
</details>

---

## 📝 歷次打卡與更新
- [2026-02-22：初次提交 (多棵 BIT 維護特定 Depth 與 `__builtin_popcountll` 踩坑紀錄)](#2026-02-22-初次提交)

---

## 💡 2026-02-22 初次提交

### 🎯 直覺 (Intuition)
第一眼看到這題，直覺是需要一個資料結構來處理「單點更新 (Point Update)」與「區間查詢 (Range Query)」，所以一定會用到 BIT (Fenwick Tree) 或線段樹。
但難點在於，查詢條件是特定的 `popcount-depth` $k$。如果我們仔細推演一下最大的數字 $10^{15}$（小於 $2^{60}$），即使是包含最多 1 的情況，其 popcount-depth 其實非常小！既然 $k$ 的種類不多，我們完全可以直接開 $k+1$ 棵 BIT，其中 `BIT[k]` 專門負責維護「popcount-depth 為 $k$ 的數字出現次數」。

### 🛠️ 解題思路 (Approach)
1. **推導最大的 popcount-depth**：
   - 題目給定的數值上限為 $10^{15} < 2^{60}$。
   - 假設我們考慮一個包含 63 個 1 的數字（例如二進位 `11...11`）：
     $63 (\text{二進位 } 111111) \to 6 \to 2 \to 1$ (Depth: 3)。
     或者 $62 (\text{二進位 } 11...100) \to 61 \to \dots$
   - 經過簡單推演，發現所有不大於 $10^{15}$ 的數字，其 popcount-depth 最大只有 4（題目限制 $k \le 5$ 也暗示了這點）。
2. **多棵 BIT 維護狀態**：
   - 我們宣告 5 棵 BIT (`BITs[0]` ~ `BITs[4]`)。
   - `BITs[d]` 維護原陣列中，`popcount-depth = d` 的元素分佈情況。
3. **處理查詢與更新**：
   - 遇到操作 `1` (查詢)：直接回傳 `BITs[k]` 中區間 `[l, r]` 的總和 `getSum(r, k) - getSum(l - 1, k)`。若查詢的 $k \ge 5$，則必定為 0。
   - 遇到操作 `2` (更新)：先計算原數字的 depth $d_{old}$，在 `BITs[d_{old}]` 中將該位置 `-1`。再計算新數字的 depth $d_{new}$，在 `BITs[d_{new}]` 中將該位置 `+1`，最後更新原陣列。
4. **⚠️ 避坑指南：`__builtin_popcountll`**：
   - 在計算 `popcount` 時，原本直覺使用 `__builtin_popcount`，卻在測資卡關。原因是輸入數字最大可達 $10^{15}$，超過了 32-bit `int` 的範圍。必須改用支援 `long long` 的 **`__builtin_popcountll`** 才能正確計算！

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}((N + Q) \log N)$。計算 `popcount-depth` 的時間可視為常數 $\mathcal{O}(1)$，每個元素的初始化、更新或查詢 BIT 都只需要 $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(N \times K)$，其中 $K = 5$。共需建立 5 棵大小為 $N+1$ 的 BIT。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int n;
    vector<vector<int>> BITs;
    
    vector<int> popcountDepth(vector<long long>& nums, vector<vector<long long>>& queries) {
        n = nums.size();
        
        // 開 5 棵 BIT，分別維護 popcount-depth 0 到 4 的元素
        BITs = vector<vector<int>>(5, vector<int>(n + 1, 0));
        for(int i = 0; i < n; ++i) {
            int d = cal_depth(nums[i]);
            update(i, 1, d);
        }
        
        vector<long long> Nums(nums.begin(), nums.end());
        vector<int> rt; 
        rt.reserve(queries.size());
        
        for(auto q : queries) {
            // Type 1: 區間查詢
            if(q[0] == 1) {
                // 最大 depth 不超過 4，若查詢 k >= 5 直接回傳 0
                if(q[3] < 5)
                    rt.push_back(getSum(q[2], q[3]) - getSum(q[1] - 1, q[3]));
                else
                    rt.push_back(0);
            }
            // Type 2: 單點更新
            else {
                // 1. 移除舊數值在對應 BIT 的影響
                int old_d = cal_depth(Nums[q[1]]);
                update(q[1], -1, old_d);
                
                // 2. 加入新數值至對應的 BIT
                int new_d = cal_depth(q[2]);
                update(q[1], 1, new_d);
                
                // 3. 更新備份陣列
                Nums[q[1]] = q[2];
            }
        }

        return rt;
    }
private:
    // 計算到達 1 所需的操作次數
    int cal_depth(long long x) {
        int d = 0;
        while(x != 1) {
            // ⚠️ 陷阱：數值高達 10^15，必須使用 popcountll 而非 popcount
            x = __builtin_popcountll(x);
            ++d;
        }
        return d;
    }
    
    void update(int idx, int val, int depth) {
        ++idx;
        while(idx <= n) {
            BITs[depth][idx] += val;
            idx += (idx & -idx);
        }
    }
    
    long long getSum(int idx, int depth) {
        ++idx;
        long long sum = 0;
        while(idx > 0) {
            sum += BITs[depth][idx];
            idx -= (idx & -idx);
        }
        return sum;
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