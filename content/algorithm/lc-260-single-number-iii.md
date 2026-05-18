---
title: "LeetCode 260. Single Number III"
date: 2026-05-18T22:30:00+08:00
lastmod: 2026-05-18T22:30:00+08:00
difficulty: 1500
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "bit-manipulation", "array"]
keywords: ["LC260", "Medium", "Single Number III", "位元運算", "低位元點分群", "Lowbit"]
description: "LeetCode 第 260 題：Single Number III。難度評分：Medium。探討如何利用位元互斥或（XOR）與最低位 1（Lowbit）掩碼，將陣列精準分類為兩個子集，在 O(N) 時間與 O(1) 輔助空間下尋找兩個獨一無二的數字。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 260](https://leetcode.com/problems/single-number-iii/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`Array` $\cdot$ `Bit Manipulation`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個整數陣列 `nums`，其中恰好有兩個元素只出現一次，其餘所有元素均出現兩次。
請找出那兩個只出現一次的元素。你可以按 **任意順序** 回傳答案。

你必須設計一個時間複雜度為 $\mathcal{O}(N)$ 且僅使用 **常數級額外空間** 的演算法來解決此問題。

### 範例
- **Input**: `nums = [1,2,1,3,2,5]`
- **Output**: `[3,5]`
- **Explanation**: `[5,3]` 也是有效答案。

- **Input**: `nums = [-1,0]`
- **Output**: `[-1,0]`

### 限制條件
- $2 \le nums.length \le 3 \times 10^4$
- $-2^{31} \le nums[i] \le 2^{31} - 1$
- 陣列中除兩個特殊數字外，其餘數字均恰好出現兩次。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-18：初次提交 (Lowbit 掩碼二分群解法)](#2026-05-18-初次提交)

---

## 💡 2026-05-18 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
本題是經典題目 Single Number（其餘出現兩次，一個出現一次）的延伸進階版。如果只有一個數字出現一次，全圖進行 **互斥或 (XOR)** 就完事了，因為相同數字 XOR 會相互抵消（$X \oplus X = 0$）。

當有兩個獨一無二的數字（假設為 $A$ 和 $B$）時，如果我們對整個陣列進行全面 XOR，最終得到的結果會是：
$$\text{cur} = A \oplus B$$
由於 $A \neq B$，$\text{cur}$ 必定不為 $0$，這代表 $A$ 和 $B$ 的二進位表示中，**至少有一個位元是不相同的（一個是 0，一個是 1）**。

**核心破局點：利用 Lowbit 掩碼對陣列進行「細胞分裂」**
你的代碼採用了非常精妙的二分群策略：
1. **提取差異位元 (Lowbit)**：
   利用 `mask = cur & -cur` 提取出 $A \oplus B$ 最右側的第一個 `1`。這個 `1` 代表在這個特定的二進位位元上，$A$ 和 $B$ 其中一個數是 `1`，另一個是 `0`。
2. **條件分群**：
   依據 `num & mask` 是否為真，可以把原陣列的所有數字切分成兩大群。
   - 那些出現兩次的數字，無論分到哪一群，都一定會成對出現，並在群內 XOR 時自我消滅。
   - **關鍵是：$A$ 和 $B$ 必定會被無情地拆分到不同的群組。**
3. **收割答案**：
   我們用 `cur2` 統計其中一群的 XOR 總和，留下來的孤單數字就是 $A$（或 $B$）。而另一個數字則可以透過整體結果再次 XOR 輕鬆取回：`cur ^ cur2`。

> **⚠️ 關於 `unsigned int` 的極致細節：**
> 陣列中的元素可能包含 `INT_MIN`（$-2^{31}$）。在 C++ 中，對最小負數進行取負操作（`-cur`）會引發有號整數溢位（Signed Overflow），這屬於**未定義行為 (Undefined Behavior)**。你將變數宣告為 `unsigned int`，完美避開了負數邊界造成的編譯器行為風險，是非常嚴謹且專業的防禦性編程（Defensive Programming）。

---

### 🛠️ 解題思路 (Approach)
1. **第一次遍歷**：全面 XOR 得到 $A \oplus B$ 的混合同步值 `cur`。
2. **特徵提取**：藉由 `cur & -cur` 取出最低位的差異位元 `mask`。
3. **第二次遍歷**：
   - 遍歷整個 `nums` 陣列。
   - 利用 `if(num & mask)` 過濾出在該位元為 `1` 的子集，並用 `cur2` 進行局部 XOR 累加。
4. **輸出轉換**：回傳 `{cur2, cur ^ cur2}` 並強制轉換回原題目要求的 `int` 陣列。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。我們一共對 `nums` 陣列進行了兩次完整的線性掃描，整體時間複雜度與 $N$ 成完美線性關係。
- **空間複雜度**: $\mathcal{O}(1)$。只配置了常數個位元操作變數，沒有向系統申請與資料量相關的額外空間。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        // 1. 使用 unsigned int 避免 int 在處理負數反轉或負數邊界時發生有號整數溢位
        unsigned int cur = 0;
        for(auto num : nums) {
            cur ^= num; // 執行完畢後 cur = A ^ B
        }

        // 2. 提取 A 與 B 二進位表示中最右側第一個相異的位元 (Lowbit)
        unsigned int mask = cur & -cur;
        
        unsigned int cur2 = 0;
        // 3. 根據該相異位元，將陣列中的數字分為兩群進行過濾
        for(auto num : nums) {
            if(num & mask) {
                cur2 ^= num; // cur2 最終將會分離出其中一個只出現一次的獨立數字
            }
        }
        
        // 4. 另一個獨立數字可由全體結果 (A ^ B) ^ A = B 完美推導出來
        return {int(cur2), int(cur ^ cur2)};
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