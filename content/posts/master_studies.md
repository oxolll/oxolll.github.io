---
date: 2026-01-23T04:12:37+08:00
draft: false
title: "🎓 碩士研究簡介"
categories: ["TCS & Research"]
tags: ["Complexity", "Circuit", "Research", "Master"]
keywords: ["三層電路下界", "Majority", "Local Enumeration", "計算複雜度", "陽明交大"]
weight: 2
ShowToc: false
---

<style>
    :root {
        --accent-blue: #66d9ef;
        --accent-yellow: #e6db74;
        --accent-green: #a6e22e;
        --accent-pink: #f92672;
        --text-muted: #aaa;
        --border-color: #3e3e42;
    }

    .page-intro {
        font-size: 16px;
        line-height: 1.8;
        color: #eee;
        margin-bottom: 40px;
        background-color: #252526;
        padding: 20px;
        border-radius: 8px;
        border-left: 5px solid var(--accent-blue);
    }

    /* 時間軸主線 */
    .timeline {
        position: relative;
        padding-left: 30px;
        border-left: 2px solid #333;
        margin-left: 10px;
        padding-bottom: 20px;
    }

    /* 論文卡片 */
    .paper-card {
        position: relative;
        background-color: #1e1e1e;
        border: 1px solid var(--border-color);
        border-radius: 6px;
        padding: 15px;
        margin-bottom: 20px;
        transition: transform 0.2s, border-color 0.2s;
    }
    .paper-card:hover {
        border-color: var(--accent-blue);
        transform: translateX(5px);
        background-color: #262629;
    }

    /* 時間軸的小圓點 */
    .paper-card::before {
        content: "";
        position: absolute;
        left: -37px; /* 調整圓點位置以對齊線條 */
        top: 20px;
        width: 12px;
        height: 12px;
        border-radius: 50%;
        background-color: #333;
        border: 2px solid var(--accent-blue);
        z-index: 1;
    }

    /* 論文連結 */
    .paper-link {
        color: var(--accent-blue);
        font-weight: 600;
        text-decoration: none;
        font-size: 16px;
        display: block;
        margin-bottom: 6px;
        line-height: 1.4;
    }
    .paper-link:hover {
        text-decoration: underline;
    }
    
    /* 作者與年份 */
    .paper-meta {
        font-size: 13px;
        color: #888;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
    .paper-year {
        color: var(--accent-yellow);
        font-weight: bold;
        background-color: #333;
        padding: 1px 6px;
        border-radius: 4px;
        font-size: 12px;
    }

    /* 實驗區塊 (插入在時間軸中) */
    .exp-block {
        position: relative;
        margin-bottom: 40px;
        background-color: rgba(102, 217, 239, 0.1); 
        border: 1px dashed var(--accent-blue);
        padding: 15px;
        border-radius: 6px;
        color: #ccc;
        font-size: 14px;
        line-height: 1.6;
    }
    .exp-block::before {
        content: "🧪"; /* 實驗圖示 */
        position: absolute;
        left: -44px;
        top: 15px;
        width: 24px;
        height: 24px;
        text-align: center;
        background-color: #1e1e1e;
        border: 1px solid #333;
        border-radius: 50%;
        font-size: 14px;
        line-height: 24px;
        z-index: 2;
    }

    /* 核心論文高亮 */
    .highlight-card {
        border-left: 3px solid var(--accent-pink);
        background-color: rgba(249, 38, 114, 0.05);
    }
    .highlight-card::before {
        background-color: var(--accent-pink);
        border-color: var(--accent-pink);
    }
</style>

<div class="page-intro">
    碩士後半段時間，我專注於研讀論文與進行實驗。
    <br>
    以下依照<strong>閱讀時間先後順序 (Timeline)</strong> 列出我所研讀的關鍵文獻，並穿插說明在此期間我所進行的相關實驗。從基礎的 SAT Solver 實作，一路延伸至電路複雜度 (Circuit Complexity) 的前沿研究。
</div>

<div class="timeline">
    <div class="paper-card">
        <a href="/papers/Dancing_Links/" class="paper-link">Dancing Links (DLX)</a>
        <div class="paper-meta">Donald E. Knuth <span class="paper-year">2000</span></div>
    </div>
    <div class="exp-block">
        <strong style="color: #fff;">🧪 實驗紀錄：</strong><br>
        閱讀完 Knuth 的論文後，我使用 C++ 實作了 DLX 演算法與基礎 SAT Solver。這是我踏入 SAT 問題研究的第一步，建立了解題器的基礎架構。
    </div>
    <div class="paper-card">
        <a href="/papers/Depth_Two_(n-2)_Majority_Circuits_for_n_Majority/" class="paper-link">Depth Two $(n-2)$-Majority Circuits for $n$-Majority</a>
        <div class="paper-meta">Kazuyuki Amano et al. <span class="paper-year">2018</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Short_monotone_formulae_for_the_majority_function/" class="paper-link">Short monotone formulae for the majority function</a>
        <div class="paper-meta">L. G. Valiant <span class="paper-year">1984</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Analysis_of_boolean_functions/" class="paper-link">Analysis of Boolean Functions (Textbook)</a>
        <div class="paper-meta">Ryan O'Donnell <span class="paper-year">2014</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Depth_2_threshold_circuits/" class="paper-link">Depth-$2$ threshold circuits</a>
        <div class="paper-meta">Meena Mahajan <span class="paper-year">2019</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/The_average_sensitivity_of_bounded_depth_circuits/" class="paper-link">The average sensitivity of bounded-depth circuits</a>
        <div class="paper-meta">Ravi B. Boppana <span class="paper-year">1997</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Satisfiability_coding_lemma/" class="paper-link">Satisfiability coding lemma</a>
        <div class="paper-meta">Ramamohan Paturi et al. <span class="paper-year">1997</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Depth_two_majority_circuits_for_majority_and_list_expanders/" class="paper-link">Depth two majority circuits for majority and list expanders</a>
        <div class="paper-meta">Kazuyuki Amano <span class="paper-year">2018</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Explicit_construction_of_a_small_epsilon_net_for_linear_threshold_functions/" class="paper-link">Explicit construction of a small $\epsilon$-net</a>
        <div class="paper-meta">Rabani & Shpilka <span class="paper-year">2009</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Existence_and_polynomial_time_construction_of_biregular_bipartite_Ramanujan_graphs_of_all_degrees/" class="paper-link">Existence and polynomial time construction of Ramanujan graphs</a>
        <div class="paper-meta">Gribinski & Marcus <span class="paper-year">2021</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Interlacing_families_IV_Bipartite_Ramanujan_graphs_of_all_sizes/" class="paper-link">Interlacing families IV: Bipartite Ramanujan graphs</a>
        <div class="paper-meta">Marcus, Spielman & Srivastava <span class="paper-year">2018</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/New_explicit_constant_degree_lossless_expanders/" class="paper-link">New explicit constant-degree lossless expanders</a>
        <div class="paper-meta">Louis Golowich <span class="paper-year">2024</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/Depth_three_circuits_for_inner_product_and_majority_functions/" class="paper-link">Depth-three circuits for inner product and majority functions</a>
        <div class="paper-meta">Kazuyuki Amano <span class="paper-year">2023</span></div>
    </div>
    <div class="paper-card highlight-card">
        <a href="/papers/Local_enumeration_and_majority_lower_bounds/" class="paper-link">🔥 Local enumeration and majority lower bounds</a>
        <div class="paper-meta">Gurumukhani et al. <span class="paper-year">2024</span></div>
        <div style="font-size: 12px; color: #ddd; margin-top: 5px; border-top: 1px dashed #444; padding-top: 5px;">
            * 碩論關鍵啟發：提出 Local Enumeration 架構，解決了 $k=3$ 的下界問題。
        </div>
    </div>
    <div class="paper-card">
        <a href="/papers/Circuits_with_medium_fan_in/" class="paper-link">Circuits with medium fan-in</a>
        <div class="paper-meta">Pavel Hrubeš & Anup Rao <span class="paper-year">2015</span></div>
    </div>
    <div class="paper-card">
        <a href="/papers/CNF_Encodings_of_Symmetric_Functions/" class="paper-link">CNF Encodings of Symmetric Functions</a>
        <div class="paper-meta">Gregory Emdin et al. <span class="paper-year">2024</span></div>
    </div>
    <div class="paper-card highlight-card">
        <a href="/papers/Local_Enumeration__The_Not_All_Equal_Case/" class="paper-link">🔥 Local Enumeration: The Not-All-Equal Case</a>
        <div class="paper-meta">Gurumukhani et al. <span class="paper-year">2025</span></div>
    </div>
    <div class="exp-block" style="border-color: var(--accent-pink); background-color: rgba(249, 38, 114, 0.1);">
        <strong style="color: #fff;">🧪 碩士論文實驗：</strong><br>
        基於上述兩篇 Local Enumeration 論文，我撰寫程式模擬 $k=4, 5$ 的情形，分析 Hamming Distance 分布。實驗數據與理論推導吻合，最終成功將下界推廣至 $k>3$ 並投稿至 CCC。
    </div>

</div>

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