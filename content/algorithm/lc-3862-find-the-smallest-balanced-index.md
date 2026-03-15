---
title: "LeetCode 3862. Find the Smallest Balanced Index"
date: 2026-03-09T13:05:00+08:00
lastmod: 2026-03-09T13:05:00+08:00
difficulty: 1697
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Prefix Sum", "Math"]
keywords: ["LC3862", "1697", "Medium", "Balanced Index", "Prefix Sum", "Early Pruning"]
description: "LeetCode 第 3862 題：Find the Smallest Balanced Index。難度評分：1697。探討如何利用前綴和思想與防溢位剪枝，以 O(N) 時間找到左右和積相等的平衡索引。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3862](https://leetcode.com/problems/find-the-smallest-balanced-index/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1697)</span>   
> **核心主題**：`Array` $\cdot$ `Prefix Sum` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
如果索引 `i` 左邊所有元素的**總和**，等於其右邊所有元素的**乘積**，則稱該索引為平衡索引 (Balanced Index)。
（若左側沒有元素，總和視為 0；若右側沒有元素，乘積視為 1）。

請回傳**最小的**平衡索引。如果不存在，則回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-09：初次提交 (總和扣除與乘積動態維護，搭配防溢位剪枝)](#2026-03-09-初次提交)

---

## 💡 2026-03-09 初次提交

### 🎯 直覺 (Intuition)
要在陣列中找左右關係，最好的方法就是維護整體狀態。我們可以先算出整個陣列的「總和」，然後從右向左遍歷。每次把當前元素從總和中扣除，剩下的就是「左邊的和」。同時維護一個從右往左累積的「乘積」。
這題最大且最致命的陷阱在於：右邊的乘積會暴增到超出 `long long` 的範圍！但既然陣列長度最多 $10^5$，單一元素最大 $10^9$，左邊的總和極限也才 $10^{14}$。所以只要乘積超過這個極限，左邊的總和就永遠追不上了，此時直接 `break` 即可完美避開溢位！

### 🛠️ 解題思路 (Approach)
1. **預算總和**：先用一次遍歷求出所有元素的總和 `sum`。
2. **逆向推演**：從陣列最後面 `i = nums.size() - 1` 開始往前遍歷。
3. **動態更新左和與右積**：
   - 先將 `nums[i]` 從 `sum` 中扣除（此時 `sum` 就是嚴格在 `i` 左邊的總和）。
   - 若 `sum == prod`，則更新答案 `res = i`（因為我們是從右往左掃，覆蓋下去最後留下的就會是「最小索引」）。
4. **防溢位剪枝 (Pruning)**：
   - 下一輪的乘積會是 `prod * nums[i]`。
   - 由於最大的可能總和為 $10^{14}$，如果 `prod * nums[i] > 1e14`，後續的乘積只會越來越大，絕對不可能等於任何左側總和，因此提早 `break` 跳出迴圈，防止溢位並加速執行。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。由於我們加入了 `1e14` 的剪枝，在多數情況下甚至遠低於 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(1)$。只維護了和與乘積。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int smallestBalancedIndex(vector<int>& nums) {
        long long sum = 0;
        long long prod = 1;
        
        // 1. 預先計算總和
        for(int i = 0; i < nums.size(); ++i)
            sum += nums[i];
            
        int res = -1;
        // 2. 從右往左遍歷，維護左側和與右側乘積
        for(int i = nums.size() - 1; i >= 0; --i) {
            sum -= nums[i]; // sum 變為嚴格左側的總和
            
            if(sum == prod)
                res = i; // 從右往左更新，最後留下的一定是最小的索引
                
            // 3. 剪枝與防溢位：左側最大和不可能超過 10^14
            if(prod > 1e14 / nums[i]) break; 
            
            prod *= nums[i];
        }
        return res;
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