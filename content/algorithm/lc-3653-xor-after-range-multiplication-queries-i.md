---
title: "LeetCode 3653. XOR After Range Multiplication Queries I"
date: 2026-04-08T22:35:00+08:00
lastmod: 2026-04-08T22:35:00+08:00
difficulty: 1556
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "simulation", "math", "bit-manipulation"]
keywords: ["LC3653", "1556", "Medium", "XOR After Range", "Multiplication Queries", "Simulation"]
description: "LeetCode 第 3653 題：XOR After Range Multiplication Queries I。難度評分：1556。探討如何透過直觀的陣列模擬，配合 64 位元整數轉型防止溢位，以 O(Q * N) 時間複雜度完成區間步長乘法更新，並求得最終陣列的 XOR 總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3653](https://leetcode.com/problems/xor-after-range-multiplication-queries-i/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1556)</span>  
> **核心主題**：`Array` $\cdot$ `Simulation` $\cdot$ `Math` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums` 以及一個二維陣列 `queries`，其中每個查詢 `queries[i] = [l, r, step, mul]` 代表一個操作。
對於每一個查詢，你必須將陣列 `nums` 中索引從 `l` 到 `r` 且間隔為 `step` 的所有元素（即 `l`, `l + step`, `l + 2 * step`...）乘以 `mul`。
由於數值可能非常大，每次乘法操作後，請將結果對 $10^9 + 7$ 取同餘 (Modulo)。
在處理完所有的查詢後，請回傳陣列 `nums` 中所有元素的 **XOR 總和**（即 `nums[0] ^ nums[1] ^ ... ^ nums[n-1]`）。

### 限制條件
*(註：本題為 Queries I，測資範圍較小，允許 $O(Q \times N)$ 級別的解法)*
</details>

---

## 📝 歷次打卡與更新
- [2026-04-08：初次提交 (直接模擬與型別安全乘法)](#2026-04-08-初次提交)

---

## 💡 2026-04-08 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求對陣列進行特定區間與特定步長的乘法更新，最後求 XOR 總和。
因為這是 "Queries I" 版本，測資規模通常允許我們直接模擬整個過程。
我們只需要遍歷每一個查詢，並使用一個迴圈從 `l` 開始，每次前進 `step` 步，直到超過 `r`。在更新數值時，最關鍵的細節是**防止乘法溢位**。兩個 32 位元整數（如 $10^9$ 與 $10^9$）相乘會超出 32 位元有號整數的上限，因此在進行乘法運算時，必須先將其中一個運算元轉型為 64 位元的 `long long`，計算完成並取餘數後，再存回原陣列。

### 🛠️ 解題思路 (Approach)
1. **宣告常數**：定義 `MOD = 1e9 + 7`。
2. **模擬查詢更新**：
   - 走訪每一個 `queries` 中的查詢 `q`。
   - 建立內層迴圈，索引 `i` 從 `q[0]` 開始，條件為 `i <= q[1]`，每次遞增 `q[2]` (`i += q[2]`)。
   - 更新數值：`nums[i] = (nums[i] * 1ll * q[3]) % MOD`。其中 `1ll` 強制將乘法過程提升至 64 位元計算，確保安全。
3. **計算 XOR 總和**：
   - 宣告變數 `rt = 0`。
   - 走訪更新完畢的 `nums` 陣列，將所有元素與 `rt` 進行 XOR 運算 (`rt ^= num`)。
4. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(Q \times \frac{N}{step} + N)$。其中 $Q$ 是查詢的數量，$N$ 是陣列的長度。在最差情況下（`step = 1` 且區間涵蓋整個陣列），每個查詢需要遍歷 $N$ 個元素，總時間為 $\mathcal{O}(Q \times N)$。最後計算 XOR 需 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(1)$。在原陣列上進行 In-place 修改，無需額外的資料結構。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    
    int xorAfterQueries(vector<int>& nums, vector<vector<int>>& queries) {
        int n = nums.size();
        
        // 1. 模擬執行所有區間乘法查詢
        for(auto q : queries) {
            // q[0]: 左邊界 l, q[1]: 右邊界 r, q[2]: 步長 step, q[3]: 乘數 mul
            for(int i = q[0]; i <= q[1]; i += q[2]) {
                // 使用 1ll 將乘法轉為 64-bit long long，避免 Overflow
                nums[i] = (nums[i] * 1ll * q[3]) % MOD;
            }
        }
        
        // 2. 計算所有元素的 XOR 總和
        int rt = 0;
        for(auto num : nums) {
            rt ^= num;
        }
        
        return rt;
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