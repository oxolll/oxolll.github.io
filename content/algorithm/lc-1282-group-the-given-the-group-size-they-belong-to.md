---
title: "LeetCode 1282. Group the People Given the Group Size They Belong To"
date: 2026-03-26T15:38:00+08:00
lastmod: 2026-03-26T15:38:00+08:00
difficulty: 1267
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash table", "greedy"]
keywords: ["LC1282", "1267", "Medium", "Group the People", "Array Grouping"]
description: "LeetCode 第 1282 題：Group the People Given the Group Size They Belong To。難度評分：1267。探討如何利用靜態陣列取代動態 Hash Map 進行分類，以 O(N) 時間與空間複雜度高效完成分組操作。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1282](https://leetcode.com/problems/group-the-people-given-the-group-size-they-belong-to/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1267)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
有 $n$ 個人被分成若干個未知的群組。每個人被標記為從 0 到 $n - 1$ 的唯一 ID。
給定一個整數陣列 `groupSizes`，其中 `groupSizes[i]` 代表第 $i$ 個人所在的群組大小。例如，如果 `groupSizes[1] = 3`，則代表 ID 為 1 的人必須在一個大小嚴格為 3 的群組中。

請回傳一個陣列，包含這些人成功分組後的結果。每個人都必須**恰好**存在於一個群組中，且每個群組的大小必須與其成員的 `groupSizes` 值相符。
題目保證至少存在一種有效的解答。若有多種解，回傳任意一種皆可。

### 限制條件
- $groupSizes.length == n$
- $1 \le n \le 500$
- $1 \le groupSizes[i] \le n$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (靜態陣列分類法)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一個典型的分組問題。由於題目保證有解，我們只需將要求相同群組大小的人集中在一起。一旦該群組的人數達到其要求的大小，就將該群組確立並存入結果陣列，隨後清空該分類以接收下一批要求相同大小的人。
為了追求最佳執行效能，可以捨棄常數開銷較大的 `std::unordered_map`。因為 `groupSizes[i]` 的最大值不會超過 $n$，我們可以直接宣告一個大小為 $n + 1$ 的二維陣列 `group` 作為靜態分類的載體。

### 🛠️ 解題思路 (Approach)
1. **初始化資料結構**：宣告結果矩陣 `rt`。宣告分類陣列 `group`，大小為 $n + 1$，每個元素為一個一維 `vector<int>`。
2. **單次遍歷與分組**：
   - 走訪 `groupSizes` 陣列，取得每個人要求的群組大小 `num`。
   - 將當前人物 ID `i` 推入對應的分類陣列 `group[num]` 中。
   - 檢查該分類陣列目前的長度是否已達到 `num`。
   - 若達到，代表該群組已滿，將 `group[num]` 推入結果陣列 `rt`，並呼叫 `clear()` 清空 `group[num]`，以便後續相同要求的人繼續使用。
3. **回傳結果**：走訪結束後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是 `groupSizes` 的長度。只需走訪陣列一次。`push_back` 與 `clear` 攤提後的複雜度皆為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N)$。除了結果矩陣外，宣告了大小為 $N + 1$ 的二維陣列 `group`。在任何時刻，`group` 內部儲存的總元素數量不會超過 $N$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> groupThePeople(vector<int>& groupSizes) {
        int n = groupSizes.size();
        vector<vector<int>> rt;
        
        // 利用陣列索引直接對應 groupSize，省去 Hash Map 的常數開銷
        vector<vector<int>> group(n + 1, vector<int>());
        
        for(int i = 0; i < n; ++i) {
            int num = groupSizes[i];
            group[num].push_back(i);
            
            // 當該群組收集到指定人數時，封裝並推入結果陣列，然後清空狀態
            if(group[num].size() == num) {
                rt.push_back(group[num]);
                group[num].clear();
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