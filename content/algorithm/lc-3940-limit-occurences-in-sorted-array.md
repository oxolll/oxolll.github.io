---
title: "LeetCode 3940. Limit Occurrences in Sorted Array"
date: 2026-05-24T15:50:00+08:00
lastmod: 2026-05-24T15:50:00+08:00
difficulty: 1200
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "two-pointers", "counting"]
keywords: ["LC3940", "Easy", "Limit Occurrences", "Sorted Array", "雙指標", "連續計數"]
description: "LeetCode 第 3940 題：Limit Occurrences in Sorted Array。難度評分：Easy。探討如何利用排序陣列的連續性，透過線性掃描與計數，將每個元素的出現次數限制在最大 k 次之內。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3940](https://leetcode.com/problems/limit-occurrences-in-sorted-array/) *(註：題號對應依據實際題庫)* > **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Counting` $\cdot$ `Two Pointers`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個已經按 **非遞減順序** 排序的整數陣列 `nums`，以及一個整數 `k`。
請你回傳一個新的陣列，其中原陣列中的每個元素最多只能出現 `k` 次。
回傳的陣列也必須保持原有的排序順序。

### 範例
- **Input**: `nums = [1,1,1,2,2,3], k = 2`
- **Output**: `[1,1,2,2,3]`
- **Explanation**: 
  數字 1 原本出現 3 次，限制保留最多 2 次。
  數字 2 原本出現 2 次，保留 2 次。
  數字 3 原本出現 1 次，保留 1 次。

### 限制條件
- $1 \le nums.length \le 10^5$
- $1 \le k \le 10^5$
- `nums` 已按非遞減順序排序。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-24：初次提交 (連續計數與截斷法)](#2026-05-24-初次提交)

---

## 💡 2026-05-24 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
因為輸入的陣列已經排序好，這意味著所有相同的數字一定都會「相鄰」在一起。
我們不需要使用複雜的 Hash Map 去統計全局頻率，只需要維護一個「當前正在觀察的數字 (`prev`)」以及「它連續出現的次數 (`c`)」即可。

當我們遇到一個與 `prev` 不同的新數字時，就代表前一個數字的連續區段已經結束了。此時，我們只需將 $\min(c, k)$ 個 `prev` 塞入結果陣列中，然後重新開始計算新數字的次數。

**核心破局點：**
1. **區段結算**：利用 `min(c, k)` 完美達成「不超過 $k$ 次」的邏輯限制。
2. **尾部處理 (Trailing Processing)**：當整個陣列走訪完畢時，最後一段連續數字的結算還留在變數中（因為沒有遇到「不一樣的數字」來觸發寫入），所以必須在迴圈結束後手動補上最後一次的寫入。

### 🛠️ 解題思路 (Approach)
1. 宣告結果陣列 `rt`，並初始化 `prev = nums[0]`，計數器 `c = 0`。
2. 遍歷陣列 `nums` 中的每一個數字 `num`：
   - 若 `num == prev`，說明還在同一個連續區段，計數器 `c` 加 1，並 `continue`。
   - 若 `num != prev`，代表切換到新的數字了。執行迴圈將 $\min(c, k)$ 個 `prev` 寫入 `rt`。
   - 更新 `prev = num`，並將新數字的計數器重置為 `c = 1`。
3. 迴圈結束後，將最後殘留在 `prev` 和 `c` 中的最後一組數字寫入 `rt`。
4. 回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。我們只對陣列進行了一次線性掃描。雖然內部有一個 `for` 迴圈負責 `push_back`，但每個元素被塞入 `rt` 的總次數絕不會超過原陣列長度 $N$，因此整體操作次數依然是線性的。
- **空間複雜度**: $\mathcal{O}(1)$。扣除掉用來作為答案回傳的 `rt` 陣列，我們只使用了常數個狀態變數（`prev`, `c`）。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> limitOccurrences(vector<int>& nums, int k) {
        vector<int> rt;
        if(nums.empty()) return rt; // 安全防禦 (依據題意長度至少為 1，此行可省)

        int prev = nums[0];
        int c = 0;
        
        for(auto num : nums) {
            // 如果遇到相同的數字，持續累加計數
            if(num == prev) {
                ++c;
                continue;
            }
            
            // 遇到不同的數字時，結算上一段的數字，最多保留 k 個
            for(int i = 0; i < min(c, k); ++i) {
                rt.push_back(prev);
            }
            
            // 狀態切換，開始計算新數字
            prev = num;
            c = 1;
        }
        
        // 關鍵：補償處理最後一段還未結算的數字區塊
        for(int i = 0; i < min(c, k); ++i) {
            rt.push_back(prev);
        }
        
        return rt;
    }
};
```

---

### 💡 延伸思維：原地修改 (In-Place) 挑戰
如果在面試中，面試官要求 **空間複雜度 $\mathcal{O}(1)$ (不允許額外開 vector，直接在原陣列上修改)**，你可以使用「慢指標」來覆寫陣列：
```cpp
class Solution {
public:
    int limitOccurrences(vector<int>& nums, int k) {
        int slow = 0;
        for (int fast = 0; fast < nums.size(); ++fast) {
            // 如果當前坑位少於 k 個，或者當前數字不等於慢指標往回推 k 格的數字
            if (slow < k || nums[fast] != nums[slow - k]) {
                nums[slow] = nums[fast];
                ++slow;
            }
        }
        // 最終 slow 即為新陣列的長度
        return slow; // 或 nums.resize(slow); return nums;
    }
};
```
*這種原地覆寫的寫法是這類題型（如 LC 26, LC 80）的高階延伸，非常值得掌握！*

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