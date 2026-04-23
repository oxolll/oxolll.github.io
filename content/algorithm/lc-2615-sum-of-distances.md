---
title: "LeetCode 2615. Sum of Distances"
date: 2026-04-23T10:45:00+08:00
lastmod: 2026-04-23T10:45:00+08:00
difficulty: 1793
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash-table", "prefix-sum", "math"]
keywords: ["LC2615", "Medium", "1793", "Sum of Distances", "Prefix Sum"]
description: "LeetCode 第 2615 題：Sum of Distances。難度評分：1793。探討如何利用雜湊表將相同數值的索引分組，並透過前綴和 (Prefix Sum) 結合嚴謹的數學絕對值拆解公式，以 O(N) 線性時間複雜度極速算出距離總和。本版包含記憶體預分配優化。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2615](https://leetcode.com/problems/sum-of-distances/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1793)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Prefix Sum` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為 $n$ 的整數陣列 `nums`。
請你回傳一個長度為 $n$ 的整數陣列 `arr`，其中 `arr[i]` 的值等於所有滿足 `nums[j] == nums[i]` 且 $j \neq i$ 的 $|i - j|$ 之總和。如果不存在這樣的 $j$，則 `arr[i]` 等於 $0$。

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-23：初次提交 (雜湊分組與前綴和絕對值拆解)](#2026-04-23-初次提交)
- [2026-04-23：優化更新 (陣列空間預分配，消除 Reverse 開銷)](#2026-04-23-優化更新)

---

## 💡 2026-04-23 優化更新

### 🎯 演算法分析 (Algorithm Analysis)
如果對於每個位置都去遍歷尋找相同的值並計算距離，時間複雜度將會是 $\mathcal{O}(N^2)$。
為了達成 $\mathcal{O}(N)$ 的解法，我們必須採用 **分組處理** 與 **前綴和 (Prefix Sum)**。

**數學推導 (絕對值拆解)**：
假設某個數值在陣列中出現的索引列表為 $x$（且 $x$ 天然是遞增的）。對於其中第 $i$ 個索引 $x[i]$，其距離總和為：
$$Sum = \sum_{j=0}^{sz-1} |x[i] - x[j]|$$
因為 $x$ 是遞增的，我們可以直接去掉絕對值符號，將其拆分為「左半部」與「右半部」：
1. **左半部 ($j < i$)**：此時 $x[i] > x[j]$。
   左半總和 = $\sum_{j=0}^{i-1} (x[i] - x[j]) = i \cdot x[i] - \sum_{j=0}^{i-1} x[j]$
2. **右半部 ($j > i$)**：此時 $x[j] > x[i]$。
   右半總和 = $\sum_{j=i+1}^{sz-1} (x[j] - x[i]) = \sum_{j=i+1}^{sz-1} x[j] - (sz - 1 - i) \cdot x[i]$

將上述兩式相加，並利用預先建構好的前綴和陣列 `pre` 替換 $\sum x[j]$，即可得到 $\mathcal{O}(1)$ 的狀態轉移公式：
$$Total = x[i] \cdot (i - (sz - 1 - i)) + (pre[sz-1] - pre[i]) - pre[i-1]$$
（註：實作中需注意邊界 $i=0$ 時 `pre[i-1]` 為 $0$）。

### 🛠️ 解題思路 (Approach)
1. **雜湊分組**：
   - 走訪 `nums`，利用 `unordered_map<int, vector<int>> mp` 記錄每個數值出現的所有索引位置。
2. **前綴和運算與公式套用**：
   - 準備 `res` Hash Map 儲存每個數值對應的距離總和陣列。
   - 遍歷 `mp` 中的每個群組 `x`。
   - 計算該群組的索引前綴和 `pre`。
   - 走訪群組內的每個元素 `x[i]`，套用數學推導公式計算距離總和，並存入 `res[num]`。
3. **記憶體預分配與還原**：
   - 預先宣告大小為 $n$ 的解答陣列 `vector<long long> rt(n, 0)`。
   - 從 `nums` 的尾端開始倒序取出 `res[nums[i]].back()` 直接精準寫入 `rt[i]` 中，並將其彈出 (`pop_back`)。此作法完美避開了重新反轉陣列的效能開銷。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列長度。分組、計算前綴和、代入公式與精準填入結果皆為線性掃描，無反轉開銷。
- **空間複雜度**: $\mathcal{O}(N)$。使用 Hash Map 儲存索引分組與暫存結果，佔用線性空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<long long> distance(vector<int>& nums) {
        int n = nums.size();

        // 1. 將相同數值的索引分組
        unordered_map<int, vector<int>> mp;
        for(int i = 0; i < n; ++i) {
            mp[nums[i]].push_back(i);
        }

        // 2. 針對每個分組進行前綴和與距離計算
        unordered_map<int, vector<long long>> res;
        for(auto& [num, x] : mp) {
            int sz = x.size();
            vector<long long> pre(sz, 0);
            
            // 計算前綴和
            for(int i = 0; i < sz; ++i) {
                pre[i] = (i > 0 ? pre[i-1] : 0) + x[i];
            }

            // 套用絕對值拆解公式，以 O(1) 時間計算每個位置的距離總和
            for(int i = 0; i < sz; ++i) {
                // 左邊數量 i，右邊數量 (sz - i - 1)
                // x[i] * (左邊數量 - 右邊數量) + 右邊總和 - 左邊總和
                long long current_val = x[i] * 1LL * (i - (sz - i - 1)) + 
                                        (pre[sz-1] - pre[i]) - 
                                        (i > 0 ? pre[i-1] : 0);
                res[num].push_back(current_val);
            }
        }

        // 3. 空間預分配優化：直接將計算結果精準填入對應的索引，省去 reverse 操作
        vector<long long> rt(n, 0);
        for(int i = n - 1; i >= 0; --i) {
            rt[i] = res[nums[i]].back();
            res[nums[i]].pop_back();
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