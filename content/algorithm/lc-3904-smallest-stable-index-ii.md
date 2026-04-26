---
title: "LeetCode 3904. Smallest Stable Index II"
date: 2026-04-19T15:35:00+08:00
lastmod: 2026-04-19T15:35:00+08:00
difficulty: 1351
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum", "math"]
keywords: ["LC3904", "Medium", "Smallest Stable Index II", "Prefix Maximum", "Suffix Minimum"]
description: "LeetCode 第 3904 題：Smallest Stable Index II。難度評分：Medium (1351)。探討在測資範圍放大的情況下，如何利用後綴陣列 (Suffix Array) 預先處理最小值，並配合動態維護的前綴最大值，以 O(N) 線性時間複雜度完美避開雙層迴圈的效能瓶頸。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3904](https://leetcode.com/problems/smallest-stable-index-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1351)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Maximum` $\cdot$ `Suffix Minimum`
> **🔗 系列題連結**：[👉 點此查看 Part I (LeetCode 3903) 題解筆記](../3903-smallest-stable-index-i/)

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
*(此為 Part I 的進階版)*
給定一個整數陣列 `nums` 以及一個整數 `k`。
請找出一個**最小的索引** `i` ($0 \le i < nums.length$)，使得滿足以下「穩定條件 (Stable Condition)」：
- 從陣列開頭到索引 `i` 的最大值，減去從索引 `i` 到陣列結尾的最小值，其結果必須小於等於 `k`。
即：$\max(nums[0 \dots i]) - \min(nums[i \dots n-1]) \le k$。

如果找不到任何滿足條件的索引，請回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 10^5$ (測資可能大於 Part I)
- $1 \le nums[i], k \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-19：初次提交 (後綴最小值與前綴最大值掃描)](#2026-04-19-初次提交)

---

## 💡 2026-04-19 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是 Part I 的進階版，若使用雙層迴圈暴力尋找左側最大值與右側最小值，時間複雜度 $\mathcal{O}(N^2)$ 必定會導致 TLE (Time Limit Exceeded)。
最佳解法是利用 **空間換取時間 (Space-Time Tradeoff)**，透過一次由右至左的掃描，建立一個長度為 $N$ 的「後綴最小值陣列」。接著再由左至右掃描陣列，動態維護「前綴最大值」，並在 $\mathcal{O}(1)$ 的時間內完成條件判斷，達成整體的 $\mathcal{O}(N)$ 線性時間複雜度。

### 🛠️ 解題思路 (Approach)
1. **建立後綴最小值陣列 (`mm`)**：
   - 宣告大小為 $N$ 的陣列 `mm`。
   - 宣告變數 `m` 記錄當前遇到的最小值，初始設為 `INT_MAX`。
   - 使用 `for` 迴圈從 $N-1$ 倒退走到 $0$。每次更新 `m = min(nums[i], m)`，並將其寫入 `mm[i] = m`。
2. **尋找最小穩定索引**：
   - 宣告變數 `mx` 記錄前綴最大值，初始設為 `nums[0]`。
   - 使用 `for` 迴圈從 $0$ 走到 $N-1$。
   - 每次遇到新元素，更新最大值 `mx = max(mx, nums[i])`。
   - 檢查穩定條件：`mx - mm[i] <= k`。因為我們是從索引 0 開始遞增尋找，**第一次滿足條件的索引就絕對是最小的穩定索引**，直接 `return i`。
3. **例外處理**：若迴圈結束仍未找到合法索引，則回傳 `-1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 為陣列長度。從右向左掃描一次建立 `mm` 需 $\mathcal{O}(N)$，從左向右掃描一次尋找答案需 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(N)$。額外配置了長度為 $N$ 的陣列 `mm` 來儲存後綴最小值。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int firstStableIndex(vector<int>& nums, int k) {
        int n = nums.size();
        
        // 1. 預處理後綴最小值
        vector<int> mm(n, 0);
        int m = INT_MAX;
        for(int i = n - 1; i >= 0; --i) {
            m = min(nums[i], m);
            mm[i] = m;
        }
        
        // 2. 動態維護前綴最大值，並由左至右尋找第一個符合條件的索引
        int mx = nums[0];
        for(int i = 0; i < n; ++i) {
            mx = max(mx, nums[i]);
            
            // 判斷穩定條件：左側最大值 - 右側最小值 <= k
            if(mx - mm[i] <= k) {
                return i; // 因為是從左向右找，第一個找到的必定是最小索引
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