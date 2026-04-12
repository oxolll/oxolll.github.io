---
title: "LeetCode 3900. Longest Balanced Substring After One Swap"
date: 2026-04-13T10:00:00+08:00
lastmod: 2026-04-13T10:00:00+08:00
difficulty: 1800
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "hash-table", "prefix-sum", "math"]
keywords: ["LC3900", "Medium", "N/A", "Longest Balanced Substring", "Prefix Sum", "Hash Map"]
description: "LeetCode 第 3900 題：Longest Balanced Substring After One Swap。難度評分：Medium (N/A)。探討如何利用前綴和 (Prefix Sum) 搭配 Hash Map，透過數學推導找出差值為 2 或 -2 的潛在區間，並利用雙重索引紀錄 (First & Second) 完美處理外部替換字元的邊界條件，以 O(N) 時間複雜度求得一次交換後的最長平衡子字串。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 3900](https://leetcode.com/problems/longest-balanced-substring-after-one-swap/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (N/A)</span>  
> **核心主題**：`String` $\cdot$ `Hash Table` $\cdot$ `Prefix Sum` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
給定一個僅由字元 `'0'` 與 `'1'` 組成的字串 `s`。
你可以選擇字串中的**最多兩個**不同位置的字元進行**恰好一次**交換 (Swap)。
所謂的「平衡子字串 (Balanced Substring)」是指在該連續子字串中，字元 `'0'` 的數量與字元 `'1'` 的數量完全相同。
請回傳在允許執行最多一次交換操作後，字串 `s` 中可能出現的**最長平衡子字串**的長度。

### 限制條件
- $1 \le s.length \le 10^5$
- `s[i]` 僅為 `'0'` 或 `'1'`。
</details>

---

