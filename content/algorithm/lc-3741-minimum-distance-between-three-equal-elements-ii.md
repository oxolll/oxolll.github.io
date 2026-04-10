---
title: "LeetCode 3741. Minimum Distance Between Three Equal Elements II"
date: 2026-04-11T10:30:00+08:00
lastmod: 2026-04-11T10:30:00+08:00
difficulty: 1449
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash-table", "math", "greedy"]
keywords: ["LC3741", "1449", "Medium", "Minimum Distance", "Three Equal Elements", "Hash Table"]
description: "LeetCode 第 3741 題：Minimum Distance Between Three Equal Elements II。難度評分：1449。探討在測資範圍放大的情況下，如何利用 Hash Table 安全地分組記錄索引，並透過貪婪滑動視窗與數學距離化簡公式，以 O(N) 時間複雜度求得三等值元素的最小距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3741](https://leetcode.com/problems/minimum-distance-between-three-equal-elements-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1449)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Math` $\cdot$ `Greedy`
> **🔗 系列題連結**：[👉 點此查看 Part I (LeetCode 3740) 題解筆記](../lc-3740-minimum-distance-between-three-equal-elements-i/)

---

## 📝 歷次打卡與更新
- [2026-04-11：初次提交 (Hash Map 索引分組與數學化簡)](#2026-04-11-初次提交)

---

## 💡 2026-04-11 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是 Part I 的進階版。核心的數學推導依然成立：
三個相同數值的索引 $i < j < k$，其兩兩距離總和可化簡為：
$$ Distance = (j - i) + (k - j) + (k - i) = 2 \times (k - i) $$
為了使距離最小，這三個元素在該數值的「出現位置清單」中必須是**連續相鄰**的。

**Part II 的關鍵差異 (陷阱)**：
進階版的測資範圍通常會放大，`nums[i]` 的值可能遠大於陣列長度 `n`。如果使用陣列 `vector<vector<int>>` 來作為桶子 (Buckets) 分類，會直接導致記憶體超載 (MLE) 或陣列越界 (Segmentation Fault)。
因此，我們**必須且只能**使用基於雜湊表的資料結構 `unordered_map` 來記錄每個數值出現的索引列表，以確保空間複雜度被嚴格限制在 $\mathcal{O}(N)$。

### 🛠️ 解題思路 (Approach)
1. **安全索引分組 (Safe Grouping)**：
   - 宣告 `unordered_map<int, vector<int>> idxs`。
   - 遍歷原陣列 `nums`，將每個數值的索引安全地推入對應的列表中。
2. **貪婪尋找最小距離**：
   - 宣告變數 `rt = INT_MAX` 記錄全域最小距離。
   - 走訪 `idxs` 中的每一個索引清單 `list`。
   - 若 `list.size() < 3`，跳過不處理。
   - 利用長度為 3 的滑動視窗遍歷清單，套用公式 `2 * (list[j+2] - list[j])` 計算距離，並更新 `rt`。
3. **回傳結果**：若 `rt` 仍為 `INT_MAX` 回傳 `-1`，否則回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 為陣列長度。走訪陣列與後續遍歷 Hash Map 內容的次數總和均為線性時間。
- **空間複雜度**: $\mathcal{O}(N)$。Hash Map 最多儲存 $N$ 個索引節點，不受 `nums[i]` 實際數值大小的影響。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minimumDistance(vector<int>& nums) {
        int n = nums.size();
        
        // 【關鍵優化】必須使用 unordered_map，防止 nums[i] 過大導致的陣列越界
        unordered_map<int, vector<int>> idxs;
        for(int i = 0; i < n; ++i) {
            idxs[nums[i]].push_back(i);
        }

        int rt = INT_MAX;
        
        // 走訪所有分類好的索引清單
        for(auto& [val, list] : idxs) {
            // 無法構成三等值條件，直接跳過
            if(list.size() < 3) continue;
            
            // 使用滑動視窗檢查相鄰的三個位置
            // 套用化簡公式：2 * (最右側索引 - 最左側索引)
            for(int j = 0; j < list.size() - 2; ++j) {
                rt = min(rt, 2 * (list[j+2] - list[j]));
            }
        }

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