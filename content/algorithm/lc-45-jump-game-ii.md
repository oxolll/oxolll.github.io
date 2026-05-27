---
title: "LeetCode 45. Jump Game II"
date: 2026-05-27T19:30:00+08:00
lastmod: 2026-05-27T19:30:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "greedy", "dynamic-programming", "bfs"]
keywords: ["LC45", "Medium", "Jump Game II", "貪心算法", "最少跳躍次數", "一維 BFS"]
description: "LeetCode 第 45 題：Jump Game II。探討如何將 O(N^2) 的動態規劃，透過極值追蹤優化至 O(N)，並進一步降維轉換為 O(1) 空間的一維 BFS (貪心演算法)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 45](https://leetcode.com/problems/jump-game-ii/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Greedy` $\cdot$ `BFS`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個長度為 $n$ 的整數陣列 `nums`。你最初位於陣列的 **第一個索引**，陣列中的每個元素代表你在該位置可以跳躍的 **最大長度**。
請回傳到達最後一個索引所需的 **最少跳躍次數**。
*(題目保證你一定可以到達最後一個索引)*

### 範例
- **Input**: `nums = [2,3,1,1,4]`
- **Output**: `2`
- **Explanation**: 
  最少跳躍次數為 2 次。
  第一步從索引 0 跳 1 步到索引 1，然後第二步從索引 1 跳 3 步到達最後一個索引。

- **Input**: `nums = [2,3,0,1,4]`
- **Output**: `2`

### 限制條件
- $1 \le nums.length \le 10^4$
- $0 \le nums[i] \le 1000$
- 保證絕對可以抵達 `nums[n-1]`。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-27：貪心演算法 / 一維 BFS 最優解 (O(N) 時間, O(1) 空間)](#解法一-貪心演算法--一維-bfs-on-時間-o1-空間)
- [2026-05-27：初次提交 (帶有極值優化的 DP)](#解法二-帶有極值優化的動態規劃-on-時間-on-空間)

---

## 💡 解法一：貪心演算法 / 一維 BFS ($\mathcal{O}(N)$ 時間, $\mathcal{O}(1)$ 空間)

### 🎯 演算法分析
這題本質上是 **無權重圖的最短路徑問題**，最直觀的解法應該是 BFS。
但在這題中，我們不需要真的開一個 Queue。因為從左向右跳躍的特性，BFS 的「每一層」其實就是陣列上的一段**連續區間**。
1. 第 0 步能抵達的區間：`[0, 0]`
2. 第 1 步能抵達的區間：從第 0 步的範圍出發，最遠能到哪裡。
3. 第 2 步能抵達的區間：從第 1 步的範圍出發，最遠能到哪裡。

**核心破局點：區間邊界維護**
我們只需維護兩個變數：
- `current_end`：目前這一步（這一層）所能達到的最遠邊界。
- `max_far`：在巡覽目前這一層的過程中，為「下一步」探索到的最遠極限。
當索引 `i` 走到 `current_end` 時，代表這一層探索完了，我們必須強制進行一次跳躍（步數 $+1$），並將下一層的邊界更新為 `max_far`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需遍歷陣列一次。
- **空間複雜度**: $\mathcal{O}(1)$。只使用三個整數變數，無需 DP 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return 0; // 如果長度只有 1，已經在終點，不需跳躍

        int jumps = 0;        // 總跳躍次數
        int current_end = 0;  // 當前這一步所能涵蓋的最遠邊界
        int max_far = 0;      // 在當前步驟範圍內，所能探索到的「下一步」極限

        // 注意：迴圈只走到 n - 2。因為走到最後一個點時不需要再跳躍。
        for (int i = 0; i < n - 1; ++i) {
            // 在當前區間內，不斷刷新下一步所能到達的最遠極限
            max_far = max(max_far, i + nums[i]);

            // 當走到了當前這一步的邊界，代表必須進行一次跳躍才能繼續前進
            if (i == current_end) {
                ++jumps;
                current_end = max_far; // 更新下一層的邊界
                
                // 提早結束優化：如果下一步的極限已經涵蓋終點，可以直接跳出
                if (current_end >= n - 1) break;
            }
        }

        return jumps;
    }
};
```

---

## 💡 解法二：帶有極值優化的動態規劃 ($\mathcal{O}(N)$ 時間, $\mathcal{O}(N)$ 空間)

### 🎯 演算法分析
這是從標準 DP 演化而來的優秀寫法。
定義 `mm[i]` 為到達索引 `i` 的最少跳躍次數。透過內層迴圈更新從 `i` 出發能到達的所有點 `j`：`mm[j] = min(mm[j], mm[i] + 1)`。

然而，純粹的雙層迴圈會導致 $\mathcal{O}(N^2)$ 的複雜度。此解法的精髓在於引入了 `far` 變數：
由於「第一次被更新的點必然是最短距離」，我們不需要重複去更新已經走過的區間。透過 `j = max(i + 1, far)`，強制內層迴圈只從「未被探索過的處女地」開始推進，將總體操作次數完美壓制在 $\mathcal{O}(N)$。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。雖然是雙層迴圈，但因 `far` 的阻擋，內層迴圈總共只會執行 $N$ 次。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了大小為 $N$ 的 `mm` 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size();
        vector<int> mm(n, INT_MAX);
        mm[0] = 0;
        
        int far = 1; // 記錄目前已經被更新過的最遠索引
        
        for (int i = 0; i < n; ++i) {
            // 從尚未被更新過的地方開始，避免重複做白工
            for (int j = max(i + 1, far); j <= min(n - 1, i + nums[i]); ++j) {
                mm[j] = min(mm[j], mm[i] + 1);
            }
            // 更新已經探索過的極限
            far = max(far, i + nums[i]);
        }

        return mm.back();
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