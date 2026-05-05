---
title: "LeetCode 61. Rotate List"
date: 2026-05-05T10:55:00+08:00
lastmod: 2026-05-05T10:55:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "linked-list", "two-pointers", "math"]
keywords: ["LC61", "Medium", "Rotate List", "Linked List", "鏈結串列", "Cycle and Break"]
description: "LeetCode 第 61 題：Rotate List。難度評分：Medium。探討如何優雅地旋轉鏈結串列，包含利用陣列降維的 O(N) 空間快速解法，以及標準面試期望的「閉環後斷開」O(1) 空間最佳化解法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 61](https://leetcode.com/problems/rotate-list/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Linked List` $\cdot$ `Two Pointers` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個鏈結串列的頭節點 `head`，請將該鏈結串列向右旋轉 `k` 個位置。

### 範例
- **Input**: `head = [1,2,3,4,5], k = 2`
- **Output**: `[4,5,1,2,3]`
- **Explanation**: 
  向右旋轉 1 步: `[5,1,2,3,4]`
  向右旋轉 2 步: `[4,5,1,2,3]`

- **Input**: `head = [0,1,2], k = 4`
- **Output**: `[2,0,1]`
- **Explanation**: 
  向右旋轉 4 步相當於向右旋轉 `4 % 3 = 1` 步。

### 限制條件
- 鏈結串列中的節點數量範圍為 `[0, 500]`
- $-100 \le Node.val \le 100$
- $0 \le k \le 2 \times 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-05：初次提交 (陣列重構法 O(N) 空間與閉環斷開法 O(1) 空間)](#2026-05-05-初次提交-雙解法)

---

## 💡 2026-05-05 初次提交 (雙解法)

### 🎯 演算法分析 (Algorithm Analysis)
這題的核心難點在於尋找旋轉後的「新頭節點」與「新尾節點」。因為 $k$ 的值可能遠大於鏈結串列的長度 $L$，所以第一步絕對是先求出 $L$，並將實際需要旋轉的步數化簡為 $k \pmod L$。

針對 Linked List 的指標操作，這裡提供兩種思維：
1. **陣列重構法 (Array Mapping)**：直接將節點指標存入陣列，將指標問題降維成單純的「陣列索引數學計算」。好處是思維簡單、不容易出現指標遺失的 Bug。
2. **閉環後斷開 (Cycle & Break)**：這是面試中最標準的解法。將原本的尾節點連上頭節點形成一個「環 (Cycle)」，接著往前走 $L - (k \pmod L)$ 步找到新的尾節點，將環斷開即可。

---

### 🛠️ 方法一：陣列重構法 (輔助陣列) - $\mathcal{O}(N)$ 空間
利用 `vector<ListNode*>` 將所有節點依序存起來。計算出實際旋轉量後，利用模數運算 `$(-k + L + i) \pmod L$` 精準算出每個節點旋轉後應該指向的新位置，最後手動補上 `nullptr` 收尾。

#### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        vector<ListNode*> arr;

        // 1. 將所有節點存入陣列
        while(head != nullptr) {
            arr.push_back(head);
            head = head->next;
        }

        int l = arr.size(); 
        if(l == 0) return nullptr;
        
        // 2. 取餘數，過濾掉多餘的完整旋轉
        k %= l;
        
        // 3. 重新建立指標連接
        for(int i = 0; i < l - 1; ++i) {
            int cur = (-k + l + i) % l;
            int next = (-k + l + i + 1) % l;
            arr[cur]->next = arr[next];
        }
        
        // 4. 處理新的尾節點與頭節點
        arr[(-k + l + l - 1) % l]->next = nullptr;
        return arr[(-k + l) % l];
    }
};
```

---

### 🛠️ 方法二：閉環後斷開 (Cycle and Break) - $\mathcal{O}(1)$ 空間
這是不需要額外空間的最佳化解法。
- **步驟 1**：走訪整個鏈結串列，計算長度 $L$，同時定位到原本的**尾節點 (tail)**。
- **步驟 2**：將尾節點連向頭節點 (`tail->next = head`)，形成一個環。
- **步驟 3**：新的尾節點位置會落在從頭開始算起的第 $L - (k \pmod L) - 1$ 個節點。我們走訪到該位置。
- **步驟 4**：記錄新的頭節點 (`new_tail->next`)，並將環斷開 (`new_tail->next = nullptr`)。

#### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if (!head || !head->next || k == 0) return head;

        // 1. 計算長度並找到目前的尾節點
        int len = 1;
        ListNode* tail = head;
        while (tail->next != nullptr) {
            tail = tail->next;
            len++;
        }

        // 2. 計算實際需要旋轉的步數
        k %= len;
        if (k == 0) return head;

        // 3. 首尾相連形成環
        tail->next = head;

        // 4. 尋找新的尾節點 (從 head 往前走 len - k - 1 步)
        ListNode* new_tail = head;
        for (int i = 0; i < len - k - 1; ++i) {
            new_tail = new_tail->next;
        }

        // 5. 確立新的頭節點並斷開環
        ListNode* new_head = new_tail->next;
        new_tail->next = nullptr;

        return new_head;
    }
};
```

---

### 📊 複雜度總結對比
| 解法 | 時間複雜度 | 空間複雜度 | 特色與適用場景 |
| :--- | :--- | :--- | :--- |
| **方法一：陣列重構** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | 實作直觀、邏輯降維，適合競賽中快速防 Bug。 |
| **方法二：閉環斷開** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | 指標操作優美，無額外記憶體開銷，面試標準解法。 |

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
````</ListNode*></ListNode*>