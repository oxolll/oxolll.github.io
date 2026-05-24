---
title: "LeetCode 3927. Minimize Array Sum Using Divisible Replacements"
date: 2026-05-10T20:35:00+08:00
lastmod: 2026-05-10T20:35:00+08:00
difficulty: 1651
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "greedy", "hash-map", "sieve"]
keywords: ["LC3927", "Medium", "Minimize Sum", "Divisibility", "貪心", "埃氏篩"]
description: "LeetCode 第 3927 題：Minimize Array Sum Using Divisible Replacements。探討如何利用因數替換規則，結合貪心策略與類埃氏篩演算法，將陣列總和最小化。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3927](https://leetcode.com/problems/minimize-array-sum-using-divisible-replacements/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1651)</span>  
> **核心主題**：`Greedy` $\cdot$ `Math` $\cdot$ `Hash Map`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個正整數陣列 `nums`。你可以執行以下操作任意次數：
- 選擇兩個下標 $i$ 和 $j$，如果 $nums[i]$ 是 $nums[j]$ 的倍數（即 $nums[i] \pmod{nums[j]} == 0$），則可以將 $nums[i]$ 替換為 $nums[j]$。

請回傳執行操作後，陣列可能達到的**最小總和**。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-10：初次提交 (類埃氏篩貪心合併法)](#2026-05-10-初次提交)

---

## 💡 2026-05-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的核心是一個簡單的貪心規律：若想讓總和最小，我們必須將每個數字替換成它能轉換成的「最小可能值」。根據規則，只要 $A$ 是 $B$ 的倍數， $A$ 就能變身成 $B$。

**核心破局點：**
1. **傳遞性 (Transitivity)**：如果 $A$ 是 $B$ 的倍數，$B$ 是 $C$ 的倍數，那麼 $A$ 最終可以透過 $B$ 變成 $C$。
2. **最小因數歸納**：對於陣列中的任何數 $X$，如果陣列中存在其因數，它應該被替換為這些因數中最小的一個。
3. **頻率統計與合併**：我們不需要真的去修改陣列，只需統計每個數字出現的頻率。從小到大遍歷，當前數字如果是較大數字的因數，就將較大數字的「配額」全部吸收到當前數字身上。

### 🛠️ 解題思路 (Approach)
1. **計數**：使用 `unordered_map` 或陣列統計每個數字的出現次數。
2. **排序去重**：取得陣列中所有不重複的數字並由小到大排序。
3. **篩選與吸收 (The Sieve Logic)**：
   - 由小到大遍歷排序後的唯一數字 `cur`。
   - 如果 `cur` 還存在於 map 中，則枚舉它的倍數 `cur * 2`, `cur * 3`... 直到達到最大範圍 ($10^5$)。
   - 如果倍數存在於 map 中，將該倍數的次數累加到 `cur`，並從 map 中移除該倍數。
   - *注意：從小到大遍歷保證了每個數都會被「吸」到它在陣列中存在的最小因數上。*
4. **求和**：最後遍歷 map，計算 `num * count` 的總和。

### 📊 複雜度分析
- **時間複雜度**: $O(N + M \log M)$。
  - 統計頻率與排序需 $O(N \log N)$。
  - 內層倍數枚舉類似於埃氏篩的複雜度，為 $M(1/2 + 1/3 + 1/4 \dots) = O(M \log M)$，其中 $M$ 是數值最大值。
- **空間複雜度**: $O(M)$。需額外空間存儲頻率統計。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long minArraySum(vector<int>& nums) {
        // 1. 頻率統計
        unordered_map<int, int> mp;
        for(auto num : nums) ++mp[num];

        // 2. 取得排序後的唯一數字清單
        // 修正原代碼順序：應先 sort 再 unique
        sort(nums.begin(), nums.end());
        nums.erase(unique(nums.begin(), nums.end()), nums.end());
        
        int n = nums.size(); 
        const int MAX_VAL = 1e5;

        // 3. 貪心合併：從小到大尋找倍數並吸收
        for(int i = 0; i < n; ++i) {
            int cur = nums[i];
            // 如果當前數字已經被更小的因數吸收了，就跳過
            if(mp.find(cur) == mp.end()) continue;

            // 枚舉 cur 的倍數
            for(int j = 2; (long long)cur * j <= MAX_VAL; ++j) {
                int target = cur * j;
                if(mp.find(target) != mp.end()) {
                    // 將倍數的出現次數轉移給最小因數
                    mp[cur] += mp[target];
                    mp.erase(target);
                }
            }
        }

        // 4. 計算最終最小總和
        long long rt = 0;
        for(auto& [num, c] : mp) {
            rt += (long long)num * c;
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