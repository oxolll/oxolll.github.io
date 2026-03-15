---
title: "LeetCode 1944. Number of Visible People in a Queue"
date: 2026-03-15T23:57:00+08:00
lastmod: 2026-03-15T23:57:00+08:00
difficulty: 2104
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Stack", "Monotonic Stack", "Binary Search", "Array"]
keywords: ["LC1944", "2104", "Hard", "Visible People", "單調堆疊"]
description: "LeetCode 第 1944 題：Number of Visible People in a Queue。難度評分：Hard(2104)。探討如何利用單調堆疊 (Monotonic Stack) 搭配二分搜尋或貪心計數，由右至左高效計算隊列中每個人能看見的人數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1944](https://leetcode.com/problems/number-of-visible-people-in-a-queue/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(2104)</span>    
> **核心主題**：`Stack` $\cdot$ `Monotonic Stack` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
有 `n` 個人排成一列，給定一個長度為 `n` 的整數陣列 `heights`，其中 `heights[i]` 代表第 `i` 個人的身高。
每個人都面向右邊。第 `i` 個人能「看見」第 `j` 個人的條件為：
- $i < j$ (第 `j` 個人在第 `i` 個人右邊)
- 對於所有介於 $i$ 與 $j$ 之間的人 $k$ ($i < k < j$)，都必須滿足 $\min(heights[i], heights[j]) > heights[k]$。

簡單來說，一個人只能看見他右邊比他矮的人；一旦遇到一個比他高（或一樣高）的人，他的視線就會被完全擋住，無法看見更右邊的任何人。
請回傳一個長度為 `n` 的陣列 `answer`，其中 `answer[i]` 是第 `i` 個人在隊列中能看見的人數。

### 限制條件
- $n == heights.length$
- $1 \le n \le 10^5$
- $1 \le heights[i] \le 10^5$
- 所有身高皆為**唯一 (Distinct)**。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (單調堆疊搭配二分搜尋計數)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
這題的視線阻擋機制，完美契合**單調堆疊 (Monotonic Stack)** 的特性。
因為我們要看的是「右邊」的人，所以**由右向左 (Right-to-Left)** 遍歷陣列是最直覺的。當我們從右向左掃描時，如果遇到一個很高的人，那麼他右邊那些比較矮的人，對於「更左邊的人」來說就永遠不可見了（因為視線會被這個高個子擋住）。
因此，我們可以維護一個「嚴格遞減的單調堆疊」（從堆疊底到堆疊頂是遞減的）。堆疊裡面儲存的，正是目前為止「有可能被左邊的人看見」的候選人身高。

### 🛠️ 解題思路 (Approach)
1. **宣告資料結構**：使用 `st` 作為單調堆疊，`rt` 儲存每個人的答案。
2. **由右至左遍歷**：
   - 由於 `st` 內部元素是嚴格遞減的（例如 `[10, 8, 6]`），如果我們反向讀取 `st.rbegin()` 到 `st.rend()`，它就是一個嚴格遞增的序列 (`[6, 8, 10]`)。
   - **二分搜計數**：使用 `lower_bound` 在這個反向的序列中尋找第一個 $\ge heights[i]$ 的位置 `k`。這個 `k` 正好代表了堆疊中有多少個人的身高嚴格小於 $heights[i]$（這些人一定能被看見）。
   - **視線阻擋判定**：
     - 若 `k == st.size()`：代表堆疊裡所有人比他矮，他可以看見全部的人。加入答案 `k`。
     - 若 `k < st.size()`：代表堆疊裡存在比他高的人。他可以看見所有 `k` 個比他矮的人，**加上**第一個比他高的人（那個人擋住了視線）。因此加入答案 `k + 1`。
3. **維護單調性**：將堆疊中所有 $\le heights[i]$ 的矮個子全部 `pop_back()` 移除，因為他們對更左邊的人來說已經沒用了（被當前這個人擋住了）。然後將當前身高 `push_back()` 推入堆疊。
4. **反轉結果**：因為我們是由右向左遍歷並 `push_back` 到 `rt`，最後記得將 `rt` 陣列反轉 (`reverse`) 回正確順序。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。遍歷 $N$ 次，每次進行 `lower_bound` 二分搜花費 $\mathcal{O}(\log N)$。內部的 `while` pop 迴圈雖然看起來是巢狀的，但每個元素最多被 push 和 pop 各一次，為攤提的 $\mathcal{O}(1)$。整體被二分搜主導。
- **空間複雜度**: $\mathcal{O}(N)$。用於儲存答案陣列 `rt` 與單調堆疊 `st`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> canSeePersonsCount(vector<int>& heights) {
        vector<int> rt;
        vector<int> st; // 單調遞減堆疊
        
        for(int i = heights.size() - 1; i >= 0; --i) {
            // 利用 rbegin/rend 讓遞減序列變為遞增，使用 lower_bound 找第一個 >= 當前身高的人
            auto k = lower_bound(st.rbegin(), st.rend(), heights[i]) - st.rbegin();
            
            if(k == st.size()) {
                // 全部都比自己矮，視野未被阻擋
                rt.push_back(k);
            } else {
                // 視野被第 k+1 個人(第一個比自己高的人)阻擋
                rt.push_back(k + 1);
            }
            
            // 維護單調堆疊：移除所有比自己矮的，因為他們不可能被更左邊的人看到了
            while(!st.empty() && st.back() <= heights[i]) {
                st.pop_back();
            }
            st.push_back(heights[i]);
        }

        // 因為是由右向左建立答案，最後需要反轉
        reverse(rt.begin(), rt.end());
        return rt;
    }
};
```

---

### 💡 額外小知識：純 $\mathcal{O}(N)$ 優化寫法
其實在單調堆疊中，我們可以在 `while` pop 矮個子的同時「順便計數」，這樣就可以省去 `lower_bound` 的 $\mathcal{O}(\log N)$ 時間，將整體複雜度壓到絕對的 $\mathcal{O}(N)$：

```cpp
class Solution {
public:
    vector<int> canSeePersonsCount(vector<int>& heights) {
        int n = heights.size();
        vector<int> rt(n, 0);
        vector<int> st;
        
        for(int i = n - 1; i >= 0; --i) {
            int count = 0;
            // 一邊 pop 被擋住的矮個子，一邊計算看見了幾個人
            while(!st.empty() && st.back() < heights[i]) {
                ++count;
                st.pop_back();
            }
            // 如果堆疊沒空，代表遇到了一個比自己高的人，視線被他擋住，但還是看得到他
            if(!st.empty()) {
                ++count;
            }
            rt[i] = count;
            st.push_back(heights[i]);
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