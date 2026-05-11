---
title: "LeetCode 2553. Separate the Digits in an Array"
date: 2026-05-11T13:00:00+08:00
lastmod: 2026-05-11T13:00:00+08:00
difficulty: 1216
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "simulation", "math"]
keywords: ["LC2553", "Easy", "Separate Digits", "數位拆解", "陣列操作"]
description: "LeetCode 第 2553 題：Separate the Digits in an Array。難度評分：1216。探討如何將整數陣列中的每個數字拆解為單個數位，並保持原始順序輸出。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2553](https://leetcode.com/problems/separate-the-digits-in-an-array/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1216)</span>  
> **核心主題**：`Array` $\cdot$ `Simulation` $\cdot$ `Basic Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個正整數陣列 `nums`，請你傳回一個陣列 `answer`，該陣列包含 `nums` 中每個整數拆解後的數位，且數位出現的順序應與它們在 `nums` 中出現的順序相同。

### 範例
- **Input**: `nums = [13, 25, 83, 77]`
- **Output**: `[1, 3, 2, 5, 8, 3, 7, 7]`
- **Explanation**: 
  - 13 拆解為 1, 3
  - 25 拆解為 2, 5
  - ... 以此類推，組合後順序不變。

### 限制條件
- $1 \le nums.length \le 1000$
- $1 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-11：初次提交 (逆向遍歷與數位拆解)](#2026-05-11-初次提交)

---

## 💡 2026-05-11 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
拆解數字通常使用取餘數 (`% 10`) 和整除 (`/ 10`)。然而，取餘數會由低位（個位）開始獲取，這與要求的輸出順序相反。

**核心破局點：雙重逆序**
為了保持 $O(1)$ 的單次插入效率並最終得到正確順序，你的代碼採用了非常有意思的邏輯：
1. **外層逆序**：從 `nums` 的最後一個元素開始處理。
2. **內層自然序**：透過 `% 10` 拿到的數位也是該數字的「倒序」。
3. **最終翻轉**：將收集到的所有數位整體 `reverse`。

這樣處理後的結果為：`reverse(所有數字的倒序集合)`，正好等於「所有數字的正序集合」。

### 🛠️ 解題思路 (Approach)
1. **初始化**：準備結果陣列 `rt`。
2. **從後往前遍歷**：遍歷 `i` 從 $n-1$ 到 $0$。
3. **數位提取**：
   - 使用 `while(nums[i])` 迴圈。
   - 每次取 `nums[i] % 10` 加入 `rt`。
   - 更新 `nums[i] /= 10`。
4. **反轉結果**：使用 `std::reverse(rt.begin(), rt.end())` 將整體的倒序轉為正序。

### 📊 複雜度分析
- **時間複雜度**: $O(N \cdot D)$。其中 $N$ 是陣列長度，$D$ 是數字的平均位數（在本題最大為 5）。`reverse` 操作也是線性時間，總體仍為線性。
- **空間複雜度**: $O(N \cdot D)$。需要存儲所有拆解後的數位。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> separateDigits(vector<int>& nums) {
        vector<int> rt;
        
        // 1. 從陣列末尾往前遍歷，配合 %10 取出的低位數位
        for(int i = nums.size() - 1; i >= 0; --i) {
            int val = nums[i];
            // 2. 拆解單個數字的數位 (注意：這裡會由個位數往高位數存)
            while(val > 0) {
                rt.push_back(val % 10);
                val /= 10;
            }
        }
        
        // 3. 由於外層是逆序遍歷，內層存入的也是數位逆序
        // 經過一次整體 reverse 後，順序會變回正確的正序
        reverse(rt.begin(), rt.end());
        
        return rt;
    }
};
```

---

### 📝 思考與優化
雖然「逆序 + reverse」非常巧妙，但在面試中也可以考慮使用 `to_string` 或是「將數位暫存後反轉再加入」的方式，讓邏輯更直觀。但你的解法在空間利用率和減少小範圍記憶體分配上是有優勢的。

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