---
title: "LeetCode 2075. Decode the Slanted Ciphertext"
date: 2026-04-07T14:30:00+08:00
lastmod: 2026-04-07T14:30:00+08:00
difficulty: 1759
draft: false
hidden: false
# --- 目錄設定 ---
ShowToc: true
TocOpen: false
# --- 數學公式設定 ---
math: true
categories: ["algorithm"]
tags: ["leetcode", "string", "simulation", "math"]
keywords: ["LC2075", "1759", "Medium", "Slanted Ciphertext", "Matrix", "Simulation"]
description: "LeetCode 第 2075 題：Decode the Slanted Ciphertext。難度評分：1759。探討如何利用數學公式扣除矩陣左下角的無效區域，並透過 1D 至 2D 索引映射精準且高效地模擬對角線字串解碼。"
---

## 📊 題目資訊
> **題目連結**：[LeetCode 2075](https://leetcode.com/problems/decode-the-slanted-ciphertext/) *(註：題號對應依據實際題庫)*
> **難度評分**：<span style="color: #ffc01e; font-weight: bold;">Medium (1759)</span>  
> **核心主題**：`String` $\cdot$ `Simulation` $\cdot$ `Math`

---

## 📖 題目描述
<details>
<summary>🔍 <b>點擊展開題目詳情與限制</b></summary>

### 說明
一個字串 `originalText` 透過一種特殊的方式進行編碼，得到字串 `encodedText`。
編碼過程如下：
1. 給定一個整數 `rows`，將字串放入一個擁有 `rows` 列的二維矩陣中。字串是沿著**對角線**從左上到右下填入的。
2. 當一條對角線填滿或到達矩陣邊界時，從下一條對角線的起點（第一列的下一個欄位）繼續填入。
3. 矩陣中未被填入字元的部分將被補上空格 `' '`。
4. 最後，逐列 (row by row) 讀取矩陣中的字元，組合成一維字串 `encodedText`。

給定編碼後的字串 `encodedText` 與列數 `rows`，請回傳解碼後的原始字串 `originalText`。
注意：`originalText` 結尾不包含任何尾隨空格 (Trailing spaces)。

### 限制條件
- $0 \le encodedText.length \le 10^6$
- `encodedText` 僅包含小寫英文字母與空格。
- `encodedText` 是一個有效的編碼字串。
- $1 \le rows \le 1000$
</details>

---

## 📝 歷次打卡與更新
- [2026-04-07：初次提交 (數學邊界優化與對角線模擬)](#2026-04-07-初次提交)

---

## 💡 2026-04-07 初次提交

### 🎯 演算法分析 (Algorithm Analysis)
要解碼這個字串，我們必須反向模擬編碼過程：在概念上將長度為 $N$ 的一維字串視為 $Rows \times Cols$ 的二維矩陣，並沿著對角線讀取字元。
- 矩陣的總欄數為 `cols = n / rows`。
- 一維字串到二維矩陣的索引映射公式為：`1D_Index = i * cols + j`。
- 對角線的移動規律為：從 `(0, pre)` 出發，每次向右下移動一步至 `(i+1, j+1)`，直到觸及矩陣的底部 (`i == rows-1`) 或右側邊界 (`j == cols-1`)。隨後開啟下一條對角線 `(0, pre+1)`。

**數學最佳化**：
由於對角線總是向右下方延伸，矩陣的「左下角」必然是一個完全空白的無效區域。這個直角三角形的元素數量恰好為 $1 + 2 + \dots + (rows-1) = \frac{rows \times (rows-1)}{2}$。
我們可以直接利用 `loop = n - rows * (rows - 1) / 2` 算出**絕對有效**的字元總數。這讓我們可以直接使用一個 `while(loop--)` 迴圈來控制讀取次數，完全省去對無效區域的存取判斷。

### 🛠️ 解題思路 (Approach)
1. **空字串防呆**：若 `encodedText` 為空，直接回傳 `""`。
2. **計算矩陣維度與迴圈次數**：
   - 取得字串總長度 `n`。
   - 計算總欄數 `cols = n / rows`。
   - 計算需要讀取的有效字元總數 `loop = n - rows * (rows - 1) / 2`。
3. **對角線走訪模擬**：
   - 宣告 `pre = 0` 記錄當前對角線在第一列的起始欄位。
   - 宣告 `i = 0`, `j = 0` 為當前讀取座標。
   - 執行 `while(loop--)` 迴圈：
     - 將當前座標對應的一維索引 `i * cols + j` 的字元加入結果字串 `rt`。
     - 若觸及底部或右側邊界 (`i == rows-1 || j == cols-1`)：代表該對角線結束。重置座標準備進入下一條對角線：將起點右移 (`++pre`)，將 `j` 重置為 `pre`，並將 `i` 設為 `-1` (因為後續會統一執行 `++i`)。
     - 座標向右下推進：`++i`, `++j`。
4. **去除尾隨空格**：
   - 依據題意，原始字串不包含尾隨空格。
   - 從結果字串尾端向前尋找第一個非空格的字元索引 `i`。
   - 利用 `rt.substr(0, i + 1)` 擷取有效部分並回傳。

### 📊 複雜度分析
- **時間複雜度**: $\mathcal{O}(N)$。其中 $N$ 是 `encodedText` 的長度。我們只遍歷了有效的對角線元素，並在最後執行了一次 $\mathcal{O}(N)$ 的尾隨空格裁剪。
- **空間複雜度**: $\mathcal{O}(N)$。宣告了一個字串 `rt` 用於儲存解碼後的結果。

### 💻 程式碼實作 (C++)
```cpp
class Solution {
public:
    string decodeCiphertext(string encodedText, int rows) {
        if(encodedText.empty()) return "";
        
        int n = encodedText.size();
        int cols = n / rows;
        string rt = "";
        
        int pre = 0; // 記錄下一條對角線的起始欄位
        int i = 0, j = 0;
        
        // 數學最佳化：扣除左下角必定為空的三角形區域，取得有效字元總數
        int loop = n - rows * (rows - 1) / 2;
        
        while(loop--) {
            // 一維陣列模擬二維矩陣存取
            rt += encodedText[i * cols + j];
            
            // 若觸及底邊或右邊，移動至下一條對角線起點
            if(i == rows - 1 || j == cols - 1) {
                ++pre;
                j = pre;
                i = -1; // 配合下方的 ++i，使其回到第 0 列
            }
            
            // 向右下方移動
            ++i; 
            ++j;
        }

        // 去除還原過程可能產生的尾隨空格 (Trailing spaces)
        i = rt.size() - 1;
        while(i >= 0 && rt[i] == ' ') {
            --i;
        }
        
        return rt.substr(0, i + 1);
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