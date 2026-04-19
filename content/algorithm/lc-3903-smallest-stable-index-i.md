---
title: "LeetCode 3903. Smallest Stable Index I"
date: 2026-04-19T15:25:00+08:00
lastmod: 2026-04-19T15:25:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum", "math"]
keywords: ["LC3903", "Easy", "Smallest Stable Index I", "Prefix Maximum", "Suffix Minimum"]
description: "LeetCode 第 3903 題：Smallest Stable Index I。難度評分：Easy (N/A)。探討如何利用後綴陣列 (Suffix Array) 預先處理最小值，並配合動態維護的前綴最大值，以 O(N) 線性時間複雜度完美避開雙層迴圈的效能瓶頸。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3903](https://leetcode.com/problems/smallest-stable-index-i/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Maximum` $\cdot$ `Suffix Minimum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums` 以及一個整數 `k`。
請找出一個**最小的索引** `i` ($0 \le i < nums.length$)，使得滿足以下「穩定條件 (Stable Condition)」：
- 從陣列開頭到索引 `i` 的最大值，減去從索引 `i` 到陣列結尾的最小值，其結果必須小於等於 `k`。
即：$\max(nums[0 \dots i]) - \min(nums[i \dots n-1]) \le k$。

如果找不到任何滿足條件的索引，請回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i], k \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-19：初次提交 (後綴最小值與前綴最大值掃描)](#2026-04-19-初次提交)

---

## 💡 2026-04-19 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求判斷 $\max(nums[0 \dots i]) - \min(nums[i \dots n-1]) \le k$。
如果針對每一個索引 $i$ 都重新尋找左側的最大值與右側的最小值，時間複雜度將會是慘烈的 $\mathcal{O}(N^2)$，在 $N = 10^5$ 的限制下絕對會 TLE (Time Limit Exceeded)。

破局的關鍵在於 **狀態的預處理 (Preprocessing)**：
1. **右側最小值**：我們可以從陣列最右側開始向左掃描，利用一個陣列 `mm` 記錄「從當前位置到陣列結尾的最小值」。這只需要 $\mathcal{O}(N)$ 的時間。
2. **左側最大值**：我們不需要額外開一個陣列。在從左向右尋找答案的過程中，使用一個變數 `mx` 隨時更新並記錄當前遇到的最大值即可。這同樣也是 $\mathcal{O}(N)$ 的時間。

結合這兩個技巧，我們就能在單次遍歷中以 $\mathcal{O}(1)$ 的時間判定每個索引是否符合條件，將整體時間複雜度降維至 $\mathcal{O}(N)$。

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
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 為陣列長度。從右向左掃描一次建立 `mm` 需 $\mathcal{O}(N)$，從左向右掃描一次尋找答案需 $\mathcal{O}(N)$，整體為線性時間複雜度。
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