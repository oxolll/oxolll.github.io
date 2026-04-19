---
title: "LeetCode 3896. Minimum Operations to Transform Array into Alternating Prime"
date: 2026-04-12T10:10:00+08:00
lastmod: 2026-04-12T10:10:00+08:00
difficulty: 1435
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "number-theory", "greedy", "array"]
keywords: ["LC3896", "Medium", "1435", "Alternating Prime", "Sieve of Eratosthenes", "Greedy"]
description: "LeetCode 第 3896 題：Minimum Operations to Transform Array into Alternating Prime。難度評分：Medium (1435)。探討如何利用埃拉托斯特尼篩法 (Sieve of Eratosthenes) 預處理質數表，並透過貪婪法 (Greedy) 計算轉換為交替質數與非質數所需的最少操作次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3896](https://leetcode.com/problems/minimum-operations-to-transform-array-into-alternating-prime/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1435)</span>  
> **核心主題**：`Math` $\cdot$ `Number Theory` $\cdot$ `Greedy` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。你可以對陣列中的任何元素執行以下操作任意次數：
- 將該元素的值加 1。

我們的目標是將這個陣列轉換為「交替質數陣列 (Alternating Prime Array)」。定義如下：
- 若索引 `i` 為偶數 ($0, 2, 4, \dots$)，則 `nums[i]` 必須是**質數**。
- 若索引 `i` 為奇數 ($1, 3, 5, \dots$)，則 `nums[i]` 必須是**非質數** (合數、0 或 1)。

請計算並回傳達成此目標所需的**最少操作次數**。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-12：初次提交 (埃拉托斯特尼篩法預處理與貪婪法)](#2026-04-12-初次提交)

---

## 💡 2026-04-12 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是標準的「數論預處理」結合「貪婪法 (Greedy)」的題型。
因為我們只能對數字進行「加 1」的操作，且要求**最少**操作次數，所以貪婪策略非常明確：
- 當需要質數時，尋找大於等於當前數字的**最近一個質數**。
- 當需要非質數時，尋找大於等於當前數字的**最近一個非質數**。

為了避免在尋找過程中頻繁呼叫 $\mathcal{O}(\sqrt{N})$ 的質數判斷函式導致 TLE，最佳策略是**空間換取時間**：
使用 **埃拉托斯特尼篩法 (Sieve of Eratosthenes)** 在極短的時間內建構出全局的質數對照表。
**⚠️ 邊界防禦：** 雖然 $nums[i] \le 10^5$，但在不斷往上加的過程中，數值會超過 $10^5$。我們將篩法的上限安全地設定為 $2 \times 10^5$，足以涵蓋所有尋找下一個質數的需求。

### 🛠️ 解題思路 (Approach)
1. **質數篩法預處理 (Sieve of Eratosthenes)**：
   - 宣告大小為 $200005$ 的布林陣列 `isprime`，初始全為 `true`。
   - 手動設定 `isprime[0] = false` 與 `isprime[1] = false`。
   - 走訪 $2$ 到 $2 \times 10^5$，若遇到質數，則將其所有的倍數標記為 `false` (合數)。
2. **貪婪計算操作次數**：
   - 宣告 `rt = 0` 記錄總操作次數。
   - 走訪陣列 `nums`，依據索引奇偶性進行判斷：
     - **奇數索引 (目標為非質數)**：若當前數字已是非質數則跳過；否則利用 `while` 迴圈貪婪地將數字 `+1`，直到查表結果為非質數，並累加操作次數。
     - **偶數索引 (目標為質數)**：若當前數字已是質數則跳過；否則利用 `while` 迴圈貪婪地將數字 `+1`，直到查表結果為質數，並累加操作次數。
3. **回傳結果**：迴圈結束後，回傳總操作次數 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \log \log M + N \times G)$。
  - 建立質數表的篩法耗時 $\mathcal{O}(M \log \log M)$，其中 $M = 2 \times 10^5$。
  - 走訪陣列耗時 $\mathcal{O}(N)$，尋找下一個目標值的 `while` 迴圈最多執行 $G$ 次（$G$ 為相鄰質數之間的最大間距）。在 $2 \times 10^5$ 範圍內，最大質數間距僅為 $86$，可視為極小的常數。整體時間複雜度優異。
- **空間複雜度**: $\mathcal{O}(M)$。預處理的質數表 `isprime` 佔用 $2 \times 10^5$ 大小的布林陣列空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minOperations(vector<int>& nums) {
        // 1. 質數篩法預處理 (Sieve of Eratosthenes)
        // 上限開到 2e5 以確保即使 nums[i] 逼近 1e5，往上尋找時仍不會越界
        vector<bool> isprime(2e5 + 5, true);
        isprime[0] = false;
        isprime[1] = false;
        for(int i = 2; i <= 2e5; ++i) {
            if(isprime[i] == false) continue;
            // 質數的倍數皆為合數
            for(int j = 2 * i; j <= 2e5; j += i) {
                isprime[j] = false;
            }
        }
        
        int rt = 0;
        
        // 2. 貪婪計算最少操作次數
        for(int i = 0; i < nums.size(); ++i) {
            if((i % 2) == 1) {
                // 奇數索引：目標為非質數
                if(!isprime[nums[i]]) continue;
                
                int up = 0;
                int k = nums[i];
                // 貪婪往上找，直到變成非質數
                while(k <= 2e5 && isprime[k]) {
                    ++up;
                    ++k;
                }
                rt += up;
            } else {
                // 偶數索引：目標為質數
                if(isprime[nums[i]]) continue;
                
                int up = 0;
                int k = nums[i];
                // 貪婪往上找，直到變成質數
                while(k <= 2e5 && !isprime[k]) {
                    ++up;
                    ++k;
                }
                rt += up;
            }
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