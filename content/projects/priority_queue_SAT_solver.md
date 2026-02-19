---
date: 2026-01-23T04:27:46+08:00
draft: false
title: "基於 TreeSearch [Gur24] 架構之 PQ-based SAT 求解器"
categories: ["TCS & Research"]
tags: ["SAT Solver", "TreeSearch", "Algorithm", "Complexity", "Python"]
keywords: ["SAT", "Local Enumeration", "Positive Clause", "Empty Clause", "Gur24"]
weight: 2
---

<div style="background-color: var(--tertiary); padding: 16px 20px; border-radius: 8px; border-left: 5px solid #00b8a3; margin-bottom: 30px; display: flex; align-items: center; gap: 10px;">
  <span style="font-size: 1.2em;">🚀</span>
  <div>
    <strong style="display: block; font-size: 1.1em;">專案傳送門</strong>
    <a href="https://github.com/oxolll/priority-queue-sat-solver" target="_blank" style="text-decoration: none; color: #00b8a3; font-weight: bold;">前往 GitHub Repository 查看完整程式碼 ↗</a>
  </div>
</div>

## 1. 前言：為什麼不直接用 MiniSat？

在理論計算機科學 (TCS) 的研究中，我們經常面對 **SAT (布爾可滿足性問題)**。雖然工業界成熟的求解器（如 MiniSat, Kissat）採用 CDCL 架構，但在特定研究場景——如**尋找極小解 (Minimal Solutions)** 或**證明電路複雜度下界**時，傳統搜尋路徑未必最佳。

本專案基於 **Gur24 (Local enumeration and majority lower bounds)** 提出的 **TreeSearch** 演算法架構。我嘗試跳脫傳統 DPLL 的遞迴思維，改用 **優先佇列 (Priority Queue, PQ)** 來動態管理「正子句」的展開。這不僅是一個求解器，更是一個關於搜尋啟發式與局部列舉 (Local Enumeration) 的探索實驗。

## 2. 核心架構：正子句驅動的 TreeSearch

本演算法的核心是基於 **TreeSearch** 進行擴展。不同於一般將變數放入 PQ 的做法，本架構的核心在於對 **正子句 (Positive Clauses)** 的管理。

### 演算法流程圖
*(如果下方圖表未載入，請重新整理頁面)*

<div class="mermaid" style="background-color: var(--tertiary); padding: 20px; border-radius: 8px; margin: 15px auto 20px auto; max-width: 600px; text-align: center;">
graph TD
    A(["Start: Input CNF Formula"]) --> B(["Initialize PQ with all Positive Clauses"])
    B --> C{"PQ is Empty?"}
    C -- Yes --> D(["Final Return: SATISFIABLE"])
    C -- No --> E(["Pop Best Positive Clause"])
    E --> F(["Branching: Pick a Literal"])
    F --> G(["Assign True and Unit Propagation"])
    G --> H{"Empty Clause?"}
    H -- Yes --> I(["Path Conflict: Prune Branch"])
    I --> J{"All Literals exhausted?"}
    H -- No --> K{"PQ now Empty?"}
    K -- Yes --> L(["Final Return: SATISFIABLE"])
    K -- No --> M(["Recurse Search"])
    M --> J
    J -- No --> F
    J -- Yes --> N(["Final Return: UNSATISFIABLE"])
</div>

### 滿足與不滿足的嚴謹判定
本演算法對滿足性的判定完全遵循 TreeSearch 的理論邏輯：

* <span style="color: #00b8a3; font-weight: bold;">PQ 隨時為空即判定為滿足 (SAT)</span>：演算法維護一個存放「正子句」的優先佇列。如果在搜尋的任何階段，**PQ 變為空**，代表公式中剩餘的子句全部包含至少一個負文字。此時，只需將剩餘變數設為 `False`，即可滿足所有剩餘約束。
* <span style="color: #ff6b6b; font-weight: bold;">全路徑產生空子句判定為不滿足 (UNSAT)</span>：搜尋過程中，若某一賦值分支推導出 **空子句 (Empty Clause)**，代表該路徑發生衝突（Conflict），必須回溯。只有當 TreeSearch 窮舉了所有分枝，且**每一條可能的路徑最終都衍生出了空子句**時，才會判定為 **UNSAT**。

## 3. 核心邏輯概念展示 (Python)

```python
import heapq

def solve(self):
    while True:
        # [關鍵判定 1] 若正子句池為空，代表剩餘子句皆含負文字，直接滿足
        if not self.positive_clause_pq:
            self.assign_remaining_to_false()
            return "SAT"

        # 從 PQ 取出最具約束力的正子句進行 Local Enumeration
        target_clause = heapq.heappop(self.positive_clause_pq)
        
        # 針對子句中的變數進行分枝擴展 (Branching)
        for assignment in self.generate_branches(target_clause):
            status = self.unit_propagation(assignment)
            
            if status == "EMPTY_CLAUSE":
                continue # 路徑衝突，回溯
            elif status == "SUCCESS":
                if self.recursive_tree_search() == "SAT":
                    return "SAT"
        
        # 若所有分枝皆導致空子句衝突，則該子樹為 UNSAT
        return "UNSAT"
```

**💡 關鍵設計特點：**
1. **無深度限制**：原演算法用於解決特定深度，本專案擴展為全域搜尋策略。
2. **尋找極小解**：優先處理正子句並將餘項設為 `False` 的策略（Shoot），使 Solver 特別傾向於找到「極小解」，對電路複雜度分析極具價值。

## 4. 實驗結果與效能分析

我使用 **SATLIB** 標準測資進行了大量測試。下圖展示了不同測資下的求解效率：

<div align="center" style="margin: 25px 0;">
  <img src="/images/solver_cactus_plot_final.png" alt="Solver Performance Cactus Plot" style="border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); max-width: 90%;">
  <p style="color: var(--secondary); font-size: 0.9em; margin-top: 10px;">▲ 效能表現 (Cactus Plot)</p>
</div>

* **一般實驗結果**：在處理 50 變數的測資時，大部分 SAT 案例能在極短時間內觸發「PQ 為空」的滿足條件。
* **僅找一解之結果 (v3.1)**：針對極小解優化的 v3.1 版本，在鎖定第一個解的效率上，PQ 的引導能顯著減少不必要的搜尋開銷。

## 5. 如何使用 (Usage)

<div style="background-color: var(--tertiary); padding: 15px; border-radius: 8px;">
<strong>1. 準備環境：</strong>
<pre style="margin-top: 8px; margin-bottom: 15px;"><code>git clone https://github.com/oxolll/priority-queue-sat-solver.git
cd priority-queue-sat-solver
unzip benchmarks.zip</code></pre>

<strong>2. 執行求解：</strong>
<pre style="margin-top: 8px; margin-bottom: 0;"><code>python main2.py benchmarks/uf20-01.cnf</code></pre>
</div>

## 6. 結語

本專案證明了以 **TreeSearch** 為骨架，並配合 **正子句優先佇列** 管理的架構，能有效地處理 $k$-SAT 問題。這不僅在初始滿足性判定上具有效率，更在理論層面提供了對於 `UNSAT` 證明的嚴謹框架。

---
*📁 演算法架構 (PPT)：請至專案 GitHub 的 `docs/pq_base.pptx` 下載。*

<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: true, theme: 'dark' });
</script>
<script>
    MathJax = {
      tex: { inlineMath: [['$', '$'], ['\\(', '\\)']], displayMath: [['$$','$$'], ['\\[', '\\]']], processEscapes: true },
      options: { skipHtmlTags: ['script', 'noscript', 'style', 'textarea', 'pre'] }
    };
</script>
<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>