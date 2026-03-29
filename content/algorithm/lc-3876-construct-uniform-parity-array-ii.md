---
title: "LeetCode 3876. Construct Uniform Parity Array II"
date: 2026-03-22T16:05:00+08:00
lastmod: 2026-03-22T16:05:00+08:00
difficulty: 1443
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "sorting"]
keywords: ["LC3876", "Medium", "1443", "Uniform Parity", "Sorting"]
description: "LeetCode 第 3876 題：Construct Uniform Parity Array II。難度評分：Medium (1443)。探討如何利用排序尋找極值，並透過最小值之奇偶性判定整個陣列是否具備達成一致奇偶性的充分條件。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3876](https://leetcode.com/problems/construct-uniform-parity-array-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1443)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Sorting`   
> **基礎版本**：🔗 [👉 點此查看本題的基礎版本：3875. Construct Uniform Parity Array I](../lc-3875-construct-uniform-parity-array-i/)

---

## 📝 歷次打卡與更新
- [2026-03-22：初次提交 (排序與最小值奇偶性判定)](#2026-03-22-初次提交)

---

## 💡 2026-03-22 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題加入了更嚴格的陣列操作限制。陣列元素之間互相操作改變奇偶性的可行性，嚴格依賴於陣列中**最小值**的奇偶狀態。
若陣列中存在至少一個奇數，則必須利用該奇數與其他偶數進行操作以統一奇偶性。若陣列中的最小值為偶數，則無法透過減法或其他規定的數學運算將較大的奇數轉換為偶數而不產生負值（或違反規則）。
因此，演算法的核心邏輯為：
1. 若陣列最小元素為奇數，必定可以轉換所有元素，回傳 `true`。
2. 若陣列最小元素為偶數，則陣列中**絕對不可**出現任何奇數。若出現，則回傳 `false`。

### 🛠️ 解題思路 (Approach)
1. **排序尋找極值**：呼叫 `sort(nums1.begin(), nums1.end())` 將陣列遞增排序，使得 `nums1[0]` 成為全域最小值。
2. **提取初始狀態**：設定 `state = nums1[0] % 2` 記錄最小值的奇偶性。
3. **線性驗證**：
   - 遍歷整個陣列。若 `nums1[i]` 的奇偶性與 `state` 相同，則符合條件，`continue`。
   - 若發生奇偶性不一致，進一步檢查：若 `state == 0`（最小值為偶數）且當前元素為奇數 (`nums1[i] % 2 == 1`)，則觸發不可能的狀態，立即回傳 `false`。
4. **回傳結果**：若迴圈順利結束，回傳 `true`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。主要的計算開銷來自對陣列 `nums1` 的排序操作。後續遍歷驗證的時間為 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(\log N)$ 或 $\mathcal{O}(1)$，取決於 C++ 內部 `std::sort` 實作時的遞迴深度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool uniformArray(vector<int>& nums1) {
        // 1. 遞增排序，取得全域最小值
        sort(nums1.begin(), nums1.end());
        
        // 2. 記錄最小值的奇偶狀態 (0 為偶數，1 為奇數)
        int state = (nums1[0] % 2);

        // 3. 驗證剩餘元素的奇偶性
        for(int i = 0; i < nums1.size(); ++i) {
             if(state == nums1[i] % 2) continue;
             
             // 若最小值為偶數，但陣列中存在奇數，則無法統一奇偶性
             if(state == 0 && ((nums1[i] % 2) == 1)) {
                 return false;
             }
        }
        
        return true;
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