---
title: "LeetCode 2540. Minimum Common Value"
date: 2026-05-19T13:30:00+08:00
lastmod: 2026-05-19T13:30:00+08:00
difficulty: 1249
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "array", "two-pointers", "binary-search"]
keywords: ["LC2540", "Easy", "1249", "Minimum Common Value", "雙指標", "有序陣列"]
description: "LeetCode 第 2540 題：Minimum Common Value。難度評分：1249。探討如何利用兩個陣列皆為嚴格非遞減有序的特徵，使用雙指標在線性時間內尋找最小的公共元素。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2540](https://leetcode.com/problems/minimum-common-value/)  
> **難度評分**：<span style="color: #00b8a3; font-weight: bold;">Easy (1249)</span>  
> **核心主題**：`Array` $\cdot$ `Two Pointers` $\cdot$ `Binary Search`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給你兩個整數陣列 `nums1` 和 `nums2`，它們都已經按 **非遞減順序** 排好序。
請你傳回兩個陣列中最小的 **公共整數**。如果不存在公共整數，請傳回 `-1`。

若一個整數在 `nums1` 和 `nums2` 中都至少出現一次，則稱這個整數是兩個陣列的「公共整數」。

### 範例
- **Input**: `nums1 = [1,2,3], nums2 = [2,4]`
- **Output**: `2`
- **Explanation**: 兩個陣列的最小公共元素是 2。

- **Input**: `nums1 = [1,2,3,6], nums2 = [2,3,4,5]`
- **Output**: `2`

### 限制條件
- $1 \le nums1.length, nums2.length \le 10^5$
- $1 \le nums1[i], nums2[j] \le 10^9$
- `nums1` 和 `nums2` 都是按 **非遞減順序** 排列的。
</details>

---

## 📝 歷次打卡與更新
- [2026-05-19：初次提交 (有序陣列雙指標法)](#2026-05-19-初次提交)

---

## 💡 2026-05-19 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
這題的關鍵突破點在於題目給定的核心性質：**兩個輸入陣列都已經是有序的 (Sorted)**。
因為陣列是由小到大排列，如果我們希望找到「最小」的公共元素，只要**從兩個陣列的開頭開始掃描**，第一個碰到的相同數字就一定是全域最小的公共公共值。

**核心破局點：雙指標同步追趕**
你的解法非常漂亮地捕捉到了這個單調性。我們配置兩個指標 `i` 與 `j` 分別指向兩個陣列。
- 若 `nums1[i] > nums2[j]`：說明 `nums2[j]` 太小了，它不可能在 `nums1` 當前或往後的區間找到伴侶，所以應將較小的指標推進 `++j`。
- 若 `nums1[i] < nums2[j]`：同理，`nums1[i]` 太小了，不可能在 `nums2` 後續中配對，應前進 `++i`。
- 若 `nums1[i] == nums2[j]`：找到目標，立即退出。

你的程式碼在進入 `while` 的下一步就進行了越界檢查：
```cpp
if (i == nums1.size() || j == nums2.size()) return -1;
```
這使得只要其中一個指標走完陣列長度，就能安全且及時地中斷並判定為無解。

---

### 🛠️ 解題思路 (Approach)
1. **初始化**：雙指標 `i = 0`, `j = 0`。
2. **條件比對**：當 `nums1[i] != nums2[j]` 時執行循環：
   - 數值較小的那一方指標向前推動一步。
   - 隨後立即檢查 `i` 或 `j` 是否已跨越陣列邊界，若是則代表完全沒有重疊交集，回傳 `-1`。
3. **返回解**：若跳出迴圈，代表此時 `nums1[i] == nums2[j]` 成立，該值即為最小公共值。

---

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N + M)$。其中 $N$ 是 `nums1` 的長度，$M$ 是 `nums2` 的長度。在最壞情況下，兩個指標會各自從頭走到尾，總共執行不超過 $N + M$ 次比較，屬於標準的線性時間。
- **空間複雜度**: $\mathcal{O}(1)$。只使用了兩個整數指標變數，沒有產生任何額外的記憶體耗費。

---

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int getCommon(vector<int>& nums1, vector<int>& nums2) {
        int i = 0, j = 0;
        
        // 1. 利用雙指標在有序數軸上進行追趕
        while (nums1[i] != nums2[j]) {
            // 誰的數值小，誰就向前推動，試圖逼近對方的數值
            if (nums1[i] > nums2[j]) {
                ++j;
            } else {
                ++i;
            }

            // 2. 邊界防禦：只要任一指標超出陣列範圍，說明已無交集可能
            if (i == nums1.size() || j == nums2.size()) {
                return -1;
            }
        }

        // 3. 跳出迴圈時，nums1[i] 必定等於 nums2[j]，且此值為最早遇見的最小公共值
        return nums1[i];
    }
};
```

---

### 💡 延伸思維：面試常見的二分搜尋優化
雖然雙指標法是本題最對稱、在 $N \approx M$ 時效率最高的解法。但如果面試官改變了數據規模限制，例如：**`nums1.length` 非常小（如 10），而 `nums2.length` 非常大（如 $10^6$）**。

此時雙指標需要掃描長度為 $10^6$ 的大陣列，時間會逼近 $\mathcal{O}(M)$。此時更好的作法是：**遍歷較小的 `nums1`，並在超大陣列 `nums2` 中對每個元素進行 `std::binary_search`**。這樣時間複雜度會優化成 $\mathcal{O}(N \log M)$，在不對稱測資下效率更為驚人！

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