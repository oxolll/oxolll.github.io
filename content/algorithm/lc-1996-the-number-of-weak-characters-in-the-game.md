---
title: "LeetCode 1996. The Number of Weak Characters in the Game"
date: 2026-05-04T16:15:00+08:00
lastmod: 2026-05-04T16:15:00+08:00
difficulty: 1860
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "sorting", "greedy", "monotone"]
keywords: ["LC1996", "Medium", "Weak Characters", "Sorting", "Greedy", "降維"]
description: "LeetCode 第 1996 題：The Number of Weak Characters in the Game。難度評分：1860。探討如何利用巧妙的雙維度自訂排序（攻擊力降序、防禦力升序），將二維的比較問題降維，以 O(N log N) 的貪心策略極速找出所有的弱勢角色。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1996](https://leetcode.com/problems/the-number-of-weak-characters-in-the-game/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1860)</span>  
> **核心主題**：`Array` $\cdot$ `Sorting` $\cdot$ `Greedy` $\cdot$ `Stack`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
你正在玩一款包含多個角色的遊戲，每個角色都有兩個主要屬性：攻擊力 (attack) 和防禦力 (defense)。
給你一個二維整數陣列 `properties`，其中 `properties[i] = [attack_i, defense_i]` 代表第 $i$ 個角色的屬性。

如果存在另一個角色，其攻擊力和防禦力都**嚴格大於**該角色的攻擊力和防禦力，則我們稱該角色為 **「弱勢角色」 (Weak Character)**。
換句話說，如果存在一個角色 $j$ 滿足 $attack_j > attack_i$ 且 $defense_j > defense_i$，則角色 $i$ 是弱勢角色。

請回傳弱勢角色的總數。

### 範例
- **Input**: `properties = [[5,5],[6,3],[3,6]]`
- **Output**: `0`
- **Explanation**: 沒有任何角色的攻擊力與防禦力同時嚴格小於其他角色。

- **Input**: `properties = [[1,5],[10,4],[4,3]]`
- **Output**: `1`
- **Explanation**: 第三個角色 `[4,3]` 的攻擊力和防禦力都嚴格小於第二個角色 `[10,4]`，所以它是弱勢角色。

### 限制條件
- $2 \le properties.length \le 10^5$
- $properties[i].length == 2$
- $1 \le attack_i, defense_i \le 10^5$
</details>

---

## 📝 歷次打卡與更新
- [2026-05-04：初次提交 (雙維度自訂排序 + 貪心維護最大值)](#2026-05-04-初次提交)

---

## 💡 2026-05-04 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題本質上是尋找「二維偏序」中的被支配點。如果直接使用雙層迴圈尋找，時間複雜度為 $\mathcal{O}(N^2)$，必定 TLE。
要將時間降為 $\mathcal{O}(N \log N)$，我們必須透過 **排序 (Sorting)** 來降維。

**破局關鍵：精心設計的排序規則**
我們希望在掃描陣列時，只要檢查「防禦力」是否比目前看過的最大防禦力小即可。為了做到這點，我們必須保證「已經看過的角色，其攻擊力一定大於等於當前角色」。
因此：
1. **第一維度：攻擊力降序 (Descending)**。這樣保證了前面出現的攻擊力 $\ge$ 後面出現的攻擊力。
2. **第二維度：防禦力升序 (Ascending)**。這是最精妙的一手！當攻擊力相同時，如果防禦力也降序，就會發生「同攻擊力中防禦高的人，誤把防禦低的人標記為弱勢」的錯誤（題目要求攻擊力必須**嚴格大於**）。改為防禦力**升序**後，對於同一組攻擊力的人，防禦力是從小到大出現的。因此，更新過的最大防禦力 `mx_def` 絕對不可能來自於同攻擊力的人，必定是來自於前面**攻擊力嚴格大於**他們的角色！

### 🛠️ 解題思路 (Approach)
1. **自訂排序**：
   - 比較 `a[0]` 和 `b[0]` (攻擊力)。如果不相等，回傳 `a[0] > b[0]` (降序)。
   - 如果相等，比較 `a[1]` 和 `b[1]` (防禦力)，回傳 `a[1] < b[1]` (升序)。
2. **貪心掃描**：
   - 初始化 `rt = 0` (弱勢角色數量) 和 `mx_def = 0` (歷史出現過的最大防禦力)。
   - 遍歷排序後的陣列：
     - 若當前角色的防禦力 `def < mx_def`，代表必定存在一個前面出現過的角色，其攻擊力嚴格大於當前角色 (排序保證)，且防禦力也嚴格大於當前角色 (此條件保證)。因此當前角色為弱勢角色，`++rt`。
     - 若 `def >= mx_def`，代表當前角色沒有被前面的人壓制，更新 `mx_def = def`。
3. **回傳結果**：迴圈結束後回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N \log N)$。排序需要 $\mathcal{O}(N \log N)$，隨後的一次線性遍歷需要 $\mathcal{O}(N)$，總時間複雜度由排序主導。
- **空間複雜度**: $\mathcal{O}(\log N)$。取決於 C++ `std::sort` 的底層遞迴深度空間（IntroSort）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int numberOfWeakCharacters(vector<vector<int>>& properties) {
        // 自訂排序：攻擊力由大到小排；若攻擊力相同，防禦力由小到大排。
        sort(properties.begin(), properties.end(), [](const auto& a, const auto& b){
            if(a[0] != b[0]) return a[0] > b[0]; // 攻擊力降序
            return a[1] < b[1];                  // 防禦力升序 (防呆設計，避免同攻擊力誤殺)
        });

        int rt = 0;
        int mx_def = 0; // 記錄從左至右掃描過程中，遇到的最大防禦力
        
        for(auto& p : properties) {
            int def = p[1];
            // 因為攻擊力是降序，只要當前的防禦力小於看過的最大防禦力，
            // 就代表一定存在一個攻擊力嚴格大於它、且防禦力也嚴格大於它的角色。
            if(def < mx_def) {
                ++rt;
            } else {
                // 更新歷史最大防禦力
                mx_def = def;
            }
        }
        return rt;
    }
};

/*
User Comments & Notes:
atki > atkj && defi > defj
排序策略：
攻擊力大 -> 小 (確保前面掃過的人攻擊力絕對 >= 自己)
當攻擊力相同時，防禦力小 -> 大 (確保 mx_def 不會被同一批攻擊力的人提早更新而造成誤殺)
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