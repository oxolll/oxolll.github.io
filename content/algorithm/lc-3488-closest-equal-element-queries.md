---
title: "LeetCode 3488. Closest Equal Element Queries"
date: 2026-04-16T15:00:00+08:00
lastmod: 2026-04-16T15:00:00+08:00
difficulty: 1699
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary-search", "hash-table"]
keywords: ["LC3488", "1699", "Medium", "Closest Equal Element Queries", "Binary Search", "Circular Array"]
description: "LeetCode 第 3488 題：Closest Equal Element Queries。難度評分：1699。探討如何利用全域陣列進行 O(N) 的索引分組，並結合二分搜尋 (Binary Search) 與環狀距離公式，以 O(Q log N) 解決環狀陣列的最短同值距離查詢。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3488](https://leetcode.com/problems/closest-equal-element-queries/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1699)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search` $\cdot$ `Hash Table`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為 $n$ 的整數陣列 `nums`（將其視為一個**環狀陣列**），以及一個查詢陣列 `queries`。
對於每一個查詢 `queries[i] = q`，你需要找到在 `nums` 陣列中與 `nums[q]` 數值相同的另一個元素，並且計算出該元素與索引 `q` 之間的最短環狀距離。
如果 `nums` 中不存在其他數值等於 `nums[q]` 的元素，則該查詢的回傳值為 `-1`。

環狀陣列中兩個索引 $i$ 與 $j$ 的最短距離定義為：$\min(|i - j|, n - |i - j|)$。

### 限制條件
- $1 \le n \le 10^5$
- $1 \le nums[i] \le 10^6$
- $1 \le queries.length \le 10^5$
- $0 \le queries[i] < n$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-16：初次提交 (全域分組與二分搜尋環狀查找)](#2026-04-16-初次提交)

---

## 💡 2026-04-16 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道結合「元素追蹤」與「環狀距離計算」的查詢題。
為了解決多筆查詢，我們需要**預處理**：將相同數值的索引分門別類記錄下來。由於陣列是循序走訪的，紀錄下來的索引清單必定是**嚴格遞增**的。
當我們查詢索引 `q` 時，其目標值為 `nums[q]`。
1. 若該數值的索引清單長度 $\le 1$，代表它是獨一無二的，回傳 `-1`。
2. 若存在多個，距離 `q` 最近的同值元素，必定是該索引清單中，排在 `q` **正前方** 或 **正後方** 的元素。
由於這是一個環狀陣列，我們可以直接透過 `(k + 1) % sz` 找到右鄰居，透過 `(k - 1 + sz) % sz` 找到左鄰居。最後套用環狀距離公式取最小值即可。

### 🛠️ 解題思路 (Approach)
1. **全域陣列優化 (競技程式技巧)**：
   - 由於 `nums[i] \le 10^6`，宣告 `vector<int> cols[1000005]` 於全域。這能避免每次執行函式時重新配置大量記憶體導致的 TLE。
2. **預處理索引分組**：
   - 走訪 `nums`，將索引 `i` 推入 `cols[nums[i]]`。
3. **處理查詢**：
   - 對於每個查詢 `q`，找出目標值 `tar = nums[q]`。
   - 若 `cols[tar].size() <= 1`，結果加入 `-1`。
   - 否則，利用 `lower_bound` 找出 `q` 在 `cols[tar]` 清單中的確切位置 `k`。
   - 計算前一個索引 `backward = cols[tar][(k - 1 + sz) % sz]` 與後一個索引 `forward = cols[tar][(k + 1) % sz]`。
   - 計算到達這兩個鄰居的環狀距離，並取最小值加入結果中。
4. **精準資源回收 (Garbage Collection)**：
   - 為了讓全域陣列在下一筆測資中能正常使用，我們不需要清空 $10^6$ 個桶子。只需走訪一次原陣列 `nums`，將有出現過的數字對應的 `cols` 清空即可：`cols[num].clear()`。這能省下極大的常數時間。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + Q \log F)$。其中 $N$ 是 `nums` 的長度，$Q$ 是查詢的數量，$F$ 是某個數字的最大出現頻率 ($F \le N$)。建立分組與清除分組耗時 $\mathcal{O}(N)$，每次查詢使用二分搜尋耗時 $\mathcal{O}(\log F)$。整體執行時間極快。
- **空間複雜度**: $\mathcal{O}(\max(nums[i]) + N)$。全域陣列佔用固定空間 $\approx 10^6$ 個 `vector` 標頭，所有桶子內部儲存的元素總數剛好為 $N$。

### 💻 程式碼實作 (C++)
```cpp
// 競技程式設計技巧：全域宣告避免頻繁的動態記憶體配置
vector<int> cols[1000005]; 

class Solution {
public:
    vector<int> solveQueries(vector<int>& nums, vector<int>& queries) {
        int n = nums.size();
        
        // 1. 索引分組：將相同數值的索引收集在一起 (天然保持遞增)
        for(int i = 0; i < n; ++i) {
            cols[nums[i]].push_back(i);
        }
        
        vector<int> rt;
        // 2. 處理查詢
        for(auto& q : queries) {
            int tar = nums[q];
            
            // 若該數字只出現過一次，代表無其他同值元素
            if(cols[tar].size() <= 1) {
                rt.push_back(-1);
            } else {
                int sz = cols[tar].size();
                
                // 使用二分搜尋快速定位當前查詢索引 q 在清單中的位置 k
                auto k = lower_bound(cols[tar].begin(), cols[tar].end(), q) - cols[tar].begin();
                
                // 尋找清單中的環狀右鄰居與左鄰居
                int forward = cols[tar][(k + 1) % sz];
                int backward = cols[tar][(k - 1 + sz) % sz];
                
                // 計算到達兩個鄰居的最短環狀距離
                // 環狀距離公式：min(|a - b|, n - |a - b|)
                int left = min(abs(forward - q), abs(forward + n - q));
                int right = min(abs(backward - q), abs(backward - n - q));
                
                rt.push_back(min(left, right));
            }
        }

        // 3. 精準清除：只清空有使用到的桶子，避免 O(max_val) 的 memset 導致 TLE
        for(auto num : nums) {
            cols[num].clear();
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