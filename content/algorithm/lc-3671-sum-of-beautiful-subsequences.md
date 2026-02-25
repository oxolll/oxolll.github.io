---
title: "LeetCode 3671. Sum of Beautiful Subsequences"
date: 2026-02-26T04:28:00+08:00
lastmod: 2026-02-26T04:28:00+08:00
difficulty: 2647
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Binary Indexed Tree (Fenwick Tree)", "Dynamic Programming", "Math", "Inclusion-Exclusion", "Number Theory"]
keywords: ["LC3671", "2647", "Hard", "GCD", "Inclusion-Exclusion", "BIT Flattening"]
description: "LeetCode 第 3671 題：Sum of Beautiful Subsequences。難度評分：2647。探討如何利用 Binary Indexed Tree 優化嚴格遞增子序列的 DP，並透過容斥原理計算精確的最大公因數 (GCD) 貢獻。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3671](https://leetcode.com/problems/sum-of-beautiful-subsequences/)
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard(2647)</span>   
> **核心主題**：`Dynamic Programming` $\cdot$ `Number Theory (GCD)` $\cdot$ `Binary Indexed Tree` $\cdot$ `Inclusion-Exclusion`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
若一個子序列是**嚴格遞增**的，我們稱其為「美麗子序列 (Beautiful Subsequence)」。
一個美麗子序列的「美麗值 (Beauty)」定義為：該子序列長度乘以子序列中所有元素的最大公因數 (GCD)。
請回傳 `nums` 中所有美麗子序列的美麗值總和，並對 $10^9 + 7$ 取餘數。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-02-26：初次提交 (一維 BIT 攤平最佳化與數論容斥)](#2026-02-26-初次提交)

---

## 💡 2026-02-26 初次提交

### 🎯 直覺 (Intuition)
直接計算「最大公因數 (GCD) 恰好為 $g$」的子序列數量極度困難。在數論問題中，處理 GCD 的標準轉換是：**先計算「GCD 為 $g$ 的倍數」的數量，再透過容斥原理 (Inclusion-Exclusion Principle) 扣除多餘的部分**。
若要求子序列的 GCD 是 $g$ 的倍數，代表該子序列中的每一個元素都必須是 $g$ 的倍數。因此，問題轉換為：對於每一個可能的公因數 $g$，我們需要計算由 $g$ 的倍數所構成的「嚴格遞增子序列」數量。這可以透過維護多棵 Binary Indexed Tree (BIT) 來達成。

### 🛠️ 解題思路 (Approach)
1. **定義狀態**：
   - 令 $F(g)$ 為：所有元素皆為 $g$ 的倍數的嚴格遞增子序列數量。
   - 令 $N(g)$ 為：GCD **恰好**為 $g$ 的嚴格遞增子序列數量。
   - 根據定義，可以推導出 $N(g) = F(g) - \sum_{k=2} N(k \cdot g)$。
2. **預處理因數與 BIT 記憶體攤平**：
   - 找出陣列的最大值 $mx$。對 $1$ 到 $mx$ 的所有數字預先計算其因數。
   - 需要為每個 $g$ 建立一棵 BIT 來維護 DP 狀態。為了避免二維 `vector` 造成的記憶體破碎與 Cache Miss，計算每個 $g$ 所需的 BIT 大小（即 $\lfloor mx/g \rfloor + 1$），透過 `offsets` 陣列將所有 BIT 攤平 (Flatten) 進單一的一維陣列中。
3. **子序列 DP 計算 $F(g)$**：
   - 遍歷 `nums` 中的每個數字 `num`。
   - 對於 `num` 的每一個因數 `div`，查詢對應 BIT 中索引小於 `num/div` 的總和（代表接在更小的倍數後面）。
   - 更新 $F(div)$ 的值，並將新的 DP 狀態更新回 BIT 中。
4. **容斥計算 $N(g)$ 與最終解答**：
   - 從 $mx$ 倒序枚舉回 $1$。$N(g)$ 初始設為 $F(g)$，接著扣除所有 $g$ 的倍數 $mul$ 的確切數量 $N(mul)$。
   - 總和即為 $\sum_{g=1}^{mx} g \times N(g)$。所有運算過程皆須對 $10^9 + 7$ 取同餘。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \log M + N \cdot d(M) \log M)$。
  - $M$ 為數值最大值 `mx`，$N$ 為陣列長度。
  - 預處理因數與攤平 BIT 耗時 $\mathcal{O}(M \log M)$。
  - DP 轉移中，每個數字平均有 $d(M)$ 個因數，每次 BIT 操作為 $\mathcal{O}(\log M)$。
  - 容斥計算耗時 $\mathcal{O}(M \log M)$。
