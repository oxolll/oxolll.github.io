---
title: "LeetCode 3942. Minimum Operations to Sort a Permutation"
date: 2026-05-24T22:15:00+08:00
lastmod: 2026-05-24T22:15:00+08:00
difficulty: 1854
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "simulation", "brainteaser"]
keywords: ["LC3942", "Medium", "Sort a Permutation", "環形陣列", "旋轉與反轉", "單調性"]
description: "LeetCode 第 3942 題：Minimum Operations to Sort a Permutation。探討如何利用環形陣列的相鄰元素升降次數，判斷數列的環形單調性，並在 O(N) 時間內透過 0 的偏移量計算出最少的操作步數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3942](https://leetcode.com/problems/minimum-operations-to-sort-a-permutation/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Brainteaser`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為 $n$ 的排列 `nums`（包含從 **0** 到 **n - 1** 的所有整數，每個數字恰好出現一次）。
你可以對陣列執行以下兩種操作任意次數：
1. **旋轉 (Shift)**：將陣列向左或向右平移一個位置（成本為 1）。
2. **反轉 (Reverse)**：將整個陣列顛倒過來（成本為 1）。

請你計算將 `nums` 排序為遞增序列 `[0, 1, 2, ..., n-1]` 所需的 **最少操作次數**。如果無法透過這兩種操作排序，請回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 10^5$
- `nums` 是一個包含 `0` 到 `n - 1` 的排列。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-24：初次提交 (環形升降計數與偏移量推導)](#2026-05-24-初次提交)

---

## 💡 2026-05-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的破局點在於理解「旋轉」與「反轉」這兩個操作的幾何意義：
- **旋轉** 不會改變元素之間的相對循環順序。
- **反轉** 會將循環順序從「順時針」變成「逆時針」。

因此，一個陣列如果能夠被還原為 `[0, 1, ..., n-1]`，它在環形視角下 **必定是嚴格單調的**。
這意味著，如果我們比較所有相鄰元素（包含尾首連接）：
- 對於**環形遞增**的數列，只會出現恰好一次的下降（也就是最大值掉回 `0` 的地方）。
- 對於**環形遞減**的數列，只會出現恰好一次的上升（也就是最小值跳回最大值的地方）。
- 如果下降次數和上升次數 **都 $\ge 2$**，代表數列是雜亂無章的，絕對無法透過旋轉和反轉來排序。

### 🛠️ 解題思路 (Approach)
1. **環形遍歷與計數**：
   - 遍歷陣列（索引從 `1` 到 `n`），利用模除 `i % n` 將首尾相連。
   - 計算相鄰元素的遞增次數 `inc` 與遞減次數 `dec`。
   - 同時記錄數字 `0` 出現的位置 `zero_p`。
2. **無解判定**：
   - 若 $\min(\text{inc}, \text{dec}) \ge 2$，直接回傳 `-1`。
3. **判斷單調方向**：
   - 從 `0` 的位置開始往後走一圈，確認數列究竟是環形遞增還是環形遞減（存入布林值 `isinc`）。
4. **計算最少步數**：
   - **情況 A：環形遞增 (`isinc == true`)**
     - 方法 1：直接向左旋轉 `zero_p` 次，成本為 `zero_p`。
     - 方法 2：先反轉 (1) + 向右旋轉 `n - zero_p` 次 (n - zero_p) + 再反轉回來 (1)，成本為 `2 + n - zero_p`。
     - 取兩者最小值：$\min(\text{zero\_p}, 2 + n - \text{zero\_p})$。
   - **情況 B：環形遞減 (`isinc == false`)**
     - 必須使用一次反轉將其變為遞增，成本先加 1。
     - 方法 1：先向左（或向右）旋轉調整到特定位置後再反轉，對應的最小調整成本為 $\min(\text{zero\_p} + 1, n - 1 - \text{zero\_p})$。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需對長度為 $N$ 的陣列進行兩次線性遍歷，判定單調性與計算公式均為常數時間。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了常數個整數變數，無需額外的記憶體開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minOperations(vector<int>& nums) {
        int n = nums.size();
        int inc = 0, dec = 0;
        int zero_p = -1;
        
        // 1. 環形檢查相鄰元素的升降狀態，並尋找 0 的位置
        for(int i = 1; i <= n; ++i) {
            int idx = i % n;
            if(nums[idx] > nums[i - 1]) {
                ++inc;
            } else {
                ++dec;
            }

            if(nums[idx] == 0) zero_p = i;
        }
        
        // 2. 如果升降次數都大於等於 2，代表數列錯亂，無法排序
        if(min(inc, dec) >= 2) return -1;
        
        zero_p %= n;

        // 3. 從 0 的位置開始，判斷是否為環形遞增
        bool isinc = true;
        for(int i = 1; i < n; ++i) {
            int p = (i + zero_p) % n;
            // 只要有一處下降，就不是環形遞增 (即為環形遞減)
            if(nums[(p - 1 + n) % n] > nums[p]) {
                isinc = false;
            }
        }
        
        // 4. 根據單調方向套用對應的最小操作次數公式
        if (isinc) {
            return min(zero_p, 2 + n - zero_p);
        } else {
            return 1 + min(zero_p + 1, n - 1 - zero_p);
        }
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