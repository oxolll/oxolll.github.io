---
title: "LeetCode 3947. Maximum Number of Items From Sale II"
date: 2026-06-03T16:00:00+08:00
lastmod: 2026-06-03T16:00:00+08:00
difficulty: 2215
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "greedy", "math", "sorting", "number-theory"]
keywords: ["LC3947", "Medium", "Maximum Number of Items From Sale II", "貪心演算法", "調和級數", "配對問題", "單價篩選"]
description: "LeetCode 第 3947 題：Maximum Number of Items From Sale II。探討如何將條件贈送規則轉化為獨立的配對交易，利用調和級數篩法 (Harmonic Sieve) 在 O(N log N) 時間內計算倍數頻率，並透過嚴格的單價比較執行貪心策略，求得最大物品總數。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3947](https://leetcode.com/problems/maximum-number-of-items-from-sale-ii/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Greedy` $\cdot$ `Math (Harmonic Series)` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個二維陣列 `items`，其中 `items[i] = [value_i, price_i]`，代表第 $i$ 個物品的值與價格。同時給你一個整數 `budget`，代表你擁有的總預算。

商店的促銷活動如下：
- 對於任意兩個不同的物品 $i$ 和 $j$，如果 `items[j][0]` (即 $value_j$) 是 `items[i][0]` ($value_i$) 的 **倍數**，你可以將它們視為一個 **配對優惠 (Pair Deal)**。
- 購買這個配對優惠只需要花費 `price_i`，且你會同時獲得這 2 個物品。
- 每個這樣的配對優惠皆為獨立存在，你可以購買任意數量的有效配對。
- 同時，你也可以花費原本的價格，單獨且無限次地購買商店中的任何物品。

請你計算在不超過 `budget` 的情況下，最多可以獲得 **多少個** 物品。

