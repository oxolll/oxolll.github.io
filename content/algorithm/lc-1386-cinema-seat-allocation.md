---
title: "LeetCode 1386. Cinema Seat Allocation"
date: 2026-03-16T11:00:00+08:00
lastmod: 2026-03-16T11:00:00+08:00
difficulty: 1636
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "Bit Manipulation", "Greedy", "Sorting"]
keywords: ["LC1386", "1636", "Medium", "Cinema Seat", "Bitmask", "位元運算"]
description: "LeetCode 第 1386 題：Cinema Seat Allocation。難度評分：1636。探討如何利用位元遮罩 (Bitmask) 與排序，在 O(M log M) 時間內高效判斷電影院座位分配的最大家庭數量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1386](https://leetcode.com/problems/cinema-seat-allocation/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1636)</span>    
> **核心主題**：`Array` $\cdot$ `Bit Manipulation` $\cdot$ `Greedy` $\cdot$ `Sorting`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
一個電影院有 $n$ 排座位，每排有 10 個座位，編號從 1 到 10。
給定一個陣列 `reservedSeats`，其中 $reservedSeats[i] = [row, col]$ 代表第 $row$ 排的第 $col$ 個座位已經被預約了。

一個家庭包含 4 個人，他們需要坐在**同一排相鄰**的 4 個位子上。此外，這 4 個位子在過道 (Aisle) 的分配上有特殊規定，只能是以下三種區塊之一：
1. **左區塊**：座位 2, 3, 4, 5
2. **右區塊**：座位 6, 7, 8, 9
3. **中區塊**：座位 4, 5, 6, 7

請回傳電影院最多能容納幾個家庭。

### 限制條件
- $1 \le n \le 10^9$
- $1 \le reservedSeats.length \le \min(10 \times n, 10^4)$
- $reservedSeats[i].length == 2$
</details>

---

## 📝 歷次打卡與更新
- [2026-03-16：初次提交 (排序分群與 Bitmask 狀態檢查)](#2026-03-16-初次提交)

---

## 💡 2026-03-16 初次提交

### 🎯 直覺 (Intuition)
電影院有 $n$ 排，且 $n$ 高達 $10^9$，如果我們宣告一個長度為 $n$ 的陣列來記錄每一排絕對會 Memory Limit Exceeded (MLE)。
實際上，我們只需要關注**「有被預約」**的那些排數，對於那些「完全沒被預約」的排，每一排都可以毫無懸念地塞進 2 個家庭（左區塊 2~5 與右區塊 6~9）。
對於有被預約的排，我們可以使用一個 10-bit 的整數來表示座位狀態，1 代表空位，0 代表已被預約。
題目要求的 3 個合法連續區塊，剛好可以轉換成 3 個整數常數 (Bitmask)。透過 `&` 運算，我們可以在 $\mathcal{O}(1)$ 的時間內瞬間驗證某個區塊是否完全為空。

### 🛠️ 解題思路 (Approach)
1. **排序與分群**：
   - 將 `reservedSeats` 進行排序，這樣同一排的預約紀錄就會連續出現。
   - 宣告 `count = n`，每處理一條有預約的「新排」，就把 `count` 減 1。最後剩下的 `count` 就是完全全空的排。
2. **位元遮罩 (Bitmask) 設計**：
   - 將一排初始狀態設為 `1023` (二進位 `1111111111`，代表 10 個位子都是空的)。
   - 使用公式 `10 - col` 將座位 1~10 映射到位元 9~0。若該位子被預約，利用 `XOR (^)` 或是清除位元的操作將該 bit 設為 0。
   - **檢查常數推導**：
     - 左區塊 (2,3,4,5) 對應 bit (8,7,6,5) $\rightarrow 2^8 + 2^7 + 2^6 + 2^5 = \mathbf{480}$。
     - 右區塊 (6,7,8,9) 對應 bit (4,3,2,1) $\rightarrow 2^4 + 2^3 + 2^2 + 2^1 = \mathbf{30}$。
     - 中區塊 (4,5,6,7) 對應 bit (6,5,4,3) $\rightarrow 2^6 + 2^5 + 2^4 + 2^3 = \mathbf{120}$。
3. **貪心結算**：
   - 利用 `(status & mask) == mask` 來檢查該區塊是否完美存活。
   - 優先檢查：如果左區塊 (`480`) 和右區塊 (`30`) 都活著，這排可以坐 2 個家庭。
   - 否則，只要 左、中、右 任何一個區塊活著，這排就能坐 1 個家庭。
4. **輸出結果**：回傳 `計算出的家庭數 + 2 * 剩餘全空排數`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(M \log M)$，其中 $M$ 為 `reservedSeats` 的長度。主要的效能瓶頸在於對預約紀錄進行排序。後續的合併與位元檢查只需 $\mathcal{O}(M)$ 線性時間。
- **空間複雜度**: $\mathcal{O}(\log M)$。C++ `std::sort` 底層的遞迴堆疊深度。若不計入排序空間，演算法本身為 $\mathcal{O}(1)$。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maxNumberOfFamilies(int n, vector<vector<int>>& reservedSeats) {
        // 將預約紀錄排序，確保同一排的紀錄集中在一起
        sort(reservedSeats.begin(), reservedSeats.end());
        int count = n; // 記錄完全沒有預約的「全空排」數量
        int rt = 0;

        // 位元常數：
        // 左區塊 (座位 2,3,4,5) : 480
        // 中區塊 (座位 4,5,6,7) : 120
        // 右區塊 (座位 6,7,8,9) : 30

        for(int i = 0; i < reservedSeats.size(); ) {
            int row = reservedSeats[i][0];
            int status = 1023; // 初始狀態：1111111111 (10 個位子皆空)
            --count; // 這排有被預約，扣除全空排額度
            
            // 處理同一排的所有預約紀錄
            while(i < reservedSeats.size() && reservedSeats[i][0] == row) {
                // 將對應的 col 轉成 0-based 反向 bit index 並設為 0
                status ^= (1 << (10 - reservedSeats[i][1]));
                ++i;
            }
            
            // 貪心判斷：能坐 2 個家庭就坐 2 個，否則看能不能坐 1 個
            if((status & 480) == 480 && (status & 30) == 30) {
                rt += 2;
            }
            else if((status & 480) == 480 || (status & 120) == 120 || (status & 30) == 30) {
                ++rt;
            }
        }

        // 最終答案 = 有預約的排所容納的家庭數 + (全空排數量 * 2)
        return rt + 2 * count;
    }
};

/* 位元推導註解：
   512 256 128  64  32  16   8   4   2   1 (權重)
     1   2   3   4   5   6   7   8   9  10 (Col)
     
   左區塊 (2~5): 256 + 128 + 64 + 32 = 480
   中區塊 (4~7): 64 + 32 + 16 + 8 = 120
   右區塊 (6~9): 16 + 8 + 4 + 2 = 30
*/
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