---
title: "LeetCode 33. Search in Rotated Sorted Array"
date: 2026-05-22T12:40:00+08:00
lastmod: 2026-05-22T12:40:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary-search"]
keywords: ["LC33", "Medium", "Search in Rotated Sorted Array", "二分搜尋", "旋轉排序陣列"]
description: "LeetCode 第 33 題：Search in Rotated Sorted Array。難度評分：Medium。探討如何在經過旋轉的遞增陣列中，利用局部有序性進行 O(log n) 的二分搜尋。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 33](https://leetcode.com/problems/search-in-rotated-sorted-array/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
有一個整數陣列 `nums`，該陣列原本是按遞增順序排列的，但在預先未知的某個點上進行了旋轉。
（例如，`[0,1,2,4,5,6,7]` 可能會變成 `[4,5,6,7,0,1,2]`）。

請你在這個陣列中搜尋 `target`，如果陣列中存在這個目標值，則回傳它的索引，否則回傳 `-1`。

你必須設計一個時間複雜度為 $\mathcal{O}(\log n)$ 的演算法來解決此問題。

### 範例
- **Input**: `nums = [4,5,6,7,0,1,2], target = 0`
- **Output**: `4`

- **Input**: `nums = [4,5,6,7,0,1,2], target = 3`
- **Output**: `-1`

- **Input**: `nums = [1], target = 0`
- **Output**: `-1`

### 限制條件
- $1 \le nums.length \le 5000$
- $-10^4 \le nums[i] \le 10^4$
- `nums` 中的每個值都是 **唯一** 的。
- `nums` 保證原本是遞增陣列，且可能經過旋轉。
- $-10^4 \le target \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-22：初次提交 (局部有序二分搜尋法)](#2026-05-22-初次提交)

---

## 💡 2026-05-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在一個旋轉過的有序陣列中，無論我們從哪裡切一刀（取得 `mid`），**左半部和右半部之中，必定至少有一個半部是嚴格遞增的**。這是本題最核心的數學特質。

**核心破局點：判斷局部有序區間**
我們不再盲目地直接判斷 `target` 與 `mid` 的大小，而是先確立哪一邊是「規矩的（有序的）」：
1. 若 `nums[mid] <= nums[r]`：這代表 **右半部是嚴格遞增的**。
   - 此時我們可以明確地檢查 `target` 是否落在這個規矩的區間內：`nums[mid] < target <= nums[r]`。
   - 如果是，代表目標在右邊，令 `l = mid + 1`；否則目標在左邊（或不存在），令 `r = mid - 1`。
2. 若 `nums[mid] > nums[r]`：這代表右半部有斷層，因此 **左半部必然是嚴格遞增的**。
   - 檢查 `target` 是否落在左半部的規矩區間內：`nums[l] <= target < nums[mid]`。
   - 如果是，目標在左邊，令 `r = mid - 1`；否則在右邊，令 `l = mid + 1`。

### 🛠️ 解題思路 (Approach)
1. **初始化雙指標**：令左邊界 `l = 0`，右邊界 `r = nums.size() - 1`。
2. **進入二分循環**：當 `l <= r` 時進行搜索。
3. **命中判定**：取得 `mid` 後，若 `nums[mid] == target`，直接回傳 `mid`。
4. **區間判斷與收縮**：
   - 先判斷哪一半部為有序。
   - 再判斷 `target` 是否落在該有序區間的上下界之內。
   - 依據結果縮減一半的搜尋範圍。
5. **未命中**：若迴圈結束仍未找到，回傳 `-1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(\log n)$。標準的二分搜尋，每次迭代穩定將搜索空間減半。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個指標變數，無額外空間開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        
        while (l <= r) {
            int mid = l + (r - l) / 2;
            
            // 找到目標，直接回傳
            if (nums[mid] == target) return mid;
            
            // 判斷右半部是否為嚴格遞增 (局部有序)
            if (nums[mid] <= nums[r]) {
                // 如果目標值落在右半部這個明確的範圍內
                if (target > nums[mid] && target <= nums[r]) {
                    l = mid + 1; // 往右縮限
                } else {
                    r = mid - 1; // 否則往左尋找
                }
            } 
            // 若右半部不是遞增，則左半部必定是嚴格遞增
            else {
                // 如果目標值落在左半部這個明確的範圍內
                if (target >= nums[l] && target < nums[mid]) {
                    r = mid - 1; // 往左縮限
                } else {
                    l = mid + 1; // 否則往右尋找
                }
            }
        }

        return -1;
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