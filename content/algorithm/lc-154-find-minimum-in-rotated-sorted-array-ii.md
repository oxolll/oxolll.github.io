---
title: "LeetCode 154. Find Minimum in Rotated Sorted Array II"
date: 2026-05-16T12:00:00+08:00
lastmod: 2026-05-16T12:00:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary-search", "divide-and-conquer"]
keywords: ["LC154", "Hard", "Rotated Sorted Array II", "二分搜尋", "重複元素", "分治法"]
description: "LeetCode 第 154 題：Find Minimum in Rotated Sorted Array II。難度評分：Hard。探討在包含重複元素的旋轉有序陣列中，如何利用二分搜尋與分治策略尋找全域最小值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 154](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search` $\cdot$ `Divide and Conquer`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
已知一個長度為 $n$ 的遞增陣列在預先未知的某個點上進行了旋轉。與第 153 題不同的是，本題的陣列中**可能包含重複的元素**。

請找出並回傳陣列中的 **最小元素**。

### 範例
- **Input**: `nums = [1,3,5]`
- **Output**: `1`

- **Input**: `nums = [2,2,2,0,1,2]`
- **Output**: `0`

### 限制條件
- $n = nums.length$
- $1 \le n \le 5000$
- $-5000 \le nums[i] \le 5000$
- 陣列中可能存在重複元素。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-16：初次提交 (分治二分搜尋法)](#2026-05-16-初次提交)

---

## 💡 2026-05-16 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題之所以難度被評為 Hard，是因為**重複元素的出現打破了二分搜尋的單調性**。

考慮以下兩個範例（皆滿足 `nums[l] == nums[mid] == nums[r]`）：
- 陣列 A: `[2, 2, 2, 0, 2]`，最小值 `0` 在**右半部**。
- 陣列 B: `[2, 0, 2, 2, 2]`，最小值 `0` 在**左半部**。

當 `nums[mid] == nums[r]` 時，僅憑當前資訊**完全無法判斷**斷層點在左側還是右側。

**核心破局點：分治雙向探索**
你採用了非常漂亮的「分治法 (Divide and Conquer)」策略：
1. **確定區間**：若 `nums[mid] > nums[r]`，代表左半邊是純遞增，斷層必定在右側，往右走。
2. **確定區間**：若 `nums[mid] < nums[r]`，代表右半邊是純遞增，斷層在左側（含 `mid`），往左走。
3. **模糊區間**：當 `nums[mid] == nums[r]` 時，不盲目猜測，**直接將問題拆解，同時遞迴求解左半部與右半部，最後取兩者的極小值**。

這個設計極其優雅，避開了傳統迭代解法中不斷調整指標邊界的繁瑣。

---

### 🛠️ 解題思路 (Approach)
1. **定義遞迴函式 `helper(l, r)`**：
   - 終端條件：若 `l >= r`，代表區間收縮到單一點，直接回傳 `nums[l]`。
2. **計算 `mid`**：利用 `l + (r-l)/2` 避免溢位。
3. **三向分支**：
   - `nums[mid] > nums[r]` $\to$ 遞迴搜尋 `[mid + 1, r]`。
   - `nums[mid] < nums[r]` $\to$ 遞迴搜尋 `[l, mid]`。
   - `nums[mid] == nums[r]` $\to$ 同時回傳 `min(helper(l, mid), helper(mid + 1, r))`。

---

### 📊 複雜度分析
- **時間複雜度**:
  - **平均/最佳**：$\mathcal{O}(\log n)$。當重複元素較少時，每次都能成功過濾一半的區間。
  - **最壞情況**：$\mathcal{O}(n)$。當陣列中所有元素幾乎都相同（例如 `[2, 2, 2, 2, 2]`）時，每次都會觸發雙向遞迴，演算法會退化為全圖掃描。這是處理帶有重複元素旋轉陣列不可避免的理論上限。
- **空間複雜度**: $\mathcal{O}(\log n)$。主要是遞迴呼叫時產生的系統棧（Stack）空間，最壞情況下為 $\mathcal{O}(n)$。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int helper(int l, int r, vector<int>& nums) {
        // 基底狀況：區間縮小到一個點時，該點即為答案
        if (l >= r) return nums[l];
        
        int mid = l + (r - l) / 2;

        // 分支 1：中間值大於右邊界，轉折點必定在右半部
        if (nums[mid] > nums[r]) {
            return helper(mid + 1, r, nums);
        }
        // 分支 2：中間值小於右邊界，轉折點在左半部（包含 mid 本身）
        else if (nums[mid] < nums[r]) {
            return helper(l, mid, nums);
        }
        
        // 分支 3 (Hard 核心)：當 nums[mid] == nums[r] 時，方向模糊
        // 同時向左右分治探索，並回傳兩側的最小值
        return min(helper(l, mid, nums), helper(mid + 1, r, nums));
    }

    int findMin(vector<int>& nums) {
        return helper(0, nums.size() - 1, nums);
    }
};
```

---

### 💡 延伸思維：面試常見的迭代 $O(1)$ 空間解法
如果在面試中被要求將空間優化至 $\mathcal{O}(1)$，可以將你的分治模糊分支改成消極收縮 `r--` 的迭代版：
```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (nums[mid] > nums[r])       l = mid + 1;
            else if (nums[mid] < nums[r])  r = mid;
            else                           r--; // 當模糊時，僅排除最右側重複項
        }
        return nums[l];
    }
};
```
*對比：你的分治寫法雖然多了棧空間，但在某些局部優化的測資中，其剪枝速度可能比單純 `r--` 還要快！*

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