---
title: "LeetCode 2130. Maximum Twin Sum of a Linked List"
date: 2026-06-14T14:40:00+08:00
lastmod: 2026-06-14T14:40:00+08:00
difficulty: 1317
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "linked-list", "two-pointers"]
keywords: ["LC2130", "Medium", "1317", "Maximum Twin Sum", "鏈結串列", "快慢指標", "反轉串列", "雙指標"]
description: "LeetCode 第 2130 題：Maximum Twin Sum of a Linked List。難度評分：1317。探討兩種解法：直觀的陣列轉換法 (O(N) 空間)，以及面試必考的快慢指標結合就地反轉串列的 O(1) 空間最優化解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2130](https://leetcode.com/problems/maximum-twin-sum-of-a-linked-list/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1317)</span>  
> **核心主題**：`Linked List` $\cdot$ `Two Pointers`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
在一個大小為偶數 `n` 的鏈結串列中，第 `i` 個節點（索引從 `0` 開始）的 **孿生節點 (Twin Node)** 是第 `(n - 1 - i)` 個節點。
- 例如，若 `n = 4`，節點 `0` 的孿生節點是節點 `3`，節點 `1` 的孿生節點是節點 `2`。這也是串列中僅有的兩對孿生節點。

**Twin Sum (孿生和)** 定義為一個節點與其孿生節點的值相加之和。
給你一個長度為偶數的鏈結串列的頭節點 `head`，請回傳該鏈結串列中 **最大的孿生和**。

### 範例
- **Input**: `head = [5,4,2,1]`
- **Output**: `6`
- **Explanation**: 
  - 節點 0 的孿生節點為 3，孿生和 = `5 + 1 = 6`。
  - 節點 1 的孿生節點為 2，孿生和 = `4 + 2 = 6`。
  最大孿生和為 6。

- **Input**: `head = [4,2,2,3]`
- **Output**: `7`
- **Explanation**: 
  - 孿生和分別為 `4 + 3 = 7` 以及 `2 + 2 = 4`。
  最大孿生和為 7。

### 限制條件
- 串列中的節點數目是範圍 `[2, 10^5]` 內的偶數。
- $1 \le Node.val \le 10^5$
</details>

---

## 📝 歷次打卡與演算法進化
- [解法二：快慢指標 + 就地反轉 ($\mathcal{O}(1)$ 空間最優解)](#💡-解法二快慢指標--就地反轉-o1-空間最優解)
- [解法一：陣列轉換法 (直觀解)](#💡-解法一陣列轉換法-直觀解)

---

## 💡 解法一：陣列轉換法 (直觀解)

### 🎯 演算法分析
Linked List 最大的缺點就是不支援 $\mathcal{O}(1)$ 的隨機存取，導致我們無法輕易從尾巴拿取資料。
最直觀的解法就是將其「降維」成我們熟悉的 Array (`vector`)：遍歷一次串列，將所有值存入陣列中；接著透過 `i` 與 `n - 1 - i` 的索引關係，在 $\mathcal{O}(1)$ 時間內配對孿生節點，並找出最大值。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。需遍歷串列一次，再遍歷陣列一半，整體時間為線性。
- **空間複雜度**: $\mathcal{O}(N)$。開闢了 `vector<int>` 儲存所有的節點值。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int pairSum(ListNode* head) {
        ListNode* p = head;
        vector<int> arr;
        
        // 1. 將 Linked List 轉換為 Array
        while(p != nullptr) {
            arr.push_back(p->val);
            p = p->next;
        }
        
        int n = arr.size();
        int rt = INT_MIN;
        
        // 2. 利用索引計算 Twin Sum 找最大值
        for(int i = 0; i < n / 2; ++i) {
            rt = max(rt, arr[i] + arr[n - 1 - i]);
        }
        
        return rt;
    }
};
```

---

## 💡 解法二：快慢指標 + 就地反轉 ($\mathcal{O}(1)$ 空間最優解)

### 🎯 演算法分析
為了突破 $\mathcal{O}(N)$ 的空間限制，我們必須在 Linked List 上直接操作。
要讓前半部與後半部的節點進行配對，我們可以將串列「對半切」，並將後半部的方向 **反轉 (Reverse)**。
這是一個經典的組合技：
1. **快慢指標找中點**：`slow` 每次走一步，`fast` 每次走兩步。當 `fast` 到底時，`slow` 剛好落在後半部的開頭。
2. **反轉後半部**：將 `slow` 開始的串列就地反轉，時間 $\mathcal{O}(N)$，空間 $\mathcal{O}(1)$。
3. **雙指標走訪**：一個指標 `left` 從原串列頭部出發，另一個 `right` 從反轉後的後半部頭部出發，一步一步同步推進並計算和。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。找中點、反轉、計算總和，各需遍歷串列一半或全部，常數相加後仍為嚴格線性時間。
- **空間複雜度**: $\mathcal{O}(1)$。僅使用了幾個指標變數，沒有開闢額外陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int pairSum(ListNode* head) {
        // 1. 使用快慢指標尋找中點
        ListNode* slow = head;
        ListNode* fast = head;
        // 因為保證是偶數長度，當 fast 為空時，slow 剛好在 n/2 的位置
        while(fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }

        // 2. 就地反轉後半部串列 (從 slow 開始)
        ListNode* prev = nullptr;
        ListNode* curr = slow;
        while(curr != nullptr) {
            ListNode* nextTemp = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nextTemp;
        }

        // 3. 同步走訪並計算最大孿生和
        int max_sum = 0;
        ListNode* left = head;
        ListNode* right = prev; // prev 是反轉後後半部的新頭節點
        
        while(right != nullptr) {
            max_sum = max(max_sum, left->val + right->val);
            left = left->next;
            right = right->next;
        }

        // (Optional) 實戰中若面試官要求，可在此處再次反轉 prev，將串列恢復原狀

        return max_sum;
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