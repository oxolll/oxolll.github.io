---
title: "LeetCode 3093. Longest Common Suffix Queries"
date: 2026-05-28T12:20:00+08:00
lastmod: 2026-05-28T12:20:00+08:00
difficulty: 2118
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "trie", "design"]
keywords: ["LC3093", "Hard", "2118", "Longest Common Suffix", "Trie", "字典樹", "後綴查詢"]
description: "LeetCode 第 3093 題：Longest Common Suffix Queries。難度評分：2118。探討如何將後綴匹配問題轉化為倒序字串的字典樹 (Trie) 前綴匹配，並透過擴充 Trie 節點狀態預處理最佳解，實現極速查詢。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3093](https://leetcode.com/problems/longest-common-suffix-queries/)  
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2118)</span>  
> **核心主題**：`String` $\cdot$ `Trie` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個字串陣列 `wordsContainer` 和 `wordsQuery`。
對於每個 `wordsQuery[i]`，你需要從 `wordsContainer` 中找出一個與其有 **最長公共後綴 (Longest Common Suffix)** 的字串。

如果有多個字串與查詢字串有相同長度的最長公共後綴，請依據以下優先級來打破平手 (Tie-breaking)：
1. 選擇在 `wordsContainer` 中 **長度最短** 的字串。
2. 如果長度仍然相同，則選擇在 `wordsContainer` 中 **索引最小** 的字串。

請回傳一個整數陣列 `ans`，其中 `ans[i]` 是 `wordsQuery[i]` 的最佳匹配字串在 `wordsContainer` 中的索引。

### 範例
- **Input**: `wordsContainer = ["abcd","bcd","xbcd"], wordsQuery = ["cd","bcd","xyz"]`
- **Output**: `[1,1,1]`
- **Explanation**:
  - 查詢 `"cd"`：與 `"abcd"`, `"bcd"`, `"xbcd"` 的公共後綴長度分別為 2, 2, 2。長度最短的是 `"bcd"`，索引為 1。
  - 查詢 `"bcd"`：與三者的公共後綴長度分別為 3, 3, 3。長度最短的是 `"bcd"`，索引為 1。
  - 查詢 `"xyz"`：沒有任何公共後綴（長度為 0）。長度最短的是 `"bcd"`，索引為 1。

