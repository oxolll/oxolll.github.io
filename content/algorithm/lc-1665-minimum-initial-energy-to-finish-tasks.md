---
title: "LeetCode 1665. Minimum Initial Energy to Finish Tasks"
date: 2026-05-12T23:45:00+08:00
lastmod: 2026-05-12T23:45:00+08:00
difficulty: 1900
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "greedy", "sorting", "binary-search"]
keywords: ["LC1665", "Hard", "Minimum Initial Energy", "貪心算法", "二分搜尋"]
description: "LeetCode 第 1665 題：Minimum Initial Energy to Finish Tasks。難度評分：1900。探討如何透過定義任務執行順序的優先級，並結合二分搜尋或直接累加法，計算完成所有任務所需的最少初始能量。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1665](https://leetcode.com/problems/minimum-initial-energy-to-finish-tasks/)
> **難度評分**：<span style="color: #ff375f; font-weight: bold;">Hard (1900)</span>  
> **核心主題**：`Greedy` $\cdot$ `Sorting` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個任務陣列 `tasks`，其中 `tasks[i] = [actual_i, minimum_i]`：
- `actual_i` 是完成第 $i$ 個任務 **實際耗費** 的能量。
- `minimum_i` 是開始第 $i$ 個任務 **至少需要** 的能量。

你可以按任意順序執行任務。請回傳完成所有任務所需的 **最少初始能量**。

### 範例
- **Input**: `tasks = [[1,3],[2,4],[10,11],[10,12],[8,9]]`
- **Output**: `32`
- **Explanation**: 
  按順序執行任務：[10,12], [10,11], [8,9], [1,3], [2,4]。
  初始能量 32，剩餘能量演變：32 -> 22 -> 12 -> 4 -> 3 -> 1。

### 限制條件
- $1 \le tasks.length \le 10^5$
- $1 \le actual_i \le minimum_i \le 10^4$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-12：初次提交 (二分搜尋法與直接累加法)](#2026-05-12-初次提交)

---

## 💡 2026-05-12 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的關鍵在於如何排序。假設有兩個任務 $T_1(a_1, m_1)$ 和 $T_2(a_2, m_2)$：
1. 若先執行 $T_1$ 再執行 $T_2$，所需初始能量 $E \ge \max(m_1, a_1 + m_2)$。
2. 若先執行 $T_2$ 再執行 $T_1$，所需初始能量 $E \ge \max(m_2, a_2 + m_1)$。

要讓 $E$ 最小，我們希望 $a_1 + m_2 < a_2 + m_1$，移項後得到：
$$m_1 - a_1 > m_2 - a_2$$
結論：**優先執行 `minimum - actual` (差值) 較大的任務**。

---

### 🛠️ 方法一：二分搜尋 + 貪心排序
利用答案的單調性（如果能量 $X$ 可以完成，則 $X+1$ 也可以），我們可以在總能量範圍內進行二分搜尋，並在每次 `check` 時按上述貪心規則模擬。

#### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    static bool cmp(vector<int>& a, vector<int>& b) {
        // 優先處理 (minimum - actual) 較大的任務
        int diff1 = a[1] - a[0], diff2 = b[1] - b[0];
        if (diff1 == diff2) return a[1] > b[1];
        return diff1 > diff2;
    }

    bool isOK(int mid, vector<vector<int>>& t) {
        for (int i = 0; i < t.size(); ++i) {
            // 檢查當前剩餘能量是否滿足任務最低要求
            if (t[i][1] > mid) return false;
            mid -= t[i][0];
        }
        return true;
    }

    int minimumEffort(vector<vector<int>>& tasks) {
        int l = 0, r = 0;
        for (int i = 0; i < tasks.size(); ++i) {
            l += tasks[i][0]; // 最小值為 actual 總和
            r += max(tasks[i][0], tasks[i][1]); // 最大值可能範圍
        }

        // 1. 根據貪心策略排序
        sort(tasks.begin(), tasks.end(), cmp);

        // 2. 二分搜尋最優初始能量
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (isOK(mid, tasks)) r = mid;
            else l = mid + 1;
        }
        return l;
    }
};
```

---

### 🛠️ 方法二：直接貪心累加
在確定最優排序後，我們不需要二分搜尋。我們可以直接從頭模擬：如果當前能量不夠執行下一個任務，我們就「精準地」在初始能量中補上差額。

#### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    // 比較規則：按差值 (minimum - actual) 降序排列
    static bool cmp(const vector<int>& a, const vector<int>& b) {
        int diffA = a[1] - a[0];
        int diffB = b[1] - b[0];
        if (diffA == diffB) return a[1] > b[1];
        return diffA > diffB;
    }

    int minimumEffort(vector<vector<int>>& tasks) {
        // 1. 按最優規則排序
        sort(tasks.begin(), tasks.end(), cmp);

        int res = 0;  // 最終需要的初始總能量
        int cur = 0;  // 目前手中擁有的能量
        
        for (const auto& t : tasks) {
            // 如果目前剩餘能量低於任務門檻
            if (cur < t[1]) {
                int add = t[1] - cur; // 計算不足的缺口
                res += add;           // 補在初始能量裡
                cur += add;           // 同步增加目前能量
            }
            // 執行任務，扣除實際消耗
            cur -= t[0];
        }

        return res;
    }
};
```

---

### 📊 複雜度分析
| 方法 | 時間複雜度 | 空間複雜度 | 特色 |
| :--- | :--- | :--- | :--- |
| **二分搜尋法** | $O(N \log N + N \log (\sum M))$ | $O(1)$ | 邏輯穩健，適用於大部分範圍判定問題。 |
| **直接累加法** | $O(N \log N)$ | $O(1)$ | 效率最高，代碼簡潔，展現對貪心本質的理解。 |

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