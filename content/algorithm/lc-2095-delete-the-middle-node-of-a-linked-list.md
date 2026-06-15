---
title: "LeetCode 2095. Delete the Middle Node of a Linked List"
date: 2026-06-15T15:00:00+08:00
lastmod: 2026-06-15T15:00:00+08:00
difficulty: 1324
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "linked-list", "two-pointers"]
keywords: ["LC2095", "Medium", "1324", "Delete Middle Node", "鏈結串列", "快慢指標", "雙指標", "節點刪除"]
description: "LeetCode 第 2095 題：Delete the Middle Node of a Linked List。難度評分：1324。探討如何利用經典的快慢指標 (Fast & Slow Pointers) 技巧結合 prev 追蹤指標，在 O(N) 時間與 O(1) 空間內優雅地定位並刪除鏈結串列的中間節點。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2095](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1324)</span>  
> **核心主題**：`Linked List` $\cdot$ `Two Pointers`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個鏈結串列的頭節點 `head`。請你刪除串列的 **中間節點 (Middle Node)**，並回傳修改後的鏈結串列的頭節點。

對於一個長度為 `n` 的鏈結串列，其中間節點為第 $\lfloor n / 2 \rfloor$ 個節點（索引從 `0` 開始計算）。
- 例如，長度為 `1, 2, 3, 4, 5` 的串列，中間節點的索引分別為 `0, 1, 1, 2, 2`。

### 範例
- **Input**: `head = [1,3,4,7,1,2,6]`
- **Output**: `[1,3,4,1,2,6]`
- **Explanation**: 
  串列長度為 7，中間節點的索引為 $\lfloor 7 / 2 \rfloor = 3$。
  節點值為 7 的節點被刪除。

- **Input**: `head = [1,2,3,4]`
- **Output**: `[1,2,4]`
- **Explanation**: 
  長度為 4，中間節點的索引為 $\lfloor 4 / 2 \rfloor = 2$。
  節點值為 3 的節點被刪除。

- **Input**: `head = [2,1]`
- **Output**: `[2]`
- **Explanation**: 
  長度為 2，中間節點的索引為 $\lfloor 2 / 2 \rfloor = 1$。
  節點值為 1 的節點被刪除。

### 限制條件
- 串列中的節點數目在範圍 $[1, 10^5]$ 內。
- $1 \le Node.val \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-15：初次提交 (快慢指標與 prev 追蹤)](#2026-06-15-初次提交)

---

## 💡 2026-06-15 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
在鏈結串列 (Linked List) 中尋找中點，最標準的解法就是 **「快慢指標 (Fast & Slow Pointers)」**。
- `slow` 指標每次前進一步。
- `fast` 指標每次前進兩步。
因為 `fast` 的速度是 `slow` 的兩倍，所以當 `fast` 走到串列尾端時，`slow` 必定會精準地落在串列的中點。

**核心破局點：如何刪除節點？**
在單向鏈結串列中，要刪除節點 $X$，我們必須拿到 $X$ 的 **前一個節點 (prev)**，然後執行 `prev->next = X->next`。
因此，我們在快慢指標的基礎上，額外引入一個 `prev` 指標。在每次 `slow` 準備往前走之前，先用 `prev` 記錄下 `slow` 當前的位置。
當迴圈結束時：
- `slow` 停在需要被刪除的中間節點。
- `prev` 停在中點的前一個節點。
此時我們只需執行 `prev->next = slow->next`，即可完美且安全地將中間節點從串列中剔除。

### 🛠️ 解題思路 (Approach)
1. **邊界防禦 (Edge Case)**：
   - 題目保證節點數至少為 1。如果串列只有一個節點 (`head->next == nullptr`)，則中間節點就是它自己。刪除後串列變空，直接回傳 `nullptr`。
2. **初始化指標**：
   - `prev = nullptr`：用於追蹤 `slow` 的前一個節點。
   - `slow = head`：慢指標。
   - `fast = head`：快指標。
3. **快慢指標尋找中點**：
   - 當 `fast != nullptr` 且 `fast->next != nullptr` 時（確保 `fast` 可以安全走兩步）：
     - `prev = slow`：保存當前位置。
     - `slow = slow->next`：慢指標走一步。
     - `fast = fast->next->next`：快指標走兩步。
4. **執行刪除操作**：
   - 迴圈結束後，`slow` 即為要被刪除的節點，`prev` 為其前驅節點。
   - 執行跳線連接：`prev->next = slow->next`。
5. **回傳結果**：回傳原串列的頭節點 `head`。

*(註：在實務工程中，若語言無自動垃圾回收，應加上 `delete slow;` 以避免 Memory Leak。LeetCode 平台通常會代為回收，不寫亦可通過。)*

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是鏈結串列的長度。快指標最多遍歷串列一次即可找到中點，執行效率極高。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了 `prev`、`slow`、`fast` 三個指標變數，完全不佔用額外記憶體。

### 💻 程式碼實作 (C++)
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 * int val;
 * ListNode *next;
 * ListNode() : val(0), next(nullptr) {}
 * ListNode(int x) : val(x), next(nullptr) {}
 * ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* deleteMiddle(ListNode* head) {
        // 特殊情況處理：若串列只有一個節點，刪除後回傳空指標
        if(head->next == nullptr) return nullptr;
        
        ListNode* prev = nullptr; // 追蹤 slow 的前一個節點
        ListNode* slow = head;    // 慢指標 (每次走一步)
        ListNode* fast = head;    // 快指標 (每次走兩步)
        
        // 利用快慢指標尋找中點
        while(fast != nullptr && fast->next != nullptr) {
            prev = slow;                // 在 slow 移動前，記錄其位置
            slow = slow->next;          // slow 往前一步
            fast = fast->next->next;    // fast 往前兩步
        }

        // 迴圈結束時，slow 停在要刪除的中間節點
        // 將 prev 的 next 繞過 slow，接向 slow 的下一個節點
        prev->next = slow->next;
        
        // (Optional) 工程實務上建議釋放被刪除節點的記憶體：
        // delete slow;

        return head;
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