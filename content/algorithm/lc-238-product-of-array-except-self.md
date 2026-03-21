---
title: "LeetCode 238. Product of Array Except Self"
date: 2026-03-16T10:15:00+08:00
lastmod: 2026-03-21T18:10:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix sum"]
keywords: ["LC238", "Medium", "N/A", "Product Except Self", "Prefix Product", "O(1) Space"]
description: "LeetCode 第 238 題：Product of Array Except Self。難度評分：Medium (經典面試題)。探討如何在不使用除法的情況下，利用前綴與後綴乘積以 O(N) 時間複雜度求解，並妥善處理陣列中的零。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 238](https://leetcode.com/problems/product-of-array-except-self/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`，請回傳一個陣列 `answer`，使得 `answer[i]` 等於 `nums` 中除了 `nums[i]` 之外其餘所有元素的乘積。

題目保證 `nums` 的任何前綴或後綴的乘積都能夠放入 32-bit 整數中。
**進階要求**：你必須在 $\mathcal{O}(N)$ 的時間複雜度內寫出一個**不使用除法**的演算法。

### 範例
- **Input**: `nums = [1,2,3,4]`
  **Output**: `[24,12,8,6]`

- **Input**: `nums = [-1,1,0,-3,3]`
  **Output**: `[0,0,9,0,0]`

### 限制條件
- $2 \le nums.length \le 10^5$
- $-30 \le nums[i] \le 30$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：新增標準前綴與後綴乘積陣列相乘法](#2026-03-21-標準前後綴乘積法)
- [2026-03-16：初次提交 (跳躍式前綴後綴乘積與零計數法)](#2026-03-16-初次提交)

---

## 💡 2026-03-21 標準前後綴乘積法

### 🎯 直覺 (Intuition)
這是處理本題最核心、最不容易出錯的標準思維。「除了自己以外的乘積」可以完美拆解為兩個部分：
1. 自己**左側**所有數字的乘積（前綴乘積）。
2. 自己**右側**所有數字的乘積（後綴乘積）。
如果我們預先算出這兩個陣列，那麼對於任何一個索引 `i`，答案就是 `left_p[i-1] * right_p[i+1]`。這種方法完全不需要特別去計算陣列裡有幾個 `0`，因為如果在 `i` 之外有 `0`，前後綴乘積自然會把 `0` 涵蓋進去並傳遞下去。

### 🛠️ 解題思路 (Approach)
1. **建立前後綴陣列**：
   - 宣告 `left_p` 陣列，`left_p[i]` 記錄從 `nums[0]` 乘到 `nums[i]` 的累積乘積。
   - 宣告 `right_p` 陣列，`right_p[n-1-i]` 記錄從 `nums[n-1]` 往回乘到 `nums[n-1-i]` 的累積乘積。
   - 利用 `i > 0 ? left_p[i-1] : 1` 這類的三元運算子，漂亮地處理了陣列頭尾邊界的問題。
2. **組合最終答案**：
   - 宣告結果陣列 `rt`。
   - 遍歷原陣列，`rt[i]` 的值即為左側乘積 (`left_p[i-1]`) 乘上右側乘積 (`right_p[i+1]`)。
   - 同樣利用三元運算子確保當 `i=0` 或 `i=n-1` 時，缺少的那一側乘積以 `1` 替代，避免越界且不影響結果。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。遍歷兩次陣列，常數極小。
- **空間複雜度**: $\mathcal{O}(N)$。使用了 `left_p` 與 `right_p` 兩個輔助陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> left_p(n, 0);
        vector<int> right_p(n, 0);
        
        // 1. 同時建立由左至右、由右至左的累積乘積陣列
        for(int i = 0; i < n; ++i) {
            // 左側前綴乘積
            left_p[i] = (i > 0 ? left_p[i-1] : 1) * nums[i];
            // 右側後綴乘積 (注意索引對應)
            right_p[n-1-i] = (n-i < n ? right_p[n-i] : 1) * nums[n-1-i];
        }

        // 2. 組合答案：左邊所有數的乘積 * 右邊所有數的乘積
        vector<int> rt(n, 0);
        for(int i = 0; i < n; ++i) {
            rt[i] = (i > 0 ? left_p[i-1] : 1) * (i + 1 < n ? right_p[i+1] : 1);
        }
        
        return rt;
    }
};
```

---

## 💡 2026-03-16 初次提交

### 🎯 直覺 (Intuition)
如果可以使用除法，我們只需要把所有數字乘起來，再逐一除以當前數字即可。但題目禁止除法，而且還需要考慮數字為 `0` 的邊界情況（不能除以 0）。
「除了自己以外的乘積」其實可以拆解為：**「自己左邊所有數字的乘積」 $\times$ 「自己右邊所有數字的乘積」**。
因此，我們可以預先算出兩個陣列：`forward` 記錄由左至右的累積乘積，`backward` 記錄由右至左的累積乘積。
特別地，陣列中的 `0` 是決定勝負的關鍵：
1. **超過 1 個 `0`**：任何數字除了自己以外，都還會乘到至少一個 `0`，所以整個答案陣列必定全為 `0`。
2. **剛好 1 個 `0`**：除了那個 `0` 的位置會是其他所有非零數字的乘積外，其餘位置都會因為乘到這個 `0` 而變成 `0`。
3. **沒有 `0`**：正常套用左右乘積的組合。

### 🛠️ 解題思路 (Approach)
1. 宣告 `forward` 與 `backward` 陣列，並使用 `l` 和 `r` 來追蹤累積乘積。
2. **第一趟掃描**：
   - 同時由前往後計算 `forward`，由後往前計算 `backward`。
   - 記錄 `0` 出現的次數 (`zero_cnt`)。
   - **巧妙設計**：如果遇到非 `0` 的數字才將其推入 `forward` 和 `backward`。這使得陣列中只保留了「純粹的非零乘積」。
3. **提早結束**：如果 `zero_cnt > 1`，直接回傳全為 0 的陣列。
4. **組合答案**：
   - 遍歷原陣列構造結果 `rt`。
   - 如果陣列中有 1 個 `0`，但當前元素不是 `0`，則答案必為 0（`rt.push_back(0)`）。
   - 如果當前元素是 `0` (或根本沒有 0)，則從 `forward` 與 `backward` 中取出對應的乘積相乘。因為我們當初跳過了 `0`，此時的索引提取恰好能對齊正確的前綴與後綴範圍。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。遍歷兩次陣列，常數極小。
- **空間複雜度**: $\mathcal{O}(N)$。使用了 `forward` 與 `backward` 兩個輔助陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> forward; forward.reserve(n);
        vector<int> backward; backward.reserve(n);
        int l = 1, r = 1;
        int zero_cnt = 0;
        
        // 1. 建立非零元素的前綴與後綴乘積
        for(int i = 0; i < n; ++i) {
            if(nums[i] == 0) {
                ++zero_cnt;
            }
            if(nums[i]) {
                l *= nums[i];
                forward.push_back(l);
            }
            if(nums[n - 1 - i]) {
                r *= nums[n - 1 - i];
                backward.push_back(r);
            }
        }
        
        // 2. 超過一個 0，答案必定全為 0
        if(zero_cnt > 1) return vector<int>(n, 0);
        
        // 3. 組合最終結果
        vector<int> rt; rt.reserve(n);
        for(int i = 0; i < n; ++i) {
            if(zero_cnt && nums[i] != 0) {
                // 若有 1 個零，其他非零位置的乘積必定為 0
                rt.push_back(0);
            } else {
                // 完美提取左右乘積 (因為零被跳過，索引依然能精準對齊)
                rt.push_back((i > 0 ? forward[i - 1] : 1) * (i < n - 1 ? backward[n - 2 - i] : 1));
            }
        }

        return rt;
    }
};
```

