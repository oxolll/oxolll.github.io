---
title: "LeetCode 3740. Minimum Distance Between Three Equal Elements I"
date: 2026-04-11T10:00:00+08:00
lastmod: 2026-04-11T10:00:00+08:00
difficulty: 1287
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash-table", "math", "greedy"]
keywords: ["LC3740", "1287", "Easy", "Minimum Distance", "Three Equal Elements", "Hash Table"]
description: "LeetCode 第 3740 題：Minimum Distance Between Three Equal Elements I。難度評分：1287。探討如何利用 Hash Table 分組記錄索引，並透過貪婪滑動視窗與數學距離化簡公式，以 O(N) 時間複雜度安全且高效地求得三等值元素的最小距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3740](https://leetcode.com/problems/minimum-distance-between-three-equal-elements-i/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1287)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Math` $\cdot$ `Greedy`

---

## 📝 歷次打卡與更新
- [2026-04-11：初次提交 (索引分組與數學化簡)](#2026-04-11-初次提交)

---

## 💡 2026-04-11 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
題目的目標是找出陣列中三個數值相同的元素，並使其距離總和最小。
假設這三個元素在原陣列中的索引分別為 $i, j, k$ 且滿足 $i < j < k$。
根據距離定義，兩兩之間的距離總和為：
$$ Distance = (j - i) + (k - j) + (k - i) = 2 \times (k - i) $$

從數學公式中可以發現一個極其重要的性質：**中間元素 $j$ 的具體位置並不影響最終的距離總和**，距離完全由最左側的 $i$ 與最右側的 $k$ 決定。
因此，為了讓 $2 \times (k - i)$ 盡可能小，我們挑選的三個相同元素在「該數值的索引清單」中**必須是連續相鄰的**。我們只需要將每個數值出現的索引分類收集起來，然後在每個清單中利用大小為 3 的滑動視窗檢查每一組連續的三個索引即可。

### 🛠️ 解題思路 (Approach)
1. **索引分組 (Grouping)**：
   - 使用 `unordered_map<int, vector<int>> idxs` 來儲存陣列中每個數值出現的索引列表。使用 Hash Map 可避免因數值過大 (如 $10^9$) 造成的記憶體越界問題。
   - 遍歷原陣列 `nums`，將索引 `i` 推入對應的 `idxs[nums[i]]` 中。
2. **貪婪尋找最小距離**：
   - 宣告變數 `rt = INT_MAX` 記錄全域最小距離。
   - 走訪 `idxs` 中的每一個索引清單。
   - 若該清單的長度小於 3 (`size < 3`)，代表該數值無法湊齊三個，直接跳過。
   - 利用迴圈遍歷該清單，檢查所有長度為 3 的連續子陣列 (即索引從 `j` 到 `j + 2`)。
   - 根據化簡公式，計算距離為 `2 * (清單[j+2] - 清單[j])`，並利用 `min` 嘗試更新 `rt`。
3. **回傳結果**：
   - 迴圈結束後，若 `rt` 仍為 `INT_MAX`，代表陣列中沒有任何數值出現至少三次，回傳 `-1`。否則回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 為陣列長度。走訪原陣列建立 Hash Map 需 $\mathcal{O}(N)$，後續遍歷所有索引清單的次數總和也恰好等於 $N$，整體時間複雜度為線性。
- **空間複雜度**: $\mathcal{O}(N)$。Hash Map `idxs` 總共需要儲存 $N$ 個索引值。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumDistance(vector<int>& nums) {
        int n = nums.size();
        // 使用 unordered_map 防止當 nums[i] 數值極大時導致陣列越界
        unordered_map<int, vector<int>> idxs;
        
        // 將每個數字出現的索引分類記錄
        for(int i = 0; i < n; ++i) {
            idxs[nums[i]].push_back(i);
        }

        int rt = INT_MAX;
        
        // 遍歷所有分類好的索引清單
        for(auto& [val, list] : idxs) {
            // 如果該數字出現次數不到 3 次，無法構成條件，直接跳過
            if(list.size() < 3) continue;
            
            // 使用長度為 3 的滑動視窗 (即索引 j 到 j+2)
            // 數學化簡：距離總和 = (j - i) + (k - j) + (k - i) = 2 * (k - i)
            for(int j = 0; j < list.size() - 2; ++j) {
                rt = min(rt, 2 * (list[j+2] - list[j]));
            }
        }

        // 若 rt 未被更新，代表無解
        return rt == INT_MAX ? -1 : rt;
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