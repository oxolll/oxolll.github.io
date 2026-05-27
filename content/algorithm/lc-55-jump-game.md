---
title: "LeetCode 55. Jump Game"
date: 2026-05-27T16:16:00+08:00
lastmod: 2026-05-27T16:16:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "dynamic-programming", "difference-array"]
keywords: ["LC55", "Medium", "Jump Game", "貪心算法", "差分陣列", "區間覆蓋", "最遠可達"]
description: "LeetCode 第 55 題：Jump Game。探討如何利用差分陣列處理區間覆蓋問題，並進階推導出利用貪心算法 (Greedy) 維護「最遠可達距離」，以 O(N) 時間與 O(1) 空間達成極致最佳化。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 55](https://leetcode.com/problems/jump-game/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Dynamic Programming`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個非負整數陣列 `nums`。你最初位於陣列的 **第一個索引**，陣列中的每個元素代表你在該位置可以跳躍的 **最大長度**。
請判斷你是否能夠到達最後一個索引。如果可以，回傳 `true`；否則回傳 `false`。

### 範例
- **Input**: `nums = [2,3,1,1,4]`
- **Output**: `true`
- **Explanation**: 可以先從索引 0 跳 1 步到索引 1，然後再從索引 1 跳 3 步到達最後一個索引。

- **Input**: `nums = [3,2,1,0,4]`
- **Output**: `false`
- **Explanation**: 無論怎麼跳，最後都會到達索引 3。但該位置的最大跳躍長度是 0，因此永遠無法到達最後一個索引。

### 限制條件
- $1 \le nums.length \le 10^4$
- $0 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-27：新增貪心演算法 (最遠可達距離) 最優解](#解法二-貪心演算法-on-時間-o1-空間-最優解)
- [2026-05-27：初次提交 (差分陣列與前綴和區間覆蓋)](#解法一-差分陣列與前綴和-on-時間-on-空間)

---

## 💡 解法一：差分陣列與前綴和 ($\mathcal{O}(N)$ 時間, $\mathcal{O}(N)$ 空間)

### 🎯 演算法分析
這個解法的思維承襲自複雜的區間跳躍問題。對於當前位置 `i`，它能賦予後方區間 `[i + 1, i + nums[i]]` 可達的權利。
我們利用一個差分陣列 `diff` 來記錄區間的變化：
- 進入可達區間起點：權重加 1。
- 離開可達區間終點：權重減 1。
透過線性掃描並累加前綴和 `d`，只要 `d > 0` 就代表當前節點被過去的某個起點覆蓋（即為可達）。如果遇到 `d == 0`，代表斷層發生，直接回傳 `false`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需遍歷陣列一次。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了一個大小為 $N+1$ 的差分陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        // 差分陣列，大小為 n+1 防越界
        vector<int> diff(n + 1, 0);
        
        // 初始狀態：起點可達 (區間 [0, 0] 覆蓋 1 次)
        diff[0] = 1; 
        diff[1] = -1;
        
        int d = 0; // 滾動前綴和，代表當前點被覆蓋的次數
        for(int i = 0; i < n; ++i) {
            d += diff[i];
            
            // 若覆蓋次數為 0，代表過不去這道坎，提早宣告失敗
            if(d == 0) return false;
            
            // 當前點可達，為未來的可達區間發放許可證
            ++diff[i + 1];
            // 注意邊界：不可超過 n
            --diff[min(i + nums[i] + 1, n)];
        }
        
        return d > 0;
    }
};
```

---

## 💡 解法二：貪心演算法 ($\mathcal{O}(N)$ 時間, $\mathcal{O}(1)$ 空間 最優解)

### 🎯 演算法分析
這是本題最經典且極致簡化的解法。
因為題目只要求「最大跳躍長度」（代表可以跳 $1 \dots nums[i]$ 步，是一個連續的範圍），這意味著只要我們能抵達某個最遠的點 `mx`，那麼在 `mx` 之前的所有點我們**必然都有辦法抵達**。

**核心破局點：極限思維 (Greedy)**
我們只需要維護一個變數 `mx`（目前所能抵達的最遠索引）。
遍歷陣列中的每一個位置 `i`：
1. **可達性驗證**：如果當前的索引 `i` 已經超過了我們能抵達的最遠極限 `mx` (`i > mx`)，代表出現了無法跨越的斷層（例如連續的 0），直接回傳 `false`。
2. **擴展極限**：如果 `i` 是可達的，我們就嘗試用當前點的跳躍能力來刷新最遠極限：`mx = max(mx, i + nums[i])`。
最後只要確認 `mx` 是否能涵蓋最後一個索引即可。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。線性掃描陣列，每個元素僅處理一次。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了一個整數變數 `mx`，完美達到常數空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int mx = 0; // 記錄當前能跳到的最遠索引
        
        for(int i = 0; i < nums.size(); ++i) {
            // 如果當前位置已經超出了我們能抵達的極限，代表卡死了
            if(i > mx) return false;
            
            // 利用當前位置的跳躍能力，嘗試刷新最遠極限
            mx = max(mx, i + nums[i]);
        }

        // 檢查最遠極限是否足以涵蓋陣列的最後一個位置
        return mx >= nums.size() - 1;
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