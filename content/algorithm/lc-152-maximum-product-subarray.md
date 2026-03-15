---
title: "LeetCode 152. Maximum Product Subarray"
date: 2026-03-16T01:28:00+08:00
lastmod: 2026-03-16T01:28:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Dynamic Programming"]
keywords: ["LC152", "N/A", "Medium", "Maximum Product", "Kadane's Algorithm", "DP"]
description: "LeetCode 第 152 題：Maximum Product Subarray。難度評分：N/A (經典面試題)。探討如何透過動態規劃同時維護局部最大值與局部最小值，以 O(N) 時間複雜度解決因為「負負得正」導致的乘積最大化問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 152](https://leetcode.com/problems/maximum-product-subarray/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>    
> **核心主題**：`Array` $\cdot$ `Dynamic Programming`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`，請找出一個具有最大乘積的非空連續子陣列（子陣列最少包含一個數字），並回傳該最大乘積。

測試案例保證答案能夠被儲存在 32-bit 整數中。

### 範例
- **Input**: `nums = [2,3,-2,4]`
  **Output**: `6`
  **Explanation**: 子陣列 `[2,3]` 有最大的乘積 6。

- **Input**: `nums = [-2,0,-1]`
  **Output**: `0`
  **Explanation**: 結果不能是 2，因為 `[-2,-1]` 不是一個連續子陣列。

### 限制條件
- $1 \le nums.length \le 2 \times 10^4$
- $-10 \le nums[i] \le 10$
- 任何前綴或後綴的乘積保證適合 32-bit 整數。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-16：初次提交 (雙狀態 DP 維護最大/最小乘積)](#2026-03-16-初次提交)

---

## 💡 2026-03-16 初次提交

### 🎯 直覺 (Intuition)
這題與「最大子陣列和 (Maximum Subarray Sum)」非常相似，但乘法多了一個致命的變數：**負數**。
如果我們只追蹤「目前為止的最大乘積」，當我們遇到一個負數時，這個最大乘積瞬間就會變成極小的負數。但如果陣列後面還有一個負數呢？原本那個極小的負數一旦乘上後面的負數，就會「負負得正」，一躍成為新的全局最大值！
因此，為了解決這個翻轉效應，我們在遍歷陣列時，必須**同時記錄「包含當前數字的最大乘積」與「包含當前數字的最小乘積」**。

### 🛠️ 解題思路 (Approach)
1. **狀態定義**：
   - `mx`：代表以當前元素結尾的連續子陣列中，能創造出的**最大乘積**。
   - `mm`：代表以當前元素結尾的連續子陣列中，能創造出的**最小乘積**（用來儲備負負得正的潛力）。
   - `MX`：全局最大乘積，初始化為 `INT_MIN`。
2. **狀態轉移**：
   對於每一個元素 `nums[i]`，新的局部最大/最小值可能來自三種情況：
   - `nums[i]` 本身自立門戶（前面的乘積太爛或是遇到 0，不如從現在重新開始）。
   - `mx * nums[i]`（正數延續正數，或負數延續負數）。
   - `mm * nums[i]`（依賴負數翻轉：原本的極小負數乘上現在的負數）。
3. **滾動更新**：
   - 計算新的最大值 `nmx = max({nums[i], mx * nums[i], mm * nums[i]})`。
   - 計算新的最小值 `nmm = min({nums[i], mm * nums[i], mx * nums[i]})`。
   - 更新全局最大值 `MX = max(MX, nmx)`。
   - 將 `mx` 和 `mm` 更新為這一輪算出的 `nmx` 和 `nmm`，繼續往下走。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需對陣列進行一次線性掃描。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了少數幾個變數 (`mx`, `mm`, `MX`, `nmx`, `nmm`) 進行狀態的滾動轉移，不需要額外的陣列空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int mx = 1;
        int mm = 1;
        int MX = INT_MIN;
        
        for(int i = 0; i < nums.size(); ++i) {
            // 同時考慮三種可能：從自己重新開始、最大值乘自己、最小值乘自己(負負得正)
            int nmx = max(max(nums[i], mx * nums[i]), mm * nums[i]);
            int nmm = min(min(nums[i], mm * nums[i]), mx * nums[i]);
            
            // 更新全局最大值
            MX = max(max(MX, nmx), nmm);
            
            // 滾動更新局部狀態
            mx = max(nmx, nmm);
            mm = min(nmx, nmm);
        }

        return MX;
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