---
title: "LeetCode 1268. Search Suggestions System"
date: 2026-04-01T10:15:00+08:00
lastmod: 2026-04-01T10:15:00+08:00
difficulty: 1573
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "string", "trie", "depth-first-search"]
keywords: ["LC1268", "1573", "Medium", "Search Suggestions System", "Trie", "DFS"]
description: "LeetCode 第 1268 題：Search Suggestions System。難度評分：1573。探討如何利用字典樹 (Trie) 儲存字彙庫，並配合深度優先搜尋 (DFS) 在每個前綴節點找出字典序最小的前三個推薦字詞。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1268](https://leetcode.com/problems/search-suggestions-system/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1573)</span>  
> **核心主題**：`Array` $\cdot$ `String` $\cdot$ `Trie` $\cdot$ `Depth-First-Search`

---

## 📝 歷次打卡與更新
- [2026-04-01：初次提交 (Trie 搭配 DFS 搜尋)](#2026-04-01-初次提交)

---

## 💡 2026-04-01 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
此題是標準的自動補齊 (Auto-complete) 情境。最直觀且效能穩定的資料結構是 **字典樹 (Trie)**。
將所有 `products` 插入 Trie 後，當使用者輸入 `searchWord` 時，我們逐字元在 Trie 中向下移動。停在某個前綴節點時，題目要求找出以該前綴開頭且「字典序最小的 3 個字串」。
因為 Trie 的指標陣列 (`next[26]`) 天生就具備了 `a` 到 `z` 的字典序排列特性，我們只要從當前節點發動深度優先搜尋 (DFS)，依序走訪子節點並收集遇到的完整字串 (`isend == true`)，最先收集到的 3 個字串必然是字典序最小的。

### 🛠️ 解題思路 (Approach)
1. **建立 Trie 結構**：
   - 定義 `Node` 包含大小為 26 的指標陣列與 `isend` 標記。
   - *(技術細節：在解構子中，使用 `new` 配置的節點必須使用 `delete` 釋放記憶體，避免使用 `free()` 產生未定義行為)*。
2. **資料插入**：將 `products` 中的所有字串插入 Trie 中。
3. **前綴搜尋與 DFS**：
   - 走訪 `searchWord`，記錄當前累積的前綴字串 `cur`，並將指標 `p` 向下移動。
   - 若途中發現 `p == nullptr`，代表 Trie 中不再有此字首的商品，立即中斷走訪。
   - 在每個有效的 `p` 節點，呼叫 `dfs` 函式，由小到大 (`0` 到 `25`) 遞迴深入，一旦找到 3 個字串就 `return` 結束該次搜尋。
   - 將找到的陣列推入結果陣列 `rt` 中。
4. **補齊空陣列**：若 `searchWord` 比對到一半就斷了，剩餘的字元對應的推薦列表必須是空的，因此依據數量補齊 `{}`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M + L \times 26)$。其中 $M$ 是所有 `products` 的總字元數（用於建構 Trie），$L$ 是 `searchWord` 的長度。每次 DFS 最多走訪找出 3 個字串，耗時受到常數限制。
- **空間複雜度**: $\mathcal{O}(M)$。Trie 節點總數取決於商品字串的前綴重疊程度，最差情況下為所有字元的總和。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
    class Node {
    public:
        vector<Node*> next;
        bool isend;
        
        Node() {
            next = vector<Node*>(26, nullptr);
            isend = false;
        }
        
        // 修正：new 配置的物件必須以 delete 釋放，而非 free
        ~Node() {
            for(int i = 0; i < 26; ++i) 
                delete next[i];
        }
    };
    
    Node* root;

    void dfs(Node* p, vector<string>& tmp, string& cur) {
        if(p->isend) {
            tmp.push_back(cur);
            if(tmp.size() == 3) return;
        }

        // 依據 a-z 順序遞迴，確保找出的是字典序最小的組合
        for(int i = 0; i < 26; ++i) {
            if(p->next[i] == nullptr) continue;
            
            cur.push_back('a' + i);
            dfs(p->next[i], tmp, cur);
            cur.pop_back(); // 回溯復原
            
            if(tmp.size() == 3) return;
        }
        return;
    }
    
public:
    vector<vector<string>> suggestedProducts(vector<string>& products, string searchWord) {
        root = new Node();
        
        // 1. 將所有產品名稱加入 Trie
        for(auto product : products) {
            Node* p = root;
            for(auto c : product) {
                if(p->next[c - 'a'] == nullptr) {
                    p->next[c - 'a'] = new Node();
                }
                p = p->next[c - 'a'];
            }
            p->isend = true;
        }

        vector<vector<string>> rt;
        Node* p = root;
        string cur = "";
        
        // 2. 依序處理使用者輸入的每個字元
        for(auto c : searchWord) {
            cur += c;
            p = p->next[c - 'a'];
            
            // 若 Trie 中已無此字首，終止搜尋
            if(p == nullptr) break;
            
            vector<string> tmp;
            dfs(p, tmp, cur);
            rt.push_back(tmp);
        }
        
        // 3. 針對後續無法匹配的字元補上空的推薦清單
        int n = rt.size();
        for(int i = 0; i < searchWord.size() - n; ++i) {
            rt.push_back({});
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