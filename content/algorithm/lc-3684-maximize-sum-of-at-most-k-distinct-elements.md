---
title: "LeetCode 3684. Maximize Sum of At Most K Distinct Elements"
date: 2026-04-06T20:00:00+08:00
lastmod: 2026-04-06T20:00:00+08:00
difficulty: 1298
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash-table", "heap"]
keywords: ["LC3684", "1298", "Easy", "Top K", "Distinct", "Priority Queue", "Min-Heap"]
description: "LeetCode 第 3684 題：Maximize Sum of At Most K Distinct Elements。難度評分：1298。探討如何結合 Hash Set (去重) 與 Min-Heap (維護 Top K)，以 O(N log K) 的優化時間複雜度找出陣列中最大的 K 個不重複元素。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3684](https://leetcode.com/problems/maximize-sum-of-at-most-k-distinct-elements/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1298)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Heap (Priority Queue)`

---

## 📝 歷次打卡與更新
- [2026-04-06：初次提交 (Hash Set 去重 + Min-Heap 維護 Top K)](#2026-04-06-初次提交)

---

## 💡 2026-04-06 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標是從陣列中挑選出「最多 $K$ 個」且「數值不重複」的元素，並使它們的總和最大。這等價於**找出陣列中最大的 $K$ 個不重複元素**。
解決此類 "Top K" 問題的最優資料結構是 **最小堆積 (Min-Heap)**。
我們不需要對整個陣列進行 $\mathcal{O}(N \log N)$ 的全域排序，只需維護一個大小為 $K$ 的 Min-Heap。當 Heap 的大小超過 $K$ 時，將 Heap 頂端（也就是當前 $K+1$ 個元素中最小的那個）彈出。這樣遍歷結束後，Heap 裡留下的必定是全域最大的 $K$ 個元素。
同時，搭配 `unordered_set` 進行 $\mathcal{O}(1)$ 的去重檢查，確保所有進入 Heap 的元素皆為唯一值。

### 🛠️ 解題思路 (Approach)
1. **宣告資料結構**：
   - `priority_queue<int, vector<int>, greater<>> pq`：宣告一個 Min-Heap，用於動態維護當前最大的 $K$ 個元素。
   - `unordered_set<int> occured`：用於記錄已經處理過的數字，確保元素不重複。
2. **遍歷與維護 Top K**：
   - 走訪陣列 `nums` 中的每一個數字 `num`。
   - 若 `num` 已經存在於 `occured` 集合中，直接跳過 (`continue`)。
   - 若為新數字，將其推入 `pq`，並記錄到 `occured` 集合中。
   - **容量控制**：若推入後 `pq.size() > k`，立刻將 Heap 頂端（最小值）彈出 (`pq.pop()`)，確保 Heap 內永遠只保留最大的 $K$ 個數字。
3. **結果收集與排序**：
   - 宣告結果陣列 `rt`。
   - 將 `pq` 中剩餘的元素逐一取出並推入 `rt`。
   - 由於 Min-Heap 彈出的順序是由小到大，而題目通常預期回傳的陣列或為了計算方便，將 `rt` 進行反向排序 (`sort(rt.rbegin(), rt.rend())`)，使其呈現由大到小的遞減序列。
4. **回傳結果**：回傳 `rt` 陣列。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log K)$。其中 $N$ 是陣列的長度。Hash Set 的去重檢查平均為 $\mathcal{O}(1)$。對每個不重複的元素推入/彈出容量為 $K$ 的 Heap，耗時 $\mathcal{O}(\log K)$。最後對 $K$ 個元素進行排序耗時 $\mathcal{O}(K \log K)$。整體時間複雜度由 $\mathcal{O}(N \log K)$ 主導。
- **空間複雜度**: $\mathcal{O}(N)$。最差情況下（所有元素皆不重複），`occured` 集合需要儲存 $N$ 個元素。Heap 僅佔用 $\mathcal{O}(K)$ 的空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> maxKDistinct(vector<int>& nums, int k) {
        // 宣告 Min-Heap 來維護前 K 大的元素
        priority_queue<int, vector<int>, greater<>> pq;
        unordered_set<int> occured; // 用於去重
        
        for(auto num : nums) {
            // 若數字已處理過，則跳過
            if(occured.find(num) != occured.end()) continue;
            
            pq.push(num);
            occured.insert(num);
            
            // 保持 Heap 大小不超過 K，剔除較小的元素
            if(pq.size() > k) {
                pq.pop();
            }
        }

        vector<int> rt;
        // 將 Heap 中的元素全數取出
        while(!pq.empty()) {
            rt.push_back(pq.top()); 
            pq.pop();
        }
        
        // 排序為由大到小的遞減序列
        sort(rt.rbegin(), rt.rend());
        
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