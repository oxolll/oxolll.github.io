---
title: "LeetCode 1696. Jump Game VI"
date: 2026-05-27T22:30:00+08:00
lastmod: 2026-05-27T22:30:00+08:00
difficulty: 1954
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic-programming", "heap", "monotonic-queue", "sliding-window"]
keywords: ["LC1696", "Medium", "1954", "Jump Game VI", "優先佇列", "延遲刪除", "單調佇列"]
description: "LeetCode 第 1696 題：Jump Game VI。難度評分：1954。探討如何利用 Max-Heap (優先佇列) 搭配延遲刪除技巧解決區間最大值問題，並進階使用單調佇列 (Monotonic Queue) 達成 O(N) 的極致效能。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1696](https://leetcode.com/problems/jump-game-vi/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1954)</span>  
> **核心主題**：`Dynamic Programming` $\cdot$ `Heap (Priority Queue)` $\cdot$ `Monotonic Queue`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個下標從 **0** 開始的整數陣列 `nums` 和一個整數 `k`。
你一開始在索引 `0` 處。每一步，你可以從索引 `i` 跳到索引 `i + j`，其中 $1 \le j \le \min(n - 1 - i, k)$。

你的 **得分** 是你經過的所有索引處的 `nums` 值之和。
請回傳你能獲得的 **最大得分**。

### 範例
- **Input**: `nums = [1,-1,-2,4,-7,3], k = 2`
- **Output**: `7`
- **Explanation**: 最優路徑為 `1 -> -1 -> 4 -> 3`，總得分為 $1 + (-1) + 4 + 3 = 7$。

- **Input**: `nums = [10,-5,-2,4,0,3], k = 3`
- **Output**: `17`
- **Explanation**: 最優路徑為 `10 -> 4 -> 3`，總得分為 $10 + 4 + 3 = 17$。

- **Input**: `nums = [1,-5,-20,4,-1,3,-6,-3], k = 2`
- **Output**: `0`

### 限制條件
- $1 \le nums.length, k \le 10^5$
- $-10^4 \le nums[i] \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-27：新增單調佇列 (Monotonic Queue) O(N) 最優解](#解法二-單調佇列-monotonic-queue-on-時間最優解)
- [2026-05-27：初次提交 (Max-Heap 與延遲刪除法)](#解法一-優先佇列-max-heap-與延遲刪除-on-log-n)

---

## 💡 解法一：優先佇列 (Max-Heap) 與延遲刪除 ($O(N \log N)$)

### 🎯 演算法分析
本題的動態規劃狀態轉移方程式非常直觀：
$$dp[i] = nums[i] + \max(dp[i-k], dp[i-k+1], \dots, dp[i-1])$$

因為我們需要頻繁地在一個大小為 $k$ 的滑動窗口中尋找「最大值」，使用 `priority_queue` (Max-Heap) 是一個絕佳的選擇。
**核心破局點：延遲刪除 (Lazy Deletion)**
當滑動窗口向前移動時，過期的元素會留在 Heap 中。我們不需要刻意去 Heap 底層把它們挖出來刪除（這會非常耗時）。我們只需要在每次要「取用 Heap 頂端最大值」之前，檢查 Heap 頂的元素的索引是否已經過期（`< i - k`），如果是，就將其 `pop()`，直到頂端是一個合法的有效元素為止。

### 📊 複雜度分析
- **時間複雜度**: $O(N \log N)$。每個元素最多被 `push` 和 `pop` 入優先佇列各一次，每次操作時間為 $O(\log N)$。
- **空間複雜度**: $O(N)$。優先佇列中最多可能儲存 $N$ 個元素。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxResult(vector<int>& nums, int k) {
        int n = nums.size();
        // pq 儲存 {到達該點的最大得分, 該點的索引}
        priority_queue<pair<int, int>> pq;
        
        for(int i = 0; i < n - 1; ++i) {
            // 延遲刪除：如果堆頂的元素已經不在視窗 [i-k, i-1] 範圍內，則將其剔除
            while(!pq.empty() && pq.top().second < i - k) {
                pq.pop();
            }
            
            // 取得當前視窗內的最大得分，若 pq 為空則初始為 0
            int cur_mx = pq.empty() ? 0 : pq.top().first;
            
            // 將當前點的狀態推入堆中
            pq.push({cur_mx + nums[i], i});
        }
        
        // 處理最後一個元素 (終點) 的結算
        while(!pq.empty() && pq.top().second < n - 1 - k) {
            pq.pop();
        }
        
        return (pq.empty() ? 0 : pq.top().first) + nums.back();
    }
};
```

---

## 💡 解法二：單調佇列 (Monotonic Queue) ($O(N)$ 時間最優解)

### 🎯 演算法分析
既然我們要維護的是一個「滑動窗口最大值」，這恰好是 **單調佇列 (Monotonic Queue)** 的主場！
利用雙向佇列 `deque`，我們可以維護一個**嚴格遞減**的候選名單：
1. **淘汰過期者**：如果 `deque` 頭部的索引已經小於 `i - k`，代表它離開窗口了，從前方彈出。
2. **計算當前值**：因為佇列是遞減的，所以 `deque` 頭部永遠是當前窗口內的最大值。當前得分即為 `dp[i] = nums[i] + dp[dq.front()]`。
3. **維護單調性 (核心)**：在將當前索引 `i` 推入 `deque` 尾部之前，如果發現 `dp[i]` 大於等於 `deque` 尾部的元素，代表尾部的那些元素「既比 `i` 老，得分又比 `i` 低」，它們未來絕對不可能成為最大值，直接從尾部無情刪除。

### 📊 複雜度分析
- **時間複雜度**: $O(N)$。雖然內部有 `while` 迴圈，但每個元素最多只會被 `push_back` 一次，並被 `pop_front` 或 `pop_back` 一次。均攤時間複雜度為嚴格的線性 $O(N)$。
- **空間複雜度**: $O(N)$。`dp` 陣列與 `deque` 佇列皆消耗 $O(N)$ 空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxResult(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> dp(n, 0);
        dp[0] = nums[0];
        
        // 雙向佇列，用來儲存候選的最大值「索引」
        deque<int> dq;
        dq.push_back(0);
        
        for(int i = 1; i < n; ++i) {
            // 1. 淘汰過期者：移出已經不在窗口 [i-k, i-1] 內的索引
            if(!dq.empty() && dq.front() < i - k) {
                dq.pop_front();
            }
            
            // 2. 狀態轉移：佇列頭部必然是當前窗口內的最優解
            dp[i] = nums[i] + dp[dq.front()];
            
            // 3. 維護單調遞減性：將較小且較舊的無用狀態從尾部剔除
            while(!dq.empty() && dp[dq.back()] <= dp[i]) {
                dq.pop_back();
            }
            
            // 將當前最新狀態加入候選名單
            dq.push_back(i);
        }
        
        return dp.back();
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