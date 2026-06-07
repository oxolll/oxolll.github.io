---
title: "LeetCode 2196. Create Binary Tree From Descriptions"
date: 2026-06-07T13:40:00+08:00
lastmod: 2026-06-07T13:40:00+08:00
difficulty: 1643
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "tree", "hash-table", "graph"]
keywords: ["LC2196", "Medium", "1643", "Create Binary Tree", "二元樹", "雜湊表", "入度", "單次掃描"]
description: "LeetCode 第 2196 題：Create Binary Tree From Descriptions。難度評分：1643。探討如何利用 Hash Map 高效實例化與拼接節點，並搭配 Hash Set 單次掃描動態維護入度為 0 的根節點，達成 O(N) 的最優建樹演算法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2196](https://leetcode.com/problems/create-binary-tree-from-descriptions/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1643)</span>  
> **核心主題**：`Tree` $\cdot$ `Hash Table` $\cdot$ `Graph`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個二維整數陣列 `descriptions`，其中 `descriptions[i] = [parent_i, child_i, isLeft_i]` 代表著一個二元樹的節點關係：
- `parent_i` 是 `child_i` 的父節點。
- 如果 `isLeft_i == 1`，表示 `child_i` 是左子節點。
- 如果 `isLeft_i == 0`，表示 `child_i` 是右子節點。

請你根據這些描述構造出一棵二元樹，並回傳其 **根節點 (Root)**。
題目保證輸入的資料可以構造出一棵有效的二元樹。

### 範例
- **Input**: `descriptions = [[20,15,1],[20,17,0],[50,20,1],[50,80,0],[80,19,1]]`
- **Output**: `[50,20,80,15,17,19]` (回傳根節點 50，此為層序遍歷的結果)
- **Explanation**: 
  50 是根節點，因為它從未作為任何節點的子節點 (入度為 0)。

### 限制條件
- $1 \le descriptions.length \le 10^4$
- $1 \le parent_i, child_i \le 10^5$
- $0 \le isLeft_i \le 1$
- `descriptions` 保證可以構造出一棵有效的二元樹。
</details>

---

## 📝 歷次打卡與更新
- [2026-06-07：初次提交 (Hash Map 節點映射與單次掃描尋根法)](#2026-06-07-初次提交)

---

## 💡 2026-06-07 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標有兩個：一是「實例化並連接節點」，二是「找出這棵樹的 Root」。
由於陣列中給出的關係是無序的，我們無法預知哪一個節點會先出現。

**核心破局點 1：無序節點的實例化 (Hash Map)**
我們可以利用一個 `unordered_map<int, TreeNode*>` 來對映「節點值」與「實體節點的記憶體位址」。當讀取到 `parent` 或 `child` 時，如果它還不存在於 Map 中，我們就立刻 `new` 出來；如果已經存在，就直接取用，確保全域只有唯一一個對應的實體。

**核心破局點 2：圖論中的入度思維 (In-degree)**
一棵樹的「根節點」有一個絕對特性：**它絕對不會是任何人的子節點（入度為 0）**。
為了達成「單次掃描 (Single Pass)」的最佳效能，我們可以維護一個候選人集合 `cand`：
- 當一個節點以 `parent` 身份第一次被創建時，它有可能是 Root，加入 `cand`。
- 當一個節點以 `child` 身份出現時，它 **絕對不可能** 是 Root，若它存在於 `cand` 中則將其剔除。
迴圈結束後，`cand` 中必然只會剩下唯一的元素，那就是整棵樹的 Root！

### 🛠️ 解題思路 (Approach)
1. 宣告 `mp` 負責儲存所有節點指標，`cand` 負責追蹤潛在的 Root。
2. 遍歷 `descriptions`，解析出 `p` (父節點), `c` (子節點), `isLeft` (方向)。
3. **處理父節點**：若 `p` 不在 `mp` 中，將其創建，並將其視為 Root 候選人加入 `cand`。
4. **處理子節點**：若 `c` 不在 `mp` 中，將其創建。
5. **剝奪 Root 資格**：既然 `c` 成為了別人的子節點，它就失去了當 Root 的資格，若 `c` 存在於 `cand` 中則 `erase(c)`。
6. **建立連結**：根據 `isLeft`，將 `mp[p]` 的 `left` 或 `right` 指向 `mp[c]`。
7. **回傳結果**：迴圈結束後，取出 `cand` 中的唯一元素，利用 `mp` 找到其實體指標並回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是 `descriptions` 陣列的長度。我們只對陣列進行了一次線性掃描，內部對 Hash Map 和 Hash Set 的操作 (insert, find, erase) 均攤時間皆為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N)$。在最壞情況下，每個節點都需要被儲存在 `mp` 中，最多有 $N+1$ 個節點；而 `cand` 集合最多也會儲存常數倍的節點。整體空間與節點數量成正比。

### 💻 程式碼實作 (C++)
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 * int val;
 * TreeNode *left;
 * TreeNode *right;
 * TreeNode() : val(0), left(nullptr), right(nullptr) {}
 * TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 * TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* createBinaryTree(vector<vector<int>>& descriptions) {
        // mp 用於映射節點值與實際的 TreeNode 指標
        unordered_map<int, TreeNode*> mp;
        
        // cand 用於動態追蹤可能成為 Root 的候選節點
        unordered_set<int> cand;
        
        for(int i = 0; i < descriptions.size(); ++i) {
            int p = descriptions[i][0];
            int c = descriptions[i][1];
            bool isLeft = descriptions[i][2];
            
            // 如果父節點尚未建立，建立它並暫列為 Root 候選人
            if(mp.find(p) == mp.end()) {
                mp[p] = new TreeNode(p);
                cand.insert(p);
            }

            // 如果子節點尚未建立，建立它 (不需要放入 cand，因為它確定是別人的 child)
            if(mp.find(c) == mp.end()) {
                mp[c] = new TreeNode(c);
            }

            // 無論子節點是否剛建立，它既然作為 child 出現，就絕對不能是 Root
            if(cand.find(c) != cand.end()) {
                cand.erase(c);
            }
            
            // 連接父子節點
            if(isLeft) {
                mp[p]->left = mp[c];
            } else {
                mp[p]->right = mp[c];
            }
        }

        // 唯一留在 cand 裡面的節點，就是整棵樹的根節點
        return mp[*(cand.begin())];
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