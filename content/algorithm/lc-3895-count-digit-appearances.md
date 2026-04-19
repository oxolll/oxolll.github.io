---
title: "LeetCode 3895. Count Digit Appearances"
date: 2026-04-12T10:05:00+08:00
lastmod: 2026-04-12T10:05:00+08:00
difficulty: 1269
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "array"]
keywords: ["LC3895", "Medium", "1269", "Count Digit Appearances", "Digit Extraction"]
description: "LeetCode 第 3895 題：Count Digit Appearances。難度評分：Medium (1269)。探討如何利用取模 (% 10) 與除法 (/ 10) 拆解整數，並嚴謹處理數字 0 的邊界防呆條件，以計算特定數字在陣列中出現的總次數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3895](https://leetcode.com/problems/count-digit-appearances/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1269)</span>  
> **核心主題**：`Math` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums` 以及一個目標單一數字 `digit` ($0 \le digit \le 9$)。
請計算並回傳 `digit` 在 `nums` 陣列所有元素的各個位數中，總共出現的次數。

### 限制條件
- $1 \le nums.length \le 10^5$
- $0 \le nums[i] \le 10^9$
- $0 \le digit \le 9$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-12：初次提交 (數字拆解與邊界防呆)](#2026-04-12-初次提交)

---

## 💡 2026-04-12 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這是一道測驗基礎 **數字拆解 (Digit Extraction)** 的題目。
在不轉換為字串 (String) 的前提下，最高效的拆解方式是利用數學運算：
1. 透過取模運算 `num % 10` 可以精準取得數字的個位數。
2. 透過整數除法運算 `num / 10` 可以將數字向右推移，捨棄已處理的個位數。

**⚠️ 核心陷阱 (Edge Case)**：
本題最大的一個隱藏陷阱在於「數字為 $0$」的處理。標準的 `while(k > 0)` 迴圈在遇到 $k = 0$ 時會直接被略過。如果陣列中存在數字 $0$，且我們要尋找的 `digit` 剛好也是 $0$，這個 $0$ 就會被漏算！因此，必須針對此極端情況加入額外的防呆邏輯。

### 🛠️ 解題思路 (Approach)
1. **初始化**：宣告全域計數器 `cnt = 0`。
2. **走訪陣列**：使用 `for` 迴圈逐一處理 `nums` 中的每個數字 `num`。
3. **邊界防呆攔截**：
   - 優先檢查特殊情況：如果 `num == 0` 且目標 `digit == 0`，則直接將 `cnt` 加 1，並使用 `continue` 提早進入下一個數字的處理，避免進入下方會被跳過的 `while` 迴圈。
4. **迴圈拆解**：
   - 宣告變數 `k = num` 進行操作。
   - 使用 `while(k)` 迴圈（只要 $k \neq 0$ 就繼續）：
     - 檢查 `k % 10` 是否等於 `digit`，若相等則 `++cnt`。
     - 將 `k` 除以 10 (`k /= 10`) 進入下一位數。
5. **回傳結果**：走訪完所有數字後，回傳總計數 `cnt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times \log_{10} M)$。其中 $N$ 是陣列的長度，$M$ 是陣列中數字的最大值。內層的 `while` 迴圈執行次數取決於數字的位數，最大約為 10 次，整體時間複雜度可視為線性時間。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了常數個變數進行運算。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int countDigitOccurrences(vector<int>& nums, int digit) {
        int cnt = 0;
        
        for(auto num : nums) {
            // 💡 邊界防呆保護 (Edge Case Protection)
            // 若數字本身為 0 且尋找的目標位數也是 0，while(k) 會直接失效，需特別處理
            if(num == 0 && digit == 0) {
                ++cnt;
                continue;
            }
            
            int k = num;
            // 透過數學運算逐位拆解數字
            while(k) {
                // 取出個位數進行比對
                if((k % 10) == digit) {
                    ++cnt;
                }
                // 向右推移，捨棄已處理的個位數
                k /= 10;
            }
        }
        return cnt;
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