---
title: "LeetCode 3920. Maximize Fixed Points After Deletions"
date: 2026-05-04T14:10:00+08:00
lastmod: 2026-05-04T14:10:00+08:00
difficulty: 2263
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary-search", "dynamic-programming", "lis"]
keywords: ["LC3920", "Hard", "Fixed Points", "LIS", "最長遞增子序列", "二維偏序"]
description: "LeetCode 第 3920 題：Maximize Fixed Points After Deletions。探討如何將「陣列刪除與位移」問題，透過數學條件重塑，優雅地轉換為二維偏序排序，最終以 O(N log N) 的最長非遞減子序列 (LIS) 演算法完美破解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3920](https://leetcode.com/problems/maximize-fixed-points-after-deletions/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2263)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search` $\cdot$ `Dynamic Programming (LIS)`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個 0-indexed 的整數陣列 `nums`。
一個索引 $i$ 被稱為 **固定點 (Fixed Point)**，如果滿足 $nums[i] == i$。
你可以對陣列執行「刪除元素」的操作任意次數（每次刪除一個元素，其後方的元素會往前遞補，索引減一）。
請計算在進行最佳的刪除操作後，陣列中**最多**可以擁有多少個固定點。

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-04：初次提交 (條件重塑 + 二維偏序 LIS)](#2026-05-04-初次提交)

---

## 💡 2026-05-04 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題如果正面思考「該刪除誰」，會陷入無窮的狀態分支。
真正的破局點在於「反向思考」：**哪些元素有資格成為固定點，且它們之間如何共存？**

**1. 成為固定點的基本條件**
一個數字 $nums[i]$ 若要在刪除後變成固定點，代表它的新索引必須等於 $nums[i]$。
這意味著，它必須向前移動 `i - nums[i]` 個位置。
因為我們只能刪除元素（讓後面的元素往前補），所以：
- 移動步數不能為負：$i \ge nums[i]$ 是成為固定點的**先決條件**。
- 移動步數即為「它前面被刪除的元素數量」。我們定義這個需求量為 $dec = i - nums[i]$。

**2. 多個固定點的共存條件 (二維偏序)**
假設我們選中了兩個數字 $A$ 和 $B$ 作為最終的固定點，且原陣列中 $A$ 在 $B$ 的前面 ($i_A < i_B$)。
為了讓兩者同時成為固定點，它們必須滿足：
- **數值大小**：$val_A < val_B$ (因為索引必定是遞增的)。
- **刪除需求量**：$B$ 前面被刪除的數量，至少要包含 $A$ 前面被刪除的數量。也就是 $dec_A \le dec_B$。

### 🛠️ 解題思路 (Approach)
這就完美轉換成了一個經典的**「二維偏序 (2D Poset)」問題**！
1. **收集候選人**：
   走訪陣列，若 $i \ge nums[i]$，將一個 `pair<val, dec>` 存入 `sorted` 陣列中。
2. **第一維排序 (數值遞增)**：
   對 `sorted` 陣列進行排序。第一維 $val$ 由小到大排；若 $val$ 相同，為了避免在後續的 LIS 中同時選中兩個相同的數值，我們將第二維 $dec$ **由大到小排** (確保相同的 $val$ 最多只會被選中一次)。
3. **第二維求解 LIS (非遞減子序列)**：
   在保證第一維合法的順序下，我們只需要在第二維 $dec$ 找出一個**最長非遞減子序列 (Longest Non-Decreasing Subsequence)**。
   使用經典的 `upper_bound` 搭配 DP 陣列（Patience Sorting 概念）在 $\mathcal{O}(N \log N)$ 內秒殺。

*(註：這裡使用 `upper_bound` 是因為 $dec_A \le dec_B$ 是被允許的，若是嚴格遞增則需使用 `lower_bound`。)*

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。過濾候選人需 $\mathcal{O}(N)$，排序候選人需 $\mathcal{O}(N \log N)$，對第二維求解 LIS 需 $\mathcal{O}(N \log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。用於儲存候選人陣列 `sorted` 以及 LIS 的狀態陣列 `dp`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 自訂排序規則：
    // 1. 第一維 (數值 val) 由小到大排序
    // 2. 當數值相同時，第二維 (刪除量 dec) 由大到小排序 (避免相同數值在 LIS 中被重複選取)
    static bool cmp(pair<int, int>& a, pair<int, int>& b) {
        if(a.first != b.first) return a.first < b.first;
        return a.second > b.second;
    }
    
    int maxFixedPoints(vector<int>& nums) {
        int n = nums.size();
        
        // sorted 儲存候選人：{val, decrease_num}
        vector<pair<int, int>> sorted; 
        for(int i = 0; i < n; ++i) {
            // 先決條件：原始索引必須大於等於數值，才有往前移動的空間
            if(i >= nums[i]) {
                sorted.push_back({nums[i], i - nums[i]});
            }
        }

        // 進行二維偏序排序
        sort(sorted.begin(), sorted.end(), cmp);
        
        // 對第二維 (decrease_num) 求解 LIS (最長非遞減子序列)
        vector<int> dp;
        for(auto p : sorted) {
            int dec = p.second;
            // 因為允許相同的 dec 共存 (非嚴格遞增)，使用 upper_bound
            auto it = upper_bound(dp.begin(), dp.end(), dec);
            
            if(it == dp.end()) {
                dp.push_back(dec); // 可以接在最後面，LIS 長度加一
            } else {
                *it = dec;         // 替換掉較大的元素，增加後續潛力
            }
        }

        // DP 陣列的大小即為最長合法子序列的長度，也是最多能擁有的固定點數量
        return dp.size();
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