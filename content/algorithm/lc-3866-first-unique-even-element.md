---
title: "LeetCode 3866. First Unique Even Element"
date: 2026-03-15T14:35:00+08:00
lastmod: 2026-03-15T14:35:00+08:00
difficulty: 1209
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Hash Table", "Counting"]
keywords: ["LC3866", "1209", "Easy", "Unique Element", "Frequency Array"]
description: "LeetCode 第 3866 題：First Unique Even Element。難度評分：1209。探討如何利用數值範圍限制，以固定大小的陣列取代 Hash Map 來進行 O(N) 的高效頻率計數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3866](https://leetcode.com/problems/first-unique-even-element/) *(註：題號對應依據實際 LeetCode 題庫)* > **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1209)</span>    
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`，請找出並回傳陣列中**第一個**出現且**只出現過一次**的**偶數**元素。
如果陣列中不存在這樣的元素，則回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 100$ *(依據解法推斷之數值範圍)*
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (陣列頻率計數與兩次遍歷)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
題目要求尋找「第一個」滿足特定條件（偶數且唯一）的元素。
要判斷一個元素是否「唯一」，我們必須先知道整個陣列中每個元素的出現總數。因此，我們需要對陣列進行兩次掃描 (Two Passes)：
第一趟：統計每個數字的出現頻率。
第二趟：依照陣列原始順序（保證找到的是「第一個」），檢查該數字是否為偶數且頻率為 1。

由於題目數值範圍通常不大（例如最大到 100），與其使用底層邏輯複雜的 `std::unordered_map`，不如直接宣告一個大小為 101 的一維陣列來充當 Hash Table。這樣可以大幅降低常數時間的開銷。

### 🛠️ 解題思路 (Approach)
1. **頻率統計**：
   - 宣告一個大小為 101 的整數陣列 `cnt` 並初始化為 0。
   - 遍歷 `nums` 陣列，將對應數字的計數器加一 (`++cnt[num]`)。
2. **尋找目標**：
   - 再次從頭遍歷 `nums` 陣列（這確保了我們一旦找到答案，就絕對是原始陣列中的「第一個」）。
   - 檢查兩個條件：
     1. 是否為偶數：`num % 2 == 0`
     2. 是否唯一：`cnt[num] == 1`
   - 若同時滿足，立即回傳該數字。
3. **無解處理**：
   - 若遍歷完整個陣列都沒有找到符合條件的數字，則回傳 `-1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$，其中 $N$ 為陣列長度。我們總共遍歷了陣列兩次，且內部判斷皆為 $\mathcal{O}(1)$ 的常數時間操作。
- **空間複雜度**: $\mathcal{O}(U)$，其中 $U$ 為數值宇宙的大小（此題為 101）。由於這是一個很小的常數，空間複雜度可以視為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int firstUniqueEven(vector<int>& nums) {
        // 利用數值範圍不大的特性，直接開陣列當 Hash Table (極速優化)
        vector<int> cnt(101, 0);
        
        // 第一趟：統計所有數字的出現頻率
        for(auto num : nums) {
            ++cnt[num];
        }
            
        // 第二趟：依照原陣列順序尋找第一個符合條件的元素
        for(auto num : nums) {
            if(num % 2 == 0) { // 必須是偶數
                if(cnt[num] == 1) { // 必須只出現一次
                    return num;
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