- **空間複雜度**: $\mathcal{O}(M \log M)$。
  - 所有 BIT 攤平後的總大小為 $\sum_{g=1}^{M} \frac{M}{g} \approx M \ln M$。`divs` 陣列的總空間也與之同階。

### 💻 程式碼實作 (C++)

#### 版本一：一維 BIT 攤平最佳化 (推薦)
利用 `offsets` 將二維結構壓平為一維，大幅提升 Cache 命中率並壓低常數時間。
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int totalBeauty(vector<int>& nums) {
        int mx = *max_element(nums.begin(), nums.end());

        // 1. 預處理所有數字的因數
        vector<vector<int>> divs(mx + 1);
        for(int i = 1; i <= mx; ++i)
            divs[i].reserve(128); // 預先分配記憶體減少 Reallocation
            
        for(int i = 1; i <= mx; ++i) {
            for(int j = i; j <= mx; j += i) {
                divs[j].push_back(i);
            }
        }

        // 2. 攤平 BIT 結構 (Flatten 2D to 1D) 以優化 Cache Locality
        vector<int> offsets(mx + 1, 0);
        int pos = 0;
        for(int g = 1; g <= mx; ++g) {
            offsets[g] = pos;
            pos += (mx / g + 1);
        }

        vector<long long> BIT(pos, 0);

        // BIT 區間查詢
        auto query = [&](int g, int idx) {
            long long sum = 0;
            while(idx > 0) {
                sum = (sum + BIT[offsets[g] + idx]) % MOD;
                idx -= (idx & -idx);
            }
            return sum;
        };

        // BIT 單點更新
        auto update = [&](int g, int idx, int val) {
            while(idx < mx / g + 1) {
                BIT[offsets[g] + idx] = (BIT[offsets[g] + idx] + val) % MOD;
                idx += (idx & -idx);
            }
        };

        // 3. 子序列 DP 計算 F(g)
        vector<long long> F(mx + 1, 0);
        for(auto num : nums) {
            for(auto div : divs[num]) {
                // 查詢比 num 小的嚴格遞增子序列數量
                long long prev = query(div, num / div - 1);
                long long cur = prev + 1; // 加上自身作為起點的情況
                
                F[div] = (F[div] + cur) % MOD;
                update(div, num / div, cur);
            }
        }

        // 4. 容斥計算 N(g) (Exact GCD)
        vector<long long> N(mx + 1, 0);
        for(int g = mx; g >= 1; --g) {
            N[g] = F[g];
            for(int mul = 2 * g; mul <= mx; mul += g) {
                N[g] = (N[g] - N[mul] + MOD) % MOD;
            }
        }

        // 5. 計算總和
        long long rt = 0;
        for(int g = 1; g <= mx; ++g) {
            rt = (rt + g * N[g]) % MOD;
        }

        return rt;
    }
};
```

<details>
<summary><b>點擊展開：版本二 (二維 BIT 初始實作)</b></summary>

使用標準的 `vector<vector<long long>>` 建構多棵 BIT，邏輯最直觀，但在極限測資下可能有 Cache Miss 的效能隱患。
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    int totalBeauty(vector<int>& nums) {
        int mx = *max_element(nums.begin(), nums.end());

        vector<vector<int>> divs(mx + 1);
        for(int i = 1; i <= mx; ++i) {
            for(int j = i; j <= mx; j += i) {
                divs[j].push_back(i);
            }
        }

        vector<vector<long long>> BITs(mx + 1);
        for(int g = 1; g <= mx; ++g) {
            BITs[g].assign(mx / g + 2, 0);
        }

        auto update = [&](int g, int idx, int val) {
            for(; idx < BITs[g].size(); idx += (idx & -idx)) {
                BITs[g][idx] += val;
                BITs[g][idx] %= MOD;
            }
        };

        auto query = [&](int g, int idx) {
            long long sum = 0;
            for(; idx > 0; idx -= (idx & -idx)) {
                sum += BITs[g][idx];
                sum %= MOD;
            }
            return sum;
        };

        vector<long long> F(mx + 1, 0);
        for(int num : nums) {
            for(int g : divs[num]) {
                int idx = num / g;
                long long prev_s = query(g, idx - 1);
                long long cur_s = (prev_s + 1) % MOD;

                update(g, idx, cur_s);
                F[g] += cur_s;
                F[g] %= MOD;
            }
        }

        vector<long long> exact(mx + 1, 0);
        long long rt = 0;
        for(int g = mx; g > 0; --g) {
            exact[g] = F[g];
            for(int mul = 2 * g; mul <= mx; mul += g) {
                exact[g] = (exact[g] - exact[mul] + MOD) % MOD;
            }

            rt += g * exact[g];
            rt %= MOD;
        }

        return rt;
    }
};
```
</details>

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