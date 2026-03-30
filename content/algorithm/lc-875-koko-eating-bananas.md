---
title: "LeetCode 875. Koko Eating Bananas"
date: 2026-03-30T16:58:00+08:00
lastmod: 2026-03-30T16:58:00+08:00
difficulty: 1765
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "binary search"]
keywords: ["LC875", "1765", "Medium", "Koko Eating Bananas", "Binary Search on Answer"]
description: "LeetCode 第 875 題：Koko Eating Bananas。難度評分：1765。探討如何利用「對答案進行二分搜尋 (Binary Search on Answer)」，以 O(N log M) 時間複雜度找出滿足時間限制的最小吃香蕉速率。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 875](https://leetcode.com/problems/koko-eating-bananas/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1765)</span>  
> **核心主題**：`Array` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
珂珂喜歡吃香蕉。這裡有 $n$ 堆香蕉，第 $i$ 堆中有 `piles[i]` 根香蕉。警衛已經離開了，並將在 `h` 小時後回來。
珂珂可以決定她吃香蕉的速率 `k`（單位：根/小時）。每個小時，她將會選擇一堆香蕉，並從中吃掉 `k` 根。如果這堆香蕉少於 `k` 根，她將吃掉這堆的所有香蕉，並且在這一個小時內不會再去吃其他堆的香蕉。
珂珂喜歡慢慢吃，但仍希望能在警衛回來前吃完所有的香蕉。

請回傳她可以在 `h` 小時內吃完所有香蕉的**最小整數速率** `k`。

### 限制條件
- $1 \le piles.length \le 10^4$
- $piles.length \le h \le 10^9$
- $1 \le piles[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-30：初次提交 (對答案二分搜尋)](#2026-03-30-初次提交)

---

## 💡 2026-03-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題是極為經典的「對答案進行二分搜尋 (Binary Search on Answer)」問題。
我們觀察目標變數「速率 $k$」的特性：
- 若速率太慢，所需時間大於 $h$，則該速率不合法，更慢的速率必定也不合法。
- 若速率夠快，所需時間小於或等於 $h$，則該速率合法，且更快的速率也必定合法。
這種具備「單調性」的驗證函數，非常適合用二分搜尋來快速定位臨界點（最小合法速率）。
速率的下界為 $1$（最慢），上界為香蕉堆中的最大值（再快也沒有意義，因為一個小時最多只能吃一堆）。

### 🛠️ 解題思路 (Approach)
1. **設定二分搜尋邊界**：
   - 下界 `l = 1`。
   - 上界 `r = *max_element(piles.begin(), piles.end())`。
2. **二分搜尋迴圈**：
   - 當 `l < r` 時，取得中點 `speed = l + (r - l) / 2` 進行測試。
   - 宣告 `time = 0`，遍歷每一堆香蕉，計算以當前 `speed` 吃完需要幾個小時。
   - **計算所需時間**：利用 `(x + speed - 1) / speed` 實作整數的無條件進位 (Ceiling) 除法，避免浮點數轉換的精度問題與運算開銷。
   - 驗證條件：
     - 若 `time > h`：代表吃太慢了，合法的答案必須更大，更新 `l = speed + 1`。
     - 若 `time <= h`：代表這個速度合法，但可能有更慢的合法速度，更新 `r = speed` 繼續向左限縮。
3. **回傳結果**：當 `l == r` 時，即找到滿足條件的最小速率，回傳 `l`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log M)$。其中 $N$ 是 `piles` 的長度，$M$ 是 `piles` 中的最大值。尋找最大值需 $\mathcal{O}(N)$，二分搜尋範圍大小為 $M$，執行次數為 $\mathcal{O}(\log M)$，每次需遍歷陣列耗時 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用常數個變數進行邊界與時間的記錄。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minEatingSpeed(vector<int>& piles, int h) {
        // 速率下界為 1，上界為陣列中的最大值
        int l = 1, r = *max_element(piles.begin(), piles.end());
        
        // 對答案 (速率) 進行二分搜尋
        while(l < r) {
            int speed = l + (r - l) / 2;
            int time = 0;
            
            // 計算以當前 speed 吃完所有香蕉所需的時間
            for(auto x : piles) {
                // (x + speed - 1) / speed 實現整數無條件進位除法
                time += (x + speed - 1) / speed;
            }
            
            // 花費時間過長，必須提高速率
            if(time > h) {
                l = speed + 1;
            } 
            // 時間符合要求，嘗試尋找更小的速率
            else {
                r = speed;
            }
        }

        return l;
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