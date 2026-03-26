---
title: "LeetCode 2375. Construct Smallest Number From DI String"
date: 2026-03-26T16:23:00+08:00
lastmod: 2026-03-26T16:23:00+08:00
difficulty: 1641
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "backtracking", "greedy"]
keywords: ["LC2375", "1641", "Medium", "Smallest Number", "DI String", "DFS"]
description: "LeetCode 第 2375 題：Construct Smallest Number From DI String。難度評分：1641。探討如何利用深度優先搜尋 (DFS) 搭配貪婪策略與提早結束機制，快速構造符合特定升降規則的最小數字。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2375](https://leetcode.com/problems/construct-smallest-number-from-di-string/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1641)</span>  
> **核心主題**：`String` $\cdot$ `Backtracking` $\cdot$ `Greedy`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的字串 `pattern`，僅包含字元 `'I'` (遞增) 與 `'D'` (遞減)。
請構造一個長度為 $n + 1$ 的字串 `num`，並滿足以下條件：
1. `num` 僅包含數字 `'1'` 到 `'9'`，且每個數字最多只能使用一次。
2. 如果 `pattern[i] == 'I'`，則 `num[i] < num[i+1]`。
3. 如果 `pattern[i] == 'D'`，則 `num[i] > num[i+1]`。

請回傳滿足上述條件且**字典序最小**的字串。

### 限制條件
- $1 \le pattern.length \le 8$
- `pattern` 僅包含字元 `'I'` 與 `'D'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-26：初次提交 (DFS 搭配提早結束機制)](#2026-03-26-初次提交)

---

## 💡 2026-03-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求使用 `1` 到 `9` 構造符合特定升降規則的最小數字，且數字不重複。這是一道標準的排列組合問題，適合使用深度優先搜尋 (DFS/Backtracking) 來求解。
為了確保最終結果是「最小值」，我們在 DFS 擴展狀態時，必須採用貪婪策略 (Greedy Strategy)，嚴格按照數字從 `1` 到 `9` 的順序進行嘗試。因為是依序遞增測試，**第一組**成功滿足所有 `pattern` 條件並到達終點的排列，必然就是全局字典序最小的最佳解。此時可直接利用全域變數狀態阻斷後續的遞迴分支，省去不必要的運算。

### 🛠️ 解題思路 (Approach)
1. **全域狀態設定**：宣告全域變數 `mm` 初始化為 `INT_MAX`。宣告布林陣列 `used` 記錄 `1` 到 `9` 是否已被使用。
2. **DFS 遞迴函式設計**：
   - 參數：`pattern` 字串、當前處理的索引 `idx`、當前構造出的數字 `cur`、狀態陣列 `used`。
   - 終止條件：若 `idx == pattern.size()`，代表已成功構造出長度為 $n + 1$ 的合法數字。更新 `mm = min(mm, cur)`，並直接 `return`。
   - 狀態轉移：將 `cur` 乘以 10 準備放入下一個位數 (`now = cur * 10`)。
3. **貪婪擴展與提早結束 (Early Exit)**：
   - 使用迴圈從 `1` 測試到 `9`。
   - **關鍵優化**：在迴圈條件加入 `mm == INT_MAX`。一旦 `mm` 被更新，代表已找到最小合法解，後續迴圈直接中斷，避免冗餘的完整排列窮舉。
   - 若數字 `i` 未被使用，且符合當前 `pattern[idx]` 的升降規則：
     - `pattern[idx] == 'I'` 且當前最後一個數字 `< i`。
     - `pattern[idx] == 'D'` 且當前最後一個數字 `> i`。
   - 若符合，標記 `used[i] = true`，傳入 `now + i` 進入下一層遞迴，回溯時再將 `used[i] = false` 復原。
4. **回傳結果**：主函式啟動迴圈逐一測試 `1` 到 `9` 作為開頭數字，最終將記錄在 `mm` 的數值轉為字串回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N!)$ (理論最差)。其中 $N$ 是 `pattern` 的長度。但由於貪婪順序嘗試與 `mm == INT_MAX` 的提早結束機制，實際執行時間極接近 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(N)$。遞迴呼叫堆疊的最大深度為 $N + 1$，且 `used` 陣列長度為常數 10。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int mm;
    string smallestNumber(string pattern) {
        mm = INT_MAX;
        vector<bool> used(10, false);
        
        // 嘗試 1 到 9 作為起點
        for(int i = 1; i <= 9; ++i) {
            used[i] = true;
            helper(pattern, 0, i, used);
            used[i] = false;
        }

        return to_string(mm);
    }
    
private:
    void helper(string& s, int idx, int cur, vector<bool>& used) {
        // 到達終點，記錄最佳解
        if(idx == s.size()) {
            mm = min(mm, cur);
            return;
        }

        int now = cur * 10;
        
        // 貪婪從 1 測試到 9，且一旦 mm 被更新就終止迴圈 (提早結束)
        for(int i = 1; i <= 9 && mm == INT_MAX; ++i) {
            if(used[i]) continue;
            
            // 驗證是否符合遞增或遞減條件
            if((s[idx] == 'I' && (cur % 10 < i)) || (s[idx] == 'D' && (cur % 10 > i))) {
                used[i] = true;
                helper(s, idx + 1, now + i, used);
                used[i] = false; // 回溯
            }  
        }
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