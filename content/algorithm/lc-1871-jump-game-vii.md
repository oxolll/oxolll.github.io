---
title: "LeetCode 1871. Jump Game VII"
date: 2026-05-25T11:45:00+08:00
lastmod: 2026-05-25T11:45:00+08:00
difficulty: 1896
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "dynamic-programming", "binary-search"]
keywords: ["LC1871", "Medium", "1896", "Jump Game VII", "二分搜尋", "區間跳躍"]
description: "LeetCode 第 1871 題：Jump Game VII。難度評分：1896。探討如何利用記錄可達節點陣列，搭配 lower_bound 二分搜尋，巧妙且精準地驗證區間跳躍的合法性。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1871](https://leetcode.com/problems/jump-game-vii/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1896)</span>  
> **核心主題**：`String` $\cdot$ `Two Pointers / Sliding Window` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個只包含 `'0'` 和 `'1'` 的字串 `s`（索引從 0 開始），同時給你兩個整數 `minJump` 和 `maxJump`。
你一開始位於索引 `0`，並且 `s[0] == '0'`。你可以從索引 `i` 跳到索引 `j`，前提是必須滿足以下所有條件：
1. $i + minJump \le j \le \min(i + maxJump, s.length - 1)$
2. $s[j] == '0'$

請你判斷是否能夠跳到字串的最後一個索引 `s.length - 1`。如果可以，回傳 `true`；否則回傳 `false`。

### 範例
- **Input**: `s = "011010", minJump = 2, maxJump = 3`
- **Output**: `true`
- **Explanation**: 
  - 第一步，從索引 0 跳到索引 3，因為 $s[3] == '0'$ 且 $0 + 2 \le 3 \le \min(0 + 3, 5)$。
  - 第二步，從索引 3 跳到索引 5，因為 $s[5] == '0'$ 且 $3 + 2 \le 5 \le \min(3 + 3, 5)$。

- **Input**: `s = "01101110", minJump = 2, maxJump = 3`
- **Output**: `false`

### 限制條件
- $2 \le s.length \le 10^5$
- `s[i]` 為 `'0'` 或 `'1'`
- `s[0] == '0'`
- $1 \le minJump \le maxJump < s.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-25：初次提交 (可達節點紀錄與二分搜尋驗證)](#2026-05-25-初次提交)

---

## 💡 2026-05-25 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的目標是判斷能否走到終點。由於跳躍存在一個區間範圍限制 $[minJump, maxJump]$，對於當前的字元 `s[i] == '0'`，我們需要知道在過去的歷史中，是否存在一個「已經確認可抵達」的起點 $p$，滿足：
$$i - maxJump \le p \le i - minJump$$

**核心破局點：單調遞增的可達陣列與極限驗證**
我們維護一個陣列 `pos`，用來儲存所有「確定可以抵達的索引」。因為我們是由左向右掃描字串，所以這個 `pos` 陣列必定是**嚴格單調遞增**的。這為我們使用二分搜尋（Binary Search）創造了完美條件：
1. **下界搜尋**：利用 `lower_bound` 在 `pos` 中尋找第一個 $\ge i - maxJump$ 的索引位置 `k`。
2. **唯一性驗證**：找到的 `pos[k]` 是所有滿足下界條件的起點中，**數值最小的（最靠左的）**。
   - 如果這個最小的起點，其距離依然太近（亦即 $pos[k] > i - minJump$），那麼 `pos` 中在它後面的所有起點肯定會更大，距離也會更近，絕對不可能滿足條件。
   - 因此，我們只需要驗證 `pos[k]` 這**唯一一個點**是否 $\le i - minJump$。如果是，當前點 `i` 就是可達的，將其推入 `pos` 中。

---

### 🛠️ 解題思路 (Approach)
1. **提早剪枝**：如果字串最後一個字元不是 `'0'`，根本不可能抵達，直接回傳 `false`。
2. **初始化狀態**：宣告 `vector<int> pos` 記錄可達節點，將起點 `0` 推入。
3. **遍歷字串**：從 $i = 1$ 掃描至 $n - 1$。
   - 若 $s[i] == '1'$，代表是障礙物，無法降落，直接 `continue`。
   - 若 $s[i] == '0'$，計算能跳到這裡的最遠合法起點極限 `idx = i - maxJump`。
   - 使用 `lower_bound` 尋找 `pos` 中第一個 $\ge idx$ 的位置 `k`。
   - 若 `k == pos.size()`，代表所有可達節點都落後於 `i - maxJump`，無法跳躍。
   - 檢查 `i - pos[k] >= minJump` (即 $pos[k] \le i - minJump$)，若成立，說明找到合法的起點區間，當前 $i$ 可達，執行 `pos.push_back(i)`。
4. **輸出結果**：最後檢查 `pos` 的最後一個元素是否為終點 `n - 1`。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log K)$，其中 $N$ 是字串長度，$K$ 是可達節點的數量（最壞情況下 $K \approx N$）。
  遍歷字串需要 $\mathcal{O}(N)$，在迴圈內針對動態增長的陣列進行二分搜尋需要 $\mathcal{O}(\log K)$。在 $10^5$ 的數量級下，$N \log N \approx 1.6 \times 10^6$ 次操作，執行極為快速，完美通過限制。
- **空間複雜度**: $\mathcal{O}(N)$。在最壞情況下（字串全為 `'0'`），`pos` 陣列需要儲存 $N$ 個索引。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canReach(string s, int minJump, int maxJump) {
        // 剪枝：終點如果不是 '0'，絕對無法抵達
        if(s.back() != '0') return false;

        int n = s.size();
        // pos 陣列用於儲存所有「確定可以成功抵達」的索引位置
        vector<int> pos;
        pos.push_back(0); // 初始化：一定能站在起點

        for(int i = 1; i < s.size(); ++i) {
            // 障礙物無法作為落腳點
            if(s[i] == '1') continue;
            
            if(s[i] == '0') {
                // 計算要跳到當前 i，起點的最小可能位置 (即最遠能從哪裡跳過來)
                int idx = i - maxJump;
                
                // 在可達陣列中，找到第一個大於等於 idx 的位置
                int k = lower_bound(pos.begin(), pos.end(), idx) - pos.begin();
                
                // 如果找不到，代表所有已知的起點都太遠了，跳不到 i
                if(k == pos.size()) continue;
                
                // 找到的 pos[k] 是滿足下界條件中「最小」的起點
                // 只要驗證這個最小起點是否滿足上界條件 (距離 >= minJump) 即可
                // 如果它滿足，當前 i 就是可達的
                if(i - pos[k] >= minJump) {
                    pos.push_back(i);
                }
            }
        }

        // 檢查紀錄中最後一個可達位置是否正好是終點
        return pos.back() == n - 1;
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