---
title: "LeetCode 3886. Sum of Sortable Integers"
date: 2026-03-29T13:15:00+08:00
lastmod: 2026-03-29T13:15:00+08:00
difficulty: 1999
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "simulation"]
keywords: ["LC3886", "Hard", "1999", "Sortable Integers", "Array Chunking", "Cyclic Shift"]
description: "LeetCode 第 3886 題：Sum of Sortable Integers。難度評分：Hard (1999)。探討如何透過遍歷陣列因數進行區塊分割，並利用模擬驗證每個子區塊是否能透過循環位移達到排序狀態，進而求出所有合法長度的總和。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3886](https://leetcode.com/problems/sum-of-sortable-integers/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (1999)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Simulation`

---

## 📝 歷次打卡與更新
- [2026-03-29：初次提交 (因數拆分與循環位移驗證)](#2026-03-29-初次提交)

---

## 💡 2026-03-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求計算滿足特定「可排序 (Sortable)」條件的長度區塊總和。
演算法的核心在於將陣列拆分為多個長度為 $i$ 的子區塊（因此 $i$ 必須是陣列總長度 $n$ 的因數）。針對每一個被拆分出來的子區塊，必須驗證其是否為「一個已排序序列的循環位移 (Cyclic Shift)」，同時還必須確保當前區塊的最小值大於等於前一個區塊的最大值（以維持整體宏觀上的非遞減狀態）。

### 🛠️ 解題思路 (Approach)
1. 宣告 `rt = 0` 儲存結果。建立外層迴圈遍歷所有可能的區塊長度 $i$ (從 1 到 $n$)。
2. **因數篩選**：如果 $n \pmod i \neq 0$，代表該長度無法完美分割陣列，直接 `continue`。
3. **區塊驗證模擬**：
   - 建立變數 `prev = 0` 記錄上一個區塊的最後元素，`flag = true` 控制整體的合法狀態。
   - 迴圈以步長 $i$ 走訪陣列，每次取出一個子區塊。
   - 在每個子區塊內部，先尋找「循環位移的斷點」，也就是尋找峰值 `mx` 的位置。
   - 利用 `lock` 變數確保峰值只會被更新一次，找到峰值後記錄其下一個位置 `id` 作為邏輯上的「起點」。
   - 如果在尋找過程中發現數值小於 `prev`，則違反了區塊間的單調性，標記 `flag = false` 並跳出。
   - **循環驗證**：找到邏輯起點 `id` 後，以該起點進行長度為 $i$ 的環狀走訪 (`(id - j + k) % i`)，確保這 $i$ 個元素是嚴格遞增或非遞減的，並將最後一個元素更新為新的 `prev` 提供給下一個區塊參考。
4. 若陣列的所有子區塊皆通過驗證 (`flag == true`)，則將該合法長度 $i$ 累加至 `rt`。
5. 最終回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times D(N))$。其中 $N$ 是陣列長度，$D(N)$ 是 $N$ 的因數個數。由於外層篩選了非因數，內層的兩次區塊走訪總共耗時約 $\mathcal{O}(N)$，故整體時間被因數的數量限制。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了數個整數變數進行指標維護與狀態標記。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int sortableIntegers(vector<int>& nums) {
        int n = nums.size();
        int rt = 0;
        
        // 測試所有可能的區塊長度 i
        for(int i = 1; i <= n; ++i) {
            // i 必須是 n 的因數
            if((n % i) != 0) continue;
            
            int prev = 0;
            bool flag = true;
            
            // 以步長 i 遍歷每個子區塊
            for(int j = 0; j < n && flag; ++j) {
                int id = j;
                int mx = nums[j];
                bool lock = false;
                
                // 尋找循環位移的斷點 (即區塊內的峰值)
                for(int k = 0; k < i && flag; ++k) {
                    if(nums[j + k] < prev) {
                        flag = false;
                        break;
                    }
                    if(mx <= nums[j + k] && !lock) {
                        mx = nums[j + k];
                        id = j + k;
                    } else {
                        lock = true;
                    }
                }
                
                // 起點為峰值的下一個元素
                ++id;
                
                // 從邏輯起點開始進行環狀驗證，確保為排序序列
                for(int k = 0; k < i && flag; ++k) {
                    int cur = nums[j + ((id - j + k) % i)];
                    if(cur < prev) {
                        flag = false;
                        break;
                    }
                    prev = cur;
                }
                
                // 移動至下一個區塊的起點
                j = j + i - 1;
            }

            // 若全區塊皆符合條件，將長度 i 累加
            if(flag) rt += i;
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