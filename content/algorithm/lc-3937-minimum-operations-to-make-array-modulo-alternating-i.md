---
title: "LeetCode 3937. Minimum Operations to Make Array Modulo Alternating I"
date: 2026-05-24T15:35:00+08:00
lastmod: 2026-05-24T15:35:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "enumeration", "greedy"]
keywords: ["LC3937", "Medium", "Modulo Alternating", "模運算", "狀態枚舉", "交替陣列"]
description: "LeetCode 第 3937 題：Minimum Operations to Make Array Modulo Alternating I。探討如何利用圓環最短距離公式與狀態枚舉，計算將陣列轉換為模 $k$ 交替陣列的最小操作次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3937](https://leetcode.com/problems/minimum-operations-to-make-array-modulo-alternating-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Math` $\cdot$ `Enumeration` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums` 和一個整數 `k`。
你可以對陣列中的任何元素執行操作：每次操作可以將某個元素的值加 $1$ 或減 $1$。

一個陣列被稱為 **模 $k$ 交替 (Modulo Alternating)**，當且僅當存在兩個 **不相等** 的整數 $x$ 和 $y$（$0 \le x, y < k$），使得：
- 對於所有偶數索引 $i$，$nums[i] \pmod k == x$
- 對於所有奇數索引 $i$，$nums[i] \pmod k == y$

請你計算將 `nums` 變成模 $k$ 交替陣列所需的 **最少操作次數**。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le k \le 100$ *(依據複雜度推斷)*
- $0 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-24：初次提交 (預處理枚舉與圓環距離計算)](#2026-05-24-初次提交)

---

## 💡 2026-05-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要將陣列變成交替狀態，我們必須決定兩個目標值 $x$ 和 $y$。因為 $k$ 的範圍通常不大（例如 $k \le 100$），我們可以**直接枚舉所有可能的目標值**。

**核心破局點 1：模運算圓環的最短距離**
將任意數字 $cur$ 透過加減 $1$ 調整至目標值 $v \pmod k$ 時，這等同於在一個周長為 $k$ 的圓環上行走。從 $cur$ 走到 $v$ 有兩個方向：
- 順時針（或正向）：$(cur - v + k) \pmod k$
- 逆時針（或反向）：$(v - cur + k) \pmod k$
最短操作次數即為這兩者的最小值。

**核心破局點 2：獨立狀態預處理**
因為奇數索引和偶數索引的變換互不影響，我們可以：
1. 預先計算把所有奇數位置變成 $v$ ($0 \le v < k$) 的總成本。
2. 預先計算把所有偶數位置變成 $v$ ($0 \le v < k$) 的總成本。
3. 最後使用兩層迴圈尋找總和最小的組合 $(i, j)$，並且強制規定 $i \neq j$ 以滿足交替條件。

---

### 🛠️ 解題思路 (Approach)
1. **宣告儲存空間**：建立兩個長度為 $k$ 的陣列 `ans` (奇數位置成本) 和 `ans2` (偶數位置成本)。
2. **枚舉目標值 $v$**：外層迴圈枚舉 $v$ 從 $0$ 到 $k-1$。
3. **計算各位置成本**：
   - 內層迴圈遍歷原陣列 `nums`，取得餘數 `cur = nums[i] % k`。
   - 利用圓環距離公式計算 `cur` 變成 `v` 的代價。
   - 若 $i$ 為奇數 (`i & 1`)，將代價累加至 `tmp1`；若為偶數，累加至 `tmp2`。
   - 迴圈結束後記錄 `ans[v] = tmp1` 與 `ans2[v] = tmp2`。
4. **配對找最優解**：雙重迴圈枚舉奇數目標 $i$ 與偶數目標 $j$。若 $i \neq j$，則以 `ans[i] + ans2[j]` 更新全局最小值 `mm`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \cdot K + K^2)$。
  - 預處理階段：需要外層遍歷 $K$ 次，內層遍歷 $N$ 次，耗時 $\mathcal{O}(N \cdot K)$。
  - 組合枚舉階段：雙重迴圈遍歷 $K \times K$ 次，耗時 $\mathcal{O}(K^2)$。
  - 整體時間效能極佳。
- **空間複雜度**: $\mathcal{O}(K)$。僅使用了兩個大小為 $K$ 的陣列來快取成本資料。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minOperations(vector<int>& nums, int k) {
        int n = nums.size();
        // ans 記錄奇數位置變成特定餘數的成本；ans2 記錄偶數位置的成本
        vector<int> ans(k, INT_MAX);
        vector<int> ans2(k, INT_MAX);
        
        // 1. 獨立預處理：計算統一變成目標餘數 v 的成本
        for(int v = 0; v < k; ++v) {
            int tmp1 = 0, tmp2 = 0;
            for(int i = 0; i < n; ++i) {
                int cur = nums[i] % k;
                
                // 圓環最短距離公式：取正向與反向差值的最小值
                int dist = min(abs((cur - v + k) % k), abs((k + v - cur + k) % k));
                
                if(i & 1) {
                    tmp1 += dist; // 奇數位置
                } else {
                    tmp2 += dist; // 偶數位置
                }
            }
            ans[v] = tmp1;
            ans2[v] = tmp2;
        }
        
        // 2. 枚舉組合：尋找合法的 (奇數目標 i, 偶數目標 j) 使得總成本最小
        int mm = INT_MAX;
        for(int i = 0; i < k; ++i) {
            for(int j = 0; j < k; ++j) {
                // 題目限制：交替陣列相鄰元素不可相等
                if(i == j) continue;
                
                mm = min(mm, ans[i] + ans2[j]);
            }
        }

        return mm;
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