---
title: "LeetCode 187. Repeated DNA Sequences"
date: 2026-06-03T20:31:00+08:00
lastmod: 2026-06-03T20:31:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table", "rolling-hash", "bit-manipulation", "sliding-window"]
keywords: ["LC187", "Medium", "Repeated DNA Sequences", "滾動雜湊", "位元運算", "狀態壓縮", "Rabin-Karp"]
description: "LeetCode 第 187 題：Repeated DNA Sequences。探討如何從基礎的字串雜湊，進階到 Rabin-Karp 滾動雜湊，最終利用 DNA 僅含 4 種字元的特性，將狀態完美壓縮進 20-bit 的整數位元運算中，達成 O(N) 時間與極致空間優化的最優解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 187](https://leetcode.com/problems/repeated-dna-sequences/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Hash Table` $\cdot$ `Rolling Hash` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
所有 DNA 都由一系列縮寫為 `'A'`, `'C'`, `'G'`, 和 `'T'` 的核苷酸組成，例如：`"ACGAATTCCG"`。在研究 DNA 時，識別出 DNA 序列中重複出現的模式非常有用。

給你一個表示 DNA 序列的字串 `s`，請你回傳所有在 DNA 分子中出現 **超過一次** 的、長度為 **10** 的子字串序列。你可以按 **任意順序** 回傳答案。

### 範例
- **Input**: `s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"`
- **Output**: `["AAAAACCCCC","CCCCCAAAAA"]`

- **Input**: `s = "AAAAAAAAAAAAA"`
- **Output**: `["AAAAAAAAAA"]`

### 限制條件
- $1 \le s.length \le 10^5$
- `s[i]` 僅為 `'A'`, `'C'`, `'G'`, 或 `'T'`
</details>

---

## 📝 歷次打卡與演算法進化
- [解法三：位元運算與狀態壓縮 (完美的 O(N) 最優解)](#💡-解法三位元運算與狀態壓縮-2-bit-compression-極致最優解)
- [解法二：Rabin-Karp 滾動雜湊 (數學解)](#💡-解法二rabin-karp-滾動雜湊-rolling-hash)
- [解法一：字串切片與雜湊表 (直觀解)](#💡-解法一字串切片與雜湊表-substring--hash-map)

---

## 💡 解法三：位元運算與狀態壓縮 (2-Bit Compression) 極致最優解

### 🎯 演算法分析
這是本題最具工程美感的極致解法！
因為 DNA 序列中只有 `A, C, G, T` 四種字元，我們完全可以用 **2 個 bit** 來編碼一個字元（例如 `00, 01, 10, 11`）。
一個長度為 10 的序列，總共只需要 $10 \times 2 = 20$ 個 bit。這意味著一個長度為 10 的 DNA 序列，可以無損且 **絕對不會發生雜湊碰撞 (Zero Collision)** 地被塞進一個標準 32-bit `int` 的整數中！

**滑動視窗的位元操作：**
當視窗向右滑動一格時，我們只需要：
1. **左移騰出空間**：將當前雜湊值左移 2 位 (`hash << 2`)。
2. **加入新字元**：與新字元的 2-bit 值進行 OR 運算 (`| mp[s[i]]`)。
3. **剔除舊字元**：最舊的字元位於第 21 與 22 個 bit（即左移了 20 位的位置）。利用 XOR 運算 (`^ (mp[s[i-10]] << 20)`)，精準將該字元的位元翻轉回 0。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        if(s.size() < 10) return {};

        // 建立 4 種字元的 2-bit 對映表
        int mp[100];
        mp['A'] = 0; // 00
        mp['C'] = 1; // 01
        mp['G'] = 2; // 10
        mp['T'] = 3; // 11
        
        int hash = 0, n = s.size();
        unordered_map<int, int> cnt;
        
        // 初始化前 10 個字元的 20-bit 狀態
        for(int i = 0; i < 10; ++i) {
            hash = (hash << 2) | mp[s[i]];
        }
        ++cnt[hash];

        vector<string> rt; 
        rt.reserve(n);
        
        for(int i = 10; i < n; ++i) {
            // 滑動視窗：左移 2 位 | 加入新字元 ^ (XOR) 消滅離開視窗的舊字元
            hash = ((hash << 2) | mp[s[i]]) ^ (mp[s[i-10]] << 20);
            
            // 只有在剛好出現第二次時才加入結果 (避免重複加入)
            if(cnt[hash] == 1) {
                rt.push_back(s.substr(i - 9, 10));
            }
            ++cnt[hash];
        }

        return rt;
    }
};
```

---

## 💡 解法二：Rabin-Karp 滾動雜湊 (Rolling Hash)

### 🎯 演算法分析
為了解決頻繁複製字串帶來的效能瓶頸，我們引入字串匹配的經典演算法：**Rabin-Karp**。
將長度為 10 的字串視為一個 $31$ 進位的數字。當視窗向右滑動時：
新雜湊值 $= \text{舊雜湊值} \times 31 + \text{新字元} - \text{移出字元} \times 31^{10}$。
如此一來，字串滑動的時間複雜度從 $\mathcal{O}(L)$ 降為了 $\mathcal{O}(1)$。使用 `unsigned long long` 讓其自然溢位，可自動達成取模效果，極大降低碰撞機率。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    using ULL = unsigned long long;
    vector<string> findRepeatedDnaSequences(string s) {
        if(s.size() < 10) return {};
        
        ULL hash = 0;
        ULL base = 31;
        ULL P = 1;
        unordered_map<ULL, int> mp;
        
        // 初始化第一組長度 10 的雜湊值
        for(int i = 0; i < 10; ++i) {
            hash = hash * base + s[i];
            P *= base; // 計算 base^10，供後續刪除字元使用
        }
        ++mp[hash];
        
        vector<string> rt;
        for(int i = 10; i < s.size(); ++i) {
            // 滾動雜湊：乘以進位 + 新字元 - 舊字元帶來的權重
            hash = hash * base + s[i] - s[i - 10] * P;
            
            // 利用等於 1 來精準過濾重複，只在第二次出現時寫入答案
            if(mp[hash] == 1) {
                rt.push_back(s.substr(i - 9, 10));
            }
            ++mp[hash];
        }

        return rt;
    }
};
```

---

## 💡 解法一：字串切片與雜湊表 (Substring & Hash Map)

### 🎯 演算法分析
最直觀且易於理解的解法。直接以長度為 10 的視窗在字串上滑動，利用 `substr` 切出子字串，並用 `unordered_map<string, int>` 統計出現次數。
儘管邏輯正確且能通過，但每次 `substr` 都需要分配記憶體並拷貝 10 個字元，且 `string` 作為 Key 進行雜湊運算時，常數開銷相對較大，是效能最慢的一種。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        if(s.size() < 10) return {};
        
        unordered_map<string, int> cnt;
        // 滑動視窗，逐一切出長度為 10 的子字串
        for(int i = 0; i <= s.size() - 10; ++i) {
            string t = s.substr(i, 10);
            ++cnt[t];
        }

        vector<string> rt;
        // 將出現次數大於 1 的字串收集起來
        for(auto& [seq, count] : cnt) {
            if(count > 1) {
                rt.push_back(seq);
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