### 限制條件
- $1 \le items.length \le 10^5$ (依據 $\mathcal{O}(N \log N)$ 複雜度推斷)
- $1 \le items[i][0] \le items.length$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-03：初次提交 (調和篩法與貪心最優化)](#2026-06-03-初次提交)

---

## 💡 2026-06-03 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題與第一集 (Sale I) 最大差異在於：第一集是買 $i$ 就可以一次性打包**所有**符合條件的 $j$，屬於不可分割的 0/1 背包問題；而這題是將每個符合條件的 $(i, j)$ 視為**獨立的配對交易**，每組交易固定獲得 2 個物品，這完美符合 **貪心演算法 (Greedy)** 的性質！

**核心破局點 1：貪心單價比較**
我們有兩種增加物品的方式：
1. **單獨購買**：最划算的選擇是永遠只買全場最便宜的物品（價格為 `mm`），平均每個物品成本為 $mm$。
2. **購買配對**：花費 `price_i` 獲得 2 個物品，平均每個物品成本為 $price\_i / 2$。

只有當配對的平均成本 **嚴格小於** 單獨購買的成本時（即 $price\_i / 2 < mm \implies price\_i < 2 \times mm$），這個配對才值得我們掏錢購買！我們將這些「划算」的配對收集起來，並依價格由低到高排序，優先買最便宜的配對。

**核心破局點 2：調和級數尋找倍數 (Harmonic Sieve)**
如果使用雙層迴圈尋找倍數，時間複雜度會高達 $\mathcal{O}(N^2)$，必定超時。
因為 `items[i][0]` 的值域被限制在 $N$ 以內，我們可以利用一個頻率陣列 `cnt` 記錄每個 `value` 出現的次數。
接著，對於每個值 $f$，我們以 $f$ 為步長，跳躍式地統計所有倍數的頻率總和：`for(int j = f; j <= n; j += f)`。
這種寫法在數學上屬於調和級數，總執行次數約為 $\frac{N}{1} + \frac{N}{2} + \frac{N}{3} + \dots \approx N \ln N$，將時間複雜度極致優化至 $\mathcal{O}(N \log N)$。

---

### 🛠️ 解題思路 (Approach)
1. **建立頻率分佈與最低價**：
   - 掃描一次陣列，用 `cnt` 記錄每個 `value` 的出現次數。
   - 同時找出全場最便宜的單品價格 `mm`。
2. **計算配對數量與過濾候選者**：
   - 遍歷每個物品 $i$（其值為 $f$）。
   - 若 $f = 1$，所有物品皆為其倍數，可配對數為 $N$。
   - 若 $f \neq 1$，利用篩法 `j += f` 累加所有倍數的出現次數。
   - 將配對總數減 1（排除與自己配對）。
   - 檢查貪心條件：若有可用的配對數 (`freeCnt > 0`) 且該配對划算 (`mm * 2 > price`)，則將 `{price, freeCnt}` 加入候選名單 `cand`。
3. **貪心購買配對**：
   - 將 `cand` 依據價格升冪排序。
   - 在預算 `rem` 允許的範圍內，盡可能買下最便宜的配對。
   - 每買下一組配對，花費 $p$，獲得 2 個物品 (`rt += 2 * num`)。
4. **消化剩餘預算**：
   - 剩下的零錢不足以購買任何划算的配對時，全部拿去買最便宜的單品：`rt += rem / mm`。
5. **回傳總物品數**。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。
  - 計算所有倍數的頻率總和，依據調和級數特性耗時 $\mathcal{O}(N \log N)$。
  - 對候選陣列 `cand` 排序，最壞情況下耗時 $\mathcal{O}(N \log N)$。
  - 整體時間複雜度十分優異。
- **空間複雜度**: $\mathcal{O}(N)$。
  - 宣告了長度為 $N+1$ 的頻率陣列 `cnt`、`freeCnt`，以及候選陣列 `cand`。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximumSaleItems(vector<vector<int>>& items, int budget) {
        int n = items.size();
        vector<int> cnt(n + 1, 0); // 記錄各 value 出現的頻率
        int mm = INT_MAX;          // 記錄全場最便宜單品價格
        
        // 1. 統計頻率與尋找全局最低價
        for(int i = 0; i < n; ++i) {
            ++cnt[items[i][0]];
            mm = min(mm, items[i][1]);
        }

        vector<int> freeCnt(n, 0);
        vector<pair<int, int>> cand; // 儲存划算的配對優惠 {價格, 配對數量}
        
        // 2. 利用調和級數篩法，快速計算每個物品能組成的配對數量
        for(int i = 0; i < n; ++i) {
            int f = items[i][0];
            
            // 針對 1 進行特判優化：所有物品都是 1 的倍數
            if(f != 1) {
                // 步長為 f 跳躍搜尋，總耗時 O(N log N)
                for(int j = f; j <= n; j += f) {
                    freeCnt[i] += cnt[j];
                }
            } else {
                freeCnt[i] += n;
            }
            
            // 扣除掉物品自身，剩下的即為可配對的其他物品數量
            --freeCnt[i];
            
            if(freeCnt[i] == 0) continue;
            
            // 貪心過濾：只有當買配對的平均成本 (price / 2) 小於買最便宜單品 (mm) 時，才值得購買
            if(mm * 2 > items[i][1]) {
                cand.push_back({items[i][1], freeCnt[i]});
            }
        }

        // 3. 貪心策略：依據配對的價格由低到高排序
        sort(cand.begin(), cand.end());

        int rem = budget;
        int rt = 0; // 記錄總獲得物品數
        
        // 4. 盡可能購買最划算的配對
        for(auto& [p, c] : cand) {
            if(rem < p) break; // 預算連一組都買不起時提早結束

            // 計算當前優惠最多能買幾組
            int num = min(c, rem / p);
            
            rt += 2 * num;     // 每組配對固定獲得 2 個物品
            rem -= num * p;    // 扣除預算
        }

        // 5. 剩下的零錢全部拿去單買最便宜的物品
        return rt + rem / mm;
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