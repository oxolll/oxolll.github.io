---
title: "LeetCode 3877. Minimum Removals to Achieve Target XOR"
date: 2026-03-22T16:10:00+08:00
lastmod: 2026-03-22T16:10:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic programming", "bit manipulation", "hash table"]
keywords: ["LC3877", "Medium", "N/A", "Minimum Removals", "Target XOR", "0/1 Knapsack"]
description: "LeetCode 第 3877 題：Minimum Removals to Achieve Target XOR。難度評分：Medium (N/A)。探討如何將移除元素的最小化問題，轉換為保留元素的最大化問題，並利用 0/1 動態規劃與 Hash Map 維護 XOR 狀態轉移。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3877](https://leetcode.com/problems/minimum-removals-to-achieve-target-xor/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Bit Manipulation` $\cdot$ `Hash Table`

---

## 📝 歷次打卡與更新
- [2026-03-21：初次提交 (基於 Map 的 0/1 DP 狀態轉移)](#2026-03-21-初次提交)

---

## 💡 2026-03-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求計算達成目標 XOR 值的「最少移除數量」。在數學上，這等價於尋找一個 XOR 總和等於 `target` 的子集合，並使其「包含的元素數量最大化」。計算出最大子集長度後，以陣列總長度減去該長度，即為最少移除數量。

這是一個標準的 **0/1 動態規劃 (0/1 DP)** 問題。對於陣列中的每一個元素 `nums[i]`，皆有兩個獨立的轉移狀態：
1. **不選取** `nums[i]`：先前的 XOR 總和保持不變，選取數量保持不變。
2. **選取** `nums[i]`：新的 XOR 總和為 `原 XOR 總和 ^ nums[i]`，選取數量為 `原數量 + 1`。

由於 XOR 運算的值域範圍可能不連續，使用 `std::map` 來記錄每一種 XOR 值對應的最大元素選取數量，能有效處理稀疏狀態 (Sparse States)。

### 🛠️ 解題思路 (Approach)
1. **初始化 DP 狀態表**：宣告 `map<int, int> mp`，記錄 `XOR值 -> 最大選取數量`。初始狀態為 `mp[0] = 0` (XOR 和為 0 時，選取 0 個元素)。
2. **滾動更新 (Rolling DP)**：
   - 遍歷陣列 `nums`。
   - 宣告一個暫存的 `map<int, int> tmp` 儲存當前回合的新狀態，以避免在迭代過程中修改原始 Map 導致無限迴圈或資料污染。
   - 遍歷上一回合的所有有效狀態 `[key, val]`：
     - **計算新狀態 (選取)**：`pos = key ^ nums[i]`。更新 `tmp[pos]` 為 $\max(\text{歷史最佳長度}, val + 1)$。
     - **維持舊狀態 (不選取)**：將上一回合的狀態繼承至 `tmp[key]`，更新 `tmp[key]` 為 $\max(\text{歷史最佳長度}, mp[key])$。
   - 利用 `std::move` 將 `tmp` 覆寫回 `mp`。
3. **回傳結果**：
   - 檢查 `mp` 中是否存在 `target` 鍵值。若不存在，回傳 `-1`。
   - 若存在，則回傳 `nums.size() - mp[target]` 計算出最少移除數量。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times K \log K)$。其中 $N$ 是陣列長度，$K$ 是在運算過程中產生的相異 XOR 值數量。每次迭代需要對長度最大為 $K$ 的 `map` 進行走訪與插入，`std::map` 的操作成本為 $\mathcal{O}(\log K)$。
- **空間複雜度**: $\mathcal{O}(K)$。動態維護的 `mp` 與 `tmp` 最多儲存 $K$ 個鍵值對。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minRemovals(vector<int>& nums, int target) {
        map<int, int> mp; // 記錄狀態： XOR 值 -> 最大選取的元素數量

        // 初始狀態
        mp[0] = 0;
        
        for(int i = 0; i < nums.size(); ++i) {
            map<int, int> tmp; // 儲存當前迭代的滾動轉移狀態
            
            for(auto [key, val] : mp) {
                // 狀態 1：選取 nums[i]
                int pos = key ^ nums[i];
                if(mp.find(pos) != mp.end())
                    tmp[pos] = max(mp[pos], val + 1);
                else
                    tmp[pos] = val + 1;
                
                // 狀態 2：不選取 nums[i]，保留原狀態
                if(mp.find(key) != mp.end())
                    tmp[key] = max(mp[key], tmp[key]);
                else
                    tmp[key] = mp[key];
            }
            
            // 將計算完畢的新狀態覆寫回主狀態表
            mp = std::move(tmp);
        }

        // 最少移除數 = 陣列總長度 - 目標 XOR 值的最大子集合長度
        return mp.find(target) == mp.end() ? -1 : nums.size() - mp[target];
    }
};
```

*(技術備註：在實作上，若已知 XOR 運算的理論最大值，將 `std::map` 替換為固定長度的一維 `vector<int>` 或是 `std::unordered_map`，能省去紅黑樹 $\mathcal{O}(\log K)$ 的節點配置與搜尋開銷，進一步將時間複雜度優化至 $\mathcal{O}(N \times K)$。)*

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