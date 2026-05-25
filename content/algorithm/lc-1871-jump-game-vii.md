---
title: "LeetCode 1871. Jump Game VII"
date: 2026-05-25T11:45:00+08:00
lastmod: 2026-05-25T16:00:00+08:00
difficulty: 1896
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "dynamic-programming", "binary-search", "prefix-sum", "difference-array"]
keywords: ["LC1871", "Medium", "1896", "Jump Game VII", "二分搜尋", "區間跳躍", "差分陣列", "前綴和"]
description: "LeetCode 第 1871 題：Jump Game VII。難度評分：1896。探討如何利用 lower_bound 二分搜尋驗證區間合法性，以及如何進階使用差分陣列與前綴和，在 O(N) 的線性時間內完美解決區間覆蓋問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1871](https://leetcode.com/problems/jump-game-vii/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1896)</span>  
> **核心主題**：`String` $\cdot$ `Prefix Sum` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個只包含 `'0'` 和 `'1'` 的字串 `s`（索引從 0 開始），同時給你兩個整數 `minJump` 和 `maxJump`。
你一開始位於索引 `0`，並且 `s[0] == '0'`。你可以從索引 `i` 跳到索引 `j`，前提是必須滿足以下所有條件：
1. $i + minJump \le j \le \min(i + maxJump, s.length - 1)$
2. $s[j]$ $==$ `'0'`

請你判斷是否能夠跳到字串的最後一個索引 `s.length - 1`。如果可以，回傳 `true`；否則回傳 `false`。

### 範例
- **Input**: `s = "011010", minJump = 2, maxJump = 3`
- **Output**: `true`
- **Explanation**: 
  - 第一步，從索引 0 跳到索引 3，因為 $s[3] == $`'0'` 且 $0 + 2 \le 3 \le \min(0 + 3, 5)$。
  - 第二步，從索引 3 跳到索引 5，因為 $s[5] == $`'0'` 且 $3 + 2 \le 5 \le \min(3 + 3, 5)$。

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
- [2026-05-25：新增 O(N) 差分陣列與前綴和最優解](#解法一-差分陣列與前綴和-on-最優解)
- [2026-05-25：初次提交 (可達節點紀錄與二分搜尋驗證)](#解法二-二分搜尋與可達性陣列-on-log-n)

---

## 💡 解法一：差分陣列與前綴和 ($\mathcal{O}(N)$ 最優解)

### 🎯 演算法分析
這題本質上是一個「區間覆蓋」問題：當我們確定索引 `i` 可達時，它能賦予後續區間 `[i + minJump, i + maxJump]` 內的 `'0'` 可達的權利。
如果我們每走到一個可達點，就用 `for` 迴圈把目標區間標記為可達，會導致 $\mathcal{O}(N^2)$ 的逾時。

**核心破局點：差分陣列 (Difference Array)**
利用差分陣列的特性，我們可以將 $\mathcal{O}(K)$ 的區間更新，壓縮成 $\mathcal{O}(1)$ 的單點操作：
1. **區間加值**：當 `i` 可達時，我們在起點 `diff[i + minJump]` 加上 1，並在終點的下一格 `diff[i + maxJump + 1]` 減去 1。
2. **滾動前綴和**：在線性掃描陣列時，我們維護一個變數 `c` 來累加 `diff` 的值。這個 `c` 就代表**「當前位置被幾個過去的合法起點所覆蓋」**。只要 `c > 0` 且當前字元為 `'0'`，當前位置就是合法的可達點！

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。只需對字串進行單次線性掃描，無任何嵌套迴圈。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了一個長度為 $N + maxJump + 1$ 的差分陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canReach(string s, int minJump, int maxJump) {
        // 剪枝：終點如果不是 '0'，絕對無法抵達
        if(s.back() == '1') return false;

        int n = s.size();
        // 差分陣列，預留 maxJump 的空間防止陣列越界
        vector<int> diff(n + 1 + maxJump, 0);
        
        // 初始狀態：起點 0 是可達的，相當於區間 [0, 0] 加 1
        diff[0] = 1; 
        diff[1] = -1;
        
        int c = 0; // 滾動前綴和，代表「覆蓋當前位置的可達路徑數量」
        
        for(int i = 0; i < n; ++i) {
            c += diff[i]; // 疊加差分值，取得當前實際的覆蓋數
            
            // 如果當前是障礙物 ('1')，或者沒有任何起點能覆蓋到這裡 (c == 0)，則無法作為跳板
            if(s[i] == '1' || c == 0) continue;

            // 當前點 i 可達且為 '0'，為未來的可達區間 [i+minJump, i+maxJump] 發放許可證
            ++diff[i + minJump];
            --diff[i + maxJump + 1];
        }

        // 迴圈結束時，c 恰好停留在 n-1 的前綴和上，若 c > 0 代表終點可達
        return c > 0;
    }
};
```

---

## 💡 解法二：二分搜尋與可達性陣列 ($\mathcal{O}(N \log N)$)

### 🎯 演算法分析
另一種思路是反向思考：對於當前的字元 `s[i] == '0'`，我們去歷史紀錄中尋找是否存在一個「已經確認可抵達」的起點 $p$，滿足 $i - maxJump \le p \le i - minJump$。

我們維護一個嚴格單調遞增的陣列 `pos` 來儲存可達節點。利用 `lower_bound` 找出 $\ge i - maxJump$ 的最小起點 `pos[k]`。
因為它是符合下界中**最小的**，如果連它都無法滿足上界（亦即 $pos[k] > i - minJump$），那麼 `pos` 後面的起點就更不可能滿足。因此只需 $\mathcal{O}(\log N)$ 檢查此單一節點即可驗證合法性。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。遍歷字串 $\mathcal{O}(N)$，內部執行二分搜尋 $\mathcal{O}(\log N)$。
- **空間複雜度**: $\mathcal{O}(N)$。用於儲存所有可達索引。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool canReach(string s, int minJump, int maxJump) {
        if(s.back() != '0') return false;

        int n = s.size();
        vector<int> pos;
        pos.push_back(0); // 初始起點

        for(int i = 1; i < s.size(); ++i) {
            if(s[i] == '1') continue;
            
            if(s[i] == '0') {
                int idx = i - maxJump; // 下界
                
                // 尋找第一個大於等於下界的起點
                int k = lower_bound(pos.begin(), pos.end(), idx) - pos.begin();
                if(k == pos.size()) continue;
                
                // 驗證找到的最小合法起點，是否也滿足上界條件
                if(i - pos[k] >= minJump) {
                    pos.push_back(i);
                }
            }
        }

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