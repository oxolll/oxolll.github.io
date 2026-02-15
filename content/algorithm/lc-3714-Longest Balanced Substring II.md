---
title: "LeetCode 3714. Longest Balanced Substring II"
date: 2026-02-13T00:20:00+08:00
lastmod: 2026-02-13T10:30:00+08:00
difficulty: 2201
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Hash Table", "Prefix Sum", "Case Analysis", "string"]
keywords: ["LC3714", "2201", "平衡子字串", "Vector Optimization"]
description: "LeetCode 第 3714 題：Longest Balanced Substring II。難度評分：2201。探討如何透過分類討論與 Vector 偏移優化來加速平衡子字串的查找。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3714](https://leetcode.com/problems/longest-balanced-substring-ii/)  
> **難度評分**：<span style="color: var(--diff-color); font-weight: bold;">2201 (Medium)</span>   
> **核心主題**：`Case Analysis` $\cdot$ `Prefix Sum` $\cdot$ `Vector Optimization`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個僅包含字元 `'a', 'b', 'c'` 的字串 `s`。
若一個子字串中**所有出現過的字元次數都相同**，則稱該子字串為「平衡子字串」。
請回傳最長平衡子字串的長度。

### 範例
- **Input**: `s = "aaabbbccc"` → **Output**: `9`
- **Explanation**: 整個字串都是平衡的，'a', 'b', 'c' 各出現 3 次。

### 限制條件
- $1 \le s.\text{length} \le 10^5$
- `s` 僅包含 'a', 'b', 'c'。
</details>

---

