---
title: "LeetCode 3943. Number of Pairs After Increment"
date: 2026-05-27T01:30:00+08:00
lastmod: 2026-05-27T01:30:00+08:00
difficulty: 2000
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "square-root-decomposition", "hash-table", "range-query"]
keywords: ["LC3943", "Hard", "Square Root Decomposition", "分塊法", "區間修改", "懶惰標記"]
description: "LeetCode 第 3943 題：Number of Pairs After Increment。探討如何利用分塊法 (Square Root Decomposition) 結合 Hash Map 與懶惰標記 (Lazy Tag)，高效處理區間加法修改與兩陣列數值配對查詢的動態問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3943](https://leetcode.com/problems/number-of-pairs-after-increment/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (N/A)</span>  
> **核心主題**：`Square Root Decomposition` $\cdot$ `Hash Table` $\cdot$ `Range Query`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定兩個整數陣列 `nums1` 和 `nums2`，以及一個二維查詢陣列 `queries`。
查詢有兩種格式：
1. **區間加法 (`q[0] == 1`)**：給定起點 `st`、終點 `end` 與增加值 `val`，將 `nums2` 中索引從 `st` 到 `end` 的所有元素加上 `val`。
2. **配對查詢 (`q[0] == 2`)**：給定一個目標值 `target`，計算有多少個數對 $(x, y)$ 滿足 $x \in nums1$ 且 $y \in nums2$，使得 $x + y = target$。

請回傳一個陣列，依序包含所有「配對查詢」的結果。

