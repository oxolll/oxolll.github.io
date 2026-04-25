---
title: "LeetCode 3909. Compare Sums of Bitonic Parts"
date: 2026-04-26T01:20:00+08:00
lastmod: 2026-04-26T01:20:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "state-machine", "math"]
keywords: ["LC3909", "Medium", "Compare Sums of Bitonic Parts", "Bitonic Array", "One-Pass"]
description: "LeetCode 第 3909 題：Compare Sums of Bitonic Parts。探討如何利用單一布林變數控制狀態機 (State Machine)，以 O(N) 單次走訪的極簡邏輯優雅地計算 Bitonic 序列的遞增與遞減段總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3909](https://leetcode.com/problems/compare-sums-of-bitonic-parts/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `State Machine`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個「雙調陣列 (Bitonic Array)」 `nums`。雙調陣列的定義為：元素先嚴格遞增至一個「山峰元素 (Peak Element)」，然後嚴格遞減。
請分別計算該陣列的「遞增部分總和」與「遞減部分總和」（註：山峰元素必須同時計入這兩個部分的總和中）。

比較這兩個總和：
- 如果遞增部分總和 **大於** 遞減部分總和，回傳 `0`。
- 如果遞增部分總和 **小於** 遞減部分總和，回傳 `1`。
- 如果兩者 **相等**，回傳 `-1`。

### 限制條件
- $3 \le nums.length \le 10^5$ (推測值)
- `nums` 保證是一個有效的雙調陣列。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-26：初次提交 (O(N) 單次走訪狀態機)](#2026-04-26-初次提交)

---

## 💡 2026-04-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理 Bitonic 序列時，最直覺的想法是先找到「山峰 (Peak)」的索引，然後再分別用兩個迴圈去加總左右兩半邊。
但為了追求極致的效能與代碼簡潔度，我們可以使用 **狀態機 (State Machine)** 的概念，將其壓縮為**單次走訪 (One-Pass)**。

我們利用一個布林變數 `f` (flag) 來記錄目前的狀態：
- `f = true`：處於遞增爬坡階段。
- `f = false`：處於遞減下坡階段。

**核心技巧 (山峰共享)**：
因為山峰元素必須同時存在於遞增段與遞減段，當我們在迴圈中發現 `nums[i] < pre` 時，代表我們已經跨過了山峰（此時的 `pre` 就是山峰元素）。在切換狀態 `f = false` 的當下，我們直接將 `dw` (遞減段總和) 的初始值設定為 `pre`，這樣就能完美地讓山峰元素同時被 `up` 與 `dw` 所涵蓋，免去了複雜的索引操作。

### 🛠️ 解題思路 (Approach)
1. **初始化狀態**：宣告 `up = 0`, `dw = 0`, `pre = 0`，以及狀態旗標 `f = true`。
2. **走訪陣列與狀態轉換**：
   - 遍歷陣列 `nums`。
   - **狀態切換檢查**：如果目前在遞增段 (`f == true`)，且當前數字小於前一個數字 (`nums[i] < pre`)，觸發反轉。將 `f` 設為 `false`，並將山峰值 `pre` 賦予 `dw` 作為遞減段的初始總和。
   - **數值累加**：依據當前的 `f` 狀態，將 `nums[i]` 加進對應的 `up` 或 `dw` 中。
   - 更新 `pre = nums[i]` 供下一次迴圈比較。
3. **結果比較**：
   - 依據題目要求的邏輯回傳：`up > dw` 回傳 `0`，`up < dw` 回傳 `1`，相等則回傳 `-1`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列長度。只需對陣列進行一次線性掃描。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個變數，不論陣列多大都不會消耗額外記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int compareBitonicSums(vector<int>& nums) {
        long long up = 0;   // 遞增段總和
        long long dw = 0;   // 遞減段總和
        long long pre = 0;  // 記錄前一個數字
        bool f = true;      // 狀態機：true 代表遞增段，false 代表遞減段
        
        for(int i = 0; i < nums.size(); ++i) {
            // 當發現數字開始下降，代表前一個數字 (pre) 就是山峰
            if(f && nums[i] < pre) {
                f = false;
                // 山峰需要重複計算，因此直接將 pre 初始化給遞減段總和 dw
                dw = pre;
            }

            // 依據目前狀態進行加總
            if(f) {
                up += nums[i];
            } else {
                dw += nums[i];
            }

            pre = nums[i];
        }
        
        // 依照題目規則回傳結果
        if(up > dw) return 0;
        if(up < dw) return 1;
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