## 📝 歷次打卡與更新
- [2026-04-13：初次提交 (前綴和與雙重 Hash Map 邊界驗證)](#2026-04-13-初次提交)

---

## 💡 2026-04-13 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
尋找「0 與 1 數量相等的子字串」標準做法是**前綴和 (Prefix Sum)**：將 `'0'` 視為 $-1$，`'1'` 視為 $+1$。當兩個索引 $i$ 與 $j$ 的前綴和相等 ($Sum_i == Sum_j$)，代表區間 $(j, i]$ 內的和為 $0$，即這是一個平衡子字串。

**加入「一次交換」條件後的推導：**
1. **無須交換**：區間和為 $0$，直接記錄最長距離 `i - first[sum]`。
2. **交換一個 `'0'` 與一個 `'1'`**：
   - 假設我們找了一個區間，其前綴和差值為 $2$ (代表區間內多了兩個 `'1'`？不，因為 `'1'` 是 $+1$，`'0'` 是 $-1$，差值為 $2$ 代表區間內 `'1'` 的數量比 `'0'` 多 $1$ 個。例如 110，總和是 +1，但如果是偶數長度，必然是多了 1 個 '1' 少了 1 個 '0'，使得總和偏差為 2)。
   - 將區間內的一個 `'1'` 與區間外的一個 `'0'` 交換後，區間內的 $+1$ 變成 $-1$，總和剛好減少 $2$，進而歸零達成平衡。
   - 同理，若前綴和差值為 $-2$，代表區間內多了一個 `'0'` 少了一個 `'1'`，需要從外部換一個 `'1'` 進來。
3. **外部字元存量驗證 (Availability Check)**：
   - 如果區間總和為 $2$，且長度為 $l$。解聯立方程式：$ones + zeros = l$ 且 $ones - zeros = 2$ $\Rightarrow 2 \times zeros = l - 2 \Rightarrow zeros = \frac{l - 2}{2}$。
   - 區間內有 $\frac{l - 2}{2}$ 個 `'0'`。為了能執行交換，**字串的其餘部分必須至少有一個 `'0'`**。因此，必須滿足 `全域 0 數量 > 區間內 0 數量`，即 `cnt0 > (l - 2) / 2`。
4. **備用索引用途 (`second` Map)**：
   - 如果最長的區間剛好涵蓋了全域所有的 `'0'` (即外部沒有 `'0'` 可以換)，這個區間就是無效的。
   - 為了不漏掉最佳解，我們必須退一步尋找「第二長」的區間。因此，我們使用 `first` 與 `second` 兩個 Hash Map 來分別記錄某個前綴和「第一次」與「第二次」出現的位置。

### 🛠️ 解題思路 (Approach)
1. 走訪字串，計算全域的 `'0'` 數量 (`cnt0`) 與 `'1'` 數量 (`cnt1`)。
2. 宣告 `first` 與 `second` Hash Map。初始邊界 `first[0] = -1`。
3. 走訪字串，維護前綴和 `sum`：
   - 遇到 `'0'` 則 `--sum`，遇到 `'1'` 則 `++sum`。
   - **檢查和為 0 (無須交換)**：若 `first[sum]` 存在，更新最大長度 `rt = max(rt, i - first[sum])`。
   - **檢查和為 2 (內部多 '1'，需外部 '0')**：找尋 `first[sum - 2]`。若找到，計算長度 `l`。若 `(l - 2) / 2 < cnt0`，代表外部有 `'0'`，更新 `rt`；若外部無 `'0'`，則檢查 `second[sum - 2]` 是否能構成有效解。
   - **檢查和為 -2 (內部多 '0'，需外部 '1')**：找尋 `first[sum + 2]`。若找到，計算長度 `l`。若 `(l - 2) / 2 < cnt1`，代表外部有 `'1'`，更新 `rt`；否則檢查 `second[sum + 2]`。
4. 將當前的 `sum` 記錄至 Hash Map 中。若 `first` 尚未記錄則寫入 `first`，若已記錄但在 `second` 中沒有，則寫入 `second`。
5. 回傳 `rt`。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 為字串長度。只需走訪字串兩次（一次統計全域數量，一次維護前綴和），Hash Map 操作皆為 $\mathcal{O}(1)$。
- **空間複雜度**: $\mathcal{O}(N)$。最差情況下，前綴和可能產生 $N$ 種不同的數值，Hash Map 需儲存這些鍵值對。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    int longestBalanced(string s) {
        int n = s.size();
        int cnt0 = 0, cnt1 = 0;
        
        // 1. 預先計算全域的 0 與 1 數量，做為後續外部交換的存量驗證
        for(auto c : s) {
            if(c == '0') ++cnt0;
            else ++cnt1;
        }

        // 記錄某個前綴和「最早」與「次早」出現的索引
        unordered_map<int, int> first;
        unordered_map<int, int> second;
        first[0] = -1; // 初始邊界
        
        int rt = 0, sum = 0;
        for(int i = 0; i < n; ++i) {
            // 0 視為 -1, 1 視為 +1
            if(s[i] == '0') --sum;
            else ++sum;

            // 情況 A：區間總和為 0，無需交換本身即為平衡子字串
            if(first.find(sum) != first.end()) {
                rt = max(rt, i - first[sum]);
            }

            // 情況 B：區間總和差值為 2 (內部多 1 少 0，需從外部換一個 0 進來)
            if(first.find(sum - 2) != first.end()) {
                int l = i - first[sum - 2];
                // 驗證外部是否有 0 可用 (全域 0 數量 > 區間內 0 數量)
                if((l - 2) / 2 < cnt0) {
                    rt = max(rt, l);
                } 
                // 若外部無 0，退而求其次嘗試次長的區間
                else if(second.find(sum - 2) != second.end()) {
                    rt = max(rt, i - second[sum - 2]);
                }
            }

            // 情況 C：區間總和差值為 -2 (內部多 0 少 1，需從外部換一個 1 進來)
            if(first.find(sum + 2) != first.end()) {
                int l = i - first[sum + 2];
                // 驗證外部是否有 1 可用
                if((l - 2) / 2 < cnt1) {
                    rt = max(rt, l);
                } 
                else if(second.find(sum + 2) != second.end()) {
                    rt = max(rt, i - second[sum + 2]);
                }
            }

            // 維護 Hash Map，確保保留最早與次早的索引以獲得最大長度
            if(first.find(sum) == first.end()) {
                first[sum] = i;
            } else if(second.find(sum) == second.end()) {
                second[sum] = i;
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