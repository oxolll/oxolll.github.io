---
title: "LeetCode 3464. Maximize the Distance Between Points on a Square"
date: 2026-04-25T14:05:00+08:00
lastmod: 2026-04-25T14:05:00+08:00
difficulty: 2805
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "binary-search", "greedy", "geometry", "array"]
keywords: ["LC3464", "Hard", "2805", "Maximize the Distance", "Binary Search", "Circular Array"]
description: "LeetCode 第 3464 題：Maximize the Distance Between Points on a Square。難度評分：2805。探討如何將二維正方形邊界降維成一維線段，並透過環狀陣列展開、二分答案 (Binary Search) 與貪婪驗證 (Greedy)，完美解決最大化最小距離的問題。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3464](https://leetcode.com/problems/maximize-the-distance-between-points-on-a-square/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (2805)</span>  
> **核心主題**：`Binary Search` $\cdot$ `Greedy` $\cdot$ `Geometry` $\cdot$ `Array`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個邊長為 `side` 的正方形，其四個頂點分別為 $(0, 0), (side, 0), (side, side), (0, side)$。
同時給定一個二維陣列 `points`，其中 `points[i] = [x_i, y_i]` 代表正方形邊界上的一個點。
請你從 `points` 中精確挑選出 `k` 個點，使得這 `k` 個點在正方形邊界上的**最小距離最大化**。
兩個點在邊界上的距離，定義為沿著正方形周界行走的較短路徑長度。
請回傳這個最大化的最小距離。

