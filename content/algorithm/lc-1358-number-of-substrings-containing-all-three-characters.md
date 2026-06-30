---
title: "LeetCode 1358. Number of Substrings Containing All Three Characters"
date: 2026-06-30T21:50:00+08:00
lastmod: 2026-06-30T21:55:00+08:00
difficulty: 1646
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "sliding-window", "counting"]
keywords: ["LC1358", "Medium", "1646", "Number of Substrings", "Sliding Window", "滑動視窗", "字元統計", "子字串計數"]
description: "LeetCode 第 1358 題：Number of Substrings Containing All Three Characters。難度評分：1646。探討如何利用雙指標 (Sliding Window) 扎實控制視窗的擴張與收縮來計數，並收錄利用「最後出現位置 (Last Seen Index)」達成極致簡潔的 O(N) 狀態優化寫法。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1358](https://leetcode.com/problems/number-of-substrings-containing-all-three-characters/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1646)</span>  
> **核心主題**：`String` $\cdot$ `Sliding Window` $\cdot$ `Counting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個字串 `s`，它只包含小寫字母 `'a'`, `'b'` 和 `'c'`。
請你回傳至少包含一個 `'a'`、一個 `'b'` 和一個 `'c'` 的 **子字串** 數量。

### 範例
- **Input**: `s = "abcabc"`
- **Output**: `10`
- **Explanation**: 
  包含 a, b, c 的子字串有："abc", "abca", "abcab", "abcabc", "bca", "bcab", "bcabc", "cab", "cabc" 和 "abc" (從 index 3 開始)。共 10 個。

- **Input**: `s = "aaacb"`
- **Output**: `3`
- **Explanation**: 
  包含 a, b, c 的子字串有："aaacb", "aacb" 和 "acb"。共 3 個。

- **Input**: `s = "abc"`
- **Output**: `1`

### 限制條件
- $3 \le s.length \le 5 \times 10^4$
- `s` 僅由字元 'a', 'b' 和 'c' 組成。
</details>

---

## 📝 歷次打卡與演算法進化
- [解法一：原創雙指標視窗控制 (扎實狀態轉移)](#💡-解法一原創雙指標視窗控制-扎實狀態轉移)
- [解法二：標準滑動視窗 (程式碼簡化)](#💡-解法二標準滑動視窗-程式碼簡化)
- [解法三：最後出現位置 (極致優化的 O(N) 狀態陣列)](#💡-解法三最後出現位置-極致優化的-on-狀態陣列)

---

## 💡 解法一：原創雙指標視窗控制 (扎實狀態轉移)

### 🎯 演算法分析
這是一套非常嚴謹且扎實的雙指標 (Sliding Window) 實作。
演算法的核心在於將視窗的操作明確分為兩大階段：**「擴張 (Expand)」** 與 **「收縮 (Shrink)」**，並利用 `while` 迴圈精準控制兩者的交替。
1. **收縮階段**：當視窗內已經集齊 `a`, `b`, `c` 時，嘗試將左指標 `j` 往右縮減，釋放不需要的字元，同時累加對應的合法子字串數量。
2. **擴張階段**：當視窗內缺乏某個字元時，右指標 `i` 往右探索，並將新字元納入計數中。
3. **殘餘處理**：字串掃描結束後，若尾部仍有合法的視窗，則繼續執行收縮直到不合法為止。

這種寫法展現了強大的邏輯控制能力，清晰地勾勒出視窗變化的每一個物理步驟。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numberOfSubstrings(string s) {
        int a = 0, b = 0, c = 0;
        int n = s.size();
        int rt = 0;
        int j = 0;
        
        // i 為右指標，負責擴張
        for(int i = 0; i < n;) {
            // 階段一：若目前已經湊齊 a, b, c，嘗試收縮左指標 j
            while(j < i && a && b && c) {
                switch(s[j]) {
                    case 'a': --a; break;
                    case 'b': --b; break;
                    default:  --c; break;
                }
                ++j;
                
                // 收縮後若依然合法，代表以當前 (i-1) 結尾的這段依然是合法的延伸
                if(a && b && c) rt += (n - i + 1); 
            }
            
            // 階段二：若缺乏某個字元，右指標 i 擴張尋找
            while(i < n && (a == 0 || b == 0 || c == 0)) {
                switch(s[i]) {
                    case 'a': ++a; break;
                    case 'b': ++b; break;
                    default:  ++c; break;
                }
                ++i;
            }
            
            // 擴張後若湊齊了，結算當前視窗對答案的貢獻
            if(a && b && c) {
                rt += (n - i + 1);
            }
        }

        // 殘餘處理：字串走完後，持續收縮左邊界直到不滿足條件
        while(j < n && a && b && c) {
            switch(s[j]) {
                case 'a': --a; break;
                case 'b': --b; break;
                default:  --c; break;
            }
            ++j;
            if(a && b && c) ++rt;
        }
        
        return rt;
    }
};
```

---

## 💡 解法二：標準滑動視窗 (程式碼簡化)

### 🎯 演算法分析
這是在解法一的基礎上，對於「計數邏輯」進行數學上的等價簡化。
當我們維護一個區間，並讓左指標 `j` 停在無法再縮小的極限位置時，這意味著：以當前右指標 `i` 為結尾的所有子字串中，合法的左起點可以落在 `0` 到 `j - 1` 的任意位置。總共有 `j` 個！因此我們只需要迴圈一遍，每次 `rt += j` 即可，省去殘餘狀態的處理。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numberOfSubstrings(string s) {
        int cnt[3] = {0}; 
        int rt = 0;
        int j = 0; 
        
        for (int i = 0; i < s.size(); ++i) {
            cnt[s[i] - 'a']++; 
            
            while (cnt[0] > 0 && cnt[1] > 0 && cnt[2] > 0) {
                cnt[s[j] - 'a']--;
                j++;
            }
            
            rt += j; 
        }
        
        return rt;
    }
};
```

---

## 💡 解法三：最後出現位置 (極致優化的 $\mathcal{O}(N)$ 狀態陣列)

### 🎯 演算法分析
我們完全不需要維護傳統意義上的「視窗」，而是只需要記錄 `'a'`, `'b'`, `'c'` **最後一次出現的索引位置 (Last Seen Index)**。
假設我們目前走到索引 `i`，並且知道 `'a'` 最後出現在 `idxA`，`'b'` 在 `idxB`，`'c'` 在 `idxC`。
要確保子字串同時包含這三個字元，子字串的左起點就**不能超過這三個索引中的最小值**。即 `min_idx = min({idxA, idxB, idxC})`。
只要左起點落在 `0` 到 `min_idx` 之間（共 `min_idx + 1` 個選擇），該子字串就必定合法。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numberOfSubstrings(string s) {
        int last[3] = {-1, -1, -1};
        int rt = 0;
        
        for (int i = 0; i < s.size(); ++i) {
            last[s[i] - 'a'] = i;
            
            int min_idx = min({last[0], last[1], last[2]});
            
            if (min_idx != -1) {
                rt += min_idx + 1;
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