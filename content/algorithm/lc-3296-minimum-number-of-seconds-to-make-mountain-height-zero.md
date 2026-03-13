---
title: "LeetCode 3296. Minimum Number of Seconds to Make Mountain Height Zero"
date: 2026-03-13T22:05:00+08:00
lastmod: 2026-03-13T22:05:00+08:00
difficulty: 1694
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Binary Search", "Math", "Array"]
keywords: ["LC3296", "1694", "Medium", "Binary Search on Answer", "Arithmetic Progression"]
description: "LeetCode 第 3296 題：Minimum Number of Seconds to Make Mountain Height Zero。難度評分：1694。探討如何利用「對答案二分搜」結合等差數列求和公式，以極高的效率求出多工協作的最短時間。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3296](https://leetcode.com/problems/minimum-number-of-seconds-to-make-mountain-height-zero/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1694)</span>    
> **核心主題**：`Binary Search` $\cdot$ `Math` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數 `mountainHeight` 代表山的高度，以及一個整數陣列 `workerTimes`，其中 `workerTimes[i]` 代表第 `i` 個工人降低 1 單位高度所需的基本時間。
當一個工人連續降低山的高度時，他所需的時間會遞增。具體來說，第 `i` 個工人降低第 `x` 單位高度所需的時間為 `workerTimes[i] * x`。
例如，如果 `workerTimes[i] = 3`，那麼他降低第 1 單位高度需 3 秒，第 2 單位需 6 秒，第 3 單位需 9 秒，以此類推。

所有工人可以**同時**平行工作。
請回傳將山的高度降為 0 所需的**最少秒數**。

### 限制條件
- $1 \le mountainHeight \le 10^5$
- $1 \le workerTimes.length \le 10^4$
- $1 \le workerTimes[i] \le 10^6$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-13：初次提交 (對答案二分搜與防浮點數誤差驗證)](#2026-03-13-初次提交)

---

## 💡 2026-03-13 初次提交

### 🎯 直覺 (Intuition)
這題如果正面去推導「每個工人該分配多少工作量」會極度複雜，因為這是個多變數最佳化問題。
但換個角度想：如果我們隨便猜一個時間 $T$，要驗證「在這個時間 $T$ 內，所有工人加起來能不能把山剷平」卻非常簡單！
這種類型（正向構造很難，但驗證答案很容易，且答案具有單調性：給的時間越多，能剷平的高度就越高）的題目，**「對答案進行二分搜尋 (Binary Search on Answer)」** 絕對是唯一且最佳的解法。

### 🛠️ 解題思路 (Approach)
1. **確立二分搜的上下界 (Bounds)**：
   - 下界 `l = 1`。
   - 上界 `r` 必須考慮最壞情況 (Worst Case)：假設只有 1 個工人，且他的基本時間是極限值 $10^6$，要剷平的高度假設為極限值 $10^5$。
     他所需的總時間為等差數列和：$x \times \frac{k(k+1)}{2} = 10^6 \times \frac{10^5 \times 10^5}{2} \approx 5 \times 10^{15}$。
     因此將 `r` 設為 `1e16 + 5` 是一個絕對安全的上界。
2. **驗證函數 (isOK)**：
   - 給定一個測試時間 `cnt`，對於每個工人 `x = workerTimes[i]`，計算他最多能降低多少高度 $k$。
   - 關係式為：$x \times \frac{k(k+1)}{2} \le cnt \implies k(k+1) \le \frac{2 \times cnt}{x}$。
   - 為了避免使用一元二次公式帶來浮點數精度誤差，我們令 $tmp = \frac{2 \times cnt}{x}$，以 `sq = sqrt(tmp)` 為基準，測試 `sq-1`, `sq`, `sq+1` 哪個才是滿足 $k(k+1) \le tmp$ 的最大整數 $k$。
   - 將所有工人能剷平的高度 $k$ 加總，若總和大於等於 `mountainHeight`，代表時間充裕，回傳 `true`。
3. **二分搜收斂**：
   - 若 `isOK(mid)` 為真，代表時間夠用，我們嘗試縮短時間 `r = mid`。
   - 否則，代表時間不夠，必須增加時間 `l = mid + 1`。
   - 最終 `l` 即為所求的最小時間。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log(\text{MaxTime}))$。$N$ 為工人數量。$\text{MaxTime} \approx 10^{16}$，其對數 $\log_2(10^{16}) \approx 54$。每次 `isOK` 掃描需 $\mathcal{O}(N)$，總共執行約 54 次掃描。排序為可選的常數優化 $\mathcal{O}(N \log N)$，整體執行速度極快。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了 `sorted` 陣列（若直接傳原陣列則為 $\mathcal{O}(1)$）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long minNumberOfSeconds(int mountainHeight, vector<int>& workerTimes) {
        vector<int> sorted = workerTimes;
        // 排序可幫助在 isOK 中提早 break 優化常數時間
        sort(sorted.begin(), sorted.end());

        // 最壞情況：1 個時間 10^6 的工人完成 10^5 的高度
        // 總時間約為 10^6 * (10^5 * 10^5 / 2) = 5 * 10^{15}
        long long l = 1, r = 1e16 + 5;
        
        while(l < r) {
            long long mid = l + (r - l) / 2;
            
            if(isOK(mountainHeight, mid, sorted)) {
                r = mid; // 時間足夠，嘗試更短的時間
            } else {
                l = mid + 1; // 時間不夠，增加時間
            }
        }

        return l;
    }
private:
    bool isOK(long long tar, long long cnt, vector<int>& workerTime) {
        for(int i = 0; i < workerTime.size(); ++i) {
            // 如果連降低 1 單位高度的時間都不夠，後面更慢的工人也不用看了
            if(workerTime[i] > cnt) break;
            
            // k(k+1) <= (2 * cnt) / x
            long long tmp = (2 * cnt) / workerTime[i];
            
            // 整數平方根估算，避免一元二次公式的浮點數誤差
            long long sq = int(sqrt(tmp)) - 1;
            long long diff = (sq * (sq + 1) > tmp ? sq - 1 : ((sq + 1) * (sq + 2) > tmp ? sq : sq + 1));
            
            tar -= diff; // 扣除該工人能剷平的高度
        }
        
        return tar <= 0; // 若目標高度 <= 0，代表任務能在時間內完成
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