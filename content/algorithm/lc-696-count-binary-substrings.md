---
title: "LeetCode 696. Count Binary Substrings"
date: 2026-02-19T15:19:00+08:00
lastmod: 2026-02-19T15:19:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "String", "Counting"]
keywords: ["LC696", "Easy", "字串處理", "C++"]
description: "LeetCode 第 696 題：Count Binary Substrings。難度評分：Easy。探討關於字串連續分群計數與 C++ 底層字串擴容陷阱的解法，完整記錄三次實作演進。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 696](https://leetcode.com/problems/count-binary-substrings/)  
> **難度評分**：<span style="color: var(--diff-color); font-weight: bold;">Easy(N/A)</span>  
> **核心主題**：`String` $\cdot$ `Two Pointers`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個二進位字串 `s`，請計算有多少個非空子字串滿足以下條件：
1. 包含相同數量的 `0` 和 `1`。
2. 所有的 `0` 和所有的 `1` 在這些子字串中是連續組合的（例如：`0011` 或 `1100`）。

重複出現的子字串要重複計算。

### 範例
- **Input**: `s = "00110011"` → **Output**: `6`
- **Explanation**: 有 6 個子字串滿足條件："0011", "01", "1100", "10", "0011", "01"。注意，"00110011" 不合法，因為 0 和 1 沒有各自群聚在一起。

### 限制條件
- $1 \le s.length \le 10^5$
- `s[i]` 必定為 `'0'` 或 `'1'`
</details>

---

## 📝 歷次打卡與更新
- [2026-02-19：初次提交 (紀錄三次演進：狀態機 $\to$ 簡化邏輯 $\to$ 避開擴容陷阱)](#2026-02-19-初次提交)

---

## 💡 2026-02-19 初次提交

### 直覺 (Intuition)
這題雖然被歸類為 Easy，但在實作上很容易把邏輯寫得很複雜。其核心本質其實是一個 **「群組計數問題」**。
觀察連續的字串塊，例如 `00011`，前面有 3 個 `0`，後面有 2 個 `1`。它們能組成的合法交替子字串數量，剛好就是這兩群長度的**最小值**（即 `min(3, 2) = 2` 個，分別是 `01` 和 `0011`）。因此，我們只需記錄相鄰區塊的長度即可。

### 解題思路 (Approach)
這題的實作經歷了三次演進，完美展示了從「功能實現」到「底層效能最佳化」的過程：

1. **初版（狀態機思維）**：
   一開始的解法偏向逐字元更新狀態，並用三元運算子來控制 `cnt_0` 與 `cnt_1`。為了處理字串結尾的邊界條件，直覺地在尾巴加上了一個虛擬字元 `'2'` 觸發最後一次結算。雖然能過，但邏輯稍顯冗長。
2. **次版（簡化連續區塊，但踩到效能陷阱）**：
   意識到其實不需要真的分開記錄 `0` 和 `1` 的數量，只需要記錄「當前連續長度 (`cur`)」與「上一個連續長度 (`pre`)」即可。程式碼變得非常乾淨，但在 LeetCode 上只擊敗了 36%。
   **效能瓶頸分析**：關鍵就在 `s += '2';` 這一行。在 C++ 中，<span style="color: #ff6b6b;">當字串長度逼近 $10^5$ 時，執行 `+=` 附加操作極有可能觸發 `std::string` 底層動態陣列的**重新配置 (Reallocation)**，導致整個大字串被複製一次，帶來隱含的額外開銷。</span>
3. **最終版（唯讀模式，100% 效能）**：
   為了達到 100% 的擊敗率，必須保持字串的原貌（唯讀狀態）。在遍歷過程中，只要遇到字元切換就把 `min(pre, cur)` 加到總和中；而字串結尾的最後一組區塊，只要在迴圈結束後於外部補上結算即可。

### 📊 複雜度分析 (最終版)
- **時間複雜度**: $O(N)$。只需嚴格遍歷字串一次，且無字串重配置的隱藏開銷。
- **空間複雜度**: $O(1)$。只使用了常數級別的整數變數。

---

### 💻 程式碼實作 (C++)

#### V1：初版 (狀態機思維)
邏輯較為複雜，使用三元運算子頻繁切換狀態，並透過 `s += '2'` 處理邊界。
```cpp
class Solution {
public:
    int countBinarySubstrings(string s) {
        int cnt_0 = 0;
        int cnt_1 = 0;
        int rt = 0;
        char pre = '2';
        s += '2'; // 觸發最後一次結算
        
        for(int i = 0; i < s.size(); ++i) {
            if(pre != s[i]) {
                rt += min(cnt_0, cnt_1);
                cnt_0 += (s[i] == '0' ? -cnt_0 : 0);
                cnt_1 += (s[i] == '1' ? -cnt_1 : 0);
                pre = s[i];
            }
            cnt_0 += (s[i] == '0' ? 1 : 0);
            cnt_1 += (s[i] == '1' ? 1 : 0);
        }

        return rt;
    }
};
```

#### V2：次版 (簡化邏輯，擊敗 36%)
大幅精簡邏輯，只記錄 `pre` 與 `cur`，但仍然保留了會觸發底層陣列擴容的 `s += '2'`。
```cpp
class Solution {
public:
    int countBinarySubstrings(string s) {
        int rt = 0, pre = 0, cur = 1;
        s += '2'; // 效能殺手：可能導致 O(N) 的字串重配置拷貝
        
        for(int i = 1; i < s.size(); ++i) {
            if(s[i-1] == s[i]) {
                ++cur;
            } else {
                rt += min(pre, cur);
                pre = cur;
                cur = 1;
            }
        }
        return rt;
    }
};
```

#### V3：最終版 (100% 效能，The C++ Way)
移除字串修改操作，保持唯讀遍歷，並將最後一次計算移至迴圈外，完美避免記憶體重配置。
```cpp
class Solution {
public:
    int countBinarySubstrings(string s) {
        int res = 0;
        int pre = 0;
        int cur = 1;
        
        for(int i = 1; i < s.size(); ++i) {
            if(s[i-1] == s[i]) {
                ++cur;
            } else {
                res += min(pre, cur);
                pre = cur;
                cur = 1;
            }
        }
        
        // 迴圈結束後，補上最後一組的結算
        return res + min(pre, cur);
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