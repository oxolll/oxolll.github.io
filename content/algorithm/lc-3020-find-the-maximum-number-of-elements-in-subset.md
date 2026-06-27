---
title: "LeetCode 3020. Find the Maximum Number of Elements in Subset"
date: 2026-06-27T14:04:00+08:00
lastmod: 2026-06-27T14:04:00+08:00
difficulty: 1741
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "hash-table", "math", "enumeration"]
keywords: ["LC3020", "Medium", "1741", "Maximum Number of Elements", "Subset", "雜湊表", "完全平方數", "逆向推導"]
description: "LeetCode 第 3020 題：Find the Maximum Number of Elements in Subset。難度評分：1741。探討如何利用雜湊表進行 O(N) 的線性頻率統計，並透過罕見的「由頂向下開根號」逆推思維，免疫溢位風險，完美找出最長的山峰指數序列。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3020](https://leetcode.com/problems/find-the-maximum-number-of-elements-in-subset/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1741)</span>  
> **核心主題**：`Hash Table` $\cdot$ `Math` $\cdot$ `Enumeration`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你一個正整數陣列 `nums`。
你需要從 `nums` 中選出一個子集，使得這個子集可以排列成以下的「山峰」模式：
$x, x^2, x^4, \dots, x^{k/2}, x^k, x^{k/2}, \dots, x^4, x^2, x$

其中 $k$ 是 2 的某個非負整數冪。換句話說，除了最大的那個山峰元素 $x^k$ 只需要出現 1 次之外，其餘所有的基底元素都必須至少出現 2 次。
（注意：長度為 1 的子集也是合法的，即只有一個元素）。

請你回傳能夠滿足上述條件的子集的 **最大元素個數**。

### 範例
- **Input**: `nums = [5,4,1,2,2]`
- **Output**: `3`
- **Explanation**: 
  我們可以選擇子集 `{2, 2, 4}`，將其排列為 `[2, 4, 2]`，符合山峰模式（其中 $x=2, x^2=4$）。最大長度為 3。

- **Input**: `nums = [1,3,2,4]`
- **Output**: `1`
- **Explanation**: 
  無法形成長度大於 1 的山峰模式，任意選擇一個數字構成長度為 1 的子集。

### 限制條件
- $2 \le nums.length \le 10^5$
- $1 \le nums[i] \le 10^9$
</details>

---

## 📝 歷次打卡與更新
- [2026-06-27：優化提交 (純 O(N) Hash Map 統計與逆向開根號推導)](#2026-06-27-優化提交)

---

## 💡 2026-06-27 優化提交

### 🎯 演算法分析 (Algorithm Analysis)
要構成題目要求的 $x, x^2, x^4, \dots, x^k, \dots, x^2, x$ 序列，本質上就是在尋找一條長度為奇數的序列。
除了最頂端的山峰 $x^k$ 只需要 1 個，其餘每一個下層的完全平方根都必須在陣列中至少存在 2 個。

**核心破局點 1：由頂向下的逆推 (Reverse Traceback)**
常規作法是枚舉 $x$ 然後不斷平方尋找 $x^2, x^4 \dots$，這容易遇到整數溢位 (Overflow) 的問題。
我們改採**逆向思維**：假設遍歷到的每一個元素 `num` 都是序列的最頂點（山峰），我們只需不斷對它**開根號**，檢查它的平方根是否存在且數量 $\ge 2$。
由於 $10^9$ 不斷開根號的深度極淺（$10^9 \to 31622 \to 177 \to 13 \to 3$），最大深度不超過 5，這種逆向推導能將內層迴圈的複雜度降至極致的 $\mathcal{O}(1)$，且完全無溢位風險。

**核心破局點 2：數字 1 的特判**
$1$ 的任何次方都是 $1$。如果陣列中有多個 $1$，它們可以自己組成一個超長的序列（例如 $1, 1, 1, 1, 1$）。
根據題目規則，山峰模式必須是對稱的，且只有最高點為 1 個，其餘皆為 2 個，這意味著 **合法序列的總長度必須是奇數**。
因此，若陣列中有 $c$ 個 $1$：
- 若 $c$ 為奇數，最大長度即為 $c$。
- 若 $c$ 為偶數，我們必須捨棄 1 個來滿足奇數長度條件，最大長度為 $c - 1$。
這可簡化為代數表達式：`mp[1] - (mp[1] % 2 == 0 ? 1 : 0)`。

### 🛠️ 解題思路 (Approach)
1. 宣告 `unordered_map<int, int> mp`，單次線性掃描 `nums` 來統計每個數字出現的頻率。
2. 預設答案 `rt = 1`（最少也能選 1 個數字）。
3. 遍歷 Hash Map 中的每一個鍵值對 `[num, cnt]`：
   - 若 `num == 1`，跳過不處理，留到最後特判。
   - 假設 `num` 為山峰，初始長度 `c = 1`，當前值 `x = num`。
   - 進入 `while` 迴圈檢查向下開根號的合法性：
     - 使用 `int(sqrt(x)) * int(sqrt(x)) == x` 嚴格判斷 `x` 是否為完全平方數。
     - 若是，將 `x` 更新為 `sqrt(x)`。
     - 檢查降級後的 `x` 是否在 Map 中，且其出現次數 `mp[x] >= 2`。如果不滿足則斷開迴圈。
     - 若滿足，則代表序列可以向左右各延伸 1 個單位，長度 `c += 2`。
   - 迴圈結束後，更新全域最大長度 `rt = max(rt, c)`。
4. 處理特判：將求得的 `rt` 與數字 1 能夠構成的最大合法長度取 `max`，回傳最終結果。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是陣列 `nums` 的長度。將元素存入 Hash Map 需要 $\mathcal{O}(N)$ 的預期時間。後續遍歷 Map 時，內層的 `while` 迴圈因為開根號的快速收斂，執行次數嚴格小於 5 次，視為 $\mathcal{O}(1)$ 的常數操作。整體為嚴格的線性時間。
- **空間複雜度**: $\mathcal{O}(N)$。最壞情況下所有數字皆不相同，Hash Map 需要儲存 $N$ 個不重複的鍵值對。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int maximumLength(vector<int>& nums) {
        unordered_map<int, int> mp;
        
        // 1. O(N) 線性掃描，統計各元素頻率
        for(auto num : nums) {
            ++mp[num];
        }
        
        int rt = 1;
        
        // 2. 遍歷 Map，將每個數字視為序列的「山峰」進行逆推
        for(auto [num, cnt] : mp) {
            // 數字 1 不會變大或變小，交由最後特判處理
            if(num == 1) continue;
            
            int c = 1;      // 山峰本身佔 1 個長度
            int x = num;    // 當前作為山峰的值
            
            // 判斷當前數字是否為完全平方數
            while(int(sqrt(x)) * int(sqrt(x)) == x) {
                x = sqrt(x); // 開根號尋找下一層基底
                
                // 若基底不存在，或基底數量不足 2 個，則無法繼續向下延伸
                if(mp.find(x) == mp.end() || mp[x] < 2) {
                    break;
                }
                
                // 找到一對合法的基底，序列長度加 2
                c += 2;
            }
            
            // 更新全域最大長度
            rt = max(rt, c);
        }

        // 3. 特判數字 1：數量必須為奇數才能構成合法序列
        return max(rt, mp[1] - (mp[1] % 2 == 0 ? 1 : 0));
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