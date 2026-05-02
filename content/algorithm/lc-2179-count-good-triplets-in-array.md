---
title: "LeetCode 2179. Count Good Triplets in an Array"
date: 2026-02-20T18:22:00+08:00
lastmod: 2026-04-30T17:15:00+08:00
difficulty: 2272
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Binary Indexed Tree (Fenwick Tree)", "Segment Tree", "Divide and Conquer", "Math"]
keywords: ["LC2179", "2272", "Hard", "BIT", "Fenwick Tree", "Coordinate Transformation", "線段樹"]
description: "LeetCode 第 2179 題：Count Good Triplets in an Array。難度評分：2272。探討如何利用座標映射將雙陣列問題降維，並透過 BIT、線段樹或分治法枚舉中間節點求解遞增三元組。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2179](https://leetcode.com/problems/count-good-triplets-in-an-array/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(2272)</span>   
> **核心主題**：`Binary Indexed Tree` $\cdot$ `Segment Tree` $\cdot$ `Divide and Conquer` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個長度皆為 $n$ 的 0-indexed 陣列 `nums1` 和 `nums2`，它們都是 `[0, 1, ..., n - 1]` 的一個排列 (Permutation)。
一個 **好三元組 (Good Triplet)** 定義為一組 3 個相異的值 $(x, y, z)$，這三個值在 `nums1` 和 `nums2` 中出現的順序皆為由左至右遞增。
換句話說，如果 $pos1_v$ 是值 $v$ 在 `nums1` 的索引，$pos2_v$ 是值 $v$ 在 `nums2` 的索引，則必須滿足：
- $pos1_x < pos1_y < pos1_z$
- $pos2_x < pos2_y < pos2_z$

請回傳好三元組的總數。

### 範例
- **Input**: `nums1 = [2,0,1,3], nums2 = [0,1,2,3]` → **Output**: `1`
- **Explanation**: 
  在 `nums1` 中符合位置遞增的三元組有 (2,0,1), (2,0,3), (2,1,3), (0,1,3)。
  其中只有 (0,1,3) 在 `nums2` 中也是位置遞增的。故答案為 1。

