---
title: "LeetCode 401. Binary Watch"
date: 2026-02-18T00:00:00+08:00
lastmod: 2026-02-18T00:00:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Bit Manipulation", "Enumeration"]
keywords: ["LC401", "1200", "Binary Watch", "Popcount"]
description: "LeetCode 第 401 題：Binary Watch。利用枚舉與位元運算 (Popcount) 找出所有符合條件的時間組合。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 401](https://leetcode.com/problems/binary-watch/)  
> **難度等級**：<span style="color: var(--diff-color); font-weight: bold;">Easy(N/A)</span> 
> **核心主題**：`Bit Manipulation` $\cdot$ `Enumeration`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
二進位手錶有 4 個 LED 代表小時 (0-11)，6 個 LED 代表分鐘 (0-59)。
給定一個整數 `turnedOn` 代表目前亮著的 LED 總數，回傳所有可能的時間。

### 格式要求
- **小時**：不能有前導零 (例如 "1:00" 正確，"01:00" 錯誤)。
- **分鐘**：必須是兩位數 (例如 "10:02" 正確，"10:2" 錯誤)。

### 範例
- **Input**: `turnedOn = 1`
- **Output**: `["0:01","0:02","0:04","0:08","0:16","0:32","1:00","2:00","4:00","8:00"]`

### 限制條件
- $0 \le turnedOn \le 10$
</details>

---

## 📝 歷次打卡與更新
- [2026-02-18：初次提交 (Enumeration + Popcount)](#2026-02-18-初次提交)

---

## 💡 2026-02-18 初次提交

### 直覺 (Intuition)
雖然這題可以用回溯法 (Backtracking) 去湊出 $C(10, k)$ 的組合，但仔細觀察題目範圍：
- 小時範圍：$0 \sim 11$
- 分鐘範圍：$0 \sim 59$
總共只有 $12 \times 60 = 720$ 種時間組合。這個數量級非常小，因此直接**枚舉所有可能的時間**，再檢查其二進位中 1 的個數 (Popcount) 是否等於 `turnedOn`，會是最簡單且不易出錯的解法。

### 解題思路 (Approach)
1.  **雙層迴圈枚舉**：
    - 外層迴圈遍歷小時 `i` ($0 \dots 11$)。
    - 內層迴圈遍歷分鐘 `j` ($0 \dots 59$)。
2.  **位元計數 (Popcount)**：
    - 使用 C++ 內建函式 `__builtin_popcount(num)` 計算二進位中 1 的個數。
    - 檢查條件：`popcount(i) + popcount(j) == turnedOn`。
3.  **字串格式化**：
    - 小時直接轉字串 `to_string(i)`。
    - 分鐘若小於 10，需補前導零 (例如 "05")，否則直接轉字串。
    - 拼接後加入結果陣列。

### 📊 複雜度分析
- **時間複雜度**: $O(1)$。
  因為迴圈次數固定為 $12 \times 60 = 720$ 次，與輸入規模無關，視為常數時間。
- **空間複雜度**: $O(1)$。
  除了回傳的答案列表外，僅使用常數空間。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<string> readBinaryWatch(int turnedOn) {
        vector<string> rt;
        // 遍歷所有可能的小時 (0-11)
        for(int i = 0; i < 12; ++i)
        {
            // 剪枝：如果光是小時的燈數就超過了，就不必看分鐘
            int cnt = __builtin_popcount(i);
            if(cnt > turnedOn) continue;
            
            // 遍歷所有可能的分鐘 (0-59)
            for(int j = 0; j < 60; ++j)
            {
                int cnt2 = __builtin_popcount(j);
                
                // 檢查總燈數是否符合
                if(cnt + cnt2 != turnedOn) continue;

                // 格式化輸出
                string hr = to_string(i);
                string min = (j < 10 ? "0" + to_string(j) : to_string(j));
                rt.push_back(hr + ":" + min);
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