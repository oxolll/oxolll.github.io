---
title: "LeetCode 1022. Sum of Root To Leaf Binary Numbers"
date: 2026-02-24T19:39:00+08:00
lastmod: 2026-02-24T19:39:00+08:00
difficulty: 1462
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Tree", "Depth-First Search", "Binary Tree"]
keywords: ["LC1022", "1462", "Easy", "DFS", "二進位轉換"]
description: "LeetCode 第 1022 題：Sum of Root To Leaf Binary Numbers。難度評分：1462。探討如何利用深度優先搜尋 (DFS) 遍歷二元樹，並在路徑中動態計算二進位數值。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1022](https://leetcode.com/problems/sum-of-root-to-leaf-binary-numbers/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy(1462)</span>   
> **核心主題**：`Tree` $\cdot$ `Depth-First Search` $\cdot$ `Binary Tree`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一棵二元樹的根節點 `root`，樹上每個節點的值皆為 `0` 或 `1`。
從根節點到每一個葉子節點 (Leaf Node) 的路徑，都可以代表一個從最高位到最低位的二進位數字。
例如，若路徑為 `0 -> 1 -> 1 -> 0 -> 1`，則代表二進位數字 `01101`，其十進位值為 `13`。

對於所有的葉子節點，請計算並回傳這些路徑所代表的數字**總和**。

### 範例
- **Input**: `root = [1,0,1,0,1,0,1]` → **Output**: `22`
- **Explanation**: 
  (100) + (101) + (110) + (111) = 4 + 5 + 6 + 7 = 22

### 限制條件
- 樹的節點數介於 `[1, 1000]` 之間。
- `Node.val` 必定為 `0` 或 `1`。
</details>

---

## 📝 歷次打卡與更新
- [2026-02-24：初次提交 (DFS 狀態傳遞)](#2026-02-24-初次提交)

---

## 💡 2026-02-24 初次提交

### 🎯 直覺 (Intuition)
要在二元樹中尋找「從根節點到葉子節點」的路徑總和，**深度優先搜尋 (DFS)** 絕對是首選。
而處理二進位字串轉換時，其實不需要真的把字串存起來再轉成整數。因為我們是從最高位 (Root) 往最低位 (Leaf) 走，每一次往下走一層，就相當於把當前的二進位數字「向左平移一位」，並在最低位補上新節點的值。這在數學上等價於 `val = val * 2 + node->val`。

### 🛠️ 解題思路 (Approach)
1. **全域變數記錄總和**：
   宣告一個全域變數 `sum`，用來累加每一條抵達葉子節點時的路徑數值。
2. **DFS 狀態傳遞 (Top-Down)**：
   設計一個 `helper(TreeNode* cur, int val)` 函式，`val` 代表從根節點走到當前節點所累積的數值。
   - 每次進入非空節點時，先更新當前數值：`val = val * 2 + cur->val`。
3. **葉子節點判定與結算**：
   - 如果當前節點沒有左子樹也沒有右子樹 (`cur->left == nullptr && cur->right == nullptr`)，代表走到這條路的盡頭（葉子節點），此時將 `val` 累加進全域變數 `sum` 中，並回傳。
4. **遞迴展開**：
   - 若不是葉子節點，則將更新後的 `val` 繼續傳遞給左右子樹 `helper(cur->left, val)` 與 `helper(cur->right, val)`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$，其中 $N$ 為二元樹的節點總數。我們必須且只需拜訪樹上的每一個節點剛好一次。
- **空間複雜度**: $\mathcal{O}(H)$，其中 $H$ 為二元樹的高度。這是因為遞迴呼叫 (Call Stack) 最大的深度即為樹高。在最差情況下（樹退化成鍊狀），空間複雜度為 $\mathcal{O}(N)$；在最佳情況下（平衡二元樹），空間複雜度為 $\mathcal{O}(\log N)$。

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
    int sum;
    int sumRootToLeaf(TreeNode* root) {
        sum = 0;
        helper(root, 0);
        return sum;
    }
private:    
    void helper(TreeNode* cur, int val){
        if(cur == nullptr) return;
        
        // 將上一層傳下來的值左移一位 (乘以2)，加上當前節點的值
        val = val * 2 + cur->val;
        
        // 若為葉子節點 (Leaf Node)，結算總和
        if(cur->left == nullptr && cur->right == nullptr) {
            sum += val;
            return;
        }
        
        // 繼續往左右子樹遍歷
        helper(cur->left, val);
        helper(cur->right, val);
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