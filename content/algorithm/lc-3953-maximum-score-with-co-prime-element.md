---
title: "LeetCode 3953. Maximum Score with Co-Prime Element"
date: 2026-06-09T15:20:00+08:00
lastmod: 2026-06-09T15:20:00+08:00
difficulty: 2100
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "number-theory", "inclusion-exclusion", "bit-manipulation"]
keywords: ["LC3953", "Hard", "Maximum Score", "互質", "排容原理", "調和級數", "質因數分解", "線性篩"]
description: "LeetCode 第 3953 題：Maximum Score with Co-Prime Element。探討如何完美結合調和級數建表、SPF 線性篩法與排容原理 (Inclusion-Exclusion Principle)，在 O(M log M) 的時間極限內高效計算陣列中的互質關係與最佳化得分。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3953](https://leetcode.com/problems/maximum-score-with-co-prime-element/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Number Theory` $\cdot$ `Inclusion-Exclusion Principle` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums` 和一個整數 `maxVal`。
對於範圍在 $[1, \max(\maxVal, \max(nums))]$ 內的每一個數字 $X$，其得分計算方式為：
$Score(X) = X - Cost(X)$

其中 $Cost(X)$ 的定義與 $X$ 和陣列 `nums` 中元素「不互質」的數量相關。*(註：依據程式碼還原之題意，實際細節包含自身存在的特判)*
請你計算出所有可能的 $X$ 中，能夠獲得的 **最大得分**。

*(註：由於是未公開/虛擬題號，以上為依據排容原理核心邏輯逆推的題意。核心目標為：快速計算陣列中有多少個數字與 $X$ 不互質 / 互質。)*

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i], maxVal \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-09：初次提交 (SPF 篩法 + 排容原理 + 狀態壓縮)](#2026-06-09-初次提交)

---

## 💡 2026-06-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
如果對每一個 $X$ 都去遍歷 `nums` 計算 GCD 來判斷互質，時間複雜度會高達 $\mathcal{O}(M \cdot N \log(\dots))$，絕對會 `Time Limit Exceeded`。

**核心破局點 1：調和級數預處理 (Harmonic Sieve)**
我們要快速知道「陣列中有多少數字是 $Y$ 的倍數」。
先建立頻率陣列 `cnt`，接著用 $\mathcal{O}(M \log M)$ 的調和級數迴圈 `for(j = i; j <= mx; j += i)`，預先算出陣列 `mul[i]`，代表 `nums` 中有幾個數字是 `i` 的倍數。

**核心破局點 2：質因數分解與線性篩 (SPF)**
要判斷兩個數字不互質，等價於它們「至少有一個共同的質因數」。
我們預先利用埃氏篩法建立 **最小質因數 (Smallest Prime Factor, SPF)** 陣列。這樣對任何數字 $X$ 進行質因數分解，時間複雜度從 $\mathcal{O}(\sqrt{X})$ 降維打擊到 $\mathcal{O}(\log X)$。提取出 $X$ 的所有相異質因數存入 `primes` 陣列。

**核心破局點 3：排容原理 (Inclusion-Exclusion Principle)**
已知 $X$ 的質因數後，如何精準算出 `nums` 中有多少數字與 $X$ 不互質？
如果 $X$ 的質因數有 $p_1, p_2$，與 $X$ 不互質的數字總數 = (是 $p_1$ 倍數的數量) + (是 $p_2$ 倍數的數量) - (同時是 $p_1 \times p_2$ 倍數的數量)。
這就是經典的排容原理。因為一個 $10^5$ 內的數字，最多只有 $6$ 個相異質因數 ( $2 \times 3 \times 5 \times 7 \times 11 \times 13 > 10^5$ )。我們可以用二進位狀態壓縮 (`1 << k`) 遍歷所有組合：
- 奇數個質因數相乘：加上其 `mul` 值。
- 偶數個質因數相乘：減去其 `mul` 值。
如此便能在 $\mathcal{O}(2^6)$ 極短時間內算出非互質的精確數量 `S`。

### 🛠️ 解題思路 (Approach)
1. **確立邊界**：尋找 $mx = \max(\maxVal, \max(nums))$ 作為處理極限。
2. **頻率與倍數統計 (`mul`)**：計算各個因數在陣列中出現的倍數總和。
3. **建立 SPF 篩**：$\mathcal{O}(M \log \log M)$ 建立最小質因數表。
4. **遍歷候選數字 $X$**：從 $1$ 到 $mx$：
   - *(特判)*：若 $X > maxVal$ 且陣列中未出現過，則跳過。
   - **質因數分解**：利用 SPF 將 $X$ 拆解為相異質因數陣列 `primes`。
   - **排容原理**：利用位元運算遍歷 $1$ 到 $2^k - 1$ 的所有組合，算出乘積 `prod`。依據選中質因數個數的奇偶性，對 `mul[prod]` 進行加減，求得不互質數量 `S`。
   - **結算 Cost 與 Score**：依據題目特判規則計算 `cost`，並更新全局最大值 `rt = max(rt, x - cost)`。
5. **回傳結果**。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \log M + M \cdot 2^K)$。
  其中 $M$ 是最大值 (約 $10^5$)，$K$ 是一個數字最多擁有的相異質因數個數 ($K \le 6$)。預處理建表耗時 $\mathcal{O}(M \log M)$，主迴圈每個數字進行排容原理耗時 $\mathcal{O}(2^K)$ (約 64 次操作)。整體極致高效。
