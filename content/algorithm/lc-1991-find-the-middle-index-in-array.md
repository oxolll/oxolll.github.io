---
title: "LeetCode 1991. Find the Middle Index in Array"
date: 2026-03-21T17:40:00+08:00
lastmod: 2026-03-21T17:40:00+08:00
difficulty: 1302
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix sum"]
keywords: ["LC1991", "1302", "Easy", "Middle Index", "Prefix Sum", "O(1) Space"]
description: "LeetCode 第 1991 題：Find the Middle Index in Array。難度評分：1302。探討如何利用前綴和與總和的數學關係，以 O(N) 時間複雜度與 O(1) 空間高效找出陣列的中間索引。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1991](https://leetcode.com/problems/find-the-middle-index-in-array/) *(註：本題與 LC 724 完全相同)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1302)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個以 0 為起始索引的整數陣列 `nums`，請找出**最左邊**的中間索引 (Middle Index)。

中間索引 `middleIndex` 的定義是：
該索引左側所有元素的總和，等於該索引右側所有元素的總和。
- 如果 `middleIndex == 0`，則左側總和視為 `0`。
- 如果 `middleIndex == nums.length - 1`，則右側總和視為 `0`。

請回傳滿足條件的最左邊 `middleIndex`。如果不存在這樣的中間索引，則回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 100$
- $-1000 \le nums[i] \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (O(1) 空間動態前綴和)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
尋找中間索引的問題，本質上是比對「左側前綴和」與「右側後綴和」是否相等。
若要達到最佳的時間複雜度與空間複雜度，我們不需要額外宣告兩個陣列來儲存前綴與後綴和。我們只需要先遍歷一次陣列計算出所有元素的「總和 (Total Sum)」。
在第二次遍歷時，維護一個變數 `pre` 代表當前的「左側總和」。當我們檢查到索引 `i` 時，只要將「總和」扣除當前元素 `nums[i]`，剩下的值就是精確的「右側總和」。直接比較兩者即可得出答案。

### 🛠️ 解題思路 (Approach)
1. **計算總和**：宣告變數 `sum`，遍歷整個 `nums` 陣列將所有元素加總。
2. **動態比對**：
   - 宣告 `pre = 0` 作為左側前綴和。
   - 由左至右再次遍歷 `nums` 陣列。
   - 每次迴圈開始時，先將 `sum` 減去 `nums[i]`。此時 `sum` 代表的就是索引 `i` 右側所有元素的總和。
   - 檢查 `pre == sum`。若相等，代表找到了中間索引，立即回傳 `i`。
   - 若不相等，將 `nums[i]` 加進 `pre` 中，供下一個索引使用。
3. **處理無解情況**：若整個迴圈結束後仍未找到符合條件的索引，則回傳 `-1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。共計遍歷陣列兩次。第一次計算總和，第二次尋找中間索引，時間開銷為線性。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了 `sum` 與 `pre` 兩個整數變數進行狀態維護，不依賴陣列長度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int findMiddleIndex(vector<int>& nums) {
        int n = nums.size();
        int sum = 0;
        
        // 1. 先計算整個陣列的總和
        for(int i = 0; i < n; ++i) {
            sum += nums[i];
        }

        int pre = 0; // 維護左側前綴和
        
        // 2. 動態檢查每一個索引
        for(int i = 0; i < n; ++i) {
            // 扣除當前元素後，sum 即為右側總和
            sum -= nums[i];
            
            // 若左側和等於右側和，回傳當前索引
            if(pre == sum) return i;
            
            // 將當前元素加入左側和，準備檢查下一個索引
            pre += nums[i];
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