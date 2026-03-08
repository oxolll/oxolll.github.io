---
title: "LeetCode 146. LRU Cache"
date: 2026-03-09T14:20:00+08:00
lastmod: 2026-03-09T14:20:00+08:00
difficulty: 1600
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Design", "Hash Table", "Queue", "Lazy Eviction"]
keywords: ["LC146", "Medium", "LRU Cache", "Queue", "延遲刪除"]
description: "LeetCode 第 146 題：LRU Cache。難度評分：Medium。探討如何捨棄傳統的雙向鏈結串列，改用 Queue 搭配計數器實作優雅的「延遲刪除 (Lazy Eviction)」策略來達成 O(1) 的 LRU Cache。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 146](https://leetcode.com/problems/lru-cache/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(N/A)</span>  
> **核心主題**：`Design` $\cdot$ `Hash Table` $\cdot$ `Queue`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
請設計並實作一個滿足 **最近最少使用 (Least Recently Used, LRU)** 快取限制的資料結構。
請實作 `LRUCache` 類別：
- `LRUCache(int capacity)`：以正整數 `capacity` 初始化 LRU 快取。
- `int get(int key)`：如果鍵 `key` 存在於快取中，則回傳其值，否則回傳 `-1`。
- `void put(int key, int value)`：如果 `key` 已經存在，則更新其值；否則，將 `key-value` 鍵值對加入快取。如果加入此操作會導致快取的鍵數量超過 `capacity`，則**驅逐 (evict)** 最近最少使用的鍵。

**進階要求**：`get` 和 `put` 函式都必須以 $\mathcal{O}(1)$ 的平均時間複雜度執行。

### 限制條件
- $1 \le capacity \le 3000$
- $0 \le key \le 10^4$
- $0 \le value \le 10^5$
- 最多會對 `get` 和 `put` 進行 $2 \times 10^5$ 次呼叫。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-09：初次提交 (Queue 搭配計數器的延遲刪除法)](#2026-03-09-初次提交)

---

## 💡 2026-03-09 初次提交

### 🎯 直覺 (Intuition)
標準的 LRU 快取通常使用「雙向鏈結串列 (Doubly Linked List)」搭配「雜湊表 (Hash Map)」來實作，以達成 $\mathcal{O}(1)$ 的節點移動與刪除。
然而，維護雙向鏈結串列的指標常常容易出錯。如果換個角度思考：我們其實不需要真的去「移動」舊紀錄，我們只需要**不斷把新的存取紀錄塞進 Queue 裡**。當 Queue 裡面累積了同一個 Key 的多筆歷史紀錄時，我們用一個計數器記下來。等到容量爆滿需要剔除元素時，我們再從 Queue 前端彈出紀錄，只要這個 Key 的計數器還沒歸零，就代表它在後面還有「更新的紀錄」，我們就單純忽略這個過期的歷史殘影（這稱為 **延遲刪除 Lazy Eviction**）。

### 🛠️ 解題思路 (Approach)
1. **資料結構定義**：
   - `queue<int> q`：記錄 Key 被操作的歷史軌跡（越近期的操作會在越後面）。
   - `unordered_map<int, int> cnt`：記錄 Queue 中每個 Key 目前存在的「歷史殘影」數量。
   - `unordered_map<int, int> mp`：記錄 Key 對應的真實 Value。
2. **`get(key)` 操作**：
   - 如果 Key 不存在，回傳 `-1`。
   - 如果存在，這代表這是一次「最新存取」。我們把 `key` 再次推入 `q` 中，並將 `cnt[key]` 加 1，最後回傳 `mp[key]`。
3. **`put(key, value)` 操作**：
   - 如果這是一個**全新**的 Key 且容量已滿 (`cap == 0`)，必須進行剔除：
     - 不斷從 `q` 的前端彈出舊紀錄 `cur_k`，並將其計數 `cnt[cur_k]` 減 1。
     - **關鍵判斷**：如果 `cnt[cur_k] == 0`，這代表我們彈出的是這個 Key 的「最後一次」存取紀錄。這意味著它真的是目前最久未被使用的元素！此時我們將容量恢復 (`++cap`)，並從 `mp` 中徹底抹除它，結束剔除流程。
   - 如果是全新的 Key 但還有容量，直接消耗容量 (`--cap`)。
   - 最後，無論是全新還是更新，都將這次操作視為「最新存取」：把 `key` 推入 `q`，`cnt[key]` 加 1，並更新 `mp[key] = value`。

### 📊 複雜度分析
- **時間複雜度**: 攤提 (Amortized) $\mathcal{O}(1)$。`get` 操作顯然是常數時間；`put` 雖然包含一個 `while` 迴圈，但每個推入 Queue 的元素最多只會被彈出一次，平均攤提下來依然是 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(Q)$，其中 $Q$ 是操作總次數。在最極端的情況下（不斷更新相同的 Key 但從未觸發剔除），Queue 可能會不斷增長。但在實際應用與 LeetCode 限制下，空間使用量是可接受的。

### 💻 程式碼實作 (C++)
```cpp
class LRUCache {
public:
    queue<int> q;                // 記錄存取歷史 (Key)
    unordered_map<int, int> cnt; // 記錄 Queue 內各 Key 的歷史軌跡數量
    unordered_map<int, int> mp;  // 記錄 Key 到 Value 的映射
    int cap;
    
    LRUCache(int capacity) {
        cap = capacity;
    }
    
    int get(int key) {
        if(mp.find(key) != mp.end()) {
            // 視為最新存取，推入歷史軌跡中
            q.push(key);
            ++cnt[key];
            return mp[key];
        }
        return -1;
    }
    
    void put(int key, int value) {
        // 若為全新的 Key
        if(mp.find(key) == mp.end()) {
            // 若容量已滿，啟動延遲刪除 (Lazy Eviction)
            while(cap == 0) {
                auto cur_k = q.front();
                q.pop();
                --cnt[cur_k];

                // 當計數歸零，代表這真的是最久未使用的紀錄
                if(cnt[cur_k] == 0) {
                    ++cap;
                    mp.erase(cur_k);
                }
            }
            --cap;
        }
        
        // 視為最新存取，更新軌跡與數值
        q.push(key);
        ++cnt[key];
        mp[key] = value;
        return;
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
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