### 限制條件
- $1 \le nums1.length, nums2.length \le 10^5$
- $1 \le queries.length \le 10^5$
- 數值範圍可能導致相加後超過 32-bit 整數，需使用 `long long`。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-27：初次提交 (分塊法與懶惰標記)](#2026-05-27-初次提交)

---

## 💡 2026-05-27 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的難點在於：查詢時需要快速知道 `nums2` 中某個數值的「出現頻率」，而 `nums2` 同時又會頻繁發生「區間修改」。
如果用線段樹，區間加法很容易，但維護區間內各種數值的頻率極其困難；如果直接維護全域 Hash Map，區間加法則會退化為 $\mathcal{O}(N)$。

**核心破局點：分塊法 (Square Root Decomposition)**
我們將 `nums2` 切分成大小約為 $\sqrt{N}$ 的連續區塊（Block）。
1. **區塊封裝**：每個區塊各自維護一個 `unordered_map` 來記錄該區塊內的數值頻率。
2. **懶惰標記 (Lazy Tag)**：為每個區塊配備一個 `add` 變數。當發生「完整覆蓋該區塊」的修改時，不修改 Map 內部元素，而是直接 `add[idx] += val`，實現 $\mathcal{O}(1)$ 更新。
3. **邊緣暴力處理**：當修改區間只「部分覆蓋」某個區塊的邊緣時，我們直接走訪這些邊緣元素，從 Map 中扣除舊值，加上 `val` 後，再將新值登錄回 Map 中，這部分最多耗時 $\mathcal{O}(\sqrt{N})$。

### 🛠️ 解題思路 (Approach)
1. **初始化分塊**：
   - 區塊大小設定為 $k = \sqrt{n}$，總區塊數 $N = \lceil n/k \rceil$。
   - 配置陣列 `nums22` 作為實際數值的備份，`arr` 作為每個區塊的 Hash Map，`add` 作為懶惰標記。
   - 掃描初始的 `nums2`，將元素頻率分類寫入對應的 `arr[i]` 中。
2. **處理區間修改 (`q[0] == 1`)**：
   - 遍歷範圍 `[st, end]`。
   - 若當前索引 `i` 剛好是一個區塊的起點 (`i % k == 0`) 且該區塊被完整覆蓋 (`i + k - 1 <= end`)，則直接更新懶惰標記 `add[i/k] += val`，並將指標 `i` 跳過整個區塊。
   - 否則（屬於不完整覆蓋的碎邊），在 `arr[idx]` 中扣除舊數值 `nums22[i]`（若頻率歸零則刪除鍵值以省空間），更新 `nums22[i] += val`，並在 Map 中登錄新數值。
3. **處理配對查詢 (`q[0] != 1`)**：
   - 目標是尋找 $y = target - x$。
   - 外層遍歷 `nums1` 中的每個元素 $x$。
   - 內層遍歷所有的區塊 $i$（共 $\sqrt{N}$ 個）。對於區塊 $i$，由於它自帶懶惰標記 `add[i]`，我們實際要在 `arr[i]` 尋找的目標數值應為 `tar = q[1] - x - add[i]`。
   - 累加查找到的頻率。

### 📊 複雜度分析
- **時間複雜度**:
  - **初始化**: $\mathcal{O}(n)$。
  - **單次區間修改**: 完整區塊更新 $\mathcal{O}(\frac{n}{k})$，邊緣破碎更新 $\mathcal{O}(k)$。當 $k \approx \sqrt{n}$ 時，單次操作最壞為 $\mathcal{O}(\sqrt{n})$。
  - **單次查詢**: 需遍歷所有 $m$ 個 `nums1` 元素並查詢 $\sqrt{n}$ 個區塊，單次查詢為 $\mathcal{O}(m \cdot \sqrt{n})$。
  - 若調整 `k` 的大小或將 `nums1` 預先壓縮頻率，效能可進一步提升。
- **空間複雜度**: $\mathcal{O}(n)$。區塊陣列、Hash Map 與懶惰標記的總空間與 `nums2` 大小成正比。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> numberOfPairs(vector<int>& nums1, vector<int>& nums2, vector<vector<int>>& queries) {
        int n = nums2.size();
        // 決定分塊大小為 sqrt(n)
        int k = sqrt(n);
        int N = (n + k - 1) / k; // 總區塊數量
        
        vector<long long> nums22(nums2.begin(), nums2.end());
        vector<unordered_map<long long, int>> arr(N); // 每個區塊專屬的頻率映射表
        vector<long long> add(N, 0); // 每個區塊專屬的懶惰標記 (Lazy Tag)
        
        // 初始化分塊資料
        for(int i = 0; i < N; ++i) {
            for(int j = i * k; j < min((i + 1) * k, n); ++j) {
                ++arr[i][nums2[j]];
            }
        }
        
        vector<int> rt; 
        rt.reserve(queries.size());
        
        for(auto& q : queries) {
            if(q[0] == 1) { // 區間加法操作
                int st = q[1], end = q[2];
                long long val = q[3];
                for(int i = st; i <= end; ++i) {
                    int idx = i / k;
                    
                    // 情況 A：當前元素是區塊起點，且整個區塊都在修改範圍內
                    if((i % k) == 0 && i + k - 1 <= end) {
                        add[idx] += val; // 直接貼上懶惰標記
                        i += k - 1;      // 一次性跳過整個區塊，達到加速效果
                    }
                    // 情況 B：邊緣破碎區間，暴力單點修改
                    else {
                        --arr[idx][nums22[i]];
                        if(arr[idx][nums22[i]] == 0) {
                            arr[idx].erase(nums22[i]); // 節省 Hash Map 空間開銷
                        }
                        nums22[i] += val;
                        ++arr[idx][nums22[i]];
                    }
                }
            }
            else { // 兩數配對查詢操作
                int c = 0;
                for(auto& num : nums1) {
                    for(int i = 0; i < N; ++i) {
                        // 尋找目標值時，必須抵銷該區塊的懶惰標記影響
                        long long tar = q[1] - num - add[i];
                        if(arr[i].find(tar) != arr[i].end()) {
                            c += arr[i][tar];
                        }
                    }
                }
                rt.push_back(c);
            }
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