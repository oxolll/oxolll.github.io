---
title: "LeetCode 208. Implement Trie (Prefix Tree)"
date: 2026-03-29T21:15:00+08:00
lastmod: 2026-03-29T21:15:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "trie", "design", "string"]
keywords: ["LC208", "Medium", "N/A", "Prefix Tree", "Trie"]
description: "LeetCode 第 208 題：Implement Trie (Prefix Tree)。難度評分：Medium (N/A)。探討如何設計與實作前綴樹 (Trie)，透過節點與指標陣列以 O(L) 時間複雜度高效支援字串插入、完全匹配搜尋與前綴搜尋。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 208](https://leetcode.com/problems/implement-trie-prefix-tree/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Design` $\cdot$ `Trie` $\cdot$ `Hash Table` $\cdot$ `String`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
前綴樹 (Trie) 是一種樹狀資料結構，用於高效地儲存和檢索字串資料集中的鍵值。這一資料結構有相當多的應用情境，例如自動補齊和拼寫檢查。

請實作 `Trie` 類別：
- `Trie()` 初始化前綴樹物件。
- `void insert(String word)` 將字串 `word` 插入前綴樹中。
- `boolean search(String word)` 如果字串 `word` 在前綴樹中，回傳 `true`（即在之前已被插入）；否則回傳 `false`。
- `boolean startsWith(String prefix)` 如果之前已經插入的字串 `word` 的前綴之一為 `prefix`，回傳 `true`；否則回傳 `false`。

### 限制條件
- $1 \le word.length, prefix.length \le 2000$
- `word` 和 `prefix` 僅由小寫英文字母組成。
- `insert`、`search` 和 `startsWith` 呼叫次數總計最多 $3 \times 10^4$ 次。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-29：初次提交 (靜態陣列節點與合併搜尋邏輯)](#2026-03-29-初次提交)

---

## 💡 2026-03-29 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
Trie 的核心概念是將字串拆解為單一字元，並依序儲存於樹的節點中。每個節點包含一個長度為 26 的指標陣列（對應 `a` 到 `z`）以及一個布林值 `isend`（標記該節點是否為某個單字的結尾）。
由於共用相同前綴的字串會共享同一條路徑上的節點，這使得 Trie 在處理大量具有重複前綴的字串時，能提供極高的空間利用率與檢索速度。尋找任何長度為 $L$ 的字串，皆只需追蹤 $L$ 個節點，時間複雜度嚴格控制在 $\mathcal{O}(L)$。

### 🛠️ 解題思路 (Approach)
1. **結構設計 (`Node`)**：
   - 包含 `vector<Node*> next` 陣列，大小固定為 26，初始化為 `NULL`。
   - 包含布林變數 `isend`，初始化為 `false`。
   - 實作解構子 `~Node()`，在物件銷毀時遞迴呼叫 `delete` 釋放子節點記憶體，防止 Memory Leak。
2. **插入操作 (`insert`)**：
   - 設定指標 `p` 指向根節點 `root`。
   - 遍歷字串 `word` 的每個字元 `c`，計算索引 `c - 'a'`。
   - 若該索引對應的指標為 `NULL`，則動態配置一個新的 `Node`。
   - 將 `p` 移動至該子節點。走訪結束後，將最後一個節點的 `isend` 設為 `true`。
3. **搜尋邏輯合併 (`search` 與 `startsWith`)**：
   - 為了減少程式碼重複，`search` 函式增加了一個預設參數 `bool ispre = false`。
   - 從根節點開始，依據字元逐層向下尋找。若遇到 `NULL` 指標，代表樹中不存在該路徑，直接回傳 `false`。
   - 迴圈順利結束時，代表路徑存在。若 `ispre` 為 `true` (來自 `startsWith` 的呼叫)，代表只需確保前綴存在，直接回傳 `true`；否則回傳 `p->isend` 確認是否為完整的單字結尾。

### 📊 複雜度分析
- **時間複雜度**: 
  - `insert`: $\mathcal{O}(L)$，其中 $L$ 是插入字串的長度。
  - `search`: $\mathcal{O}(L)$，其中 $L$ 是搜尋字串的長度。
  - `startsWith`: $\mathcal{O}(L)$，其中 $L$ 是前綴字串的長度。
- **空間複雜度**: $\mathcal{O}(N \times L \times |\Sigma|)$。其中 $N$ 是插入的字串數量，$L$ 是平均長度，$|\Sigma| = 26$ 是字元集大小。最差情況下（所有字串無共同前綴）需要配置等同於總字元數量的節點。

### 💻 程式碼實作 (C++)
```cpp
class Trie {
    class Node {
    public:
        vector<Node*> next;
        bool isend;
        
        Node() {
            next = vector<Node*>(26, NULL);
            isend = false;
        }
        
        // 遞迴釋放記憶體
        ~Node() {
            for(int i = 0; i < 26; ++i) {
                delete next[i];
            }
        }
    };
    
    Node* root;

public:
    Trie() {
        root = new Node();
    }
    
    void insert(string word) {
        Node* p = root;
        for(auto c : word) {
            if(p->next[c - 'a'] == NULL) {
                p->next[c - 'a'] = new Node();
            }
            p = p->next[c - 'a'];
        }
        p->isend = true;
    }
    
    // 合併 exact match 與 prefix match 的邏輯
    bool search(string word, bool ispre = false) {
        Node* p = root;
        for(auto c : word) {
            if(p->next[c - 'a'] == NULL) {
                return false;
            }
            p = p->next[c - 'a'];
        }
        
        // 若為前綴搜尋，路徑存在即為真；否則需檢查是否為單字結尾
        return ispre ? true : p->isend;
    }
    
    bool startsWith(string prefix) {
        return search(prefix, true);
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
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