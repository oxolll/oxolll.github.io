---
title: "LeetCode 3751. Total Waviness of Numbers in Range I"
date: 2026-06-04T14:10:00+08:00
lastmod: 2026-06-04T14:45:00+08:00
difficulty: 1404
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "math", "simulation", "prefix-sum", "depth-first-search", "precomputation"]
keywords: ["LC3751", "Medium", "1404", "Total Waviness", "數字拆解", "前綴和", "全域預處理", "DFS"]
description: "LeetCode 第 3751 題：Total Waviness of Numbers in Range I。探討兩種高效解法：一是 O(1) 空間的數學滑動視窗模擬；二是利用 DFS 全域預處理構造數字並搭配前綴和，達成單次查詢僅需 O(1) 的極致效能。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3751](https://leetcode.com/problems/total-waviness-of-numbers-in-range-i/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1404)</span>  
> **核心主題**：`Math` $\cdot$ `Simulation` $\cdot$ `Prefix Sum`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個整數 `num1` 和 `num2`。
一個數字的 **Waviness (波動度)** 定義為其所有連續三個位數形成「波峰」或「波谷」的總次數：
- **波峰 (Peak)**：當連續三位數 $(d_1, d_2, d_3)$ 滿足 $d_1 < d_2$ 且 $d_2 > d_3$。
- **波谷 (Valley)**：當連續三位數 $(d_1, d_2, d_3)$ 滿足 $d_1 > d_2$ 且 $d_2 < d_3$。

請你計算在閉區間 `[num1, num2]` 內，所有數字的 Waviness 總和。

### 限制條件
- $1 \le num1 \le num2 \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-04：新增解法二 (DFS 全域預處理與前綴和 O(1) 查詢)](#💡-解法二dfs-全域預處理--前綴和-o1-查詢最優解)
- [2026-06-04：初次提交 (純數學位數萃取法)](#💡-解法一純數學滑動視窗模擬法-o1-空間)

---

## 💡 解法一：純數學滑動視窗模擬法 ($\mathcal{O}(1)$ 空間)

### 🎯 演算法分析
本題是典型的區間計數模擬題。
判斷波峰與波谷需要連續比較三個相鄰的位數。最優雅的做法是 **純數學提取** 以避開昂貴的字串轉換：
- 對於任意數字 `x`，最後三個位數分別為：高位 `h = (x % 1000) / 100`、中位 `m = (x % 100) / 10`、低位 `l = x % 10`。
- 檢查完畢後，將 `x /= 10` 即可讓「長度為 3 的檢查視窗」向左滑動。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(K \cdot \log_{10}(M))$。$K$ 為區間大小，$M$ 為最大數字。內層操作純為整數除法與模除，效能極高。
- **空間複雜度**: $\mathcal{O}(1)$。完全不佔用額外記憶體。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int totalWaviness(int num1, int num2) {
        int rt = 0;
        
        for(int i = num1; i <= num2; ++i) {
            int x = i;
            
            // 確保數字長度至少為 3 位數
            while(x >= 100) {
                int h = (x % 1000) / 100; // 高位
                int m = (x % 100) / 10;   // 中位
                int l = x % 10;           // 低位

                // 判斷是否為波谷 (Valley) 或波峰 (Peak)
                if(h > m && m < l) ++rt;
                if(h < m && m > l) ++rt;
                
                // 向左滑動視窗
                x /= 10;
            }
        }
        return rt;
    }
};
```

---

## 💡 解法二：DFS 全域預處理 + 前綴和 ($\mathcal{O}(1)$ 查詢最優解)

### 🎯 演算法分析
在 LeetCode 的評測環境中，同一個程式會連續執行多筆測試測資。如果我們能做到 **「只算一次，造福全家」**，就能將單次查詢的時間壓縮到極致的 $\mathcal{O}(1)$。這就是俗稱的**「打表 / 全域預處理」**。

**核心破局點 1：靜態陣列與前綴和**
宣告全域/靜態的 `dp` 陣列，並使用一個布林旗標 `init` 來保證建表過程只會執行一次。
`dp[i]` 記錄從 $0$ 到 $i$ 之間所有數字的 Waviness 總和。查詢區間 `[num1, num2]` 時，只需回傳 `dp[num2] - dp[num1-1]` 即可。

**核心破局點 2：遞迴構造 (DFS)**
與其從 $1$ 數到 $10^5$ 逐一拆解位數，不如直接用 DFS **從低位到高位構造數字**。
在加入新的高位數字 `h` 時，利用傳入的 `base` (1, 10, 100...)，我們可以用數學運算 `cur / (base/10) % 10` 精準地取得先前的位數 `m` 和 `l`，在構造數字的當下就瞬間算出該數字的 Waviness (`nw`) 並寫入 `dp`。

### 📊 複雜度分析
- **時間複雜度**:
  - **初始化階段**: $\mathcal{O}(M)$，其中 $M = 10^5$。DFS 會造訪所有 $10^5$ 以內的數字一次，並進行一次線性掃描計算前綴和。所有測試測資**共用這一次開銷**。
  - **單次查詢**: $\mathcal{O}(1)$。陣列相減即得出結果。
- **空間複雜度**: $\mathcal{O}(M)$。需要一個大小為 $10^5$ 的 `dp` 靜態陣列，以及 DFS 的遞迴呼叫堆疊（最大深度約 6）。

### 💻 程式碼實作 (C++)
```cpp
// 宣告在類別外部 (或使用 static)，確保跨測資共享狀態
static int dp[100005];
static bool init = true;

class Solution {
public:
    // DFS 構造數字：cur 為目前數字，base 為當前要填入的位值 (1, 10, 100...)，wave 為目前的總波動數
    void helper(int cur, int base, int wave) {
        // 枚舉要填入的最高位數字 h (0 ~ 9)
        for(int h = 0; h <= 9; ++h) {
            int next = cur + h * base;
            
            // 題目的極限測資限制為 10^5
            if(next > 1e5) break;
            
            int nw = wave;
            
            // 當 base >= 100 時，代表 next 至少有 3 個位數，可以提取相鄰位數
            int m = (base >= 100 ? cur / (base / 10) % 10 : -1);
            int l = (base >= 100 ? cur / (base / 100) % 10 : -1);
            
            if(m >= 0 && l >= 0) {
                // 判斷是否構成波峰或波谷
                if(h > m && m < l) ++nw;
                else if(h < m && m > l) ++nw;
            }

            // 若最高位不為 0，將算出的 waviness 記錄到 dp 陣列中
            if(h != 0) {
                dp[next] = nw;
            }

            // 繼續往更高的位數遞迴構造
            if(base <= 10000) {
                helper(next, base * 10, nw);
            }
        }
    }
    
    int totalWaviness(int num1, int num2) {
        // 全域初始化：只在第一筆測資進來時執行一次
        if(init) {
            init = false;
            dp[0] = 0;
            
            // 遞迴建表
            helper(0, 1, 0);

            // 將 dp 陣列轉換為前綴和 (Prefix Sum) 陣列
            for(int i = 1; i <= 1e5; ++i) {
                dp[i] += dp[i - 1];
            }
        }

        // 任意區間查詢降維打擊：O(1) 回傳結果
        return dp[num2] - dp[num1 - 1];
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