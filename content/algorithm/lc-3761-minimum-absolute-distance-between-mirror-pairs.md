---
title: "LeetCode 3761. Minimum Absolute Distance Between Mirror Pairs"
date: 2026-04-17T13:28:00+08:00
lastmod: 2026-04-17T13:28:00+08:00
difficulty: 1668
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "hash-table", "math"]
keywords: ["LC3761", "1668", "Medium", "Minimum Absolute Distance", "Mirror Pairs", "Hash Table"]
description: "LeetCode 第 3761 題：Minimum Absolute Distance Between Mirror Pairs。難度評分：1668。探討如何利用 Hash Table 進行單次走訪 (One-pass) 掃描，預先儲存反轉數字的索引，以 O(N) 時間複雜度極速找出鏡像數字對的最小絕對距離。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3761](https://leetcode.com/problems/minimum-absolute-distance-between-mirror-pairs/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1668)</span>  
> **核心主題**：`Array` $\cdot$ `Hash Table` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
一對索引 $(i, j)$ 被稱為「鏡像數字對 (Mirror Pair)」，若滿足 $i < j$ 且 `nums[i]` 的數字反轉結果等於 `nums[j]`（反之亦然）。
例如，`12` 與 `21` 互為鏡像，`100` 與 `1` 互為鏡像（反轉後忽略前導零）。
請找出陣列中所有鏡像數字對之中，絕對距離 $|i - j|$ 的**最小值**。如果陣列中不存在任何鏡像數字對，請回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-17：初次提交 (Hash Table 單次走訪與數字反轉)](#2026-04-17-初次提交)

---

## 💡 2026-04-17 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
尋找「配對」或「距離」的最優解，通常離不開 **Hash Map (雜湊表)** 的輔助。
為了找出鏡像數字的最小距離，我們可以採用 **單次走訪 (One-pass)** 策略：
當我們遍歷到陣列的第 $j$ 個元素 `nums[j]` 時：
1. 檢查 `nums[j]` 是否存在於 Hash Map 中。如果存在，代表前面出現過某個數字，其反轉後的結果剛好等於當前的 `nums[j]`。我們計算距離 $j - \text{map}[nums[j]]$ 並更新最小值。
2. 計算當前數字 `nums[j]` 的反轉結果 `rev`，並將 `rev` 與當前索引 $j$ 存入 Hash Map 中。這代表著：「如果未來有數字剛好等於我的 `rev`，那麼我們就是一對，且我的索引是 $j$」。

由於我們是從左到右走訪，每次存入 Hash Map 的索引必定是該數值「最晚出現 (最靠右)」的位置，這完美保證了算出來的距離 $|i - j|$ 絕對是同值情況下的最小值。

### 🛠️ 解題思路 (Approach)
1. **初始化**：
   - 宣告 `unordered_map<int, int> mp` 用來儲存預期配對的數字與其索引。
   - 宣告 `mm = INT_MAX` 記錄最小絕對距離。
2. **走訪陣列**：
   - 使用 `for` 迴圈遍歷陣列索引 `j`。
   - **配對檢查**：若 `mp` 中找得到 `nums[j]`，計算距離 `j - mp[nums[j]]`，並更新全域最小值 `mm`。
   - **數字反轉**：利用 `while(k)` 迴圈，透過取模 `% 10` 與除法 `/ 10`，將 `nums[j]` 的數字完全反轉，得到 `rev`。
   - **更新紀錄**：將 `mp[rev] = j` 寫入 Hash Map，供後續的元素配對使用。
3. **回傳結果**：
   - 走訪結束後，若 `mm` 仍為 `INT_MAX`，代表未找到任何鏡像對，回傳 `-1`；否則回傳 `mm`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \times D)$。其中 $N$ 是陣列的長度，$D$ 是數字的最大位數（通常 $D \le 6$ 可視為常數）。整體時間複雜度為線性時間 $\mathcal{O}(N)$。
- **空間複雜度**: $\mathcal{O}(N)$。最差情況下，Hash Map 需要儲存 $N$ 個不同的反轉數字。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int minMirrorPairDistance(vector<int>& nums) {
        unordered_map<int, int> mp;
        int mm = INT_MAX;
        int n = nums.size();
        
        for(int j = 0; j < n; ++j) {
            // 1. 若當前數字是前面某個數字的鏡像，計算並更新最小距離
            if(mp.find(nums[j]) != mp.end()) {
                mm = min(mm, j - mp[nums[j]]);
            }
            
            // 2. 計算當前數字的反轉結果 (rev)
            int rev = 0;
            int k = nums[j];
            while(k) {
                rev *= 10;
                rev += k % 10;
                k /= 10;
            }
            
            // 3. 將反轉結果與當前索引存入 Map，等待未來的配對
            mp[rev] = j;
        }

        return mm == INT_MAX ? -1 : mm;
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