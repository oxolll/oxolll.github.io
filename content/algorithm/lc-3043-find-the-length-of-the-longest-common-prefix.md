---
title: "LeetCode 3043. Find the Length of the Longest Common Prefix"
date: 2026-05-21T14:15:00+08:00
lastmod: 2026-05-21T14:15:00+08:00
difficulty: 1688
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash-table", "string", "trie"]
keywords: ["LC3043", "Medium", "1688", "Longest Common Prefix", "字首搜尋", "雜湊表剪枝"]
description: "LeetCode 第 3043 題：Find the Length of the Longest Common Prefix。難度評分：1688。探討如何利用 Hash Set 儲存所有字首，並結合動態長度剪枝技巧，高效找出兩個陣列數字間的最長公共字首。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3043](https://leetcode.com/problems/find-the-length-of-the-longest-common-prefix/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1688)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `String` $\cdot$ `Trie`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個正整數陣列 `arr1` 和 `arr2`。

一個正整數的 **字首 (Prefix)** 是指它在十進位表示下，由左側開始的一個或多個連續數位。例如，`123` 的字首有 `1`、`12` 和 `123`。

請你傳回 `arr1` 中的某個整數 $x$ 和 `arr2` 中的某個整數 $y$ 之間，**最長公共字首** 的長度。如果沒有任何公共字首，則傳回 0。

### 範例
- **Input**: `arr1 = [1,10,100], arr2 = [1000]`
- **Output**: `3`
- **Explanation**: 
  - 1 和 1000 的公共字首是 1 (長度 1)。
  - 10 和 1000 的公共字首是 10 (長度 2)。
  - 100 和 1000 的公共字首是 100 (長度 3)。
  最長公共字首長度為 3。

- **Input**: `arr1 = [1,2,3], arr2 = [4,4,4]`
- **Output**: `0`

### 限制條件
- $1 \le arr1.length, arr2.length \le 5 \cdot 10^4$
- $1 \le arr1[i], arr2[i] \le 10^8$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-21：初次提交 (Hash Set 與動態剪枝優化)](#2026-05-21-初次提交)

---

## 💡 2026-05-21 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的直覺解法是將 `arr1` 的數字當作字串，建構一棵**字典樹 (Trie)**，然後用 `arr2` 的數字去 Trie 中進行比對。
然而，透過 **Hash Set (雜湊表)** 也可以達到極佳的實戰效果，特別是當我們引入了「單調遞增剪枝」後，效能完全不遜色於 Trie。

**核心破局點：極限剪枝優化**
1. **窮舉字首建表**：將 `arr1` 中所有數字轉為字串，並把所有的可能字首全部丟進 `unordered_set` 中。因為數字最大只有 $10^8$（最多 9 個數位），這步的空間和時間開銷非常小。
2. **長度過濾 (Length Pruning)**：在遍歷 `arr2` 時，如果當前數字轉成字串後的長度 $\le mx$（目前找到的最大公共長度），那麼它**絕對不可能**貢獻出更長的公共字首，直接 `continue` 跳過。
3. **起點跳躍 (Jump Start)**：我們不需要從長度 1 開始驗證 `arr2` 的數字。既然已知目前最大長度是 `mx`，我們直接擷取 `s.substr(0, mx)` 作為起點。如果連長度為 `mx` 的字首都不存在於 Set 中，更長的字首必然也不存在，直接 `break` 中斷即可。

---

### 🛠️ 解題思路 (Approach)
1. **建立字首池**：宣告 `unordered_set<string> hs`。
2. **填充字首**：遍歷 `arr1`，把每個數字轉為字串 `s`，透過迴圈逐字元累加並 `insert` 入 `hs` 中。
3. **尋找最長公共長度**：初始化 `mx = 0`。
   - 遍歷 `arr2` 的每個數字，轉為字串 `s`。
   - 若 `s.size() <= mx`，直接略過。
   - 提取初始前綴 `cur = s.substr(0, mx)`。
   - 從索引 `mx` 開始向後推進，每次加入一個字元，並檢查新 `cur` 是否存在於 `hs` 中。
   - 若存在，更新 `mx = cur.size()` 並繼續向後試探；若不存在，代表在該分支已走到盡頭，直接 `break`。

---

### 📊 複雜度分析
令 $N$ 為 `arr1` 的長度，$M$ 為 `arr2` 的長度，$D$ 為數字的最大數位長度（在本題中 $D \le 9$）。
- **時間複雜度**: $O(N \cdot D + M \cdot D)$。
  - 將 `arr1` 建立 Hash Set，每個數字最多產生 $D$ 個字首，字串操作耗時受限於 $D^2$。
  - 查詢 `arr2` 時，每個數字最多做 $D$ 次雜湊查詢，同樣受限於 $D^2$。
  - 由於 $D \le 9$ 視為極小的常數，整體時間複雜度可視為線性的 $O(N + M)$。
- **空間複雜度**: $O(N \cdot D)$。`unordered_set` 中最多會儲存 $N \cdot D$ 個短字串。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int longestCommonPrefix(vector<int>& arr1, vector<int>& arr2) {
        unordered_set<string> hs;
        
        // 1. 將 arr1 中所有數字的所有可能字首存入 Hash Set
        for(auto x : arr1) {
            string s = to_string(x);
            string cur = "";
            for(auto c : s) {
                cur += c;
                hs.insert(cur);
            }
        }

        int mx = 0;
        
        // 2. 遍歷 arr2 尋找能匹配的最長字首
        for(auto x : arr2) {
            string s = to_string(x);
            
            // 剪枝 1：如果自身長度都不超過已知的最大長度 mx，直接略過
            if(s.size() <= mx) continue;
            
            // 剪枝 2：起點跳躍，直接從長度 mx 開始比對
            string cur = s.substr(0, mx);

            for(int i = mx; i < s.size(); ++i) {
                cur += s[i];
                // 若能在 arr1 的字首池中找到
                if(hs.find(cur) != hs.end()) {
                    mx = cur.size(); // 更新最大長度
                    continue;
                }
                // 若當前長度找不到，更長的一定也找不到，提早中斷
                else {
                    break;
                }
            }
        }

        return mx;
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