---
title: "LeetCode 2784. Check if Array is Good"
date: 2026-05-14T15:00:00+08:00
lastmod: 2026-05-14T15:00:00+08:00
difficulty: 1376
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash-table", "counting"]
keywords: ["LC2784", "Easy", "1376", "Array is Good", "頻率統計", "排列檢查"]
description: "LeetCode 第 2784 題：Check if Array is Good。難度評分：1376。探討如何根據題目定義的 base[n] 結構，利用頻率計數與長度限制，在 O(N) 時間內判定陣列合法性。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2784](https://leetcode.com/problems/check-if-array-is-good/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1376)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums`。如果一個陣列是某個整數 $n$ 的 `base[n]` 陣列的排列，則稱該陣列是「好陣列」。
`base[n] = [1, 2, ..., n - 1, n, n]` （即長度為 $n + 1$，包含 $1$ 到 $n-1$ 各一次，且 $n$ 出現兩次）。

如果 `nums` 是好陣列，回傳 `true`；否則回傳 `false`。

### 範例
- **Input**: `nums = [1, 3, 3, 2]`
- **Output**: `true`
- **Explanation**: 此為 `base[3] = [1, 2, 3, 3]` 的排列。

- **Input**: `nums = [1, 1]`
- **Output**: `true`
- **Explanation**: 此為 `base[1] = [1, 1]` 的排列。

- **Input**: `nums = [3, 4, 4, 1, 2, 1]`
- **Output**: `false`

### 限制條件
- $1 \le nums.length \le 100$
- $1 \le nums[i] \le 200$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-14：初次提交 (頻率統計與早停優化)](#2026-05-14-初次提交)

---

## 💡 2026-05-14 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
題目定義的 `base[n]` 具有極強的數學特徵：
1. **長度關係**：陣列長度 $L$ 與最大值 $n$ 的關係必定是 $L = n + 1$。
2. **組成元素**：
   - 數字 $1$ 到 $n-1$ 各出現**恰好一次**。
   - 數字 $n$ 出現**恰好兩次**。
   - 不存在大於 $n$ 或小於 $1$ 的數字。

**核心破局點：**
我們可以直接從 `nums.size() - 1` 推算出目標 $n$。接著利用計數陣列統計。如果任何 $1 \sim n-1$ 的數字出現超過一次，或是有數字超出範圍，即可判定為 `false`。

### 🛠️ 解題思路 (Approach)
1. **確定最大值 $n$**：令 $n = nums.size() - 1$。
2. **範圍與次數檢查**：
   - 遍歷 `nums`，若有元素 `num > n`，直接回傳 `false`。
   - 使用 `cnt` 陣列記錄頻率。若 `num < n` 且 `cnt[num]` 已存在（即出現第二次），回傳 `false`。
3. **完整性檢查**：
   - 最後掃描 `cnt` 陣列，確保 $1 \sim n-1$ 每個位置的計數都等於 $1$。
   - (註：由於總長度固定，若上述條件均滿足，數字 $n$ 必然會佔據剩下的兩個位置)。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列長度。我們只需遍歷一次陣列與一次計數空間。
- **空間複雜度**: $\mathcal{O}(N)$。需要一個大小為 $n$ 的計數陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool isGood(vector<int>& nums) {
        // 1. 根據 base[n] 定義，最大值 n = 長度 - 1
        int n = nums.size() - 1;
        if (n <= 0) return false; // 長度至少為 2 (base[1] = [1, 1])

        vector<int> cnt(n + 1, 0);
        for (auto num : nums) {
            // 2. 邊界檢查：數字必須在 [1, n] 之間
            if (num <= 0 || num > n) return false;
            
            cnt[num]++;
            
            // 3. 次數檢查：除了最大值 n 以外，其餘數字只能出現一次
            if (num != n && cnt[num] > 1) return false;
        }

        // 4. 確保 1 到 n-1 每個數字都確實出現過一次
        for (int i = 1; i < n; ++i) {
            if (cnt[i] != 1) return false;
        }
        
        // 5. 確保最大值 n 確實出現了兩次
        return cnt[n] == 2;
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