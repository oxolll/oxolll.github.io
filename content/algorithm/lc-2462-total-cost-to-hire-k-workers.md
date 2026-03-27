---
title: "LeetCode 2462. Total Cost to Hire K Workers"
date: 2026-03-27T13:15:00+08:00
lastmod: 2026-03-27T13:15:00+08:00
difficulty: 1763
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "heap", "two pointers"]
keywords: ["LC2462", "1763", "Medium", "Total Cost", "Priority Queue", "Min Heap", "Two Pointers"]
description: "LeetCode 第 2462 題：Total Cost to Hire K Workers。難度評分：1763。探討如何利用雙指標結合狀態壓縮技術與最小堆積 (Min-Heap)，以 O((K + C) log C) 時間複雜度高效選取成本最低的工人。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2462](https://leetcode.com/problems/total-cost-to-hire-k-workers/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1763)</span>  
> **核心主題**：`Array` $\cdot$ `Heap (Priority Queue)` $\cdot$ `Two Pointers`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個以 0 為起始索引的整數陣列 `costs`，其中 `costs[i]` 是僱用第 `i` 位工人的成本。
同時給定兩個整數 `k` 和 `candidates`。你需要根據以下規則恰好僱用 `k` 位工人：
1. 你將執行 `k` 次僱用。
2. 在每次僱用中，你可以從剩餘陣列的前 `candidates` 個工人或後 `candidates` 個工人中，選擇成本最低的工人。
3. 如果有多位工人的成本同為最低，則選擇索引最小的工人。
4. 一旦工人被僱用，他將從陣列中移除，剩餘的工人將填補空缺。

請回傳僱用 `k` 位工人的總成本。

### 限制條件
- $1 \le costs.length \le 10^5$
- $1 \le costs[i] \le 10^5$
- $1 \le k, candidates \le costs.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-27：初次提交 (雙指標與數值狀態壓縮最小堆積)](#2026-03-27-初次提交)

---

## 💡 2026-03-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題需要在動態變化的候選名單中頻繁尋找最小值，最佳的資料結構為優先佇列 (Priority Queue / Min-Heap)。
為了維持陣列前後端各 `candidates` 個候選人，我們可以使用雙指標 (`l` 與 `r`)。當堆積彈出一個元素時，判斷該元素是來自左側還是右側，並由對應的一側補充新元素進入堆積。
為了在優先佇列中同時排序「成本」與「來源索引」，並且優先選擇左側元素，常見做法是存入 `pair<int, int>`。但透過**狀態壓縮 (State Compression)**，可以將數值乘以 10 並加上 `0` (代表左側) 或 `1` (代表右側)，直接將兩個維度的資訊壓縮進單一 `long long` 整數中。這使得底層的比較運算更為高效，且自動滿足了「成本相同時左側優先」的條件。

### 🛠️ 解題思路 (Approach)
1. **初始化資料結構**：宣告一個預設行為為 Min-Heap 的 `priority_queue`。
2. **載入初始候選人**：
   - 設立雙指標 `l = 0` 與 `r = costs.size() - 1`。
   - 建立迴圈載入左右兩端各 `candidates` 個元素。
   - 將左端元素轉換為 `costs[l++] * 10` 放入佇列。
   - 確認 `l <= r` 未發生指標交叉後，將右端元素轉換為 `costs[r--] * 10 + 1` 放入佇列。
3. **執行 K 次僱用**：
   - 宣告 `rt = 0` 記錄總成本。
   - 執行 `k` 次迴圈，每次取出佇列頂端元素 `cur`。
   - 還原實際成本：將 `cur / 10` 累加至 `rt`。
   - 判斷來源並補充元素：透過 `(cur & 1)` 檢查末位元。
     - 若為 `1` (來自右側) 且 `l <= r`，將下一個右側元素 `costs[r--] * 10 + 1` 放入佇列。
     - 若為 `0` (來自左側) 且 `l <= r`，將下一個左側元素 `costs[l++] * 10` 放入佇列。
4. **回傳結果**：回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}((K + C) \log C)$。其中 $K$ 是要僱用的工人數，$C$ 是 `candidates`。初始建立優先佇列的時間為 $\mathcal{O}(C \log C)$。後續執行 $K$ 次提取與插入，每次操作為 $\mathcal{O}(\log C)$。
- **空間複雜度**: $\mathcal{O}(C)$。優先佇列中最多同時存在 $2 \times C$ 個元素。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long totalCost(vector<int>& costs, int k, int candidates) {
        int n = costs.size();
        // 最小堆積，儲存格式：val * 10 + 來源 (0 為左側，1 為右側)
        priority_queue<long long, vector<long long>, greater<>> pq;

        int l = 0, r = n - 1;
        
        // 預先載入兩端各 candidates 個候選人
        for(int i = 0; i < candidates && l <= r; ++i) {
            pq.push(costs[l++] * 10LL);
            if(l <= r) {
                pq.push(costs[r--] * 10LL + 1);
            }
        }

        long long rt = 0;
        
        // 執行 k 次僱用
        for(int i = 0; i < k; ++i) {
            auto cur = pq.top(); 
            pq.pop();
            
            // 還原真實成本並累加
            rt += cur / 10;

            // 檢查來源端並補充新候選人進佇列
            if((cur & 1) == 1 && l <= r) {
                pq.push(costs[r--] * 10LL + 1);
            } else if(l <= r) {
                pq.push(costs[l++] * 10LL);
            }
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