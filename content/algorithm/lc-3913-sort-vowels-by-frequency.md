---
title: "LeetCode 3913. Sort Vowels by Frequency"
date: 2026-04-26T15:30:00+08:00
lastmod: 2026-04-26T15:30:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "sorting", "hash-table"]
keywords: ["LC3913", "Medium", "Sort Vowels by Frequency", "Custom Sort", "String Replacement"]
description: "LeetCode 第 3913 題：Sort Vowels by Frequency。探討如何利用雜湊表統計字元頻率與首次出現位置，透過自訂排序規則整理母音集合，最後以 O(N) 單次掃描完成字串的原地替換 (In-place Replacement)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3913](https://leetcode.com/problems/sort-vowels-by-frequency/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Sorting` $\cdot$ `Hash Table`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個字串 `s`。請你將字串中的**母音字元**（'a', 'e', 'i', 'o', 'u'）進行重新排序，而**子音字元**必須保持在它們原本的位置上。
母音的排序規則如下：
1. **主要規則**：依據該母音在字串中出現的「頻率」進行**遞減**排序（出現最多次的排在最前面）。
2. **次要規則**：如果有多個母音出現的頻率相同，則依據它們在原字串中「首次出現的索引位置」進行**遞增**排序（越早出現的排在越前面）。

請回傳重新排序母音後的字串。

### 限制條件
- $1 \le s.length \le 10^5$ (推測值)
- `s` 僅包含小寫英文字母。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-26：初次提交 (頻率與首位統計、自訂排序與原地填空)](#2026-04-26-初次提交)

---

## 💡 2026-04-26 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
處理這類「部分元素排序、部分元素固定」的題目，最佳策略是**「將需要變動的元素抽離出來處理，最後再填補回去」**。
根據題目要求的雙重排序規則，我們需要兩個關鍵數據：
1. 每個母音出現的總頻率。
2. 每個母音在原字串中第一次出現的索引值。

因此，我們可以分為三個階段來處理：
1. **資料收集**：掃描一次原字串，記錄母音的頻率，並利用一個陣列記錄其首次出現的索引。
2. **獨立排序**：將字串中存在的不重複母音放入一個暫存陣列中，依據題目要求的雙重條件進行自訂排序 (`Custom Sort`)。
3. **原地替換 (In-place Replacement)**：再次掃描原字串，只要遇到母音，就依序從排好序的母音集合中取出字元進行替換。當某個母音的配額（頻率）用盡時，指標才往下一個母音移動。這確保了子音位置不動，母音依序歸位。

### 🛠️ 解題思路 (Approach)
1. **初始化與清理**：重置全域的頻率雜湊表 `mp` 與首次出現位置陣列 `first_p`（確保多次呼叫時狀態獨立）。
2. **第一次掃描 (統計)**：
   - 走訪字串 `s`，檢查是否為母音 (`a, e, i, o, u`)。
   - 若是母音，頻率 `mp` 加一。
   - 若該母音是第一次出現 (`first_p == -1`)，則記錄當前的索引 `i`。
3. **收集並排序唯一母音**：
   - 將 `mp` 中記錄到的母音抽出放入 `tmp` 陣列。
   - 使用自訂的 `cmp` 函式對 `tmp` 進行排序：優先比較頻率（由大到小），若頻率相同則比較首次出現位置（由小到大）。
4. **第二次掃描 (填空)**：
   - 宣告指標 `j = 0` 指向 `tmp` 陣列中當前最高順位的母音。
   - 再次走訪 `s`，若遇到母音，將其替換為 `tmp[j]`。
   - 同時將 `tmp[j]` 的可用頻率減一，若頻率歸零，則將指標 `j` 往後移動至下一個母音。
5. **回傳結果**：回傳修改後的字串 `s`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是字串長度。第一次掃描收集資料耗時 $\mathcal{O}(N)$，母音最多只有 5 種，排序 5 個元素耗時 $\mathcal{O}(1)$，第二次掃描填補耗時 $\mathcal{O}(N)$。整體時間複雜度為線性。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了大小為 26 的 `first_p` 陣列與極小容量的雜湊表 `mp`（最多 5 個鍵值對），空間消耗為常數。字串的修改為原地進行。

### 💻 程式碼實作 (C++)
```cpp
// 宣告於全域以供靜態比較函式 cmp 使用 (實務上建議改用 Lambda 表達式將其封裝至函式內)
vector<int> first_p;
unordered_map<char, int> mp;

class Solution {
public:
    // 自訂排序規則
    static const bool cmp(char& a, char& b) {
        int ca = mp[a], cb = mp[b];
        // 規則一：頻率較高者排前面 (遞減)
        if(ca != cb) return ca > cb;
        // 規則二：頻率相同時，首次出現位置較早者排前面 (遞增)
        return first_p[a - 'a'] < first_p[b - 'a'];
    }
    
    string sortVowels(string s) {
        // 清理全域變數，防止 LeetCode 連續測資干擾
        mp.clear();
        first_p = vector<int>(26, -1);
        
        // 1. 第一次掃描：統計母音頻率與首次出現索引
        for(int i = 0; i < s.size(); ++i) {
            if(s[i] == 'a' || s[i] == 'e' || s[i] == 'i' || s[i] == 'o' || s[i] == 'u') {
                ++mp[s[i]];
                if(first_p[s[i] - 'a'] == -1) {
                    first_p[s[i] - 'a'] = i;
                }
            }
        }
        
        // 2. 收集出現過的唯一母音並進行排序
        vector<char> tmp;
        for(auto [c, val] : mp) {
            tmp.push_back(c);
        }
        sort(tmp.begin(), tmp.end(), cmp);
        
        // 3. 第二次掃描：原地替換母音 (In-place Replacement)
        int j = 0; // 指向排好序的母音陣列
        for(int i = 0; i < s.size(); ++i) {
            if(s[i] == 'a' || s[i] == 'e' || s[i] == 'i' || s[i] == 'o' || s[i] == 'u') {
                s[i] = tmp[j]; // 填入當前最高順位的母音
                --mp[tmp[j]];  // 消耗一次使用額度
                
                // 若該母音額度耗盡，換下一個順位的母音
                if(mp[tmp[j]] == 0) {
                    ++j;
                }
            }
        }

        return s;
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