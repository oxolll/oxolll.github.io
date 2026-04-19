---
title: "LeetCode 3885. Design Event Manager"
date: 2026-03-29T13:12:00+08:00
lastmod: 2026-03-29T13:12:00+08:00
difficulty: 1548
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "design", "heap", "hash table"]
keywords: ["LC3885", "Medium", "1548", "Event Manager", "Priority Queue", "Lazy Deletion"]
description: "LeetCode 第 3885 題：Design Event Manager。難度評分：Medium (1548)。探討如何利用優先佇列 (Priority Queue) 搭配 Hash Map 實作延遲刪除 (Lazy Deletion)，並利用數學狀態壓縮自動處理優先度相同時的 ID 排序規則。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3885](https://leetcode.com/problems/design-event-manager/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1548)</span>  
> **核心主題**：`Design` $\cdot$ `Heap (Priority Queue)` $\cdot$ `Hash Table`

---

## 📝 歷次打卡與更新
- [2026-03-29：初次提交 (優先佇列延遲刪除與 ID 狀態壓縮)](#2026-03-29-初次提交)

---

## 💡 2026-03-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
設計一個支援動態更新優先度並頻繁提取最高優先度元素的系統，核心資料結構必然是 **優先佇列 (Priority Queue / Max-Heap)**。
然而，C++ 的 `std::priority_queue` 並不支援高效尋找並修改特定元素的操作。為了解決 `updatePriority` 的問題，最標準的做法是 **延遲刪除 (Lazy Deletion)**：當優先度更新時，不修改佇列內原有的舊資料，而是直接將 `{新優先度, ID}` 塞入佇列，並利用一個 Hash Map `cur` 記錄該 ID 當前「真正」的優先度。提取資料時，只要佇列頂端的優先度與 `cur` 記錄的不符，就將其視為過期廢棄資料並彈出。

此外，本題實作有一個極為精妙的優化：C++ 的 Max-Heap 在 `pair` 比較時，若第一項（優先度）相同，會優先彈出第二項（ID）較大的元素。為了在優先度相同時優先處理 ID 較小的事件，演算法中使用了 **狀態壓縮** 的數學轉換，將存入的 ID 設為 `10^9 - 原ID`。如此一來，原 ID 越小，轉換後的 ID 就越大，完美迎合了 Max-Heap 的預設排序行為，省去了自定義比較函式 (Comparator) 的麻煩。

### 🛠️ 解題思路 (Approach)
1. **資料結構定義**：
   - `pq`：最大堆積，儲存 `{優先度, 轉換後的ID}`。
   - `cur`：Hash Map，記錄 `轉換後的ID -> 最新優先度`。
   - `trueid`：Hash Map，記錄 `轉換後的ID -> 原始ID`。
2. **建構子 (`EventManager`)**：
   - 走訪初始事件陣列，使用 `1000000000 - eventId` 計算出轉換後的 ID。
   - 更新三個資料結構：將真實 ID 存入 `trueid`，將優先度存入 `cur`，並將 `{priority, 轉換後的ID}` 推入 `pq`。
3. **更新優先度 (`updatePriority`)**：
   - 計算轉換後的 ID。更新 `cur` 記錄的最新優先度。
   - 將新的 `{newPriority, 轉換後的ID}` 直接推入 `pq` (舊資料放任其留在佇列內)。
4. **提取最高優先度 (`pollHighest`)**：
   - **延遲刪除清理**：利用 `while` 迴圈檢查 `pq.top()`。若佇列不為空，且頂端記錄的優先度不等於 `cur` 中該 ID 的最新優先度，代表這是一筆過期資料，將其 `pop()`。
   - 清理後若 `pq` 為空，回傳 `-1`。
   - 取出合法的最高優先度資料，將其從 `cur` 中移除（代表該事件已處理完畢），最後透過 `trueid` 還原並回傳原始 ID。

### 📊 複雜度分析
- **時間複雜度**:
  - `EventManager` (建構): $\mathcal{O}(E \log E)$，其中 $E$ 為初始事件數量。
  - `updatePriority`: $\mathcal{O}(\log K)$，其中 $K$ 為佇列中累積的操作次數。
  - `pollHighest`: 攤提時間複雜度為 $\mathcal{O}(\log K)$。雖然包含 `while` 迴圈清理過期節點，但每個推入的無效節點最多只會被彈出一次。
- **空間複雜度**: $\mathcal{O}(E + U)$。其中 $E$ 為事件數，$U$ 為 `updatePriority` 的呼叫次數。所有資料結構的增長皆與操作次數成正比。

### 💻 程式碼實作 (C++)
```cpp
class EventManager {
public:
    // Max-Heap: {優先度, 轉換後的ID}
    priority_queue<pair<int, int>, vector<pair<int, int>>> pq;
    // 記錄轉換後 ID 的最新有效優先度
    unordered_map<int, int> cur;
    // 記錄轉換後 ID 與真實 ID 的映射關係
    unordered_map<int, int> trueid;
    
    EventManager(vector<vector<int>>& events) {
        for(int i = 0; i < events.size(); ++i) {
            int p = events[i][1];
            // 狀態轉換：利用數學翻轉 ID 大小關係，迎合 Max-Heap 預設行為
            int id = 1000000000 - events[i][0];
            
            trueid[id] = events[i][0];
            pq.push({p, id});
            cur[id] = p;
        }
    }
    
    void updatePriority(int eventId, int newPriority) {
        int id = 1000000000 - eventId;
        // 更新最新優先度，並將新狀態推入佇列 (延遲刪除機制)
        cur[id] = newPriority;
        pq.push({newPriority, id});
    }
    
    int pollHighest() {
        // 延遲刪除 (Lazy Deletion)：清理佇列頂端所有過期的無效狀態
        while(!pq.empty() && cur[pq.top().second] != pq.top().first) {
            pq.pop();
        }

        if(pq.empty()) return -1;

        auto [a, b] = pq.top(); 
        pq.pop();
        
        // 將該事件從記錄中移除，並回傳真實 ID
        cur.erase(b);
        return trueid[b];
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