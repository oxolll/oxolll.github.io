---
title: "LeetCode 2926. Maximum Balanced Subsequence Sum"
date: 2026-05-04T22:30:00+08:00
lastmod: 2026-05-04T22:30:00+08:00
difficulty: 2448
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic-programming", "segment-tree", "math"]
keywords: ["LC2926", "Hard", "Balanced Subsequence", "Segment Tree", "LIS", "數學移項"]
description: "LeetCode 第 2926 題：Maximum Balanced Subsequence Sum。難度評分：2448。探討如何透過數學移項將限制條件轉化為一維偏序，並利用離散化與值域線段樹將 O(N^2) 的動態規劃優化至 O(N log N)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2926](https://leetcode.com/problems/maximum-balanced-subsequence-sum/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2448)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Segment Tree` $\cdot$ `Binary Indexed Tree`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為 $n$ 的 0-indexed 整數陣列 `nums`。
一個長度為 $k$ 的子序列 $indices$ 被稱為 **平衡子序列 (Balanced Subsequence)**，如果對於所有 $0 \le j < k - 1$，都滿足：
$$nums[indices[j]] - nums[indices[j+1]] \le indices[j] - indices[j+1]$$

請回傳所有可能的平衡子序列中，**最大的元素總和**。
*(註：子序列至少必須包含一個元素)*。

### 限制條件
- $1 \le nums.length \le 10^5$
- $-10^9 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-04：初次提交 (數學移項轉換 + 值域線段樹)](#2026-05-04-初次提交)

---

## 💡 2026-05-04 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的限制條件看起來很複雜，但只要具備高階競技程式的數學敏銳度，就能看破它的本質。
原條件為（假設 $i < j$ 分別為子序列中的相鄰索引）：
$$nums[i] - nums[j] \le i - j$$
我們將帶有 $i$ 的項移到不等式左邊，帶有 $j$ 的項移到右邊：
$$nums[i] - i \le nums[j] - j$$

**破局核心：狀態重塑**
我們定義一個新的陣列 $A[i] = nums[i] - i$。
原問題瞬間降維：**在原陣列中尋找一個子序列，使得其對應的 $A[i]$ 是一個「非遞減序列 (Non-Decreasing Sequence)」，並使這個子序列的 $nums[i]$ 總和最大！**
這完美轉化為了一個「最大權重非遞減子序列 (MWIS)」問題，與 LIS 的解法如出一轍。

### 🛠️ 解題思路 (Approach)
1. **數值離散化 (Coordinate Compression)**：
   - 因為 $A[i] = nums[i] - i$ 的範圍會非常大（可達 $\pm 10^9$），無法直接作為陣列索引。
   - 我們將所有的 $A[i]$ 收集起來，排序並去重，將其映射為 $1 \sim N$ 的密集排名。
2. **值域線段樹優化 DP**：
   - 如果用傳統的雙層迴圈找合法的 $j < i$，時間複雜度是 $\mathcal{O}(N^2)$，必定 TLE。
   - 我們建構一棵大小為 $4N+5$ 的線段樹，每個節點儲存「以離散化排名為 $k$ 的 $A[i]$ 作為結尾時，最大的子序列總和」。
   - 當遍歷到 $nums[i]$ 時，求出其對應的 $A[i]$ 排名 $k$。
   - 向線段樹查詢 `[1, k]` 的最大歷史總和 `past_dp`。
   - 新的總和為 `past_dp + nums[i]`，並將其單點更新回線段樹的位置 $k$ 中（取 max 保留最優解）。
3. **全負數 Corner Case 處理**：
   - 如果所有的 $nums[i]$ 都是負數，因為子序列至少要有一個元素，答案應該是陣列中最大的負數。
   - 但線段樹的預設值是 `0`（代表不選），會錯誤地回傳 `0`。
   - 解決方案：隨時維護一個單一元素的最大值 `mx`，如果最後 `mx < 0`，代表全陣列皆為負，直接回傳 `mx` 即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。計算 $A[i]$、排序與去重耗時 $\mathcal{O}(N \log N)$。遍歷 $N$ 個元素並進行線段樹查詢與更新，每次 $\mathcal{O}(\log N)$，總計 $\mathcal{O}(N \log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。需要儲存離散化陣列 `sorted` 以及大小為 $4N$ 的線段樹 `tree`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<long long> tree;
    int N;
    
    // 由下而上維護區間最大值
    void pushUp(int node) {
        tree[node] = max(tree[node << 1], tree[node << 1 | 1]);
    }
    
    // 單點更新 (傳入 L=k, R=k)
    void upd(int node, int l, int r, int L, int R, long long val) {
        if(L > R) return;
        // 抵達目標點，與歷史最佳總和取 max
        if(L <= l && r <= R) {
            tree[node] = max(tree[node], val);
            return;
        }

        int mid = l + (r - l) / 2;
        if(L <= mid) upd(node << 1, l, mid, L, R, val);
        if(R > mid)  upd(node << 1 | 1, mid + 1, r, L, R, val);

        pushUp(node);
    }
    
    // 區間查詢：查詢離散化排名 [L, R] 內的歷史最大總和
    long long qry(int node, int l, int r, int L, int R) {
        if(L > R) return -1e15; // 避免負數和影響，回傳極小值
        if(L <= l && r <= R) return tree[node];

        int mid = l + (r - l) / 2;
        long long res = LLONG_MIN;
        if(L <= mid) res = max(res, qry(node << 1, l, mid, L, R));
        if(R > mid)  res = max(res, qry(node << 1 | 1, mid + 1, r, L, R));
        return res;
    }
    
    long long maxBalancedSubsequenceSum(vector<int>& nums) {
        int n = nums.size();
        
        // 1. 數學移項轉換：A[i] = nums[i] - i
        vector<long long> sorted;
        for(int i = 0; i < n; ++i) {
            sorted.push_back(nums[i] * 1LL - i);
        }
        
        // 2. 離散化 (Coordinate Compression)
        sort(sorted.begin(), sorted.end());
        sorted.erase(unique(sorted.begin(), sorted.end()), sorted.end());
        N = sorted.size();
        tree.assign(4 * N + 5, 0); // 預設值為 0，代表遇到負的前綴和可以隨時捨棄 (不選)
        
        long long mx = LLONG_MIN;
        
        // 3. 值域線段樹優化 DP
        for(int i = 0; i < n; ++i) {
            long long cur = nums[i] * 1LL - i;
            // 取得 A[i] 映射後的排名 (1-based)
            int k = lower_bound(sorted.begin(), sorted.end(), cur) - sorted.begin() + 1;
            
            // 查詢 <= 當前 A[i] 排名的歷史最大總和
            long long past_dp = qry(1, 1, N, 1, k);
            
            // 更新回線段樹 (加上當前的 nums[i])
            upd(1, 1, N, k, k, past_dp + nums[i]);
            
            // 隨時維護單點最大值 (為了防禦全負數的 Corner Case)
            mx = max(nums[i] * 1LL, mx);
        }

        // 4. 處理全負數陣列：
        // 如果 mx < 0，代表整條陣列都沒有正數，此時只能選最大的單一負數，否則回傳全域最大值 tree[1]
        return mx < 0 ? mx : tree[1];
    }
};

/*
User Comments & Math Derivation:
條件要求：
nums[i] - nums[j] <= i - j  (假設 i < j)
移項後：
nums[i] - i <= nums[j] - j

這表示我們只要選出一個子序列，其對應的 (nums[i] - i) 呈現非遞減 (<=) 的狀態即可。
因此轉換成了最大權重非遞減子序列問題！
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