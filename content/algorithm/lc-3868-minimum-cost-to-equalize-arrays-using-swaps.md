---
title: "LeetCode 3868. Minimum Cost to Equalize Arrays Using Swaps"
date: 2026-03-15T15:13:00+08:00
lastmod: 2026-03-15T15:13:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Hash Table", "Greedy", "Math"]
keywords: ["LC3868", "N/A", "Medium", "Equalize Arrays", "Minimum Swaps"]
description: "LeetCode 第 3868 題：Minimum Cost to Equalize Arrays Using Swaps。難度評分：1800。探討如何利用 Hash Table 統計頻率差異，並透過嚴謹的貪心策略證明求得最少交換次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3868](https://leetcode.com/problems/minimum-cost-to-equalize-arrays-using-swaps/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>    
> **核心主題**：`Hash Table` $\cdot$ `Greedy` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個長度相同的整數陣列 `nums1` 與 `nums2`。
你可以執行任意次數的交換操作：每次選擇 `nums1` 中的一個元素與 `nums2` 中的一個元素進行交換。
目標是讓 `nums1` 與 `nums2` 包含完全相同的元素集合（即每個數字在兩個陣列中的出現頻率必須相同）。
請回傳達成目標所需的**最少交換次數**。如果無法達成，則回傳 `-1`。

### 限制條件
- 陣列長度及數值範圍依題目設定。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (頻率差值統計與貪心數學解)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
要讓兩個陣列的元素分佈一模一樣，代表對於任何一個數字 $x$，它在兩個陣列中的出現次數必須完全相等。
這衍生出兩個直接的結論：
1. **無解條件**：如果數字 $x$ 在兩個陣列中的**總出現次數為奇數**，那麼絕對不可能平分，直接回傳 `-1`。
2. **目標狀態**：如果總次數為偶數，那麼數字 $x$ 在單一陣列中的「目標頻率」就是 `總次數 / 2`。

如果某個數字在 `nums1` 裡的數量「超過」了目標頻率，這些多出來的份額就是「盈餘 (Surplus)」，必須被交換出去。

### 🧠 嚴謹數學證明：為什麼算盈餘除以 2 就是最佳解？
在寫出貪心解時，我們常會懷疑：「會不會有某種情況下，一次交換無法同時解決兩個問題，導致需要更多的步數？」
其實不會，證明如下：

1. **盈餘守恆定律**：
   因為 `nums1` 和 `nums2` 的長度相同。如果 `nums1` 在某些數字上有 $K$ 個「盈餘」（必須丟給 `nums2`），那麼在其他數字上，`nums1` 必定有 $K$ 個「赤字」（必須從 `nums2` 拿過來）。
   相對的，`nums1` 的盈餘，正好就是 `nums2` 的赤字；`nums2` 的盈餘，正好就是 `nums1` 的赤字。
2. **完美的一換二 (Perfect Swaps)**：
   每一次交換，我們都可以從 `nums1` 挑選一個它盈餘的數字 $A$，並從 `nums2` 挑選一個它盈餘的數字 $B$ 進行交換。
   - 交換後，`nums1` 失去了一個多餘的 $A$，得到了一個它需要的 $B$。
   - `nums2` 失去了一個多餘的 $B$，得到了一個它需要的 $A$。
   - **這一次交換，完美地同時消除了兩個盈餘狀態（一個來自 `nums1`，一個來自 `nums2`）！**
3. **結論**：
   由於每次交換必定能精準扣除兩邊各 1 個單位的盈餘，因此：
   $$\text{最少交換次數} = \text{nums1 的總盈餘數量} = \text{nums2 的總盈餘數量}$$
   在程式碼中，我們是將**所有數字**（包含 `nums1` 和 `nums2`）的盈餘全部加總，所以得到的和會是 `2 * 總盈餘數量`。最後除以 2，就是精確的最少交換次數。

### 🛠️ 解題思路 (Approach)
1. 使用三個 Hash Table 分別統計：
   - `cnt`: 兩個陣列的總合頻率。
   - `cnt1`: `nums1` 的頻率。
   - `cnt2`: `nums2` 的頻率。
2. 遍歷總頻率表 `cnt`：
   - 檢查是否為奇數，若為奇數即刻回傳 `-1`。
   - 計算該數字的盈餘：取 `cnt1` 與 `cnt2` 較大的一方，扣掉目標頻率 `val / 2`。
   - 將盈餘累加至變數 `rt`。
3. 回傳 `rt / 2`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。遍歷兩次陣列建立 Hash Table，再遍歷一次 Hash Table 進行統計，時間開銷與陣列長度成正比。
- **空間複雜度**: $\mathcal{O}(U)$，其中 $U$ 為陣列中不重複數字的數量。三個 Hash Table 所需的空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minCost(vector<int>& nums1, vector<int>& nums2) {
        unordered_map<int, int> cnt;  // 記錄總頻率
        unordered_map<int, int> cnt1; // 記錄 nums1 頻率
        unordered_map<int, int> cnt2; // 記錄 nums2 頻率
    
        for(auto num : nums1) {
            ++cnt[num];
            ++cnt1[num];
        }
        
        for(auto num : nums2) {
            ++cnt[num];
            ++cnt2[num];   
        }

        int rt = 0;
        for(auto [key, val] : cnt) {
            // 總數為奇數，絕對無法平分
            if(val % 2 == 1) return -1;
            
            // 兩邊數量已經相等，不需處理
            if(cnt1[key] == cnt2[key]) continue;
            
            // 計算盈餘並累加 (只算較多那一邊多出來的部分)
            rt += max(cnt1[key], cnt2[key]) - val / 2;
        }
        
        // rt 包含了 nums1 與 nums2 兩邊的盈餘總和
        // 因為一次 swap 可以同時解決兩邊各一個盈餘，故除以 2
        return rt / 2;
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