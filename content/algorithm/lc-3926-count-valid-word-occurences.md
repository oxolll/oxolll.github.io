---
title: "LeetCode 3926. Count Valid Word Occurrences"
date: 2026-05-10T20:20:00+08:00
lastmod: 2026-05-10T20:20:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-map", "simulation"]
keywords: ["LC3926", "Medium", "Word Count", "String Cleaning", "字串處理", "雜湊表"]
description: "LeetCode 第 3926 題：Count Valid Word Occurrences。難度評分：Medium。探討如何處理大型字串分片，透過邊界過濾與合法單字判定規則，高效統計特定單字的出現頻率。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3926](https://leetcode.com/problems/count-valid-word-occurrences/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Hash Map` $\cdot$ `Simulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個字串陣列 `chunks`（代表被分割的大型文本）和一個查詢陣列 `queries`。
一個「合法單字」的定義如下：
1. 僅由英文字母組成。
2. 可以包含內部標點（如 `hyphen` 或 `apostrophe`），但前提是標點必須緊鄰兩個英文字母（例如 `it's` 或 `well-being` 是合法的，但 `-word` 或 `word-` 不是）。

請計算每個查詢單字在文本中作為獨立單字出現的次數。

### 限制條件
- $1 \le chunks.length \le 1000$
- $1 \le queries.length \le 10^4$
- 總字串長度可能達到 $10^6$ 等級。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-10：初次提交 (兩階段清洗與分詞統計法)](#2026-05-10-初次提交)

---

## 💡 2026-05-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理大規模文本的單字統計，最忌諱在迴圈內頻繁執行 `string.substr` 或複雜的正則表達式。

**核心破局點：**
1. **邊界填充 (Padding)**：在拼接後的字串前後加上空格 `" "`。這樣在判斷標點符號的鄰居 `i-1` 和 `i+1` 時，永遠不需要擔心陣列越界。
2. **合法性過濾 (Cleaning)**：
   遍歷字串，當遇到非字母且非空格的字元時，檢查其左右兩側。如果有一側不是字母，則該字元必定不是合法單字的一部分，將其替換為空格以利後續分詞。
3. **哈希計數 (Tokenization & Counting)**：
   使用 `unordered_map` 預先統計所有切分出來的單字頻率。這樣對於每個 `query`，查詢時間僅需 $\mathcal{O}(1)$。

### 🛠️ 解題思路 (Approach)
1. **文本拼接**：將所有 `chunks` 合併為長字串 `s`，並在兩端補空格。
2. **字元清洗**：建立複本 `cpys`。針對非字母字元，若其鄰居不全為字母，則在 `cpys` 中將其轉為空格。
3. **分詞統計**：
   - 走訪 `cpys`，利用空格作為分隔符提取出連續的非空字串 `tmp`。
   - 使用 `unordered_map<string, int> mp` 累加每個 `tmp` 的出現次數。
4. **回傳查詢**：遍歷 `queries`，直接從 `mp` 中提取答案。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(L + Q)$。
  - $L$ 為總文本長度。拼接、清洗、分詞各需遍歷一次字串。
  - $Q$ 為查詢數量。每次查詢雜湊表為平均常數時間。
- **空間複雜度**: $\mathcal{O}(L)$。主要是存儲拼接後的長字串與哈希表單字的開銷。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> countWordOccurrences(vector<string>& chunks, vector<string>& queries) {
        // 1. 拼接文本並進行邊界填充，簡化鄰居判斷邏輯
        string s = " ";
        for(auto& chunk : chunks) s += chunk;
        s += " ";
        string cpys = s;

        // 2. 清洗階段：標點符號若不被字母包圍，則視為分隔符
        for(int i = 1; i < cpys.size() - 1; ++i) {
            if(s[i] == ' ' || isalpha(s[i])) continue;
            // 如果符號的左邊或右邊不是字母，則它不能構成合法單字的一部分
            if(!isalpha(s[i-1]) || !isalpha(s[i+1]))
                cpys[i] = ' ';
        }

        // 3. 分詞與頻率統計
        unordered_map<string, int> mp;
        string tmp = "";
        for(auto& c : cpys) {
            if(c == ' ') {
                if(!tmp.empty()) {
                    ++mp[tmp];
                    tmp = "";
                }
            } else {
                tmp += c;
            }
        }

        // 4. 回應查詢
        vector<int> rt; 
        rt.reserve(queries.size());
        for(auto& q : queries) {
            rt.push_back(mp.count(q) ? mp[q] : 0);
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