### 限制條件
- $n == nums1.length == nums2.length$
- $3 \le n \le 10^5$
- `nums1` 和 `nums2` 皆為 `[0, 1, ..., n - 1]` 的排列。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-30：更新提交 (線段樹 Segment Tree 反向映射與推導優化)](#2026-04-30-更新提交-線段樹)
- [2026-02-20：初次提交 (雙解法：BIT 座標映射與分治計數)](#2026-02-20-初次提交-bit--分治)

---

## 💡 2026-04-30 更新提交 (線段樹)

### 🎯 演算法分析 (Algorithm Analysis)
處理這類「在兩個排列中同時尋找相對順序」的問題，核心都是**座標映射轉換 (Coordinate Mapping)**。
在這次的解法中，我們採用了「反向映射」的思維：
把 `nums2` 當成基準，記錄每個數字在 `nums2` 中的位置 (`mp`)。接著，把這個新位置套用到 `nums1` 上。這樣一來，我們就只需要在轉換後的 `nums1` 中，尋找有多少個 **嚴格遞增的三元組** 即可！

### 🛠️ 線段樹狀態推導 (Segment Tree)
我們一樣採用「枚舉中間元素」的策略：對於每一個 $nums1[i]$，答案是 `(左邊比它小的數量) * (右邊比它大的數量)`。
利用線段樹作為動態頻率陣列，可以精準優化數學推導：
1. `left` (左邊比它小的數量)：直接查詢線段樹區間 `[1, nums1[i] - 1]`。
2. 計算右邊比它大的數量：
   - 全局比 $nums1[i]$ 大的總數是：$n - nums1[i]$。
   - 已經出現在左邊，且比 $nums1[i]$ 大的數量是：`tree[1] - left - 1` (其中 `tree[1]` 是目前插入的總數量，扣除掉比它小的 `left`，再扣掉自己 `1`)。
   - 因此 `right` = `(全局較大總數) - (左邊已出現的較大總數)`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。建立映射為 $\mathcal{O}(N)$，遍歷與線段樹更新查詢皆為 $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。線段樹需要 $4N$ 的空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<long long> tree;
    int N;

    // 單點更新：將數值 idx 加入線段樹
    void update(int node, int l, int r, int idx, long long val) {
        if(l == r) {
            tree[node] = val; // 此題因為是排列且唯一，+= 或 = 皆可
            return;
        }

        int mid = l + (r - l) / 2;
        if(idx <= mid) update(node << 1, l, mid, idx, val);
        else update((node << 1) | 1, mid + 1, r, idx, val);

        tree[node] = tree[node << 1] + tree[(node << 1) | 1];
    }

    // 區間查詢：取得區間 [L, R] 的出現次數
    long long query(int node, int l, int r, int L, int R) {
        // 安全邊界防護
        if(L > R || r < L || R < l) return 0;

        if(L <= l && r <= R) return tree[node];

        int mid = l + (r - l) / 2;
        long long left = query(node << 1, l, mid, L, R);
        long long right = query((node << 1) | 1, mid + 1, r, L, R);

        return left + right;
    }

    long long goodTriplets(vector<int>& nums1, vector<int>& nums2) {
        int n = nums2.size();
        
        // 1. 建立 nums2 的數值到索引的映射
        vector<int> mp(n, -1);
        for(int i = 0; i < n; ++i) {
            mp[nums2[i]] = i;
        }

        N = n;
        tree = vector<long long>(N * 4 + 5, 0);

        // 2. 將映射套用到 nums1 上，並轉為 1-indexed 配合線段樹
        for(int i = 0; i < n; ++i) {
            nums1[i] = mp[nums1[i]] + 1;
        }

        long long rt = 0;
        
        // 3. 枚舉中間元素
        for(int i = 0; i < n; ++i) {
            // 將自己加入線段樹
            update(1, 1, N, nums1[i], 1);

            // 計算左邊比自己小的數量
            long long left = query(1, 1, N, 1, nums1[i] - 1);
            if(left == 0) continue; // 剪枝：左邊沒有小的，無法組成三元組

            // 計算右邊比自己大的數量
            // right = (全局較大總數) - (左側較大總數)
            long long right = (n - nums1[i]) - (tree[1] - left - 1);
            
            rt += left * right;
        }

        return rt;
    }
};
```

---

## 💡 2026-02-20 初次提交 (BIT / 分治)

### 🎯 直覺 (Intuition)
要在兩個陣列中同時找相對順序，大腦很容易打結。
**破局關鍵在於「相對座標轉換」**：既然 `nums1` 和 `nums2` 都是排列組合，我們可以直接把 `nums1` 當成「標準順序」。
假設把 `nums1` 中的數字重新命名為 `1, 2, 3... n`，然後將這個新名字套用到 `nums2` 上得到陣列 `arr`。那麼原問題「在兩個陣列中順序皆遞增」，就完美降維成了：**「在陣列 `arr` 中，尋找有多少個嚴格遞增的三元組 $(arr[i] < arr[j] < arr[k])$」**！

### 🛠️ 解題思路 (Approach)

#### 核心策略：枚舉中間元素 (Fix the middle)
要找三個遞增的數字，最有效率的方法是「遍歷中間的數字 $arr[i]$」，然後找出：
1. **左邊比它小的數量 ($L$)**
2. **右邊比它大的數量 ($R$)**
對於這個固定的中間數 $arr[i]$，能組成的三元組數量就是 $L \times R$。

#### 解法一：Binary Indexed Tree (BIT)
- 透過 BIT，我們邊掃描陣列邊維護已經出現過的數字。
- 當我們走到 $arr[i]$ 時：
  - `left_smaller` = 透過 BIT 查詢區間 `[1, arr[i] - 1]` 的總和。
  - `left_larger` = 左側已處理的總數 $i$ 扣掉 `left_smaller`。
  - `right_larger` = 陣列中所有比 $arr[i]$ 大的總數 (`n - arr[i]`)，扣掉剛才算出的 `left_larger`。
- 這個數學推導完美避開了「需要兩棵樹分別從左掃和從右掃」的麻煩，只需一棵 BIT 就能邊走邊算！

#### 解法二：分治法 (Divide & Conquer)
- 利用類似 Merge Sort 找逆序對的思維，計算出每個元素右邊有幾個比它小的元素 `cnt[i]`。
- 透過數學推導：
  - 左邊比它小的數量 = 總共比它小的數量 - 右邊比它小的數量 = `nums[i] - cnt[i]`。
  - 右邊比它大的數量 = 右側總元素數量 - 右邊比它小的數量 = `(n - 1 - i) - cnt[i]`。
- 將兩者相乘即為該元素的貢獻值。

### 📊 複雜度分析
- **時間複雜度**: 
  - BIT 解法：$\mathcal{O}(N \log N)$，每次遍歷查詢與更新 BIT。
  - D&C 解法：$\mathcal{O}(N \log^2 N)$，若在 Merge 過程使用 `lower_bound` 會有額外的 $\log$ 成本。
- **空間複雜度**: $\mathcal{O}(N)$，需要額外的陣列來儲存映射關係與 BIT/輔助陣列。

### 💻 程式碼實作 (C++)

#### 方法一：BIT (優化數學推導版)
將原本冗長的算式拆解為語意清晰的變數，大幅提升可讀性。
```cpp
class Solution {
public:
    vector<int> BIT;
    int n;
    
    long long goodTriplets(vector<int>& nums1, vector<int>& nums2) {
        n = nums1.size();
        
        // 1. 建立座標轉換映射 (用 vector 取代 map，O(1) 查詢極大加速)
        vector<int> pos(n, 0);
        for(int i = 0; i < n; ++i) {
            pos[nums1[i]] = i + 1; // 轉為 1-based index 以配合 BIT
        }
            
        // 2. 將 nums2 轉換為基準陣列 arr
        vector<int> arr(n);
        for(int i = 0; i < n; ++i) {
            arr[i] = pos[nums2[i]];
        }
        
        BIT.assign(n + 1, 0);
        long long ans = 0;
        
        // 3. 枚舉中間元素，計算 L * R
        for(int i = 0; i < n; ++i) {
            // 左側比 arr[i] 小的數量 (透過 BIT 查詢)
            long long left_smaller = getSum(arr[i] - 1);
            
            // 左側比 arr[i] 大的數量 = 左側總共已出現的數量 (i) - 左側比較小的數量
            long long left_larger = i - left_smaller;
            
            // 全局比 arr[i] 大的總數量
            long long total_larger = n - arr[i];
            
            // 右側比 arr[i] 大的數量 = 全局大的數量 - 左側已經出現的大的數量
            long long right_larger = total_larger - left_larger;
            
            // 累加貢獻
            ans += left_smaller * right_larger;
            
            // 將自己加入 BIT
            update(arr[i], 1);
        }

        return ans;
    }
    
private:
    long long getSum(int index) {
        long long sum = 0;
        for (; index > 0; index -= (index & -index)) {
            sum += BIT[index];
        }
        return sum;
    }
    
    void update(int index, int val) {
        for (; index <= n; index += (index & -index)) {
            BIT[index] += val;
        }
    }
};
```

<details>
<summary><b>點擊展開：方法二 (Divide and Conquer 分治法)</b></summary>

```cpp
class Solution {
public:
    long long goodTriplets(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();

        // 座標映射轉換
        vector<int> b2a(n, 0);
        for(int i = 0; i < n; ++i) b2a[nums1[i]] = i;

        vector<int> nums(n, 0);
        for(int i = 0; i < n; ++i) nums[i] = b2a[nums2[i]];

        // 計算每個元素右側有幾個比它小的元素
        auto cnt = countSmallerAfter(nums);

        long long rt = 0;
        for(int i = 0; i < n; ++i) {
            // nums[i] - cnt[i] : 左邊比它小的數量
            // (n - i - 1) - cnt[i] : 右邊比它大的數量
            rt += 1LL * (nums[i] - cnt[i]) * (n - i - 1 - cnt[i]);
        }
        return rt;
    }
private:
    vector<int> countSmallerAfter(vector<int>& nums) {
        int n = nums.size();
        vector<int> sorted(nums.begin(), nums.end());
        vector<int> cnt(n, 0);
        solve(nums, cnt, sorted, 0, n - 1);
        return cnt;
    }
    
    void solve(vector<int>& nums, vector<int>& cnt, vector<int>& sorted, int l, int r) {
        if(l >= r) return;

        int mid = l + (r - l) / 2;
        solve(nums, cnt, sorted, l, mid);
        solve(nums, cnt, sorted, mid + 1, r);

        // 使用二分搜計算右側比較小的數量 (Time: O(N log^2 N))
        for(int i = l; i <= mid; ++i) {
            auto it = lower_bound(sorted.begin() + mid + 1, sorted.begin() + r + 1, nums[i]);
            cnt[i] += (it - (sorted.begin() + mid + 1));
        }

        // 保持區間有序以便上一層二分搜
        inplace_merge(sorted.begin() + l, sorted.begin() + mid + 1, sorted.begin() + r + 1);
    }
};
```
</details>

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