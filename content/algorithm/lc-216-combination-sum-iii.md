---
title: "LeetCode 216. Combination Sum III"
date: 2026-03-28T13:40:00+08:00
lastmod: 2026-03-28T13:40:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "backtracking"]
keywords: ["LC216", "Medium", "N/A", "Combination Sum", "Backtracking", "DFS"]
description: "LeetCode 第 216 題：Combination Sum III。難度評分：Medium (N/A)。探討如何利用回溯法 (Backtracking) 在限定的數字範圍內，找出所有總和等於特定目標值且長度固定的數字組合，並加入剪枝優化。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 216](https://leetcode.com/problems/combination-sum-iii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Backtracking`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
請找出所有相加之和為 `n` 的 `k` 個數的組合，且滿足下列條件：
- 僅能使用數字 1 到 9。
- 每個數字最多只能使用一次。

請回傳所有可能的有效組合列表。回傳的列表不能包含重複的組合，但組合內的數字順序不限。

### 限制條件
- $2 \le k \le 9$
- $1 \le n \le 60$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-28：初次提交 (帶有剪枝的 DFS 回溯法)](#2026-03-28-初次提交)

---

## 💡 2026-03-28 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題要求找出特定長度與特定總和的組合，這類「列舉所有可能」的問題是標準的 **回溯法 (Backtracking / DFS)** 題型。
為了避免產生重複的組合（例如 `[1,2,4]` 與 `[1,4,2]`），我們在遞迴時必須嚴格保持數字的選取順序（由小到大）。同時，由於只能選取 1 到 9，搜尋空間非常有限。我們可以透過在迴圈中加入條件判斷進行**剪枝 (Pruning)**：如果當前累積的總和加上下一個數字已經超過目標值 `n`，則後續更大的數字也必然超過，可直接中斷該層迴圈。

### 🛠️ 解題思路 (Approach)
1. **初始化**：宣告全域的二維陣列 `rt` 儲存結果。在主函式中宣告暫存的一維陣列 `tmp`，並呼叫遞迴輔助函式 `helper`。
2. **設計 DFS 函式 (`helper`)**：
   - 參數：`cur` (當前組合)、`sum` (當前總和)、`from` (下一個可選數字的起點)、`r` (剩餘需要挑選的數字個數)、`tar` (目標總和 `n`)。
   - **終止條件**：當 `r == 0` (已挑選 $k$ 個數字) 時，檢查 `sum` 是否等於 `tar`。若相等，則將 `cur` 推入 `rt`；否則直接 `return` 結束該分支。
   - **遞迴展開與剪枝**：使用迴圈從 `from` 遍歷至 `9`。
     - *剪枝*：若 `sum + i > tar`，直接 `return` 結束迴圈，因為後續的數字只會更大。
     - 推進狀態：將 `i` 加入 `cur`，並呼叫 `helper` 進入下一層 (傳入 `sum + i`, `i + 1`, `r - 1`)。
     - 回溯 (Backtracking)：從 `cur` 中彈出 `i` (`pop_back()`)，以嘗試下一個數字。
3. **回傳結果**：DFS 執行完畢後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(C_k^9)$。在最差情況下，需要遍歷從 9 個數字中選出 $k$ 個數字的所有組合。由於加入剪枝，實際執行次數遠小於此理論上限。
- **空間複雜度**: $\mathcal{O}(k)$。遞迴呼叫堆疊的最大深度為 $k$，暫存陣列 `cur` 的長度最多也為 $k$。回傳結果 `rt` 不計入額外空間複雜度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<vector<int>> rt;
    
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<int> tmp;
        helper(tmp, 0, 1, k, n);
        return rt;
    }
    
private:
    void helper(vector<int>& cur, int sum, int from, int r, int tar) {
        // 終止條件：當挑選數量歸零
        if(r == 0) {
            // 若總和不符，放棄此組合
            if(sum != tar) return;
            rt.push_back(cur);
            return;
        }

        // 從 from 嘗試到 9
        for(int i = from; i <= 9; ++i) {
            // 剪枝：若加入當前數字已超過目標值，後續數字必定也超過
            if(sum + i > tar) return;
            
            // 推進狀態
            cur.push_back(i);
            helper(cur, sum + i, i + 1, r - 1, tar);
            
            // 回溯復原狀態
            cur.pop_back();
        }

        return;
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