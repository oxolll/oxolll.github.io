---
title: "🧩 LeetCode 1626. Best Team With No Conflicts"
date: 2026-05-04T17:30:00+08:00
lastmod: 2026-05-04T17:40:00+08:00
difficulty: 2027
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "dynamic-programming", "segment-tree", "sorting"]
keywords: ["LC1626", "Medium", "Best Team With No Conflicts", "LIS", "Segment Tree", "線段樹"]
description: "LeetCode 第 1626 題：Best Team With No Conflicts。難度評分：2027。探討如何將「無矛盾球隊」問題轉換為「最大權重非遞減子序列 (MWIS)」，並透過標準 O(N^2) DP 建立基礎，進一步利用離散化與值域線段樹極致優化至 O(N log N)。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1626](https://leetcode.com/problems/best-team-with-no-conflicts/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (2027)</span>  
> **核心主題**：`Array` $\cdot$ `Dynamic Programming` $\cdot$ `Segment Tree` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
你是一家籃球俱樂部的經理，即將組建一支球隊。球隊的總分數等於所有球員的分數總和。
然而，球隊中不能存在「矛盾」。矛盾的定義是：**一個年齡較小的球員，其分數嚴格大於年齡較大的球員**。
如果兩名球員年齡相同，則他們之間永遠不會有矛盾。
給你兩個陣列 `scores` 和 `ages`，請回傳你能組建的無矛盾球隊的**最大總分數**。

### 範例
- **Input**: `scores = [1,3,5,10,15], ages = [1,2,3,4,5]`
- **Output**: `34`
- **Explanation**: 選擇所有球員，因為年齡較大的人分數也較高，沒有矛盾。總分 = 1+3+5+10+15 = 34。

- **Input**: `scores = [4,5,6,5], ages = [2,1,2,1]`
- **Output**: `16`
- **Explanation**: 最好選擇索引為 0, 1, 3 的球員 (分數分別為 4, 5, 5)。年齡分別為 2, 1, 1。沒有矛盾。總分 = 14？不對，最好的選擇是 [5,5,6] 也就是 index 1,3,2，分數為 16。

### 限制條件
- $1 \le scores.length, ages.length \le 1000$
- $scores.length == ages.length$
- $1 \le scores[i] \le 10^6$
- $1 \le ages[i] \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-04：初次提交 (新增 O(N^2) 標準 DP 與 O(N log N) 線段樹雙解法)](#2026-05-04-初次提交-雙解法)

---

## 💡 2026-05-04 初次提交 (雙解法)

### 🎯 演算法分析 (Algorithm Analysis)
這題的本質是求解 **最大權重非遞減子序列 (Maximum Weight Non-Decreasing Subsequence)**。
無論是哪種解法，第一步都是**解除「年齡」這個維度的限制**：將球員依「年齡升序」排序；若年齡相同，則依「分數升序」排序。
排序後，我們由左向右掃描時，後出現的球員年齡一定大於等於前面的球員。因此，我們只需要確保**後加入的球員分數 $\ge$ 前面所選球員的最大分數**，就不會產生矛盾。

---

### 🛠️ 方法一：標準動態規劃 (LIS 變形) - $\mathcal{O}(N^2)$
這是在資料量 $N = 1000$ 限制下的標準安全解法，概念完全等同於 LIS (最長遞增子序列)。
- **狀態定義**：`dp[i]` 代表選擇第 $i$ 個球員作為球隊結尾（隊內最高分）時，所能達成的最大總分。
- **狀態轉移**：
  $$dp[i] = sc_i + \max(0, \max_{j < i \text{ 且 } sc_j \le sc_i} dp[j])$$
  對於每個球員 $i$，我們往前回溯所有的球員 $j$，只要 $j$ 的分數 $\le$ $i$ 的分數，代表他們可以共存，我們就嘗試把 $i$ 接在 $j$ 構成的球隊後面。

#### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int bestTeamScore(vector<int>& scores, vector<int>& ages) {
        vector<pair<int, int>> sorted;
        int n = scores.size();
        for(int i = 0; i < n; ++i)
            sorted.push_back({ages[i], scores[i]});

        // 雙維度排序：年齡小 -> 大；年齡相同時，分數小 -> 大
        sort(sorted.begin(), sorted.end(), [](const auto& a, const auto& b){
            if(a.first != b.first) return a.first < b.first;
            return a.second < b.second;
        });

        vector<int> dp(n, 0);
        for(int i = 0; i < n; ++i) {
            int sc = sorted[i].second;
            dp[i] = sc; // 初始狀態：只有自己一個人的球隊
            
            // 往前回溯，尋找可以共存的前輩 (sc_j <= sc_i)
            for(int j = 0; j < i; ++j) {
                if(sc >= sorted[j].second) {
                    dp[i] = max(dp[i], dp[j] + sc);
                }
            }
        }

        // 找尋所有可能結尾中的最大值
        int mx = 0;
        for(auto x : dp) mx = max(mx, x);
        return mx;
    }
};

