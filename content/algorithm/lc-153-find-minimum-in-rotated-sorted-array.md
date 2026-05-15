---
title: "LeetCode 153. Find Minimum in Rotated Sorted Array"
date: 2026-05-15T15:50:00+08:00
lastmod: 2026-05-15T15:50:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary-search"]
keywords: ["LC153", "Medium", "Rotated Sorted Array", "二分搜尋", "最小值"]
description: "LeetCode 第 153 題：Find Minimum in Rotated Sorted Array。探討如何在經過旋轉的遞增陣列中，利用二分搜尋在 O(log N) 時間內找到全域最小值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 153](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
已知一個長度為 $n$ 的遞增陣列在預先未知的某個點上進行了旋轉（例如，陣列 `[0,1,2,4,5,6,7]` 可能變為 `[4,5,6,7,0,1,2]`）。
請找出並回傳陣列中的 **最小元素**。

你必須設計一個時間複雜度為 $\mathcal{O}(\log n)$ 的演算法來解決此問題。

### 範例
- **Input**: `nums = [3,4,5,1,2]`
- **Output**: `1`
- **Explanation**: 原陣列為 `[1,2,3,4,5]`，旋轉了 3 次。

- **Input**: `nums = [4,5,6,7,0,1,2]`
- **Output**: `0`

### 限制條件
- $n = nums.length$
- $1 \le n \le 5000$
- $-5000 \le nums[i] \le 5000$
- `nums` 中的所有整數都是 **唯一** 的。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-15：初次提交 (標準二分搜尋法)](#2026-05-15-初次提交)

---

## 💡 2026-05-15 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在一個正常的遞增陣列中，第一個元素一定是最小的。但旋轉後，陣列被分成了兩個遞增的部分，而全域最小值就是這兩個部分的「斷層點」。

**核心破局點：與右邊界比較**
我們觀察 `nums[mid]` 與 `nums[r]` 的關係：
1. **`nums[mid] > nums[r]`**：
   這說明 `mid` 落在左邊那個較大的遞增段，而最小值（斷層點）必然在 `mid` 的右側。因此，我們收縮左邊界：`l = mid + 1`。
2. **`nums[mid] < nums[r]`**：
   這說明 `mid` 落在右邊那個較小的遞增段，或者整個區間本來就是有序的。此時 `mid` 有可能是最小值，或者最小值在 `mid` 的左側。因此，我們收縮右邊界：`r = mid`。

為什麼不跟 `nums[l]` 比較？因為當陣列沒有被旋轉時（或局部有序時），`nums[l] < nums[mid]` 無法區分「最小值在左側」還是「斷層在右側」，邏輯會變得複雜。

### 🛠️ 解題思路 (Approach)
1. **初始化**：`l = 0`, `r = n - 1`。
2. **二分循環**：當 `l < r` 時執行：
   - 計算中間索引 `mid`。
   - 若 `nums[mid] > nums[r]`，則 `l = mid + 1`。
   - 否則，`r = mid`。
3. **結束判定**：當 `l == r` 時，`l` 指向的位置即為全域最小值。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log n)$。標準的二分搜尋，每次過濾掉一半的搜尋空間。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用常數個變數。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int findMin(vector<int>& nums) { 
        int l = 0, r = nums.size() - 1;

        // 使用左閉右閉區間，結束條件為 l == r
        while (l < r) {
            int mid = l + (r - l) / 2;

            // 關鍵判斷：與最右側元素比較
            if (nums[mid] > nums[r]) {
                // 中間值大於右側值，說明斷層(最小值)在 mid 右方
                l = mid + 1;
            } else {
                // 中間值小於等於右側值，說明 mid 可能是最小值，或最小值在左方
                r = mid;
            }
        }

        // 最終 l 和 r 會收斂到同一個位置，即最小值所在
        return nums[l];
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