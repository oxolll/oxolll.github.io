---
title: "LeetCode 3864. Minimum Cost to Partition a Binary String"
date: 2026-03-09T13:15:00+08:00
lastmod: 2026-03-09T13:15:00+08:00
difficulty: 1900
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Dynamic Programming", "Divide and Conquer", "Prefix Sum"]
keywords: ["LC3864", "N/A", "Hard", "Binary String", "Partition", "Tree Recursion"]
description: "LeetCode 第 3864 題：Minimum Cost to Partition a Binary String。難度評分：N/A。探討如何利用前綴和優化與分治法(Divide and Conquer)找出最低成本的完美二元切割策略。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3864](https://leetcode.com/problems/minimum-cost-to-partition-a-binary-string/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(N/A)</span>   
> **核心主題**：`Dynamic Programming` $\cdot$ `Divide and Conquer` $\cdot$ `String`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個二進位字串 `s`，以及兩個整數 `encCost` 和 `flatCost`。
字串中的 `'1'` 代表敏感元素，`'0'` 代表非敏感元素。
你必須將字串切分成數個片段。初始情況下整個字串是一個片段。
對於一個長度為 $L$、包含 $X$ 個敏感元素的片段：
- 如果 $X = 0$，成本為 `flatCost`。
- 如果 $X > 0$，成本為 $L \times X \times \text{encCost}$。

如果你遇到一個長度為**偶數**的片段，你可以將它平分成**兩個長度相等**的連續片段。這個切割動作的總成本為新產生的兩個片段成本之和。
請回傳將字串進行任意合法切割後，可能獲得的**最低總成本**。

### 限制條件
- $1 \le s.length \le 10^5$
- `s` 僅包含 `'0'` 與 `'1'`
- $1 \le encCost, flatCost \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-09：初次提交 (前綴和搭配完美二元樹分治法)](#2026-03-09-初次提交)

---

## 💡 2026-03-09 初次提交

### 🎯 直覺 (Intuition)
題目的切割規則非常嚴格：只能針對「長度為偶數」的片段進行「對半平分」。
這意味著字串切割的過程，實質上是一棵**完美二元樹 (Perfect Binary Tree) 的向下探索**！我們不能隨意在哪個位置切一刀，而是只能從正中間切。
因為每次都完美平分，最多切到長度為 1 為止，整體的狀態空間非常小（長度只會是 $N, N/2, N/4 \dots$）。我們只需要用分治法 (Divide and Conquer) 遞迴算出「不切的成本」與「切開後左右子樹成本之和」，取較小者即可。

### 🛠️ 解題思路 (Approach)
1. **前綴和優化計數 (Prefix Sum)**：
   我們在計算成本時會頻繁需要知道片段中 `'1'` 的個數 $X$。建構一個前綴和陣列 `pre_cnt`，即可在 $\mathcal{O}(1)$ 時間內取得區間 `[l, r]` 內 `'1'` 的總數。
2. **分治遞迴 (`helper` 函式)**：
   - 計算當前區間 `[l, r]` 的 1 個數 `X`。
   - **遞迴終止條件**：如果長度 `len` 是奇數，我們沒辦法再平分了！直接結算當前片段的成本並回傳（若 $X=0$ 回傳 `flatCost`，否則回傳公式成本）。
   - **向下探索**：若為偶數，我們嘗試平分成左右兩半 `left` 與 `right`，遞迴取得最小成本。
   - **抉擇 (取 Minimum)**：回傳 `min(當前不切的成本, 切成兩半的成本之和)`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。遞迴樹的深度為 $\mathcal{O}(\log N)$，因為每一層的總節點計算長度都是 $N$，總共造訪的區間狀態數大約為 $2N-1$，時間為線性。
- **空間複雜度**: $\mathcal{O}(N)$。用於儲存前綴和陣列 `pre_cnt`，以及最大遞迴深度所需的 Call Stack 空間 $\mathcal{O}(\log N)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long minCost(string s, int encCost, int flatCost) {
        int l = s.size();
        
        // 1. 建立前綴和陣列，以 O(1) 速度查詢區間內 1 的數量
        vector<int> pre_cnt(s.size(), 0);
        for(int i = 0; i < s.size(); ++i) {
            pre_cnt[i] += (i > 0 ? pre_cnt[i - 1] : 0) + (s[i] == '1');
        }
            
        // 2. 啟動分治遞迴
        return helper(l, pre_cnt, 0, s.size() - 1, encCost, flatCost);
    }
    
private:
    long long helper(int len, vector<int>& pre_cnt, int l, int r, long long enc, long long flat) {
        // 取得區間 [l, r] 內 1 的數量
        int X = pre_cnt[r] - (l > 0 ? pre_cnt[l - 1] : 0);
        
        // 若長度為奇數，無法繼續平分，強制結算
        if(len % 2 == 1) {
            if(X == 0) return flat;
            return len * 1ll * X * enc;
        }

        // 遞迴計算平分成左右兩半的最佳成本
        long long left = helper(len / 2, pre_cnt, l, l + (r - l) / 2, enc, flat);
        long long right = helper(len / 2, pre_cnt, l + (r - l) / 2 + 1, r, enc, flat);

        // 比較「不切」與「切開」的成本，取最小值
        if(X == 0) 
            return min(flat, left + right);
        return min(left + right, len * 1LL * X * enc);
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