/*
狀態推導筆記：
yi < yj && si < sj or yi == yj 
排序確保 age small -> big
dp[i] := 選擇第 i 個成員作為結尾時的最大分數
dp[i] = max(dp[i], max{ dp[j] + sc } if sc[j] <= sc[i])
*/
```

---

### 🛠️ 方法二：值域線段樹優化 DP - $\mathcal{O}(N \log N)$
當 $N$ 擴張到 $10^5$ 時，$\mathcal{O}(N^2)$ 的迴圈必定會超時。此時可以利用**線段樹 (Segment Tree)** 將內層迴圈的 $\mathcal{O}(N)$ 查詢壓榨到 $\mathcal{O}(\log N)$。
- **狀態轉換**：我們不再以「球員索引 $i$」作為 DP 的底層，而是以「**球員分數的值域 $k$**」作為底層。
- **作法**：
  建構一棵線段樹，每個節點儲存「以該分數作為最高分的球隊最大總分」。當處理分數為 $sc$ 的球員時，只要向線段樹**查詢區間 `[1, sc]` 的最大值**，加上 $sc$ 後，再**單點更新**回位置 $sc$ 即可。這完美取代了方法一中的內層 `j` 迴圈！*(由於分數高達 $10^6$，需先進行離散化 Coordinate Compression)*。

<details>
<summary><b>點擊展開：極致優化的 O(N log N) 線段樹實作</b></summary>

```cpp
class Solution {
public:
    vector<long long> tree;
    int N;
    
    void pushUp(int node) {
        tree[node] = max(tree[node << 1], tree[node << 1 | 1]);
    }
    
    void upd(int node, int l, int r, int L, int R, long long val) {
        if(L > R) return;
        if(L <= l && r <= R) {
            tree[node] = val; 
            return;
        }

        int mid = l + (r - l) / 2;
        if(L <= mid) upd(node << 1, l, mid, L, R, val);
        if(R > mid) upd(node << 1 | 1, mid + 1, r, L, R, val);

        pushUp(node);
    }
    
    long long qry(int node, int l, int r, int L, int R) {
        if(L > R) return 0;
        if(L <= l && r <= R) return tree[node];
        
        int mid = l + (r - l) / 2;
        long long res = 0;
        if(L <= mid) res = max(res, qry(node << 1, l, mid, L, R));
        if(R > mid) res = max(res, qry(node << 1 | 1, mid + 1, r, L, R));

        return res;
    }
    
    int bestTeamScore(vector<int>& scores, vector<int>& ages) {
        vector<pair<int, int>> sorted;
        int n = scores.size();
        for(int i = 0; i < n; ++i)
            sorted.push_back({ages[i], scores[i]});

        sort(sorted.begin(), sorted.end(), [](const auto& a, const auto& b){
            if(a.first != b.first) return a.first < b.first;
            return a.second < b.second;
        });

        // 離散化 Coordinate Compression
        vector<int> uni(scores.begin(), scores.end());
        sort(uni.begin(), uni.end());
        uni.erase(unique(uni.begin(), uni.end()), uni.end());
        
        N = uni.size();
        tree.assign(4 * N + 5, 0);
        
        for(auto p : sorted) {
            int sc = p.second;
            auto k = lower_bound(uni.begin(), uni.end(), sc) - uni.begin() + 1;
            
            // 查詢 [1, k] 的歷史最大總分，加上自己後更新回位置 k
            long long prev_max = qry(1, 1, N, 1, k);
            upd(1, 1, N, k, k, prev_max + sc);
        }
        
        return tree[1];
    }
};
```
</details>

---

### 📊 複雜度總結對比
| 解法 | 時間複雜度 | 空間複雜度 | 適用場景 |
| :--- | :--- | :--- | :--- |
| **方法一：標準 DP** | $\mathcal{O}(N^2)$ | $\mathcal{O}(N)$ | 面試穩扎穩打，資料量 $N \le 2000$ 皆可秒殺。 |
| **方法二：線段樹 DP** | $\mathcal{O}(N \log N)$ | $\mathcal{O}(N)$ | 競技程式、資料量 $N \ge 10^5$ 時的極致降維打擊。 |

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