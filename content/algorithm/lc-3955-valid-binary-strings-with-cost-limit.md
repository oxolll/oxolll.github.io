---
title: "LeetCode 3955. Valid Binary Strings With Cost Limit"
date: 2026-06-09T16:00:00+08:00
lastmod: 2026-06-09T16:00:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "backtracking", "string", "dfs"]
keywords: ["LC3955", "Medium", "Valid Binary Strings", "回溯法", "深度優先搜尋", "提早剪枝", "字串構造"]
description: "LeetCode 第 3955 題：Valid Binary Strings With Cost Limit。探討如何利用回溯法 (Backtracking) 構造二進位字串，並透過動態維護成本與提早剪枝 (Early Pruning) 技巧，高效過濾連續 1 與高成本的無效分支。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3955](https://leetcode.com/problems/valid-binary-strings-with-cost-limit/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Backtracking` $\cdot$ `String` $\cdot$ `DFS`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個整數 `n` 和 `k`。請你生成並回傳所有長度為 `n` 且滿足以下條件的二進位字串：
1. **無連續的 1**：字串中不能出現相鄰的 `'1'`（即不存在 `"11"` 的子字串）。
2. **成本限制**：字串的總成本不能超過 `k`。總成本的定義為字串中所有字元為 `'1'` 的 **索引位置（0-based）的總和**。

你可以按 **任意順序** 回傳這些字串。

### 限制條件
- $1 \le n \le 20$ (依據回溯法複雜度推斷)
- $0 \le k \le \frac{n(n-1)}{2}$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (DFS 回溯法與提早剪枝優化)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
構造排列組合的標準解法是 **回溯法 (Backtracking)**。
在每一個索引位置 $i$，我們面臨兩個決策：放入 `'0'` 或放入 `'1'`。

**核心破局點 1：相鄰限制**
放入 `'0'` 沒有任何副作用。但如果要放入 `'1'`，必須檢查字串的最後一個字元是否不為 `'1'` (`s.empty() || s.back() != '1'`)。這能保證生成的字串絕對不會出現 `"11"`。

**核心破局點 2：提早剪枝 (Early Pruning)**
與其等字串長度達到 $n$ 時才去驗證總成本，我們可以在遞迴的過程中動態傳遞 `current_cost`。
當我們打算在索引 $i$ 放入 `'1'` 時，我們預先檢查 `current_cost + i <= k`。如果不滿足，代表這條路走不通，直接放棄該分支。這種「提早剪枝」能極大化地減少無效的遞迴呼叫，讓效能顯著提升。

### 🛠️ 解題思路 (Approach)
1. 宣告全域變數 `rt` 儲存結果。
2. 設計 `dfs` 遞迴函數，接收參數：`s` (當前字串), `n` (目標長度), `k` (成本上限), `current_cost` (當前累積成本)。
3. **遞迴終止條件**：
   - 如果 `current_cost > k`，直接 `return` (防禦性剪枝)。
   - 如果 `s.size() == n`，代表成功構造出一個合法字串，加入 `rt` 並 `return`。
4. **遞迴展開 (選擇與撤銷)**：
   - 取得當前準備填入的索引 `idx = s.size()`。
   - **分支 1 (放 '0')**：直接 `s.push_back('0')`，向下遞迴，完成後 `s.pop_back()` 撤銷。
   - **分支 2 (放 '1')**：確認前一個字元不是 `'1'`，且 `current_cost + idx <= k`。滿足條件則放入 `'1'`，累積成本更新為 `current_cost + idx` 向下遞迴，完成後撤銷。
5. 在主函數 `generateValidStrings` 中呼叫 `dfs` 並回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(F_N)$。其中 $F_N$ 是費氏數列的第 $N$ 項。因為沒有連續的 1，合法字串的總數嚴格被費氏數列限制。加上 `k` 的提早剪枝，實際探索的節點數會遠小於 $2^N$。
- **空間複雜度**: $\mathcal{O}(N)$。遞迴呼叫堆疊的最大深度為 $n$，暫存字串 `s` 的長度最大也是 $n$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<string> rt;

    // current_cost: 記錄當前字串中所有 '1' 的索引總和
    void dfs(string& s, int n, int k, int current_cost) {
        // 提早剪枝：如果中途成本已經超標，直接放棄此分支
        if(current_cost > k) return;

        // 終止條件：字串長度達標，記錄結果
        if(s.size() == n) {
            rt.push_back(s);
            return;
        }

        int idx = s.size(); // 當前準備填入字元的索引位置

        // 選擇 1：填入 '0' (不需要增加 cost，也沒有連續限制)
        s.push_back('0');
        dfs(s, n, k, current_cost);
        s.pop_back(); // 回溯撤銷
        
        // 選擇 2：填入 '1'
        // 前提條件：字串為空，或者最後一個字元不是 '1' (避免連續 1)
        if(s.empty() || s.back() != '1') {
            // 提早剪枝預判：加入這個 '1' 後的成本不能超過 k
            if(current_cost + idx <= k) {
                s.push_back('1');
                dfs(s, n, k, current_cost + idx);
                s.pop_back(); // 回溯撤銷
            }
        }
    }
    
    vector<string> generateValidStrings(int n, int k) {
        string tmp = "";
        // 初始字串為空，累積成本為 0
        dfs(tmp, n, k, 0);
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