---

### 💡 額外知識：$\mathcal{O}(1)$ 空間優化寫法
LeetCode 這題有一個著名的 Follow-up：「你能否在 $\mathcal{O}(1)$ 的額外空間複雜度內解決此題？（回傳的答案陣列不計入空間複雜度）」。

為了達到 $\mathcal{O}(1)$，我們可以**把答案陣列 `rt` 直接當作左側前綴乘積陣列來用**。
第一趟掃描時，`rt[i]` 先存好「它左邊所有元素的乘積」。
第二趟掃描時，我們由右往左走，維護一個單一變數 `right_prod` 代表「它右邊所有元素的累積乘積」，並直接乘進 `rt[i]` 裡面。這樣就完全不需要計算 0 的數量，也不需要額外開陣列了！

<details>
<summary><b>點擊展開 O(1) 空間實作程式碼</b></summary>

```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> rt(n, 1);
        
        // 第一趟：rt[i] 儲存 i 左邊所有元素的乘積
        int left_prod = 1;
        for(int i = 0; i < n; ++i) {
            rt[i] = left_prod;
            left_prod *= nums[i];
        }
        
        // 第二趟：由右向左，將右邊的乘積直接乘入 rt[i]
        int right_prod = 1;
        for(int i = n - 1; i >= 0; --i) {
            rt[i] *= right_prod;
            right_prod *= nums[i];
        }
        
        return rt;
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