---
title: "LeetCode 3948. Lexicographically Maximum MEX Array"
date: 2026-06-02T16:20:00+08:00
lastmod: 2026-06-02T16:20:00+08:00
difficulty: 2122
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "hash-table"]
keywords: ["LC3948", "Hard", "Lexicographically Maximum", "MEX Array", "貪心演算法", "攤銷分析", "雙指標"]
description: "LeetCode 第 3948 題：Lexicographically Maximum MEX Array。探討如何利用貪心策略保證字典序最大，並透過倒序 Hash Map 與過期剔除技巧，將尋找子陣列 MEX 極值的複雜度完美壓制在 O(N)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3948](https://leetcode.com/problems/lexicographically-maximum-mex-array/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `Hash Table` $\cdot$ `Amortized Analysis`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums`。
你可以將 `nums` 分割成若干個 **連續且互不重疊** 的子陣列，使得每個元素都恰好屬於一個子陣列。
對於每個子陣列，計算它的 **MEX** (Minimum Excluded value，即未出現在該子陣列中的最小非負整數)。將這些 MEX 值依序排列，會形成一個新的陣列。

請你回傳所有可能的分割方式中，能夠產生 **字典序最大 (Lexicographically Maximum)** 的 MEX 陣列。

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-02：初次提交 (貪心策略與 O(N) 攤銷優化)](#2026-06-02-初次提交)

---

## 💡 2026-06-02 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
**字典序最大 (Lexicographically Maximum)** 的嚴格定義是：在比對兩個陣列時，從左邊開始第一個不同的元素，誰比較大，誰的字典序就比較大。
這意味著我們不需要考慮什麼全局最優的全盤佈局，這是一個純粹的 **貪心 (Greedy)** 問題：
**「在當前的起點下，盡可能向右擴張，直到當前子陣列的 MEX 無法再變得更大為止。」**

**核心破局點 1：尋找最大 MEX (0 到 mm-1)**
若要讓當前子陣列的 MEX 為 `mm`，則該子陣列**必須**包含從 `0` 到 `mm - 1` 的所有整數。
因此，我們從 `0` 開始往上找，若 `0` 存在於陣列中，我們就把子陣列的右邊界延伸到 `0` 的位置；接著找 `1`，若 `1` 存在，且位置更靠右，就繼續延伸右邊界。直到某個數字 `mm` 找不到為止，當前的 `mm` 即為該段的極限 MEX。

**核心破局點 2：攤銷時間複雜度 $\mathcal{O}(N)$**
如果每次都去陣列中尋找數字的位置，複雜度會退化到 $\mathcal{O}(N^2)$。
我們可以使用 Hash Map (`unordered_map<int, vector<int>>`) 來記錄每個數字出現的索引。
- **倒序存入**：從 $n-1$ 掃描到 $0$。這樣同一個數字的索引在 `vector` 中是遞減的，我們可以直接用 $\mathcal{O}(1)$ 的 `pop_back()` 取出最靠左的可用索引。
- **過期剔除 (Lazy Deletion)**：當我們在索引 `i` 尋找數字時，只要 `vector` 尾端的索引 `< i`，就代表這些是「過去的歷史」，直接 `pop_back()` 丟棄。
如此一來，每個索引生命週期內最多只會被 `push` 一次、`pop` 一次，達成完美的 $\mathcal{O}(N)$ 複雜度。

### 🛠️ 解題思路 (Approach)
1. **預處理索引 (`idxs`)**：
   - 由後往前遍歷 `nums`，將數字的索引推入 `idxs[nums[i]]` 中。
2. **貪心構建子陣列**：
   - 宣告 `rt` 儲存結果，外層迴圈指標 `i` 從 `0` 走到 `n-1`。
   - 針對當前起點 `i`，宣告 `mm = 0`，以及子陣列右邊界 `last = i`。
3. **尋找極限 MEX**：
   - `while` 迴圈嘗試尋找當前的 `mm`：
     - 清除 `idxs[mm]` 中小於 `i` 的過期索引。
     - 若 `idxs[mm]` 變為空，代表 `mm` 在當前起點後再也沒出現過，這段子陣列的最大 MEX 就是目前的 `mm`，`break` 結束尋找。
     - 若找到了，將子陣列右邊界 `last` 更新為 `max(last, idxs[mm].back())`。
     - 將使用過的索引 `pop_back()`，並將 `mm` 遞增，繼續尋找下一個數字。
4. **結算並跳轉**：
   - 將找到的極限 MEX (`mm`) 放入結果 `rt` 中。
   - 將 `i` 跳轉到 `last`。由於 `for` 迴圈會執行 `++i`，所以下一輪會精準地從 `last + 1` 開始尋找下一個子陣列。
5. **回傳結果**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。預處理需 $\mathcal{O}(N)$。構建陣列時，儘管有三層迴圈 (`for`, `while`, `while`)，但最內層的 `pop_back()` 確保了每個索引最多只被移除一次，均攤 (Amortized) 時間複雜度嚴格保證為 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(N)$。`unordered_map` 與其內部的 `vector` 總共會儲存 $N$ 個索引。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> maximumMEX(vector<int>& nums) {
        unordered_map<int, vector<int>> idxs;
        int n = nums.size(); 
        
        // 1. 倒序記錄索引，確保 pop_back() 能以 O(1) 取出最靠左的可用索引
        for(int i = n - 1; i >= 0; --i) {
            idxs[nums[i]].push_back(i);
        }

        vector<int> rt;
        
        // 2. 貪心遍歷：尋找每一個子陣列的最大 MEX
        for(int i = 0; i < n; ++i) {
            int mm = 0;       // 當前子陣列嘗試達成的 MEX
            int last = i;     // 當前子陣列的右邊界
            
            while(!idxs[mm].empty()) {
                // 清除小於當前起點 i 的過期 (歷史) 索引
                while(!idxs[mm].empty() && idxs[mm].back() < i) {
                    idxs[mm].pop_back();
                }
                
                // 如果該數字在 i 之後不存在了，則無法組成更大的 MEX
                if(idxs[mm].empty()) break;
                
                // 為了包含數字 mm，子陣列右邊界必須延伸到其所在位置
                last = max(last, idxs[mm].back());
                idxs[mm].pop_back(); // 使用過後剔除
                
                ++mm; // 繼續挑戰下一個數字
            }
            
            rt.push_back(mm); // 寫入當前子陣列的最大 MEX
            
            // 跳轉至當前子陣列的終點，配合 for 迴圈的 ++i，下一輪會從 last + 1 開始
            i = last; 
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