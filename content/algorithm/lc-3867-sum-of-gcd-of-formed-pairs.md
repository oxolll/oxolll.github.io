---
title: "LeetCode 3867. Sum of GCD of Formed Pairs"
date: 2026-03-15T14:58:00+08:00
lastmod: 2026-03-15T14:58:00+08:00
difficulty: 1406
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Math", "Number Theory", "Greedy", "Sorting"]
keywords: ["LC3867", "1406", "Medium", "GCD", "Euclidean Algorithm", "Greedy Pairing"]
description: "LeetCode 第 3867 題：Sum of GCD of Formed Pairs。難度評分：1406。探討如何透過前綴最大值計算 GCD 陣列，並運用貪心策略配對，同時展示極致精簡的輾轉相除法實作。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3867](https://leetcode.com/problems/sum-of-gcd-of-formed-pairs/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1406)</span>    
> **核心主題**：`Math` $\cdot$ `Number Theory` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。
首先，你需要建構一個新的陣列（假設為 `prefixGcd`），其中第 $i$ 個元素的值為：`nums[i]` 與 `nums[0...i]` 中的最大值兩者的**最大公因數 (GCD)**。
接著，將這個新陣列進行排序。
最後，透過貪心策略進行配對：每次從陣列中取出最小與最大的元素形成一對，並計算這一對數字的 GCD。請回傳所有配對的 GCD 總和。

### 限制條件
- $nums.length$ 保證為偶數。
- $2 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-15：初次提交 (前綴最大值 GCD、排序貪心配對與極簡輾轉相除法)](#2026-03-15-初次提交)

---

## 💡 2026-03-15 初次提交

### 🎯 直覺 (Intuition)
這題的流程可以明確拆解為三個階段，跟著題目模擬即可：
1. **動態維護最大值**：在遍歷過程中，隨時記住目前為止遇到的最大值 `mx`，並計算當前元素與 `mx` 的 GCD。
2. **極端值貪心配對**：將產生的 GCD 陣列排序後，採用「頭尾配對」（最小配最大、次小配次大）的方式。
3. **底層 GCD 實作**：雖然 C++17 提供了 `std::gcd`，但在無法引入標頭檔或需要手刻底層邏輯時，熟練掌握輾轉相除法 (Euclidean Algorithm) 的精簡寫法是非常關鍵的競技程式設計基本功。

### 🛠️ 解題思路 (Approach)
1. **建構 Prefix GCD 陣列**：
   - 宣告 `prefixGcd` 陣列。初始化 `mx = nums[0]`。
   - 遍歷 `nums`，每次先更新 `mx = max(mx, nums[i])`。
   - 將 `gcd(mx, nums[i])` 的結果存入 `prefixGcd[i]`。
2. **排序與配對計算**：
   - 對 `prefixGcd` 進行遞增排序。
   - 使用雙指標概念（迴圈跑 $N/2$ 次），將索引 $i$ 與 $N-1-i$ 的元素配對。
   - 將每一對的 `gcd(prefixGcd[n-1-i], prefixGcd[i])` 累加至結果 `rt`。
3. **超精簡 GCD 實作**：
   - 利用短路評估 (Short-circuit evaluation) 的寫法：`while((a %= b) && (b %= a));`。
   - 如果 `a %= b` 為 0，迴圈中止，此時 `a` 為 0，回傳 `a + b` 即為答案 `b`。
   - 如果不為 0，繼續執行 `b %= a`，若為 0 則迴圈中止，回傳 `a + b` 即為答案 `a`。
   - 完美避開了傳統寫法中需要額外宣告 `temp` 變數來交換值的麻煩。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。
  - 第一階段建構陣列時，計算 GCD 的時間為 $\mathcal{O}(N \log(\text{Max Val}))$。
  - 第二階段排序需 $\mathcal{O}(N \log N)$。
  - 第三階段配對計算 GCD 需 $\mathcal{O}(N \log(\text{Max Val}))$。
  - 整體由排序的 $\mathcal{O}(N \log N)$ 主導。
- **空間複雜度**: $\mathcal{O}(N)$。用於儲存長度為 $N$ 的 `prefixGcd` 陣列。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    long long gcdSum(vector<int>& nums) {
        int n = nums.size();
        vector<int> prefixGcd(n, 0);
        int mx = nums[0];
        
        // 1. 計算每個元素與當前最大值的 GCD
        for(int i = 0; i < n; ++i) {
            mx = max(mx, nums[i]);
            // 傳入時確保大數在前、小數在後（雖自訂 gcd 也能處理反向，但這樣更嚴謹）
            prefixGcd[i] = gcd(max(nums[i], mx), min(nums[i], mx));
        }

        // 2. 將產生的 GCD 陣列排序
        sort(prefixGcd.begin(), prefixGcd.end());
        long long rt = 0;
    
        // 3. 貪心配對：最小與最大配對，計算並累加其 GCD
        for(int i = 0; i < n / 2; ++i) {
            rt += gcd(prefixGcd[n - 1 - i], prefixGcd[i]);
        }
            
        return rt;
    }
    
private:
    // 超精簡輾轉相除法 (Euclidean Algorithm) 模板
    int gcd(int a, int b) {
        while((a %= b) && (b %= a));
        return a + b;
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