---
title: "LeetCode 3629. Minimum Jumps to Reach End via Prime Teleportation"
date: 2026-05-10T11:45:00+08:00
lastmod: 2026-05-10T11:45:00+08:00
difficulty: 2139
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "bfs", "math", "sieve", "graph"]
keywords: ["LC3629", "Medium", "2139", "Minimum Jumps", "Prime Teleportation", "質數傳送"]
description: "LeetCode 第 3629 題：Minimum Jumps to Reach End via Prime Teleportation。難度評分：2139。探討當「傳送」能力僅限於質數下標時，如何透過靜態最小質因數篩法 (SPF) 與 BFS 尋找最短路徑。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3629](https://leetcode.com/problems/minimum-jumps-to-reach-end-via-prime-teleportation/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (2139)</span>  
> **核心主題**：`BFS` $\cdot$ `Math` $\cdot$ `Sieve of Eratosthenes`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個整數陣列 `nums`。你最初位於下標 `0`，目標是抵達下標 `n - 1`。
在每一點 `i`，你可以執行以下操作：
1. **相鄰移動**：移動到 `i + 1` 或 `i - 1`（需在範圍內）。
2. **質數傳送**：**僅當 `nums[i]` 為質數時**，你可以直接跳躍到任何下標 `j`，只要 $nums[j]$ 的質因數中包含 $nums[i]$。

請回傳抵達終點所需的最小跳躍次數。如果無法抵達，回傳 `-1`。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^6$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-10：初次提交 (修正規則：僅質數觸發傳送門)](#2026-05-10-初次提交)

---

## 💡 2026-05-10 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題的關鍵在於傳送條件的判定：傳送不是由「共享因數」發起的，而是由「質數點」發起的。

**核心邏輯：**
1. **傳送觸發點**：只有當 $nums[i] \in \text{Primes}$，該點才具備傳送能力。
2. **傳送目的地**：若 $nums[i] = p$ (質數)，則可跳轉至所有滿足 $nums[j] \equiv 0 \pmod p$ 的下標 $j$。
3. **靜態篩法 (SPF)**：利用 `static` SPF 陣列預處理，確保 $10^6$ 內的質數判定與質因數分解皆為 $O(1)$ 或 $O(\log \log M)$。

### 🛠️ 解題思路 (Approach)
1. **預處理 (Sieve)**：使用最小質因數篩法 (SPF)，若 `spf[x] == x` 則 $x$ 為質數。
2. **建立逆向索引 (Inverted Index)**：
   遍歷陣列，將每個索引 $i$ 加入到其所有質因數 $p$ 對應的清單 `adj[p]` 中。這代表「哪些數字可以被質數 $p$ 傳送」。
3. **廣度優先搜尋 (BFS)**：
   - 走訪點 `idx` 時，首先判斷 `nums[idx]` 是否為質數。
   - 若是質數且該質數傳送門未被使用過：
     - 遍歷 `adj[nums[idx]]`，將所有未訪問的下標加入佇列。
     - 標記該質數傳送門已使用 (`prime_visited[p] = true`) 以防重複計算。
   - 處理常規的 `idx + 1` 與 `idx - 1` 移動。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \log \log M + N \omega(M))$。
  - 篩法執行一次 $\mathcal{O}(M \log \log M)$。
  - 建圖時每個索引處理其質因數 $\mathcal{O}(\omega(M))$。
  - BFS 過程中，每個索引與每個質數傳送門最多被處理一次。
- **空間複雜度**: $\mathcal{O}(M + N \omega(M))$。

### 💻 程式碼實作 (C++)
```cpp
static int spf[1000005];
static bool init = true;

class Solution {
public:
    void sieve() {
        if (init) {
            for (int i = 2; i <= 1000000; ++i) spf[i] = i;
            for (int i = 2; i * i <= 1000000; ++i) {
                if (spf[i] != i) continue;
                for (int j = i * i; j <= 1000000; j += i) {
                    if (spf[j] == j) spf[j] = i;
                }
            }
            init = false;
        }
    }

    int minJumps(vector<int>& nums) {
        sieve();
        int n = nums.size();
        if (n == 1) return 0;

        // 建立 質數 p -> 所有包含此質因數的索引清單
        unordered_map<int, vector<int>> adj; 
        for (int i = 0; i < n; ++i) {
            int cur = nums[i];
            while (cur > 1) {
                int p = spf[cur];
                adj[p].push_back(i);
                while (cur % p == 0) cur /= p;
            }
        }

        queue<pair<int, int>> q;
        vector<bool> visited(n, false);
        unordered_set<int> used_prime_portals;

        q.push({0, 0});
        visited[0] = true;

        while (!q.empty()) {
            auto [idx, step] = q.front();
            q.pop();

            if (idx == n - 1) return step;

            // 條件：僅當 nums[idx] 本身是質數時，觸發傳送
            int p = nums[idx];
            if (p > 1 && spf[p] == p) { // 判定是否為質數
                if (used_prime_portals.find(p) == used_prime_portals.end()) {
                    for (int next_idx : adj[p]) {
                        if (!visited[next_idx]) {
                            visited[next_idx] = true;
                            q.push({next_idx, step + 1});
                        }
                    }
                    used_prime_portals.insert(p); // 標記此質數傳送門已使用
                }
            }

            // 常規移動：相鄰下標
            if (idx + 1 < n && !visited[idx + 1]) {
                visited[idx + 1] = true;
                q.push({idx + 1, step + 1});
            }
            if (idx - 1 >= 0 && !visited[idx - 1]) {
                visited[idx - 1] = true;
                q.push({idx - 1, step + 1});
            }
        }

        return -1;
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