---
title: "LeetCode 1415. The k-th Lexicographical String of All Happy Strings of Length n"
date: 2026-03-15T15:27:00+08:00
lastmod: 2026-03-15T15:27:00+08:00
difficulty: 1575
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Backtracking"]
keywords: ["LC1415", "1575", "Medium", "Happy String", "DFS", "Early Stopping"]
description: "LeetCode 第 1415 題：The k-th Lexicographical String of All Happy Strings of Length n。難度評分：1575。探討如何利用帶有提早剪枝 (Early Stopping) 的回溯法，按字典序高效生成並尋找第 k 個快樂字串。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1415](https://leetcode.com/problems/the-k-th-lexicographical-string-of-all-happy-strings-of-length-n/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1575)</span>    
> **核心主題**：`String` $\cdot$ `Backtracking`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
一個 **快樂字串 (Happy String)** 是一個僅由字元 `'a'`, `'b'`, `'c'` 組成的字串，且滿足所有相鄰的字元都不相同（即 $s[i] \neq s[i+1]$）。
給定兩個整數 $n$ 和 $k$。請考慮所有長度為 $n$ 的快樂字串，並將它們按照**字典序 (Lexicographical Order)** 進行排序。

請回傳此排序列表中的第 $k$ 個字串。如果長度為 $n$ 的快樂字串總數少於 $k$，則回傳空字串 `""`。

### 限制條件
- $1 \le n \le 10$
- $1 \le k \le 100$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (DFS 回溯法與 k 值提早剪枝)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
題目的 $k$ 最大只有 100，這暗示了我們完全可以直接「按照順序」把快樂字串一個一個構造出來，直到找到第 $k$ 個為止。
構造所有組合的問題，首選絕對是 **回溯法 (Backtracking / DFS)**。為了保證結果是「字典序」，我們在每個位置的選擇順序必須嚴格按照 `'a' \to 'b' \to 'c'` 來遞迴。
為了避免 Time Limit Exceeded (TLE)，我們不需要找出所有的快樂字串。只要我們收集到的字串數量達到 $k$，就可以利用條件判斷切斷後續的所有遞迴分支，達成極速的 Early Stopping。

### 🛠️ 解題思路 (Approach)
1. **全域狀態維護**：
   - 宣告一個 `vector<string> col` 來儲存生成的快樂字串。
   - 預先 `col.reserve(105)` 來防止動態擴容的記憶體配置開銷。
2. **DFS 回溯構造**：
   - 傳入當前正在構造的字串 `s` (傳參考 `string&` 以節省拷貝開銷)。
   - **終止條件**：當 `s.size() == n` 時，代表成功構造出一個完整的快樂字串，將其推入 `col` 後回傳。
   - **選擇與驗證**：迴圈嘗試放入 `'a'`, `'b'`, `'c'`。
     - 若 `s` 非空且當前字元與 `s.back()` 相同，違反快樂字串定義，跳過 (`continue`)。
     - 否則，將字元推入 `s`，遞迴進入下一層 `helper`，出來後將字元彈出 (`pop_back`) 以恢復狀態。
3. **提早剪枝 (Pruning)**：
   - 在 `for` 迴圈的條件中加入 `col.size() < k`。只要陣列裡已經收集滿 $k$ 個字串，這個條件會瞬間阻止所有同層與上層的迴圈繼續執行，讓遞迴樹迅速收斂退出。
4. **輸出結果**：
   - 檢查 `col` 的大小。若小於 $k$，代表總數不足，回傳 `""`；否則回傳 `col[k-1]`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(K \cdot N)$。我們最多只會生成 $K$ 個長度為 $N$ 的字串。由於有了提早剪枝，一旦達到 $K$ 個就會停止搜尋，因此時間複雜度嚴格受限於 $K$，而非常規全排列的 $\mathcal{O}(3 \cdot 2^{N-1})$。
- **空間複雜度**: $\mathcal{O}(K \cdot N)$。儲存 $K$ 個長度為 $N$ 的字串需要這些空間。DFS 遞迴的 Call Stack 深度最大為 $N$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<string> col;
    
    string getHappyString(int n, int k) {
        string s = "";
        col.reserve(105); // k 最大為 100，預先配置記憶體避免 reallocation
        helper(n, k, s);
        
        // 若生成的快樂字串總數不到 k 個，回傳空字串
        return col.size() < k ? "" : col[k - 1];
    }
    
private:
    void helper(int n, int k, string& s) {
        // 達到目標長度，儲存結果
        if(s.size() == n) {
            col.push_back(s);
            return;
        }

        // 字典序嘗試 'a', 'b', 'c'
        // 提早剪枝：一旦收集滿 k 個，立刻停止所有後續遞迴
        for(int i = 'a'; i <= 'c' && col.size() < k; ++i) {
            // 快樂字串條件：相鄰字元不得相同
            if(!s.empty() && s.back() == i) continue;
            
            s += i;
            helper(n, k, s);
            s.pop_back(); // 狀態回溯
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