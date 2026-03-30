---
title: "LeetCode 2840. Check if Strings Can be Made Equal With Operations II"
date: 2026-03-30T14:49:15+08:00
lastmod: 2026-03-30T14:49:15+08:00
difficulty: 1486
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "sorting", "hash table"]
keywords: ["LC2840", "1486", "Medium", "String Operations", "Parity", "Sorting"]
description: "LeetCode 第 2840 題：Check if Strings Can be Made Equal With Operations II。難度評分：1486。探討如何利用奇偶索引獨立的特性，將字串分拆並透過排序 (O(N log N)) 或頻率統計 (O(N)) 來驗證字元集合是否一致。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2840](https://leetcode.com/problems/check-if-strings-can-be-made-equal-with-operations-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1486)</span>  
> **核心主題**：`String` $\cdot$ `Sorting` $\cdot$ `Hash Table`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個長度皆為 $n$ 的字串 `s1` 和 `s2`，字串僅包含小寫英文字母。
你可以對 `s1` 執行以下操作任意次數：
- 選擇兩個索引 `i` 和 `j`，使得 `j - i` 是**偶數**，然後交換 `s1[i]` 和 `s1[j]` 的字元。

如果可以透過上述操作讓 `s1` 等於 `s2`，請回傳 `true`，否則回傳 `false`。

### 限制條件
- $1 \le s1.length == s2.length \le 10^5$
- `s1` 和 `s2` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-30：初次提交 (奇偶分拆與排序比對)](#2026-03-30-初次提交)

---

## 💡 2026-03-30 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題是字串交換操作的經典變形。由於規定 `j - i` 必須是偶數，這在數學上意味著索引 `i` 和索引 `j` 必須具備相同的奇偶性（同為偶數，或同為奇數）。
這產生了一個強烈的限制：**位於偶數索引的字元，無論如何交換，都永遠只能待在偶數索引的位置；奇數索引的字元同理。**
因此，判斷 `s1` 能否轉換為 `s2` 的充分必要條件為：
1. `s1` 中所有偶數索引的字元集合，必須與 `s2` 中所有偶數索引的字元集合完全相同。
2. `s1` 中所有奇數索引的字元集合，必須與 `s2` 中所有奇數索引的字元集合完全相同。

將字串依據奇偶性拆分成兩組，並透過排序使其具備可比對性，即可驗證上述條件。

### 🛠️ 解題思路 (Approach)
1. **長度防呆檢查**：若 `s1` 與 `s2` 長度不同，直接回傳 `false` (雖然題目保證長度相同，但這是一個好習慣)。
2. **字串分拆**：
   - 宣告四個字串變數 `even1`, `even2`, `odd1`, `odd2`。
   - 使用單一迴圈遍歷字串索引。
   - 若索引為偶數 (`i % 2 == 0`)，將 `s1[i]` 與 `s2[i]` 分別附加至 `even1` 與 `even2`。
   - 若索引為奇數，將 `s1[i]` 與 `s2[i]` 分別附加至 `odd1` 與 `odd2`。
3. **排序與比對**：
   - 將四個新字串分別呼叫 `sort` 進行遞增排序。
   - 若 `odd1 != odd2` 或 `even1 != even2`，代表字元集合不吻合，回傳 `false`。
4. **回傳結果**：全部驗證通過後，回傳 `true`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。其中 $N$ 是字串的長度。字串拆分需要 $\mathcal{O}(N)$ 的時間，對長度為 $N/2$ 的字串進行排序需要 $\mathcal{O}(N \log N)$ 的時間。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了四個新的字串來儲存拆分後的結果，總字元數量為 $2N$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    bool checkStrings(string s1, string s2) {
        if(s1.size() != s2.size()) return false;
        
        string odd1 = "";
        string odd2 = "";
        string even1 = "";
        string even2 = "";
        
        // 1. 依據奇偶索引，將字元分拆至獨立的群組
        for(int i = 0; i < s1.size(); ++i) {
            if(i % 2 == 0) {
                even1 += s1[i];
                even2 += s2[i];
            } else {
                odd1 += s1[i];
                odd2 += s2[i];
            }
        }

        // 2. 對群組進行排序，消除相對位置的差異
        sort(odd1.begin(), odd1.end());
        sort(odd2.begin(), odd2.end());
        if(odd1 != odd2) return false;
        
        sort(even1.begin(), even1.end());
        sort(even2.begin(), even2.end());
        if(even1 != even2) return false;
        
        return true;
    }
};
```

---

### 💡 進階最佳化：$\mathcal{O}(N)$ 頻率統計 (Frequency Counting)
雖然排序法直觀易懂，但在面試中若被要求將時間複雜度降至線性 $\mathcal{O}(N)$，我們可以使用**字元頻率統計**來取代排序。
由於字元僅包含 26 個小寫英文字母，我們宣告兩個大小為 26 的整數陣列（或一個二維陣列 `cnt[2][26]` 代表 `[奇偶性][字元]`）。遍歷字串時，`s1` 的字元頻率加 1，`s2` 的字元頻率減 1。最後只要檢查這兩個陣列是否全為 0 即可。

<details>
<summary><b>點擊展開 O(N) 頻率統計實作程式碼</b></summary>

```cpp
class Solution {
public:
    bool checkStrings(string s1, string s2) {
        // cnt[0] 記錄偶數索引的頻率差異，cnt[1] 記錄奇數索引的頻率差異
        vector<vector<int>> cnt(2, vector<int>(26, 0));
        
        for(int i = 0; i < s1.size(); ++i) {
            cnt[i % 2][s1[i] - 'a']++;
            cnt[i % 2][s2[i] - 'a']--;
        }
        
        // 檢查所有字元的頻率差異是否皆已抵銷為 0
        for(int i = 0; i < 2; ++i) {
            for(int j = 0; j < 26; ++j) {
                if(cnt[i][j] != 0) {
                    return false;
                }
            }
        }
        
        return true;
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