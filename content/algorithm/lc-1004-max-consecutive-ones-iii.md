---
title: "LeetCode 1004. Max Consecutive Ones III"
date: 2026-03-21T21:20:00+08:00
lastmod: 2026-03-21T21:20:00+08:00
difficulty: 1655
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary search", "prefix sum", "sliding window"]
keywords: ["LC1004", "1655", "Medium", "Max Consecutive Ones", "Binary Search", "Prefix Sum"]
description: "LeetCode 第 1004 題：Max Consecutive Ones III。難度評分：1655。探討如何利用前綴和搭配「對答案進行二分搜尋 (Binary Search on Answer)」以 O(N log N) 時間求解，並進一步解析 O(N) 的滑動視窗最佳化解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1004](https://leetcode.com/problems/max-consecutive-ones-iii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1655)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search` $\cdot$ `Prefix Sum` $\cdot$ `Sliding Window`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個二進位陣列 `nums` 和一個整數 `k`。
如果你最多可以將 `k` 個 `0` 翻轉成 `1`，請回傳陣列中連續 `1` 的最大長度。

### 範例
- **Input**: `nums = [1,1,1,0,0,0,1,1,1,1,0], k = 2`
  **Output**: `6`
  **Explanation**: 將最後兩個 `0` 翻轉為 `1`，可得到長度為 6 的連續 `1`。

### 限制條件
- $1 \le nums.length \le 10^5$
- `nums[i]` 不是 0 就是 1。
- $0 \le k \le nums.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (前綴和與對答案二分搜尋)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求尋找連續子陣列的最大長度。這類「尋找最大極值」且具備「單調性」（若長度 $L$ 合法，則長度 $< L$ 必然也合法）的問題，非常適合使用**對答案進行二分搜尋 (Binary Search on Answer)**。

在驗證某個長度 $L$ 是否合法時，我們需要知道一個大小為 $L$ 的子陣列中包含了幾個 `0`。如果 `0` 的數量 $\le k$，則該長度合法。
為了加速驗證，我們先計算出 `nums` 的**前綴和 (Prefix Sum)**。藉此我們能在 $\mathcal{O}(1)$ 算出區間內的 `1` 的總數，再用區間長度 $L$ 減去 `1` 的總數，即為 `0` 的數量。

### 🛠️ 解題思路 (Approach)
1. **建立前綴和陣列**：
   - 宣告 `pre` 陣列，計算 `nums` 的前綴和，`pre[i]` 代表從索引 0 到 $i$ 共有多少個 1。
2. **設計驗證函式 (`isOK`)**：
   - 傳入參數 `l` 代表當前測試的子陣列長度。
   - 遍歷所有可能的起點 `i` (從 0 到 $n-l$)。
   - 利用前綴和計算區間 $[i, i+l-1]$ 內的 `0` 數量：`l - (pre[i+l-1] - pre[i-1])`。
   - 若發現有任何一個區間的 `0` 數量 $\le k$，回傳 `true`；全數驗證失敗則回傳 `false`。
3. **二分搜尋長度**：
   - 設定下界 `l = 0`，上界 `r = n`。
   - 為了尋找**最大值**，中點計算採用右偏的寫法：`int len = r - (r-l)/2;`。
   - 若 `isOK(len)` 為真，代表答案可能大於等於 `len`，將下界移至 `l = len`。
   - 否則答案必定小於 `len`，將上界移至 `r = len - 1`。
4. **回傳結果**：迴圈結束時，`l` 即為最大合法長度。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。前綴和建立需 $\mathcal{O}(N)$。二分搜尋區間長度為 $[0, N]$，需執行 $\mathcal{O}(\log N)$ 次。每次呼叫 `isOK` 需遍歷陣列，花費 $\mathcal{O}(N)$。總時間複雜度為 $\mathcal{O}(N \log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了長度為 $N$ 的 `pre` 陣列儲存前綴和狀態。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> pre(n, 0);
        
        // 1. 計算前綴和，快速取得區間內 1 的數量
        for(int i = 0; i < n; ++i) {
            pre[i] = (i > 0 ? pre[i-1] : 0) + nums[i];
        }
        
        // 2. 驗證給定長度 l 是否存在合法的連續子陣列
        auto isOK = [&](int l) {
            for(int i = 0; i < n - l + 1; ++i) {
                // 區間長度 - 區間內 1 的數量 = 區間內 0 的數量
                if(l - pre[i + l - 1] + (i > 0 ? pre[i-1] : 0) <= k) {
                    return true;
                }
            }
            return false;
        };
        
        // 3. 對目標長度進行二分搜尋
        int l = 0, r = n;
        while(l < r) {
            // 右偏中點，避免 l = len 時產生無窮迴圈
            int len = r - (r - l) / 2;
            if(isOK(len)) {
                l = len; // 該長度合法，嘗試尋找更長的
            } else {
                r = len - 1; // 該長度不合法，縮小長度
            }
        }

        return l;
    }
};
```

---

### 💡 進階最佳化：$\mathcal{O}(N)$ 滑動視窗 (Sliding Window)
雖然 $\mathcal{O}(N \log N)$ 能夠順利通過，但這題在面試中標準的 Optimal Solution 是**滑動視窗 (Two Pointers)**，能將空間降至 $\mathcal{O}(1)$ 且時間降至 $\mathcal{O}(N)$。

我們維護一個左指標 `left` 與一個右指標 `right`：
1. `right` 不斷向右擴張，遇到 `0` 就消耗掉 `k` 的額度 (`k--`)。
2. 當 `k < 0`（翻轉額度透支）時，我們必須將 `left` 向右移動，直到把某個翻轉過的 `0` 吐出來為止（若遇到 `0` 則恢復額度 `k++`）。
3. 視窗在擴張與收縮的過程中，所達到的最大長度即為答案。

<details>
<summary><b>點擊展開 O(N) 滑動視窗實作程式碼</b></summary>

```cpp
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int left = 0, right = 0;
        int max_len = 0;
        
        while (right < nums.size()) {
            // 遇到 0 則消耗一次翻轉額度
            if (nums[right] == 0) {
                k--;
            }
            
            // 額度透支，左指標必須向右收縮，直到吐出一個 0
            while (k < 0) {
                if (nums[left] == 0) {
                    k++;
                }
                left++;
            }
            
            // 記錄當前合法視窗的最大長度
            max_len = max(max_len, right - left + 1);
            right++;
        }
        
        return max_len;
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