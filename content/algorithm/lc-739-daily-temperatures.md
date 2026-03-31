---
title: "LeetCode 739. Daily Temperatures"
date: 2026-04-01T15:30:00+08:00
lastmod: 2026-04-01T15:30:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "stack", "monotonic-stack"]
keywords: ["LC739", "Medium", "N/A", "Daily Temperatures", "Monotonic Stack", "Value Bounded"]
description: "LeetCode 第 739 題：Daily Temperatures。難度評分：Medium (N/A)。探討如何利用溫度值域受限的特性透過陣列記錄最新索引，以及如何使用單調棧 (Monotonic Stack) 以嚴格 O(N) 時間複雜度求解下一個更高溫度的等待天數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 739](https://leetcode.com/problems/daily-temperatures/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Stack` $\cdot$ `Monotonic Stack`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `temperatures` 代表每天的溫度，請回傳一個陣列 `answer`，其中 `answer[i]` 代表第 `i` 天之後，需要等待幾天才能迎來更高的溫度。
如果未來都沒有更高的溫度，請在該位置填入 `0`。

### 限制條件
- $1 \le temperatures.length \le 10^5$
- $30 \le temperatures[i] \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (值域限制反向掃描法)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
尋找「下一個更大元素」是單調棧的經典應用。但本題有一個非常特殊的限制條件：**溫度的值域僅落在 30 到 100 之間**。
這意味著，當我們從後往前掃描陣列時，對於當前天的溫度 `T`，潛在的更高溫度只可能是 `T + 1` 到 `100`。我們只需維護一個大小為 105 的陣列 `lastappear`，記錄每種溫度「最新出現的索引」，然後遍歷 `T + 1` 到 `100`，找出這些索引中的最小值，即為距離最近的更高溫度的位置。這個做法巧妙地將時間複雜度轉換為 $\mathcal{O}(71 \times N)$，在常數極小的情況下等價於線性時間。

### 🛠️ 解題思路 (Approach)
1. 宣告大小為 105 的陣列 `lastappear`，初始化為 `INT_MAX`。
2. 宣告結果陣列 `rt`。
3. 從陣列尾端 `n-1` 開始反向遍歷至 `0`：
   - 宣告 `cnt = INT_MAX` 尋找最近的索引。
   - 建立內層迴圈，從 `temperatures[i] + 1` 檢查到 `100`。
   - 若該溫度曾出現過 (`lastappear[j] != INT_MAX`)，利用 `min` 更新 `cnt`。
   - 計算等待天數：若 `cnt` 仍為 `INT_MAX` 代表無更高溫，結果為 0；否則為 `cnt - i`。將結果推入 `rt`。
   - 更新當前溫度 `temperatures[i]` 在 `lastappear` 中的最新索引為 `i`。
4. 由於是反向掃描推入結果，最後需將 `rt` 進行 `reverse`，然後回傳。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        // 利用溫度範圍 30~100 的特性，記錄各溫度最後出現的索引
        vector<int> lastappear(105, INT_MAX);
        int n = temperatures.size();
        vector<int> rt; 
        rt.reserve(n);
        
        // 反向掃描
        for(int i = n - 1; i >= 0; --i) {
            int cnt = INT_MAX;
            
            // 檢查所有比當前溫度更高的可能溫度
            for(int j = temperatures[i] + 1; j <= 100; ++j) {
                if(lastappear[j] != INT_MAX) {
                    cnt = min(cnt, lastappear[j]);
                }
            }
            
            rt.push_back((cnt == INT_MAX ? 0 : cnt - i));
            lastappear[temperatures[i]] = i; // 更新當前溫度的索引
        }

        reverse(rt.begin(), rt.end());
        return rt;
    }
};
```
- **時間複雜度**: $\mathcal{O}(N \times \Sigma)$，其中 $\Sigma \le 71$ 為溫度變化範圍。
- **空間複雜度**: $\mathcal{O}(1)$ 額外空間，僅使用常數大小的 `lastappear` 陣列。

---

### 💡 進階標準解法：$\mathcal{O}(N)$ 單調棧 (Monotonic Stack)
若未來遇到值域沒有限制 (例如 $-10^9 \sim 10^9$) 的類似題目，則必須使用單調棧。
維護一個遞減的堆疊 (存儲索引)。當遇到一個比棧頂對應溫度還要高的元素時，代表我們找到了棧頂元素的「下一個更大值」，將其彈出並計算天數差。

<details>
<summary><b>點擊展開 O(N) 單調棧實作程式碼</b></summary>

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> rt(n, 0);
        stack<int> st; // 儲存尚未找到更高溫度的天數索引
        
        for(int i = 0; i < n; ++i) {
            // 當前溫度大於棧頂索引對應的溫度，進行結算
            while(!st.empty() && temperatures[i] > temperatures[st.top()]) {
                int prev_idx = st.top();
                st.pop();
                rt[prev_idx] = i - prev_idx;
            }
            st.push(i);
        }
        
        return rt;
    }
};
```
</details>

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