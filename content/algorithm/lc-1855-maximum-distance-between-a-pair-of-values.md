---
title: "LeetCode 1855. Maximum Distance Between a Pair of Values"
date: 2026-04-19T15:13:21+08:00
lastmod: 2026-04-19T15:13:21+08:00
difficulty: 1514
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary-search", "two-pointers"]
keywords: ["LC1855", "1514", "Medium", "Maximum Distance", "Two Pointers", "Binary Search"]
description: "LeetCode 第 1855 題：Maximum Distance Between a Pair of Values。難度評分：1514。完整收錄兩種解法：利用反向迭代器進行 O(M log N) 的二分搜尋，以及利用數列單調性達成 O(M + N) 理論極限的雙指標 (Two Pointers) 演算法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1855](https://leetcode.com/problems/maximum-distance-between-a-pair-of-values/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1514)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search` $\cdot$ `Two Pointers`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個 **非遞增 (Non-increasing)** 的整數陣列 `nums1` 和 `nums2`（即陣列已由大到小排序）。
一對索引 $(i, j)$ 被稱為「有效配對 (Valid Pair)」，若滿足以下兩個條件：
1. $i \le j$
2. $nums1[i] \le nums2[j]$

這對索引的「距離 (Distance)」定義為 $j - i$。
請找出並回傳所有有效配對中的 **最大距離**。如果沒有任何有效配對，請回傳 `0`。

### 限制條件
- $1 \le nums1.length, nums2.length \le 10^5$
- $1 \le nums1[i], nums2[j] \le 10^5$
- `nums1` 和 `nums2` 皆為非遞增數列。
</details>

---

## 📝 歷次打卡與解法演進
這道題目給定了「已排序」的特性，因此非常適合使用進階的搜尋技巧。以下收錄兩種高階解法：
- **[解法一：二分搜尋 (Binary Search)](#解法一-二分搜尋-binary-search)**：利用 STL 反向迭代器解決遞減數列的查尋問題。
- **[解法二：雙指標 (Two Pointers)](#解法二-雙指標-two-pointers-最優解)**：利用數列單調性達成不回溯的線性時間掃描。

---

## 💡 解法一：二分搜尋 (Binary Search)

### 🎯 演算法分析
對於每一個 `nums1[i]`，我們希望在 `nums2` 中找到一個索引 $j$ ($j \ge i$)，使得 `nums2[j] >= nums1[i]` 且 $j$ 盡可能大。
由於 `nums2` 是遞減數列，我們無法直接使用 C++ 標準函式庫中預設給遞增數列使用的 `lower_bound`。
**巧妙轉換**：我們可以使用反向迭代器 (`rbegin()`, `rend()`)，將由右至左的掃描視為遞增數列，接著尋找第一個大於等於 `nums1[i]` 的元素。找到後，再透過索引偏移量轉換回原本的 0-based 索引。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxDistance(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size(), n = nums2.size();
        int mx = 0;
        
        for(int i = 0; i < m; ++i) {
            // 使用 rbegin() 和 rend() 將遞減陣列反轉視角，進行二分搜尋
            auto it = lower_bound(nums2.rbegin(), nums2.rend(), nums1[i]);
            
            // 計算原本的正向索引 k
            // it - nums2.rbegin() 得到的是距離尾端的偏移量
            int k = n - 1 - int(it - nums2.rbegin());
            
            // 確保滿足條件 i <= j
            if(k >= i) {
                mx = max(mx, k - i);
            }
        }

        return mx;
    }
};
```
- **時間複雜度**: $\mathcal{O}(M \log N)$。遍歷 `nums1` 耗時 $\mathcal{O}(M)$，每次在 `nums2` 中二分搜尋耗時 $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個變數。

---

## 💡 解法二：雙指標 (Two Pointers) - 最優解

### 🎯 演算法分析
仔細觀察陣列的**單調性 (Monotonicity)**：
1. 當 `nums1[i]` 能夠配對到最遠的 `nums2[j]` 時，我們記錄下距離。
2. 接著我們看下一個元素 `nums1[i+1]`。因為陣列是非遞增的，必定有 `nums1[i+1] <= nums1[i]`。
3. 既然 `nums1[i+1]` 變得更小了，它**必定也能夠配對到目前的 `nums2[j]`**，甚至有機會配對到更後面的 `nums2[j+1]`、`nums2[j+2]` 等等。
4. 這意味著指標 $j$ **永遠不需要回溯**，它只需要從上次停下來的地方繼續往右走即可！

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxDistance(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size(), n = nums2.size();
        int mx = 0;
        
        // 宣告指標 j 在外層，保持狀態不回溯
        int j = 0;
        
        for(int i = 0; i < m; ++i) {
            // j 從當前位置繼續向右探索
            for(; j < n; ++j) {
                if(nums1[i] <= nums2[j]) {
                    // 若滿足條件，更新最大距離
                    mx = max(mx, j - i);
                } else {
                    // 一旦 nums2[j] 比 nums1[i] 小，因為 nums2 遞減，後面的只會更小，直接中斷
                    break;
                }
            }
        }

        return mx;
    }
};
```
- **時間複雜度**: $\mathcal{O}(M + N)$。雖然有兩層迴圈，但指標 $i$ 和 $j$ 都只會各自從頭走到尾一次，絕不回頭。這達到了理論上的最佳時間極限。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個變數。

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