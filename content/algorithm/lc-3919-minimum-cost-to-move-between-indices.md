---
title: "LeetCode 3919. Minimum Cost to Move Between Indices"
date: 2026-05-03T12:25:00+08:00
lastmod: 2026-05-03T12:25:00+08:00
difficulty: 1776
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "prefix-sum"]
keywords: ["LC3919", "Medium", "Minimum Cost", "Prefix Sum", "前綴和"]
description: "LeetCode 第 3919 題：Minimum Cost to Move Between Indices。探討如何利用雙向前綴和 (Bidirectional Prefix Sum) 與最近鄰居預處理，以 O(1) 的極速回應任意方向的區間移動成本查詢。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3919](https://leetcode.com/problems/minimum-cost-to-move-between-indices/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1776)</span>  
> **核心主題**：`Array` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的整數陣列 `nums`，代表 $n$ 個座標點的位置。
當你位於索引 $i$ 時，你可以移動到相鄰的索引 $i-1$ 或 $i+1$。移動的成本規則如下：
- 如果目標索引是距離 $nums[i]$ **絕對值最近**的相鄰點，則移動成本為 `1`。
- 否則，移動成本為兩點之間的實際距離 `|nums[i] - target_nums|`。
（若左右相鄰點距離相同，預設左邊鄰居更近，或依據題意為準：本解法設定為 `<=` 時優先選左邊）。

給定一個二維陣列 `queries`，其中 `queries[k] = [l, r]`。
對於每個查詢，請計算從索引 $l$ 移動到索引 $r$ 的最小總成本。

### 限制條件
- $2 \le nums.length \le 10^5$
- `nums` 陣列可能已排序（或嚴格遞增）。
- $1 \le queries.length \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-03：初次提交 (雙向前綴和 + 最近鄰居預處理)](#2026-05-03-初次提交)

---

## 💡 2026-05-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一個標準的「多次區間查詢 (Multiple Range Queries)」問題。如果對於每個 query 都跑一次迴圈去算成本，時間複雜度會高達 $\mathcal{O}(N \times Q)$，絕對會 TLE。
要將查詢時間降至 $\mathcal{O}(1)$，首選武器必定是 **前綴和 (Prefix Sum)**。

**核心破局點：非對稱移動成本**
這題最大的陷阱在於「向右移動的成本」與「向左移動的成本」是**不一樣的**！
舉例來說，從 $A \to B$ 的成本，取決於 $B$ 是否為 $A$ 的最近鄰居；但從 $B \to A$ 的成本，則取決於 $A$ 是否為 $B$ 的最近鄰居。
因此，我們必須建立**兩組前綴和陣列**：
1. `dp`：負責記錄由左至右 ($0 \to i$) 的累積成本。
2. `dp2`：負責記錄由右至左 ($n-1 \to n-1-i$) 的累積成本。

### 🛠️ 解題思路 (Approach)
1. **最近鄰居預處理 (`cls`)**：
   - 宣告陣列 `cls` 記錄每個索引的最優先鄰居（成本為 $1$ 的目標）。
   - 邊界處理：最左端點 `0` 只能去 `1`；最右端點 `n-1` 只能去 `n-2`。
   - 中間節點：比較 `nums[i] - nums[i-1]` 與 `nums[i+1] - nums[i]` 的大小，將較小的一方設為 `cls[i]`。
2. **建構雙向前綴和 (`dp`, `dp2`)**：
   - **向右走 (`dp`)**：`dp[i]` 代表從 $0$ 走到 $i$ 的總成本。轉移時看 `cls[i-1]` 是否就是 $i$，是的話成本 $+1$，否則加上實際距離。
   - **向左走 (`dp2`)**：`dp2[i]` 代表從右邊界 $n-1$ 往左走 $i$ 步的總成本。轉移時看 `cls[n-i]` 是否就是 $n-1-i$。
3. **$\mathcal{O}(1)$ 查詢回應**：
   - 如果 $l \le r$ (向右走)：答案為 `dp[r] - dp[l]`。
   - 如果 $l > r$ (向左走)：利用右端點為起點的 `dp2`，答案為 `dp2[n-1-r] - dp2[n-1-l]`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + Q)$。計算 `cls`, `dp`, `dp2` 各需遍歷陣列一次 $\mathcal{O}(N)$。處理 $Q$ 筆查詢，每次為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了大小為 $N$ 的輔助陣列 `cls`, `dp`, `dp2`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> minCost(vector<int>& nums, vector<vector<int>>& queries) {
        int n = nums.size();
        
        // 1. 預處理：找出每個節點的最優鄰居 (移動成本為 1 的目標)
        vector<int> cls(n, -1);
        cls[0] = 1;
        cls[n-1] = n-2;
        for(int i = 1; i < n - 1; ++i) {
            int a = nums[i] - nums[i-1];
            int b = nums[i+1] - nums[i];
            // 若左邊距離 <= 右邊距離，則最優鄰居為左邊 (依據題意邏輯)
            if(a <= b) cls[i] = i - 1;
            else cls[i] = i + 1;
        }

        // 2. 建立雙向前綴和陣列
        // dp[i]  := 從 0 移動到 i 的最小總成本 (向右)
        // dp2[i] := 從 n-1 移動到 n-1-i 的最小總成本 (向左)
        vector<int> dp(n, 0);
        vector<int> dp2(n, 0);
        for(int i = 1; i < n; ++i) {
            // 向右移動：從 i-1 走到 i
            dp[i] = dp[i-1] + (cls[i-1] == i ? 1 : nums[i] - nums[i-1]);
            
            // 向左移動：從 n-i 走到 n-1-i
            dp2[i] = dp2[i-1] + (cls[n-i] == n-1-i ? 1 : nums[n-i] - nums[n-1-i]);
        }

        // 3. O(1) 回答查詢
        vector<int> rt;
        for(auto q : queries) {
            int l = q[0], r = q[1];
            if(l <= r) {
                // 往右走
                rt.push_back(dp[r] - (l >= 1 ? dp[l] : 0));
            } else {
                // 往左走：轉換索引以對應 dp2
                rt.push_back(dp2[n-r-1] - (n-l-1 >= 0 ? dp2[n-l-1] : 0));
            }
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