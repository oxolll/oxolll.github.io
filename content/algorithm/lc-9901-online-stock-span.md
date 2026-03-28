---
title: "LeetCode 901. Online Stock Span"
date: 2026-03-28T13:48:00+08:00
lastmod: 2026-03-28T13:48:00+08:00
difficulty: 1708
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "stack", "design", "monotonic stack"]
keywords: ["LC901", "1708", "Medium", "Stock Span", "Monotonic Stack", "Array Simulation"]
description: "LeetCode 第 901 題：Online Stock Span。難度評分：1708。探討如何捨棄傳統 std::stack，利用雙陣列維護跳躍跨度，以 O(1) 攤提時間複雜度實作高效的單調棧 (Monotonic Stack)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 901](https://leetcode.com/problems/online-stock-span/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1708)</span>  
> **核心主題**：`Stack` $\cdot$ `Design` $\cdot$ `Monotonic Stack`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
設計一個類別 `StockSpanner` 來計算股票的「跨度 (Span)」。
股票當天的跨度定義為：從今天往前算起，股票價格連續小於或等於今天價格的最大天數（包含今天）。
例如，如果未來 7 天的股票價格為 `[100, 80, 60, 70, 60, 75, 85]`，則其跨度分別為 `[1, 1, 1, 2, 1, 4, 6]`。

### 介面要求
- `StockSpanner()` 初始化物件。
- `int next(int price)` 給定當日股票價格，回傳其跨度。

### 限制條件
- $1 \le price \le 10^5$
- `next` 最多會被呼叫 $10^4$ 次。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-28：初次提交 (雙陣列模擬單調棧)](#2026-03-28-初次提交)

---

## 💡 2026-03-28 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求尋找當前元素左側連續 $\le$ 當前元素的最大長度，這是標準的**單調棧 (Monotonic Stack)** 應用場景。
傳統寫法是使用 `std::stack<pair<int, int>>` 儲存 `{價格, 跨度}`，當新價格進來時，不斷將棧頂小於等於新價格的元素彈出並累加跨度，最後將合併後的 `{新價格, 總跨度}` 推入棧中。
為了進一步優化常數開銷，原實作捨棄了 `std::stack`，改用兩個陣列 `val` 與 `cnt` 分別儲存歷史價格與歷史跨度。透過 `cnt` 中儲存的跨度值進行「跳躍式存取」，在邏輯上完美等價於單調棧的彈出操作，且避開了動態配置節點的開銷。

### 🛠️ 解題思路 (Approach)
1. **資料結構設計**：
   - 類別成員：`vector<int> cnt` 儲存每個價格對應的跨度，`vector<int> val` 儲存歷史價格。整數 `idx` 追蹤當前資料總數。
   - 建構子：將 `idx` 設為 0，並推入一組虛擬資料 `(0, 0)` 作為哨兵 (Sentinel)，以簡化陣列越界的判斷。
2. **處理 `next` 操作**：
   - 每次傳入新價格 `price`，設定初始跨度 `rt = 1`（代表自身這一天）。
   - **跳躍式回溯**：設定指標 `i = idx`，並使用 `i -= cnt[i]` 進行跳躍。只要 `price >= val[i]`，就將 `rt` 加上歷史跨度 `cnt[i]`，並透過減去 `cnt[i]` 繼續向前跳過那些已經被合併計算過的日子。
   - 更新狀態：跳躍結束後，將 `price` 存入 `val` 陣列，將計算出的跨度 `rt` 存入 `cnt` 陣列，並將 `idx` 加 1。
   - 回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(1)$ 攤提時間。雖然 `next` 中包含 `for` 迴圈，但每個被跳過的日子在未來都不會被重複檢查。每個元素最多被推入與跳過一次，因此整體 $N$ 次呼叫的總時間為 $\mathcal{O}(N)$，單次呼叫的攤提時間複雜度為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是呼叫 `next` 的次數。陣列 `val` 和 `cnt` 會隨著呼叫次數線性增長。

### 💻 程式碼實作 (C++)
```cpp
class StockSpanner {
public:
    vector<int> cnt; // 儲存歷史跨度
    vector<int> val; // 儲存歷史價格
    int idx;         // 追蹤當前資料的頂端索引

    StockSpanner() {
        idx = 0;
        // 放入虛擬資料防止陣列存取越界
        cnt.push_back(0);
        val.push_back(0);
    }
    
    int next(int price) {
        int rt = 1; // 初始跨度為自己這一天
        
        // 利用 cnt[i] 進行跳躍，邏輯等同於單調棧的彈出操作
        for(int i = idx; i > 0; i -= cnt[i]) {
            if(price >= val[i]) {
                rt += cnt[i];
            } else {
                // 遇到比當前價格高的歷史價格，停止回溯
                break; 
            }
        }
        
        ++idx;
        cnt.push_back(rt);
        val.push_back(price);
        
        return rt;
    }
};

/**
 * 呼叫範例:
 * StockSpanner* obj = new StockSpanner();
 * int param_1 = obj->next(price);
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