## 📝 歷次打卡與更新
- [2026-02-14：優化提交 (Vector 取代 Map 加速)](#2026-02-14-優化提交)
- [2026-02-14：初次提交 (Map 實作)](#封存-舊版紀錄)

---

## 💡 2026-02-14 優化提交

### 直覺 (Intuition)
這是基於初次提交的**效能優化版**。
核心思路依然是 **Case Analysis** (分情況討論)，但針對 Case 2 ({a,b}, {b,c}, {a,c})，舊版使用 `map` 紀錄前綴和位置導致速度較慢。
這次改用 **固定長度的 Vector + 偏移量 (Offset)** 來取代 Map，將查找時間嚴格壓在 $O(1)$。

> **Note:** 最初解法是 AI 提示我將問題依照「出現的相異字元數量」進行分類討論，才恍然大悟，我自己太糾結在一種方法完成要求了。

### 解題思路 (Approach)
我們將問題拆解為三種情況分別計算最大長度：

1.  **Case 1: 單一字元**
    * 直接遍歷字串，維護當前連續相同字元的長度 `len`。
    * 一旦字元改變，`len` 重置。

2.  **Case 2: 兩種字元 (以 `{a, b}` 為例)**
    * **優化點**：使用 `vector<int>(2*n+1)` 代替 Map。因為差值範圍在 $[-N, N]$ 之間，我們可以加上偏移量 $N$ 來當作陣列索引。
    * **處理牆壁 (Wall)**：當遇到第三種字元（如 $c$）時，不需要清空陣列（清空是 $O(N)$），而是更新 `legal` 標記位置。 ( $\color{red} \text{lazy reset}$ )
    * **檢查邏輯**：如果查到的前綴和位置 `mp[dif]` 大於等於 `legal`，代表該區間出現在牆壁以後，是合法的平衡子字串。

3.  **Case 3: 三種字元 `{a, b, c}`**
    * 維護兩個差值：$d_{1} = a - b$，$d_{2} = b - c$。
    * 使用 `long long` 將兩個 `int` 壓縮成一個 Key：`(d1 << 32) | d2`。
    * 使用 `unordered_map` 紀錄 Key 第一次出現的位置。

### 📊 複雜度分析
- **時間複雜度**: $O(N)$。所有操作皆為線性掃描，且 Vector 存取比 Map 快得多。
- **空間複雜度**: $O(N)$。使用了固定大小的 Vector 和 Hash Map。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int longestBalanced(string s) {
        int n = s.size();
        int ans = 1;

        // Case 1: 單一字元 {a}, {b}, {c}
        // 直接找最長連續相同字元
        int len = 0;
        for(int i = 0; i < n; ++i) {
            if(i > 0 && s[i] != s[i-1])
                len = 0;
            ++len;
            ans = max(ans, len);
        }

        // Case 2: 兩種字元 {ab}, {ac}, {bc}
        // 使用 Vector + Offset 取代 Map 加速
        // 初始值設為 -2 代表未訪問過 (-1 代表 index -1)
        vector<int> mp_ab(2*n+1, -2);
        vector<int> mp_ac(2*n+1, -2);
        vector<int> mp_bc(2*n+1, -2);
        mp_ab[n] = mp_ac[n] = mp_bc[n] = -1; // 偏移量 n

        int dif_ab = 0, dif_ac = 0, dif_bc = 0;
        int legal_ab = -1, legal_ac = -1, legal_bc = -1; // 記錄"牆"的位置

        for(int i = 0; i < n; ++i) {
            int val = s[i] - 'a';
            
            // 處理 {a, b}，遇到 c (2) 為牆
            if(val == 2) {
                dif_ab = 0;
                legal_ab = i;
                mp_ab[n] = i; // 重置起點為當前牆的位置
            } else {
                dif_ab += (val == 0 ? 1 : -1);
                // 檢查是否曾經出現過該 diff，且位置在合法範圍內
                if(mp_ab[dif_ab + n] >= legal_ab)
                    ans = max(ans, i - mp_ab[dif_ab + n]);
                else
                    mp_ab[dif_ab + n] = i;
            }

            // 處理 {a, c}，遇到 b (1) 為牆
            if(val == 1) {
                dif_ac = 0;
                legal_ac = i;
                mp_ac[n] = i;
            } else {
                dif_ac += (val == 0 ? 1 : -1);
                if(mp_ac[dif_ac + n] >= legal_ac)
                    ans = max(ans, i - mp_ac[dif_ac + n]);
                else
                    mp_ac[dif_ac + n] = i;
            }

            // 處理 {b, c}，遇到 a (0) 為牆
            if(val == 0) {
                dif_bc = 0;
                legal_bc = i;
                mp_bc[n] = i;
            } else {
                dif_bc += (val == 1 ? 1 : -1);
                if(mp_bc[dif_bc + n] >= legal_bc)
                    ans = max(ans, i - mp_bc[dif_bc + n]);
                else
                    mp_bc[dif_bc + n] = i;
            }
        }

        // Case 3: 三種字元 {a, b, c}
        unordered_map<long long, int> mp_abc;
        mp_abc[0] = -1;
        int d1 = 0, d2 = 0; // d1:(a-b), d2:(b-c)
        for(int i = 0; i < n; ++i) {
            int val = s[i] - 'a';
            if(val == 0)      ++d1;
            else if(val == 1) --d1, ++d2;
            else              --d2;

            // 將兩個差值壓縮成一個 key
            long long key = (d1*1ll << 32) | (unsigned int)d2;
            if(mp_abc.find(key) != mp_abc.end())
                ans = max(ans, i - mp_abc[key]);
            else
                mp_abc[key] = i;
        }
        return ans;
    }
};
```
<details>
<summary>📦 <b>封存：舊版紀錄 (Map 實作)</b></summary>

2026-02-14 初次提交
最初的實作版本，使用 ```vector<map<int, int>>``` 來處理 Case 2。雖然邏輯正確，但 Map 的操作開銷較大，執行速度較慢。
```c++
class Solution {
public:
    using pii = pair<int, int>;
    int longestBalanced(string s) {
        int n = s.size();
        vector<int> a; a.reserve(n);
        for(int i = 0; i < n; ++i)
            a.push_back(s[i]-'a');
        
        int rt = 0;
        
        // Case 1
        vector<int> cnt(3, 0);
        for(int i = 0; i < n; ++i) {
            cnt[(a[i]+1)%3] = 0;
            cnt[(a[i]+2)%3] = 0;
            ++cnt[a[i]];
            rt = max(rt, cnt[a[i]]);http://localhost:1313/algorithm/lc-3714-longest-balanced-substring-ii/
        }
        
        // Case 2 (Use Map)
        vector<map<int, int>> diff(3, map<int, int>());
        diff[0][0] = diff[1][0] = diff[2][0] = -1;
        pii key0 = {0, 0}, key1 = {0, 0}, key2 = {0, 0};
        
        for(int i = 0; i < n; ++i) {
            if(a[i] == 0) {
                ++key0.first; ++key1.first;
                key2 = {0, 0}; diff[2].clear(); diff[2][0] = i;
            }
            if(a[i] == 1) {
                ++key0.second; ++key2.first;
                key1 = {0, 0}; diff[1].clear(); diff[1][0] = i;
            }
            if(a[i] == 2) {
                ++key1.second; ++key2.second;
                key0 = {0, 0}; diff[0].clear(); diff[0][0] = i;
            }
            
            int dif0 = key0.first - key0.second;
            int dif1 = key1.first - key1.second;
            int dif2 = key2.first - key2.second;
            
            if(diff[0].count(dif0)) rt = max(rt, i-diff[0][dif0]);
            else diff[0][dif0] = i;

            if(diff[1].count(dif1)) rt = max(rt, i-diff[1][dif1]);
            else diff[1][dif1] = i;

            if(diff[2].count(dif2)) rt = max(rt, i-diff[2][dif2]);
            else diff[2][dif2] = i;
        }

        // Case 3
        map<pii, int> dif; 
        dif[{0, 0}] = -1;
        pii key = {0, 0};
        for(int i = 0; i < n; ++i) {
            if(a[i] == 0) ++key.first;
            if(a[i] == 1) { --key.first; ++key.second; }
            if(a[i] == 2) { --key.second; }

            if(dif.count(key)) rt = max(rt, i-dif[key]);
            else dif[key] = i;
        }
        return rt;
    }
};
```
</details>

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