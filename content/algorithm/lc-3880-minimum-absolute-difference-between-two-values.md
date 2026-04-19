---
title: "LeetCode 3880. Minimum Absolute Difference Between Two Values"
date: 2026-03-29T01:38:00+08:00
lastmod: 2026-03-29T01:38:00+08:00
difficulty: 1257
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "two pointers", "greedy"]
keywords: ["LC3880", "Easy", "1257", "Minimum Absolute Difference", "Linear Scan"]
description: "LeetCode 第 3880 題：Minimum Absolute Difference Between Two Values。難度評分：Easy (1257)。探討如何透過單次線性掃描並動態維護目標值的最新索引，以 O(N) 時間複雜度與 O(1) 空間複雜度求得最小索引距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3880](https://leetcode.com/problems/minimum-absolute-difference-between-two-values/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1257)</span>  
> **核心主題**：`Array` $\cdot$ `Two Pointers` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
請找出陣列中值為 `1` 的元素與值為 `2` 的元素之間，索引的**最小絕對差值**。
如果陣列中不存在 `1` 或不存在 `2`，請回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-29：初次提交 (線性掃描與最新索引追蹤)](#2026-03-29-初次提交)

---

## 💡 2026-03-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求尋找特定數值 (`1` 和 `2`) 在陣列中索引的最小距離。
直觀的暴力解法會產生 $\mathcal{O}(N^2)$ 的時間複雜度，這在測資較大時會導致超時。
利用貪婪的思維，能產生最小距離的 `1` 和 `2`，必然在陣列中是**相鄰最近**的。因此，當我們從左向右遍歷陣列時，只需隨時記錄「最後一次看到 `1` 的位置」以及「最後一次看到 `2` 的位置」。只要這兩個位置都有效，它們的差值就是當前局部可能產生的最小距離。將其與全域最小值進行比較並更新，走訪完畢後即可得到最佳解。

### 🛠️ 解題思路 (Approach)
1. **初始化狀態**：
   - 宣告 `cnt1 = -1` 與 `cnt2 = -1`，分別用於記錄數值 `1` 和 `2` 最新出現的索引位置。
   - 宣告 `rt = INT_MAX` 用於記錄全域最小絕對差值。
2. **線性掃描**：
   - 使用迴圈遍歷陣列 `nums`。
   - 若 `nums[i] == 0`，直接跳過 (`continue`)。*(註：實際上其他不等於 1 或 2 的數值也可跳過)*。
   - 若 `nums[i] == 1`，更新最新索引：`cnt1 = i`。
   - 若 `nums[i] == 2`，更新最新索引：`cnt2 = i`。
   - 每次更新後，若 `cnt1` 與 `cnt2` 皆不為 `-1`（代表兩種數字皆已出現過），則計算當前的索引距離 `abs(cnt1 - cnt2)`，並使用 `min` 函式更新 `rt`。
3. **回傳結果**：迴圈結束後，檢查 `rt` 是否仍為 `INT_MAX`。若是，代表並未同時找到 `1` 與 `2`，回傳 `-1`；否則回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。僅需對陣列進行一次線性掃描，每次更新與比對皆為常數時間操作。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了 `cnt1`、`cnt2` 與 `rt` 三個整數變數，無額外記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minAbsoluteDifference(vector<int>& nums) {
        int cnt1 = -1;
        int cnt2 = -1;
        int rt = INT_MAX;
        
        for(int i = 0; i < nums.size(); ++i) {
            // 可選優化：略過非目標數值
            if(nums[i] == 0) continue; 
            
            // 記錄最新看到的 1 的索引
            if(nums[i] == 1) {
                cnt1 = i;
            }
            // 記錄最新看到的 2 的索引
            if(nums[i] == 2) {
                cnt2 = i;
            }

            // 只要兩者皆已出現，便計算距離並嘗試更新全域最小值
            if(cnt1 != -1 && cnt2 != -1) {
                rt = min(rt, abs(cnt1 - cnt2));
            }
        }

        // 若 rt 未被更新，代表未能同時找到 1 與 2
        return rt == INT_MAX ? -1 : rt;
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