### 限制條件
- $1 \le wordsContainer.length, wordsQuery.length \le 10^4$
- $1 \le wordsContainer[i].length \le 5000$
- $1 \le wordsQuery[i].length \le 5000$
- 兩個陣列中所有字串的總長度均不超過 $5 \cdot 10^4$。
- 字串僅由小寫英文字母組成。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-28：初次提交 (倒序 Trie 與節點狀態預處理)](#2026-05-28-初次提交)

---

## 💡 2026-05-28 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
尋找字串的「最長公共後綴」在正向比對時非常困難，但只要我們將字串 **倒序 (Reverse)** 來看，「後綴」就變成了「前綴」。處理「最長公共前綴」正是 **字典樹 (Trie)** 最擅長的工作！

**核心破局點：Trie 節點的狀態擴充 (Node Augmentation)**
題目有嚴格的平手判定規則：優先選長度短的，再選索引小的。
如果我們在查詢時才去子樹中尋找符合條件的字串，會導致時間複雜度退化（甚至超時）。
因此，我們可以在 **「建立 Trie 的過程」** 中，就把這些資訊記錄在每個節點上：
- `cur_mm`：記錄經過此節點的所有字串中，**最短的字串長度**。
- `cur_idx`：記錄該最短字串對應的 **原始索引**。

當我們將 `wordsContainer` 中的字串由後往前逐一插入 Trie 時，每經過一個節點，就更新一次該節點的 `cur_mm` 與 `cur_idx`。
這樣一來，對於任何一個查詢 `query`，我們只需要順著 Trie 由後往前盡可能地深入。**無論在哪裡停下（可能是查詢字串結束，或是沒有匹配的子節點），當前停留節點上的 `cur_idx` 就是該查詢的全域最佳解答！**

### 🛠️ 解題思路 (Approach)
1. **定義 Trie 節點 (`Node`)**：
   - `next`：大小為 26 的指標陣列。
   - `cur_mm`：初始化為 `INT_MAX`。
   - `cur_idx`：初始化為 `INT_MAX`。
2. **建構倒序 Trie**：
   - 遍歷 `wordsContainer`，取出字串 `word`、長度 `len` 以及索引 `i`。
   - 撰寫遞迴函數 `dfs`（或迭代），從字串的最後一個字元開始往回掃描，將字元插入 Trie 中。
   - **關鍵更新**：在進入每一個節點時，若 `cur_mm > len`，或是 `cur_mm == len` 且 `i < cur_idx`，則更新該節點的記錄。
3. **處理查詢**：
   - 遍歷 `wordsQuery`，對於每個字串，從尾部開始在 Trie 中進行匹配。
   - 當遇到字串結尾或找不到下一個字元時，立刻中斷，並將當前節點的 `cur_idx` 推入結果陣列。
4. **資源釋放**：
   - 撰寫 `free()` 函數，遞迴刪除動態配置的 Trie 節點，防止記憶體洩漏。

### 📊 複雜度分析
令 $N$ 為 `wordsContainer` 的總字元數，$M$ 為 `wordsQuery` 的總字元數。
- **時間複雜度**: $\mathcal{O}(N + M)$。
  - 建立 Trie：需遍歷所有候選字串的所有字元，花費 $\mathcal{O}(N)$。
  - 查詢：每個查詢字串最多沿著 Trie 走完自身的長度，花費 $\mathcal{O}(M)$。
  - 記憶體釋放同樣耗時 $\mathcal{O}(N)$。整體為線性時間，達到理論最優。
- **空間複雜度**: $\mathcal{O}(N \times 26)$。最壞情況下（沒有任何公共後綴），每個字元都會建立一個新的 Trie 節點，每個節點包含長度為 26 的指標陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 擴充狀態的字典樹節點
    class Node {
    public:
        vector<Node*> next;
        int cur_mm;  // 經過此路徑的所有字串中，最小的長度
        int cur_idx; // 該最小長度字串的原始索引
        
        Node() : cur_mm(INT_MAX), cur_idx(INT_MAX) {
            next.assign(26, nullptr);
        }
    };
    
    // 遞迴釋放字典樹記憶體
    void free(Node* cur) {
        for(int i = 0; i < 26; ++i) {
            if(cur->next[i] != nullptr) {
                free(cur->next[i]);
                delete cur->next[i];
            }
        }
    }
    
    // 遞迴插入字串 (倒序插入) 並更新節點狀態
    void dfs(string& word, int ith, int idx, Node* cur, int l) {
        // 更新當前節點的最優解狀態
        if(cur->cur_mm >= l) {
            // 長度更短，或長度相同但索引更小，則更新
            if(cur->cur_mm > l || ith < cur->cur_idx) {
                cur->cur_idx = ith;
            }
            cur->cur_mm = l;
        }

        // 遞迴終止條件：字串已全部插入
        if(idx < 0) return;

        int c = word[idx] - 'a';
        if(cur->next[c] == nullptr) {
            cur->next[c] = new Node();
        }

        // 繼續向字串前方 (Trie的深處) 遞迴
        dfs(word, ith, idx - 1, cur->next[c], l);
    }
    
    vector<int> stringIndices(vector<string>& wordsContainer, vector<string>& wordsQuery) {
        Node* p = new Node();
        
        // 1. 將所有容器內的字串倒序插入 Trie 中
        for(int i = 0; i < wordsContainer.size(); ++i) {
            int len = wordsContainer[i].size();
            dfs(wordsContainer[i], i, len - 1, p, len);
        }

        vector<int> rt; 
        rt.reserve(wordsQuery.size());
        
        // 2. 處理每一個查詢
        for(auto& s : wordsQuery) {
            auto q = p;
            // 由後往前在 Trie 中匹配
            for(int i = s.size() - 1; i >= 0; --i) {
                // 若路徑斷掉，直接中斷，當前停留的節點即為最長公共後綴
                if(q->next[s[i] - 'a'] == nullptr) break;
                q = q->next[s[i] - 'a'];
            }
            // 將停留節點上預先算好的最佳索引加入結果
            rt.push_back(q->cur_idx);
        }

        // 3. 安全釋放資源
        free(p);

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