### 限制條件
- $2 \le k \le points.length \le 10^5$ (推測值)
- $1 \le side \le 10^9$
- 保證所有 `points[i]` 都落在正方形的邊界上。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-25：初次提交 (降維轉換與二分答案法)](#2026-04-25-初次提交)

---

## 💡 2026-04-25 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是極度經典的**「最大化最小值 (Maximize the Minimum)」**題型，唯一的突破口就是 **對答案進行二分搜尋 (Binary Search the Answer)**。
然而，處理二維座標與環狀距離非常棘手，我們必須進行兩階段的幾何轉換：
1. **降維打擊 (2D to 1D)**：正方形的周長為 $4 \times side$。我們可以依據點所在的邊（下、右、上、左），將其映射到 $[0, 4 \times side)$ 的一維座標線上。
2. **破除環狀 (Circular to Linear)**：因為點可以跨越 $(0,0)$ 進行配對，這是一個環狀陣列問題。標準的解法是將陣列複製一份接在原陣列後方，並將第二段座標全部加上 $4 \times side$。這樣一來，任何長度不超過一圈的配對都可以轉化為單純的線性查找。

在驗證某個目標距離 `tar` 是否合法時，我們只需遍歷前 $N$ 個點當作「起點」，並貪婪地向後利用 `lower_bound` 尋找距離至少為 `tar` 的下一個點。若成功找到 $k$ 個點，且最後一個點與起點加上一圈距離的差距依然滿足條件，則代表此 `tar` 是合法的。

### 🛠️ 解題思路 (Approach)
1. **座標展開**：
   - 走訪 `points`，判斷座標所在的邊界，將其轉換為一維距離存入 `new_p`。
     - 底邊 ($y=0$)：距離為 $x$
     - 右邊 ($x=side$)：距離為 $side + y$
     - 頂邊 ($y=side$)：距離為 $3 \times side - x$
     - 左邊 ($x=0$)：距離為 $4 \times side - y$
2. **建構環狀陣列**：
   - 將 `new_p` 進行遞增排序。
   - 走訪排序後的 `new_p`，將 `new_p[i] + 4 * side` 推入陣列後方，陣列長度變為 $2N$。
3. **二分搜尋目標距離**：
   - 設定搜尋範圍 `l = 0`, `r = 2 * side`（在正方形上，兩點最遠距離即為周長的一半）。
   - 每次取 `mid` 作為目標最小距離。
   - 呼叫 `isOK(mid, new_p, k, side)` 進行驗證。若合法則 `l = mid` (為了最大化，將下界推高)，否則 `r = mid - 1`。*(註：計算 `mid` 時使用 `r - ((r-l)>>1)` 是為了避免無窮迴圈的向上取整技巧)*。
4. **貪婪驗證 (`isOK`)**：
   - 遍歷原陣列的前 $N$ 個點作為候選起點。
   - 使用 `for` 迴圈嘗試尋找 $k$ 個點 (程式碼中變數為 $t$)。
   - 每次利用 `lower_bound` 尋找大於等於 `start + tar` 的位置。若越界則代表當前起點失敗。
   - 若成功找齊 $k$ 個點，嚴格檢查首尾距離：`p[i] + 4 * side - start >= tar` (起點的下一圈位置減去最後一個點的位置，必須大於等於 `tar`)。若滿足則提早回傳 `true`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N + \log(\text{MaxDist}) \times N \times K \log N)$。
  - 陣列展開與排序耗費 $\mathcal{O}(N \log N)$。
  - 二分搜尋執行約 $\log(2 \times 10^9) \approx 31$ 次。
  - 每次驗證最差需要以 $N$ 個點為起點，每次尋找 $K$ 個點，使用 `lower_bound` 耗時 $\log N$。
  *(註：此複雜度在競賽中若 $N$ 較大時可能會受限，但透過貪婪的 Early Exit 機制，實務運行常數極小，能夠順利通過)*。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了 `new_p` 陣列來儲存 $2N$ 個一維化後的點。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 貪婪驗證函數：檢查是否能以最小距離 tar 挑出 t 個點
    bool isOK(int tar, vector<long long>& p, int t, long long side) {
        int m = p.size();
        int n = m / 2; // 原本的陣列長度
        
        // 嘗試以每一個原始點作為挑選的「起點」
        for(int i = 0; i < n; ++i) {
            long long start = p[i] - tar;
            bool failed = false;
            
            // 貪婪地往後尋找 t 個滿足距離的點
            for(int j = 0; j < t; ++j) {
                // 利用 lower_bound 快速尋找下一個距離大於等於 start + tar 的點
                int k = lower_bound(p.begin(), p.end(), start + tar) - p.begin();
                if(k == m) {
                    failed = true;
                    break;
                }
                start = p[k]; // 更新當前點為下一個尋找的基準
            }

            if(failed) continue; // 若無法找齊 t 個點，換下一個起點嘗試
            
            // 首尾碰撞檢查：起點的下一圈位置，與最後一個被挑選點的距離，是否也滿足 tar
            if(p[i] + 4LL * side - start >= tar) {
                return true;
            }
        }

        return false;
    }
    
    int maxDistance(int side, vector<vector<int>>& points, int k) {
        vector<long long> new_p;

        // 1. 降維打擊：將二維座標攤平成一維線段
        for(auto p : points) {
            int x = p[0], y = p[1];
            if(y == 0)
                new_p.push_back(x);
            else if(x == side)
                new_p.push_back(side + y);
            else if(y == side)
                new_p.push_back(3LL * side - x);
            else 
                new_p.push_back(4LL * side - y);
        }

        int n = new_p.size();
        sort(new_p.begin(), new_p.end());
        
        // 2. 破除環狀：複製一倍接在後方，加上一整圈的周長
        for(int i = 0; i < n; ++i) {
            new_p.push_back(new_p[i] + 4LL * side);
        }

        // 3. 二分答案：在可能的最大與最小距離間搜尋
        int l = 0, r = 2 * side;
        while(l < r) {
            // 向上取整的 mid，防止 l = mid 時造成無窮迴圈
            int mid = r - ((r - l) >> 1);
            if(isOK(mid, new_p, k, side)) {
                l = mid; // 如果滿足，嘗試更大的距離
            } else {
                r = mid - 1; // 如果不滿足，縮小距離
            }
        }

        return l;
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