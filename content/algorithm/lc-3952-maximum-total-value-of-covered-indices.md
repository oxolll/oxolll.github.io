---
title: "LeetCode 3952. Maximum Total Value of Covered Indices"
date: 2026-06-09T14:50:00+08:00
lastmod: 2026-06-09T14:50:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "dynamic-programming", "state-machine", "array"]
keywords: ["LC3952", "Medium", "Covered Indices", "狀態機 DP", "動態規劃", "滾動變數", "空間壓縮"]
description: "LeetCode 第 3952 題：Maximum Total Value of Covered Indices。探討如何利用狀態機動態規劃 (State Machine DP)，定義 dp0 與 dp1 兩個滾動變數，完美處理標記的向左覆蓋與原地覆蓋邏輯，以 O(N) 時間與 O(1) 空間達成極致最優解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3952](https://leetcode.com/problems/maximum-total-value-of-covered-indices/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `State Machine`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為 $n$ 的整數陣列 `nums` 以及一個長度相同的二進位字串 `s`。
字串中的每一個 `'1'` 代表一個標記。對於每一個標記，你可以選擇讓它：
1. **原地覆蓋**：覆蓋當前的索引 $i$。
2. **向左覆蓋**：覆蓋左側的相鄰索引 $i - 1$。

每個索引最多只能被覆蓋一次。請你計算並回傳所有被覆蓋的索引對應在 `nums` 中的數值總和的 **最大值**。

### 限制條件
- $1 \le nums.length \le 10^5$
- $s.length == nums.length$
- $s[i]$ 僅為 `'0'` 或 `'1'`
- $-10^4 \le nums[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (狀態機 DP 與 O(1) 空間壓縮)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的決策核心在於：當我們遇到 `s[i] == '1'` 時，它究竟要用來覆蓋 `nums[i]`，還是覆蓋 `nums[i-1]`？
因為覆蓋具有「互斥性」（一個索引不能被重複覆蓋），我們可以使用 **狀態機動態規劃 (State Machine DP)** 來記錄當前索引的覆蓋狀態。

**核心破局點：狀態定義與轉移**
定義兩個變數來代表結算至索引 $i$ 時的最大總和：
- `dp1`：表示索引 $i$ **被覆蓋** (由 `s[i] == '1'` 原地覆蓋) 時的最大總和。
- `dp0`：表示索引 $i$ **未被覆蓋** (由 `s[i] == '1'` 向左覆蓋了 $i-1$，或是當前為 `'0'`) 時的最大總和。

**狀態轉移方程式：**
1. **當 `s[i] == '0'`**：
   - 當前沒有標記可以做事，索引 $i$ 絕對無法由自身覆蓋，因此 `dp1` 變成 `INT_MIN`。
   - `dp0` 則繼承前一個狀態的最優解：`dp0 = max(dp0, dp1)`。*(註：若未來 $s[i+1]$ 為 `'1'` 並向左覆蓋，它會直接把 $nums[i]$ 加到這個繼承來的 `dp0` 上，邏輯完美閉環！)*
2. **當 `s[i] == '1'`**：
   - **選擇原地覆蓋 $i$**：獲得 `nums[i]`。前一個索引 $i-1$ 被不被覆蓋都無所謂，因此從 `max(dp0, dp1)` 轉移。
     $\Rightarrow$ `dp1_new = max(dp1, dp0) + nums[i]`
   - **選擇向左覆蓋 $i-1$**：獲得 `nums[i-1]`。但前提是 $i-1$ **必須未被覆蓋**，因此只能嚴格從 `dp0` 轉移！
     $\Rightarrow$ `dp0_new = dp0 + nums[i-1]`

### 🛠️ 解題思路 (Approach)
1. **初始化狀態**：
   - 若 $s[0] == '1'$，則 $dp1 = nums[0]$, $dp0 = 0$。
   - 若 $s[0] == '0'$，則 $dp1 = INT\_MIN$, $dp0 = 0$。
2. **滾動更新**：從 $i = 1$ 遍歷至 $n-1$：
   - 遇到 `'0'`：`dp0 = max(dp0, dp1)`，同時切斷 `dp1 = INT_MIN`，並 `continue`。
   - 遇到 `'1'`：先將 `dp1` 更新為 `max(dp1, dp0) + nums[i]`，再將 `dp0` 更新為 `dp0 + nums[i-1]`。(順序不可錯，需確保 `dp0_new` 使用的是上一輪的 `dp0`)。
3. **回傳極值**：遍歷結束後，回傳 `max(dp0, dp1)` 即為全局最優解。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列的長度。僅需對陣列進行一次線性掃描。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了 `dp0` 與 `dp1` 兩個變數進行滾動更新，為原地演算法的極致優化。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long maxTotal(vector<int>& nums, string s) {
        int n = nums.size();
        
        // dp0: 當前索引 i "未被原地覆蓋" 的最大總和
        // dp1: 當前索引 i "被原地覆蓋" 的最大總和
        long long dp0 = INT_MIN, dp1 = INT_MIN;
        
        // 1. 初始化狀態
        if(s[0] == '1') {
            dp1 = nums[0]; // 第 0 位有標記，原地覆蓋
            dp0 = 0;       // 第 0 位有標記，但選擇向左覆蓋 (超出邊界視同無效但保留合法狀態)
        } else {
            dp0 = 0;       // 第 0 位無標記，無法覆蓋
        }
        
        // 2. 狀態機滾動轉移
        for(int i = 1; i < n; ++i) {
            if(s[i] == '0') {
                // 當前無標記：繼承前一輪的最優解，並宣告 dp1 為不可能狀態
                dp0 = max(dp0, dp1);
                dp1 = INT_MIN;
                continue;
            }
            
            // 當前有標記 '1'，面臨兩個選擇：
            // 選擇 A：原地覆蓋 i。繼承前一輪的最優解，並加上 nums[i]
            // 注意：需防禦溢位，使用 1ll 強制轉型
            long long next_dp1 = max(dp1, dp0) * 1ll + nums[i];
            
            // 選擇 B：向左覆蓋 i-1。必須確保前一輪的 i-1 未被覆蓋 (嚴格繼承 dp0)
            long long next_dp0 = dp0 + nums[i-1];
            
            // 更新狀態
            dp1 = next_dp1;
            dp0 = next_dp0;
        }
        
        // 3. 回傳最後一輪中的最大值
        return max(dp1, dp0);
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