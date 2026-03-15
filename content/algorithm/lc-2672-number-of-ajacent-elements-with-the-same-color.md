---
title: "LeetCode 2672. Number of Adjacent Elements With the Same Color"
date: 2026-03-16T01:03:00+08:00
lastmod: 2026-03-16T01:03:00+08:00
difficulty: 1705
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Simulation", "Math"]
keywords: ["LC2672", "1705", "Medium", "Adjacent Elements", "Local Delta", "Contribution"]
description: "LeetCode 第 2672 題：Number of Adjacent Elements With the Same Color。難度評分：1705。探討如何利用「局部差分 (Local Delta)」與「貢獻度」的思想，在 O(1) 時間內動態維護相鄰同色元素的數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2672](https://leetcode.com/problems/number-adjacent-elements-with-the-same-color/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1705)</span>    
> **核心主題**：`Array` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的整數陣列 `nums`，初始時所有元素皆為 `0`（代表未上色）。
給定一個二維陣列 `queries`，其中 $queries[i] = [index_i, color_i]$。
對於每一個查詢，你需要將 `nums[index_i]` 的顏色更改為 `color_i`。

在執行完每一次查詢後，請計算並記錄陣列中**相鄰且顏色相同（且顏色不為 0）**的元素對 (Pairs) 的數量。
請回傳一個與 `queries` 長度相同的陣列 `answer`，其中 `answer[i]` 是執行完第 $i$ 次查詢後的相鄰同色元素對數量。

### 限制條件
- $1 \le n \le 10^5$
- $1 \le queries.length \le 10^5$
- $queries[i].length == 2$
- $0 \le index_i \le n - 1$
- $1 \le color_i \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-16：初次提交 (局部差分與貢獻度維護)](#2026-03-16-初次提交)

---

## 💡 2026-03-16 初次提交

### 🎯 直覺 (Intuition)
如果每一次 Query 都重新掃描整個陣列來計算相鄰同色的對數，時間複雜度會高達 $\mathcal{O}(N \times Q)$，絕對會 Time Limit Exceeded (TLE)。
仔細思考，當我們改變索引 `idx` 的顏色時，**整個陣列中只有它與左邊鄰居 (`idx - 1`) 以及它與右邊鄰居 (`idx + 1`) 的關係會受到影響**。其他地方的同色對數根本不會變！
因此，我們可以維護一個全域變數 `cur` 來記錄當前的同色對數，並採用「局部差分 (Local Delta)」的策略：
1. **移除舊貢獻**：在變更顏色前，檢查 `idx` 是否與左右鄰居同色。如果有，代表這個舊顏色即將被破壞，我們把 `cur` 減去相應的數量。
2. **更新顏色**：將 `idx` 換上新顏色。
3. **加上新貢獻**：換上新顏色後，再次檢查 `idx` 是否與左右鄰居同色。如果有，代表形成了新的同色對，我們把 `cur` 加上相應的數量。

### 🛠️ 解題思路 (Approach)
1. 宣告 `color` 陣列記錄當前每個位置的顏色，初始化為 0。宣告 `cur = 0` 追蹤當前的同色對數。
2. 遍歷每一個 `query`，取得 `idx` 與目標顏色 `c`。
3. **優化判斷**：如果目標顏色 `c` 與目前的顏色 `color[idx]` 相同，代表狀態毫無改變，直接將當前的 `cur` 推入結果陣列即可，省去後續判斷。
4. **狀態更新與差分計算**：
   - 若顏色不同，先檢查左側 (`idx - 1`)：如果原本有顏色且與舊的 `color[idx]` 相同，破壞了舊對數，`--cur`。
   - 檢查右側 (`idx + 1`)：如果原本有顏色且與舊的 `color[idx]` 相同，破壞了舊對數，`--cur`。
   - 更新陣列顏色：`color[idx] = c`。
   - 再次檢查左側 (`idx - 1`)：如果新顏色與左鄰居相同，形成新對數，`++cur`。
   - 再次檢查右側 (`idx + 1`)：如果新顏色與右鄰居相同，形成新對數，`++cur`。
5. 將更新後的 `cur` 推入結果陣列 `rt` 中。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + Q)$。其中 $N$ 是陣列長度（用於初始化 `color` 陣列），$Q$ 是查詢的數量。每次查詢的更新與判斷都只涉及常數次數的陣列存取，為 $\mathcal{O}(1)$。整體時間為線性。
- **空間複雜度**: $\mathcal{O}(N)$。用於儲存當前顏色狀態的 `color` 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> colorTheArray(int n, vector<vector<int>>& queries) {
        int cur = 0;
        vector<int> color(n, 0);
        vector<int> rt; 
        rt.reserve(queries.size()); // 預先配置記憶體，減少動態擴容開銷
        
        for(auto q : queries) {
            int idx = q[0], c = q[1];
            
            // 只有當新顏色與舊顏色不同時，才需要進行差分更新
            if(color[idx] != c) {
                // 1. 扣除舊顏色的貢獻 (破壞舊的同色對)
                if(idx > 0 && color[idx-1] != 0 && c != color[idx-1] && color[idx-1] == color[idx])
                    --cur;
                if(idx + 1 < n && color[idx+1] != 0 && c != color[idx+1] && color[idx+1] == color[idx])
                    --cur;
                    
                // 2. 更新為新顏色
                color[idx] = c;
                
                // 3. 增加新顏色的貢獻 (形成新的同色對)
                if(idx > 0 && color[idx-1] == color[idx])
                    ++cur;
                if(idx + 1 < n && color[idx] == color[idx+1])
                    ++cur;
            }
            rt.push_back(cur);
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