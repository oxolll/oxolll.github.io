---
title: "LeetCode 1980. Find Unique Binary String"
date: 2026-03-09T12:11:00+08:00
lastmod: 2026-03-09T12:11:00+08:00
difficulty: 1361
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["Algorithm"]
tags: ["LeetCode", "Array", "String", "Bit Manipulation", "Math", "Cantor's Diagonalization"]
keywords: ["LC1980", "Medium", "Binary String", "Hash Array", "Cantor"]
description: "LeetCode 第 1980 題：Find Unique Binary String。難度評分：Medium。探討如何利用資料規模特性進行整數映射，以及運用康托爾對角線構造法以 O(N) 時間複雜度得出最佳解。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 1980](https://leetcode.com/problems/find-unique-binary-string/)  
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium(1361)</span>  
> **核心主題**：`Array` $\cdot$ `String` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個長度為 $n$ 的字串陣列 `nums`，其中每個字串的長度皆為 $n$，且每個字串都由字元 `'0'` 和 `'1'` 組成。
陣列中的字串皆**互不相同**。

請回傳一個長度為 $n$ 的二進位字串，該字串**不存在**於 `nums` 之中。如果有多個符合條件的答案，回傳其中任意一個即可。

### 範例
- **Input**: `nums = ["01","10"]` → **Output**: `"11"` (或 `"00"`)
- **Input**: `nums = ["111","011","001"]` → **Output**: `"101"` (其他有效答案亦可)

### 限制條件
- $n == nums.length$
- $1 \le n \le 16$
- $nums[i].length == n$
- `nums[i]` 僅包含 `'0'` 或 `'1'`。
- `nums` 中的所有字串互不相同。
</details>

---

## 📝 歷次打卡與更新
- [2026-03-09：初次提交 (整數映射與康托爾對角線最佳解)](#2026-03-09-初次提交)

---

## 💡 2026-03-09 初次提交

### 🎯 直覺 (Intuition)
這題有兩種截然不同的破局維度：
1. **工程維度 (資料規模判定)**：觀察到 $n \le 16$，代表總組合數僅有 $2^{16} = 65536$。與其在字串上做複雜操作，不如直接轉成十進位整數，利用 Hash Array 紀錄並找出缺失值。
2. **數學維度 (康托爾對角線)**：既然有 $n$ 個字串，每個字串長度也是 $n$。如果我們構造一個新字串，讓它的第 $i$ 個字元「刻意與 `nums[i]` 的第 $i$ 個字元相反」，那麼這個新字串絕對不可能與 `nums` 中的任何一個字串相同（因為它至少在第 $i$ 個位置必定與 `nums[i]` 不同）。

### 🛠️ 解題思路 (Approach)

#### 方法一：整數轉換與陣列映射 (常規直覺解)
1. 宣告一個大小為 $2^n$ 的布林陣列 `existed`。
2. 遍歷 `nums`，利用位元左移 (`<< 1`) 將字串轉換為十進位整數，並在 `existed` 中打勾。
3. 從 $0$ 開始向上尋找第一個 `!existed[i]` 的數字。
4. 將找到的數字還原為二進位字串，並在左側補齊不足的 `'0'`。

#### 方法二：康托爾對角線構造法 (最佳解)
1. 宣告一個空字串 `ans`。
2. 遍歷索引 `i` 從 $0$ 到 $n-1$。
3. 檢查 `nums[i][i]` 的字元：如果是 `'0'`，就在 `ans` 加上 `'1'`；如果是 `'1'`，就在 `ans` 加上 `'0'`。
4. 由於 `ans` 的第 $i$ 位永遠跟第 $i$ 個字串的第 $i$ 位不同，保證 `ans` 絕對不在 `nums` 裡，構造完畢直接回傳。

### 📊 複雜度分析
- **方法一 (整數映射)**：
  - **時間複雜度**: $\mathcal{O}(N^2 + 2^N)$。字串轉整數需 $\mathcal{O}(N^2)$，尋找缺失值最差需 $\mathcal{O}(2^N)$。
  - **空間複雜度**: $\mathcal{O}(2^N)$。需開闢大小為 65536 的布林陣列。
- **方法二 (對角線構造)**：
  - **時間複雜度**: $\mathcal{O}(N)$。只需跑一次長度為 $N$ 的迴圈，每次只檢查一個字元。
  - **空間複雜度**: $\mathcal{O}(1)$。除回傳字串外，不需要額外空間。

---

### 💻 程式碼實作 (C++)

#### 版本一：康托爾對角線構造法 (最佳解)
短短幾行程式碼，完美展現了數學思維的降維打擊。
```cpp
class Solution {
public:
    string findDifferentBinaryString(vector<string>& nums) {
        string ans = "";
        int n = nums.size();
        
        // 對角線反轉構造
        for (int i = 0; i < n; ++i) {
            // 如果 nums[i] 的第 i 個字元是 '0'，我們就放 '1'，反之亦然
            ans += (nums[i][i] == '0' ? '1' : '0');
        }
        
        return ans;
    }
};
```

#### 版本二：整數轉換與陣列映射 (使用者直覺解)
穩定、防呆、利用資料規模限制暴力破解的工程實用寫法。
```cpp
class Solution {
public:
    string findDifferentBinaryString(vector<string>& nums) {
        int n = nums.size();
        vector<bool> existed(1 << n, 0);
        
        // 1. 將字串轉為整數並記錄
        for(auto num : nums) {
            int x = 0;
            for(int i = 0; i < n; ++i) {
                x <<= 1;
                if(num[i] == '1') {
                    x += 1;
                }
            }
            existed[x] = true;
        }
        
        // 2. 尋找缺失數字並轉回字串
        for(int i = 0; i < (1 << n); ++i) {
            if(!existed[i]) {
                string rt = "";
                int j = i;
                while(j) {
                    rt = char((j & 1) + '0') + rt;
                    j >>= 1;
                }
                
                while(rt.size() != n) {
                    rt = "0" + rt;
                }
                return rt;
            }
        }
        return "";
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