---
title: "LeetCode 3901. Good Subsequence Queries"
date: 2026-04-13T20:45:00+08:00
lastmod: 2026-04-13T20:45:00+08:00
difficulty: 2544
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "math", "square root decomposition", "segment tree", "number theory"]
keywords: ["LC3901", "Hard", "Good Subsequence Queries", "Square Root Decomposition", "Segment Tree", "GCD"]
description: "LeetCode 第 3901 題：Good Subsequence Queries。難度評分：Hard (2544)。探討如何解決單點更新與全域 GCD 查詢問題。完整收錄兩種高階資料結構解法：利用分塊法 (Square Root Decomposition) 達成 O(Q sqrt N) 的常數優化，以及利用線段樹 (Segment Tree) 達成 O(Q log N) 的理論極限，並結合嚴謹的數論剪枝完成最優解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3901](https://leetcode.com/problems/good-subsequence-queries/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2544)</span>  
> **核心主題**：`Array` $\cdot$ `Math` $\cdot$ `Square Root Decomposition` $\cdot$ `Segment Tree` $\cdot$ `Number Theory`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $N$ 的整數陣列 `nums`，一個目標整數 `p`，以及一系列的查詢 `queries`。
每個查詢 `queries[i] = [idx, val]` 代表將 `nums[idx]` 的值更新為 `val`。
在每次更新後，請判斷陣列中是否存在一個**真子序列 (Proper Subsequence)**，其所有元素的**最大公因數 (GCD) 恰好等於 $p$**。
如果存在，請將有效計數器加一。最後回傳滿足條件的總次數。

### 限制條件
- $1 \le nums.length, queries.length \le 50000$
- $1 \le nums[i], val, p \le 50000$
</details>

---

