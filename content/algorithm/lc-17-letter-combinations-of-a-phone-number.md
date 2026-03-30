---
title: "LeetCode 17. Letter Combinations of a Phone Number"
date: 2026-03-31T10:15:00+08:00
lastmod: 2026-03-31T10:15:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "backtracking", "hash table"]
keywords: ["LC17", "Medium", "N/A", "Letter Combinations", "Phone Number", "DFS", "Backtracking"]
description: "LeetCode 第 17 題：Letter Combinations of a Phone Number。難度評分：Medium (N/A)。探討如何利用 Hash Map 建立鍵盤映射，並透過深度優先搜尋 (DFS) 與回溯法 (Backtracking) 列舉出所有可能的字母排列組合。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 17](https://leetcode.com/problems/letter-combinations-of-a-phone-number/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Backtracking` $\cdot$ `Hash Table`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個僅包含數字 `2-9` 的字串，請回傳該數字可能代表的所有字母組合。你可以按任意順序回傳答案。
數字到字母的映射與傳統電話按鍵相同（例如 `2` 對應 `"abc"`，`9` 對應 `"wxyz"`）。注意 `1` 不對應任何字母。

### 限制條件
- $0 \le digits.length \le 4$
- `digits[i]` 是範圍 `['2', '9']` 內的數字。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-31：初次提交 (DFS 回溯與字串傳值)](#2026-03-31-初次提交)

---

## 💡 2026-03-31 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的排列組合窮舉題，最適合使用深度優先搜尋 (DFS) 搭配回溯法 (Backtracking) 來解決。
每一個數字對應一組可選的字母集合，字串長度即為決策樹的深度。我們只需建立一個 Hash Map（或陣列）來儲存 `2` 到 `9` 的字元映射，接著利用遞迴依序向下一個數字展開所有可能的字母，當達到字串結尾時便將結果收集起來。

*(邊界條件提醒：若輸入為空字串，應直接回傳空陣列，避免遞迴收集到無效的空字串 `""`。)*

### 🛠️ 解題思路 (Approach)
1. **建立映射表**：宣告一個 `unordered_map<int, string> mp`，將數字 `2` 到 `9` 對應的字串存入。
2. **防呆檢查**：若 `digits` 為空字串，直接回傳空的結果陣列 `rt`。
3. **定義 DFS 遞迴函式**：
   - 參數：`idx` (當前處理到的 `digits` 索引)、`digits` (原始輸入字串的參考)、`cur` (當前累積的字串組合)。
   - **終止條件**：若 `idx == digits.size()`，代表已構造出完整長度的組合，將 `cur` 推入結果陣列 `rt`，並返回。
   - **遞迴展開**：取得當前數字對應的字串集合 `mp[digits[idx] - '0']`。遍歷該集合中的每一個字元 `c`，呼叫 `dfs(idx + 1, digits, cur + c)` 進入下一層遞迴。
4. **啟動搜尋**：在主函式呼叫 `dfs(0, digits, "")`，完成後回傳 `rt`。

*(技術註記：此處 `cur` 參數採用「傳值 (Pass by Value)」並利用 `cur + c` 隱式產生新字串，這省去了手動呼叫 `cur.pop_back()` 的回溯步驟，讓程式碼更簡潔。由於 $N \le 4$，字串複製的常數開銷極小，此寫法在效能與可讀性間取得了良好的平衡。)*

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(4^N \cdot N)$。其中 $N$ 是 `digits` 的長度。最差情況下（例如包含多個 `7` 或 `9`），每個數字都有 4 種選擇，總共產生 $4^N$ 種組合。每次達到終點時，字串複製/加入陣列耗時 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(N)$。遞迴呼叫堆疊的最大深度為 $N$。儲存結果的 `rt` 陣列不計入額外空間複雜度。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<string> rt;
    unordered_map<int, string> mp;
    
    vector<string> letterCombinations(string digits) {
        // 防呆：空字串直接回傳空陣列
        if(digits.empty()) return rt;
        
        mp[2] = "abc", mp[3] = "def", mp[4] = "ghi", mp[5] = "jkl", 
        mp[6] = "mno", mp[7] = "pqrs", mp[8] = "tuv", mp[9] = "wxyz";

        dfs(0, digits, "");
        return rt;
    }
    
private:
    void dfs(int idx, string& digits, string cur) {
        // 終止條件：已處理完所有數字
        if(idx == digits.size()) {
            rt.push_back(cur);
            return;
        }

        // 走訪當前數字對應的所有可能字母
        for(auto c : mp[digits[idx] - '0']) {
            // 利用字串相加隱式傳遞新狀態，省去手動 pop_back() 的回溯操作
            dfs(idx + 1, digits, cur + c);
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