- **空間複雜度**: $\mathcal{O}(M)$。配置了 `cnt`, `mul`, `spf` 陣列，皆與最大值 $M$ 成正比。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxScore(vector<int>& nums, int maxVal) {
        int n = nums.size();
        int mx = maxVal;
        vector<int> cnt(100005, 0);
        
        // 1. 確立處理極限並統計頻率
        for(auto num : nums) {
            mx = max(mx, num);
            ++cnt[num];
        }

        // 2. 調和級數預處理：mul[i] 儲存陣列中「i 的倍數」的總數量
        vector<int> mul(mx + 1, 0);
        for(int i = 1; i <= mx; ++i) {
            for(int j = i; j <= mx; j += i) {
                mul[i] += cnt[j];
            }
        }

        // 3. 線性篩法：建構最小質因數 (SPF) 陣列，加速後續質因數分解
        vector<int> spf(mx + 1, 0);
        for(int i = 1; i <= mx; ++i) spf[i] = i;
        for(int i = 2; i * i <= mx; ++i) {
            if(spf[i] == i) {
                for(int j = i * i; j <= mx; j += i) {
                    if(spf[j] == j) spf[j] = i;
                }
            }
        }

        int rt = INT_MIN;
        
        // 4. 枚舉所有可能的 X 計算得分
        for(int x = 1; x <= mx; ++x) {
            if(x > maxVal && cnt[x] == 0) continue;

            vector<int> primes;
            int X = x;
            
            // 利用 SPF 在 O(log X) 內取得所有「相異質因數」
            while(X > 1) {
                int p = spf[X];
                primes.push_back(p);
                while(X % p == 0) X /= p; // 除盡該質因數，保證唯一性
            }

            int k = primes.size();
            int S = 0; // 記錄陣列中與 x 「不互質」的數量
            
            // 5. 狀態壓縮 + 排容原理
            for(int i = 1; i <= (1 << k) - 1; ++i) {
                int c = 0;
                int prod = 1;
                
                for(int j = 0; j < k; ++j) {
                    if((i >> j) & 1) {
                        ++c; // 記錄選中了幾個質因數
                        prod *= primes[j];
                    }
                }

                // 奇加偶減原則
                if(c % 2) {
                    S += mul[prod];
                } else {
                    S -= mul[prod];
                }
            }

            // 6. 計算 Cost (依照題目給定的特判規則)
            int cost = 0;
            if(cnt[x] > 0) {
                if(x == 1) cost = 0;
                else cost = S - 1; // 扣除自身
            } else {
                cost = max(1, S);
            }
            
            // 更新全局最高分
            rt = max(rt, x - cost);
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