## 📝 歷次打卡與解法演進
這道題目是處理「單點更新、全域查詢」的經典試金石。以下完整收錄兩種高階解法的演進過程：
- **[解法一：分塊法 (Square Root Decomposition)](#解法一-分塊法-square-root-decomposition)**：利用程式技巧降維打擊，常數極小。
- **[解法二：線段樹 (Segment Tree)](#解法二-線段樹-segment-tree-終極優化)**：理論複雜度最優的終極武器。

---

## 💡 解法一：分塊法 (Square Root Decomposition)

### 🎯 演算法分析
數論核心性質：**要使子序列的 GCD 為 $p$，該子序列的所有元素必須都是 $p$ 的倍數。**
我們將長度為 $N$ 的陣列切分為大小為 $\sqrt{N}$ 的區塊。每個區塊維護該區塊內所有「$p$ 的倍數」的局部 GCD。
- **單點更新**：只需重新計算該元素所在區塊的 GCD，耗時 $\mathcal{O}(\sqrt{N})$。
- **全域查詢**：只需合併所有區塊的 GCD，耗時 $\mathcal{O}(\sqrt{N})$。

此外，針對題目「真子序列」的限制，我們加入極端邊界處理：若陣列所有元素皆為 $p$ 的倍數，必須確保拔掉一個元素後 GCD 仍為 $p$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int countGoodSubseq(vector<int>& nums, int p, vector<vector<int>>& queries) {
        int n = nums.size();
        int B = sqrt(n) + 1; // 分塊大小
        int tc = 0;          // 統計 p 的倍數總數量
        vector<int> block_gcd(B, 0);
        
        // 1. 初始化分塊狀態
        for(int i = 0; i < n; i += B) {
            int local_gcd = 0;
            for(int j = i; j < min(n, i + B); ++j) {
                if(nums[j] % p == 0) {
                    if(local_gcd != p)
                        local_gcd = gcd(local_gcd, nums[j]);
                    ++tc;
                }
            }
            block_gcd[i / B] = local_gcd;
        }

        int rt = 0;
        // 2. 處理每次查詢
        for(const auto& q : queries) {
            int idx = q[0], val = q[1];
            int b = idx / B;
            
            // 單點更新
            if(nums[idx] != val) {
                if(nums[idx] % p == 0) --tc;
                if(val % p == 0) ++tc;
                nums[idx] = val;
                
                int l = b * B, r = min(n, (b + 1) * B);
                int local_gcd = 0;
                for(int i = l; i < r; ++i) {
                    if(nums[i] % p == 0)
                        local_gcd = gcd(local_gcd, nums[i]);
                    if(local_gcd == p) break; 
                }
                block_gcd[b] = local_gcd;
            }

            if(tc == 0) continue;

            // 3. 計算全域候選元素的 GCD
            int total_gcd = 0;
            for(int i = 0; i < B; ++i) {
                if(block_gcd[i] != 0)
                    total_gcd = gcd(total_gcd, block_gcd[i]);
                if(total_gcd == p) break;
            }

            if(total_gcd > p) continue;

            // 4. 針對真子序列長度限制的邊界檢查
            if(tc < n || n >= 7) {
                ++rt;
                continue;
            }
            
            // 針對極小陣列的特判：暴力驗證拔掉一個元素後是否成立
            for(int i = 0; i < n; ++i) {
                int curr_gcd = 0;
                for(int j = 0; j < n; ++j) {
                    if(i == j) continue;
                    curr_gcd = gcd(curr_gcd, nums[j]);
                }
                if(curr_gcd == p) {
                    ++rt;
                    break;
                }
            }
        }
        return rt;
    }
};
```

---

## 💡 解法二：線段樹 (Segment Tree) 終極優化

### 🎯 演算法分析
為突破分塊法 $\mathcal{O}(\sqrt{N})$ 的更新瓶頸，我們改用 **線段樹 (Segment Tree)**。
- 透過二元樹結構，將單點更新時間極致壓縮至 $\mathcal{O}(\log N)$。
- 查詢時完全無須呼叫 `query` 函式，整個陣列的有效 GCD 永遠存放在根節點 `tree[1]`，查詢時間為 $\mathcal{O}(1)$！
- **數學剪枝極限**：由於測資 $val \le 50000$，若陣列長度 $n \ge 7$ 且全為候選者，要讓拔掉任意元素後 GCD 變大，至少需要 7 個相異質因數（其乘積為 $72930 > 50000$）。因此在數學上，當 $n \ge 7$ 時絕對有解，這是一個極度嚴謹的 $\mathcal{O}(1)$ 剪枝判斷！

### 💻 程式碼實作 (C++)
```cpp
// 依據測資 N <= 50000，線段樹大小開至 4N 以確保安全
const int MAXN = 50005;
int tree[MAXN << 2];

class Solution {
public:
    int P;
    
    // O(N) 批量建樹，取代 O(N log N) 的逐一單點更新
    void build(int node, int L, int R, const vector<int>& nums) {
        if(L == R) {
            // 數論過濾：非 p 的倍數視為 0
            tree[node] = (nums[L - 1] % P == 0 ? nums[L - 1] : 0);
            return;
        }
        int mid = (L + R) >> 1;
        build(node << 1, L, mid, nums);
        build(node << 1 | 1, mid + 1, R, nums);
        // Push Up：父節點為左右子節點的 GCD
        tree[node] = gcd(tree[node << 1], tree[node << 1 | 1]);
    }
    
    // O(log N) 單點更新
    void update(int node, int L, int R, int idx, int val) {
        if(L == R) {
            tree[node] = (val % P == 0 ? val : 0);
            return;
        }
        int mid = (L + R) >> 1;
        if(idx <= mid) {
            update(node << 1, L, mid, idx, val);
        } else {
            update(node << 1 | 1, mid + 1, R, idx, val);
        }
        tree[node] = gcd(tree[node << 1], tree[node << 1 | 1]);
    }
    
    int countGoodSubseq(vector<int>& nums, int p, vector<vector<int>>& queries) {
        int n = nums.size();
        P = p;
        
        int curr_cnt = 0;
        for(int x : nums) {
            if(x % p == 0) ++curr_cnt;
        }
        
        // 初始化線段樹
        build(1, 1, n, nums);

        int rt = 0;
        for(const auto& q : queries) {
            int idx = q[0], val = q[1];
            
            // 單點更新與計數器維護
            if(nums[idx] != val) {
                if(nums[idx] % p == 0) --curr_cnt;
                if(val % p == 0) ++curr_cnt;
                
                nums[idx] = val;
                update(1, 1, n, idx + 1, val);
            }

            // O(1) 取得全域有效 GCD
            int curr_gcd = tree[1];
            
            if(curr_cnt == 0 || curr_gcd > p) continue;

            // 利用數論性質進行 O(1) 剪枝：n >= 7 且 val <= 50000 時必有解
            if(curr_cnt < n || n >= 7) {
                ++rt;
                continue;
            }

            // 針對 n <= 6 的全滿陣列，暴力驗證
            bool possible = false;
            for(int i = 0; i < n; ++i) {
                int tmp_gcd = 0;
                for(int j = 0; j < n; ++j) {
                    if(i == j) continue;
                    tmp_gcd = gcd(tmp_gcd, nums[j]);
                }
                if(tmp_gcd == p) {
                    possible = true;
                    break;
                }
            }
            if(possible) ++rt;
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