---
title: "LeetCode 3655. XOR After Range Multiplication Queries II"
date: 2026-04-09T22:00:00+08:00
lastmod: 2026-04-09T22:00:00+08:00
difficulty: 2453
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "square-root-decomposition", "difference-array"]
keywords: ["LC3655", "2453", "Hard", "XOR After Range", "Square Root Decomposition", "Modular Inverse"]
description: "LeetCode 第 3655 題：XOR After Range Multiplication Queries II。難度評分：2453。探討如何利用根號分治 (Square Root Decomposition) 策略，結合費馬小定理求模逆元與乘法差分陣列，在 O(Q sqrt N) 時間複雜度內解決帶有步長的大範圍區間乘法更新問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3655](https://leetcode.com/problems/xor-after-range-multiplication-queries-ii/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2453)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Square Root Decomposition` $\cdot$ `Difference Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
*(此為 Queries I 的進階版)*
給你一個整數陣列 `nums` 以及一個二維陣列 `queries`，其中每個查詢 `queries[i] = [l, r, step, mul]` 代表一個操作。
對於每一個查詢，你必須將陣列 `nums` 中索引從 `l` 到 `r` 且間隔為 `step` 的所有元素乘以 `mul`。
由於數值可能非常大，每次乘法操作後，請將結果對 $10^9 + 7$ 取同餘 (Modulo)。
在處理完所有的查詢後，請回傳陣列 `nums` 中所有元素的 **XOR 總和**。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le queries.length \le 10^5$
- 本題測資極大，無法單純以 $\mathcal{O}(Q \times N)$ 暴力模擬通過。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-09：初次提交 (根號分治與乘法差分陣列)](#2026-04-09-初次提交)

---

## 💡 2026-04-09 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
面對這題，我們遇到了一個兩難：
1. 若 `step` 很小（例如 1），每次查詢要更新近乎 $N$ 個元素，暴力法會超時。但可以使用 **差分陣列 (Difference Array)** 來將區間操作降至 $\mathcal{O}(1)$。
2. 若 `step` 很大，差分陣列的前綴和推導會變得非常複雜且難以維護多種 `step`，但此時每次查詢實際需要更新的元素數量卻非常少（例如 `step = 50000` 時，最多只更新 2 個元素）。

破局的關鍵在於 **根號分治 (Square Root Decomposition)**：
我們設立一個閾值 $limit = \sqrt{N}$ (約為 316)。
- **小步長 (`step <= limit`)**：將查詢依據 `step` 進行分組。對於同一種 `step` 的所有查詢，我們建立一個專屬的**乘法差分陣列**。透過費馬小定理求得乘數的模逆元 (Modular Inverse)，在區間起點乘上 `v`，在區間終點的下一個位置乘上 $v^{-1}$。最後掃描一次陣列還原出每個位置的實際倍率。
- **大步長 (`step > limit`)**：直接使用迴圈暴力更新陣列。因為 $step > \sqrt{N}$，每次迴圈最多只會執行 $\sqrt{N}$ 次，時間在可控範圍內。

### 🛠️ 解題思路 (Approach)
1. **排序查詢**：為了方便批次處理相同 `step` 的小步長查詢，先將 `queries` 依照 `step` (即 `q[2]`) 進行遞增排序。
2. **模逆元函式 (`inv`)**：利用快速冪演算法與費馬小定理 ($a^{p-2} \equiv a^{-1} \pmod p$) 來實作除法等價的乘法逆元。
3. **處理查詢**：
   - 設定分界線 `limit = sqrt(100000)`。
   - 遍歷排序後的查詢：
     - **當 `curk <= limit`**：
       - 開啟一個內部 `while` 迴圈，將所有 `step == curk` 的查詢集中處理。
       - 建立一個長度為 $M$ 且初始值為 1 的差分陣列 `diff`。
       - 對於每個查詢，在起點 `l` 將 `diff[l]` 乘上 `v`。
       - 計算出該區間的理論結束點的下個位置 `endp = r - ((r - l) % curk) + curk`，並在該點乘上 `v` 的模逆元。
       - 批次處理完該 `step` 的所有查詢後，由左至右進行間隔為 `curk` 的前綴乘積還原，並直接乘回主陣列 `a` 中。
     - **當 `curk > limit`**：
       - 由於步長很大，直接使用 `for` 迴圈暴力模擬該查詢的跳躍乘法。
4. **計算結果**：將更新完畢的陣列 `a` 中的所有元素進行 XOR 運算，回傳結果。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(Q \log Q + Q \sqrt{N} + N \sqrt{N})$。排序查詢需 $\mathcal{O}(Q \log Q)$。對於大步長，每次查詢最多執行 $\sqrt{N}$ 步，耗時 $\mathcal{O}(Q \sqrt{N})$。對於小步長，最多有 $\sqrt{N}$ 種不同的步長，每種步長需要 $\mathcal{O}(N)$ 的時間還原差分陣列，耗時 $\mathcal{O}(N \sqrt{N})$。
- **空間複雜度**: $\mathcal{O}(N)$。複製了原始陣列 `a`，以及在處理小步長時動態配置了長度為 $N$ 的差分陣列 `diff`。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    const long long MOD = 1e9 + 7;
    
    // 依據 step (q[2]) 遞增排序
    static bool cmp(vector<int>& a, vector<int>& b) {
        return a[2] < b[2];
    }

    // 利用費馬小定理求模逆元 (a^(p-2) ≡ a^-1 mod p)
    long long inv(long long v) {
        long long p = MOD - 2;
        long long rt = 1;
        while(p) {
            if(p & 1) rt = (rt * v) % MOD;
            v = (v * v) % MOD;
            p >>= 1;
        }
        return rt;
    }
    
    int xorAfterQueries(vector<int>& nums, vector<vector<int>>& queries) {
        // 集中相同 step 的查詢
        sort(queries.begin(), queries.end(), cmp);
        
        // 根號分治的分界線
        int limit = sqrt(100000);
        int m = nums.size();
        int n = queries.size();
        vector<long long> a(nums.begin(), nums.end());
        
        for(int i = 0; i < n;) {
            int curk = queries[i][2];
            
            // 情況 1：小步長，使用乘法差分陣列
            if(curk <= limit) {
                vector<long long> diff(m, 1);
                
                // 批次處理所有步長為 curk 的查詢
                while(i < n && queries[i][2] == curk) {
                    int l = queries[i][0], r = queries[i][1];
                    long long v = queries[i][3];
                    
                    // 區間起點乘上 v
                    diff[l] = (diff[l] * v) % MOD;
                    
                    // 精準計算區間終點的下一個位置
                    int endp = r - ((r - l) % curk) + curk;
                    if(endp < m) {
                        // 區間結束後乘上 v 的反元素 (除以 v)
                        diff[endp] = (diff[endp] * inv(v)) % MOD;
                    }
                    ++i;
                }
                
                // 前綴乘積還原，並直接應用至主陣列
                for(int j = 0; j < m; ++j) {
                    diff[j] = ((j - curk >= 0 ? diff[j - curk] : 1) * diff[j]) % MOD;
                    a[j] = (a[j] * diff[j]) % MOD;
                }
            } 
            // 情況 2：大步長，直接暴力模擬
            else {
                for(int l = queries[i][0]; l <= queries[i][1]; l += queries[i][2]) {
                    a[l] = (a[l] * queries[i][3]) % MOD;
                }
                ++i;
            }
        }

        // 計算最後的 XOR 總和
        long long rt = 0;
        for(auto x : a) {
            rt = (rt ^ x);
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