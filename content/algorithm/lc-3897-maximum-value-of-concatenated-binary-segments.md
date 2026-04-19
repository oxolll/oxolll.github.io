---
title: "LeetCode 3897. Maximum Value of Concatenated Binary Segments"
date: 2026-04-12T10:15:00+08:00
lastmod: 2026-04-12T10:15:00+08:00
difficulty: 1998
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "greedy", "bit-manipulation", "sorting"]
keywords: ["LC3897", "Hard", "1998", "Concatenated Binary Segments", "Modular Arithmetic", "Bit Chunking"]
description: "LeetCode 第 3897 題：Maximum Value of Concatenated Binary Segments。難度評分：Hard (1998)。探討如何利用貪婪排序決定二進位片段的最佳串接順序，並透過分塊位移 (Bit Chunking) 技巧安全地處理大數模運算，完美避開 64-bit 整數溢位陷阱。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3897](https://leetcode.com/problems/maximum-value-of-concatenated-binary-segments/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (1998)</span>  
> **核心主題**：`Math` $\cdot$ `Greedy` $\cdot$ `Bit Manipulation` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個長度皆為 $n$ 的整數陣列 `nums1` 與 `nums0`。
這代表有 $n$ 個二進位片段，對於第 $i$ 個片段，它是由 `nums1[i]` 個 `1` 緊接著 `nums0[i]` 個 `0` 所組成的字串。
你可以將這 $n$ 個片段以**任意順序**串接起來，形成一個超長的二進位字串。
請找出所有可能的串接方式中，能夠構成的**最大二進位數值**，並將結果對 $10^9 + 7$ 取模後回傳。

### 限制條件
- $n == nums1.length == nums0.length$
- $1 \le n \le 10^5$
- $0 \le nums1[i], nums0[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-12：初次提交 (貪婪排序與分塊位移防溢位)](#2026-04-12-初次提交)

---

## 💡 2026-04-12 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要讓最終的二進位數值最大化，核心的**貪婪策略 (Greedy Strategy)** 非常直觀：我們必須將對數值貢獻最大的片段，放在最左側的最高有效位 (Most Significant Bits, MSB)。
一個片段對數值的貢獻取決於：**「`1` 的數量越多越好，`0` 的數量越少越好」**。
1. **貪婪排序**：我們將片段轉換為一組鍵值 `(nums1[i], -nums0[i])` 進行遞增排序。當我們從陣列尾端（即反向）讀取時，就能優先取得「`1` 的數量最多、且 `0` 的數量最少」的片段，將其排在最高位。
2. **純 1 片段的絕對優勢**：如果一個片段沒有任何 `0` (`nums0[i] == 0`)，它絕對應該被放在最高位（因為 `0` 會稀釋後面片段的價值）。因此，我們將純 `1` 的片段獨立挑出，直接附加在排序陣列的最後面，保證它們最先被處理。
3. **大數模運算與防溢位 (Bit Chunking)**：
   當我們進行 `rt = (rt << bits) + value` 時，因為 $bits \le 10^5$，直接左移會立刻突破 `long long` 的 64-bit 極限。
   **專業防溢位技巧**：每次只移動 **30 bits**。由於取模的基數是 $MOD = 10^9 + 7 \approx 2^{30}$，在取模後 `rt` 最大約為 $10^9$。若將其左移 30 位，數值約為 $10^9 \times 2^{30} \approx 10^{18}$，剛好落在 64 位元有號整數 `long long` 的安全上限 ($9 \times 10^{18}$) 內。這完美避開了溢位 (Overflow) 導致的 WA。

### 🛠️ 解題思路 (Approach)
1. **資料分組與排序**：
   - 走訪陣列，將帶有 `0` 的片段加入 `arr` 中，儲存格式為 `{nums1[i], -1 * nums0[i]}`。
   - 呼叫 `sort(arr.begin(), arr.end())`，使其依照 `1` 的數量遞增、`0` 的數量遞減排序。
   - 將沒有 `0` 的純 `1` 片段打包成 `{nums1[i], 0}` 附加於陣列最末端。
2. **由最高位開始建構數值**：
   - 宣告 `long long rt = 0`。
   - 從陣列最末端 (`n-1`) 倒序走訪，這樣最先處理的會是最高位的片段。
3. **分塊位移 (Bit Chunking)**：
   - **處理 `1` 的部分 (`cnt1`)**：
     - 使用 `while(cnt1 >= 30)`，每次左移 30 位，並加上 30 個 `1`（即 `(1ll << 30) - 1`），立刻取模。
     - 處理剩餘的 `cnt1` 位數。
   - **處理 `0` 的部分 (`cnt2`)**：
     - 使用 `while(cnt2 >= 30)`，每次左移 30 位（不需加值，因為補 `0`），立刻取模。
     - 處理剩餘的 `cnt2` 位數。
4. **回傳結果**：走訪完所有片段後，回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。排序耗時 $\mathcal{O}(N \log N)$。位移過程雖然有 `while` 迴圈，但總體位移次數與總位元數成正比，攤銷時間極快。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了 `arr` 陣列來儲存整理後的鍵值。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    
    int maxValue(vector<int>& nums1, vector<int>& nums0) {
        int n = nums1.size();
        vector<pair<long long, long long>> arr;
        
        // 1. 收集包含 0 的片段，以 (1的數量, -0的數量) 作為排序鍵值
        for(int i = 0; i < n; ++i) {
            if(nums0[i] == 0) continue;
            // 排序策略：優先比較 1 的數量 (遞增)；若相同，則 0 的數量越少越好 (透過乘以 -1 轉為遞增)
            arr.push_back({nums1[i], -1 * nums0[i]});
        }
        
        sort(arr.begin(), arr.end());
        
        // 將純 1 的片段直接加到最後，確保它們在倒序讀取時被置於最高位
        for(int i = 0; i < n; ++i) {
            if(nums0[i] != 0) continue;
            arr.push_back({nums1[i], 0});
        }
        
        long long rt = 0;
        
        // 2. 倒序走訪，由最高位開始建構二進位數值
        for(int i = n - 1; i >= 0; --i) {
            int cnt1 = arr[i].first;
            int cnt2 = -1 * arr[i].second;
            
            // 3. 分塊位移 (Bit Chunking) 防止 64-bit 溢位
            // 處理 1 的位移：每次最多移動 30 bits
            while(cnt1 >= 30) {
                rt = ((rt << 30) + ((1ll << 30) - 1)) % MOD;
                cnt1 -= 30;
            }
            rt = ((rt << cnt1) + ((1ll << cnt1) - 1)) % MOD;

            // 處理 0 的位移：只需左移補 0，不需加值
            while(cnt2 >= 30) {
                rt = (rt << 30) % MOD;
                cnt2 -= 30;
            }
            rt = (rt << cnt2) % MOD;
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