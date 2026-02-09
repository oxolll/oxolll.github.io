---
date: 2026-01-23T04:27:18+08:00
draft: false
title: "🎓 碩士論文"
weight: 1
---

<style>
    :root {
        --bg-card: #1e1e1e;
        --border-color: #333;
        --text-main: #eee;
        --text-muted: #aaa;
        --accent-blue: #66d9ef;
        --accent-yellow: #e6db74;
        --accent-green: #a6e22e;
        --accent-pink: #f92672;
    }

    /* 通用容器 */
    .thesis-container {
        display: flex;
        flex-direction: column;
        gap: 25px;
        font-size: 16px;
        line-height: 1.6;
        color: var(--text-main);
    }

    /* 1. 標頭資訊區塊 */
    .thesis-header {
        display: flex;
        gap: 20px;
        align-items: flex-start;
        padding-bottom: 20px;
        border-bottom: 1px dashed #444;
    }
    .thesis-icon {
        width: 60px;
        height: 60px;
        flex-shrink: 0;
        border-radius: 8px;
        background-color: #fff; /* 白底讓圖示更清楚 */
        padding: 5px;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .thesis-title {
        font-size: 22px;
        font-weight: bold;
        color: #fff;
        margin-bottom: 5px;
        line-height: 1.4;
    }
    .thesis-title a {
        color: #fff;
        text-decoration: none;
    }
    .thesis-title a:hover {
        text-decoration: underline;
        color: var(--accent-blue);
    }
    .thesis-subtitle {
        font-size: 15px;
        color: var(--text-muted);
        font-style: italic;
        margin-bottom: 8px;
    }
    .thesis-meta {
        font-size: 14px;
        color: #888;
        display: flex;
        flex-wrap: wrap;
        gap: 10px;
        align-items: center;
    }
    .thesis-badge {
        display: inline-block;
        background-color: #333;
        color: var(--accent-yellow);
        padding: 2px 8px;
        border-radius: 4px;
        font-size: 12px;
        border: 1px solid var(--accent-yellow);
        margin-top: 5px;
    }

    /* 2. 內容卡片 (摘要 + 總結 + 表格) */
    .content-box {
        background-color: var(--bg-card);
        border: 1px solid var(--border-color);
        border-radius: 10px;
        padding: 30px;
        box-shadow: 0 4px 10px rgba(0,0,0,0.2);
    }

    /* 摘要標題 */
    .section-heading {
        font-size: 20px;
        font-weight: bold;
        color: #fff;
        margin-bottom: 15px;
        display: flex;
        align-items: center;
        gap: 10px;
        border-left: 4px solid var(--accent-blue);
        padding-left: 10px;
    }

    /* 總結與里程碑區塊 (Highlight Box) */
    .milestone-box {
        background-color: #252526;
        border: 1px solid #3e3e42;
        border-radius: 8px;
        overflow: hidden;
        margin: 30px 0;
        box-shadow: 0 4px 10px rgba(0,0,0,0.3);
    }
    .milestone-header {
        background-color: #333;
        padding: 10px 20px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        border-bottom: 1px solid #444;
    }
    .milestone-tag {
        background-color: #007acc;
        color: #fff;
        font-size: 11px;
        font-weight: bold;
        padding: 4px 10px;
        border-radius: 4px;
        letter-spacing: 0.5px;
    }
    .milestone-content {
        padding: 20px;
    }
    .contribution-list {
        background-color: #1e1e1e;
        border-radius: 6px;
        padding: 15px;
        border: 1px solid #333;
        margin-top: 15px;
    }

    /* 表格樣式 */
    .table-wrapper {
        overflow-x: auto; /* 讓手機版可以橫向捲動 */
        margin-bottom: 30px;
        border: 1px solid #444;
        border-radius: 6px;
    }
    .data-table {
        width: 100%;
        border-collapse: collapse;
        text-align: center;
        color: #ddd;
        font-size: 14px;
        table-layout: fixed; /* 👈 關鍵：固定表格佈局，讓上下表格寬度一致 */
    }
    .data-table th {
        background-color: #333;
        color: #fff;
        padding: 12px 5px; /* 稍微減少左右 padding 以防手機版太擠 */
        border-bottom: 1px solid #555;
        font-weight: bold;
        vertical-align: middle; /* 垂直置中 */
    }
    .data-table td {
        padding: 10px 5px;
        border-bottom: 1px solid #444;
        vertical-align: middle; /* 垂直置中 */
    }
    
    /* 👇 設定每個欄位的寬度比例，確保兩個表格對齊 👇 */
    /* 第 1 欄 (k) */
    .data-table th:nth-child(1) { width: 10%; } 
    /* 第 2 欄 (Enum) */
    .data-table th:nth-child(2) { width: 30%; }
    /* 第 3 欄 (NAE-Enum) */
    .data-table th:nth-child(3) { width: 30%; }
    /* 第 4 欄 (Lower Bound) */
    .data-table th:nth-child(4) { width: 30%; }

    .data-table tr:last-child td {
        border-bottom: none;
    }

    .table-caption {
        text-align: center;
        margin-bottom: 8px;
        color: #fff;
        font-weight: bold;
        font-size: 14px;
    }

    /* 連結樣式 */
    a.ref-link {
        color: var(--accent-blue);
        text-decoration: none;
        border-bottom: 1px dashed var(--accent-blue);
        transition: all 0.2s;
    }
    a.ref-link:hover {
        background-color: rgba(102, 217, 239, 0.1);
    }
</style>


<div class="thesis-container">
    <div class="thesis-header">
        <div class="thesis-icon">
            <img src="/images/thesis.png" width="40" height="40" alt="Thesis Icon">
        </div>
        <div style="flex: 1;">
            <div class="thesis-title">
                <a href="https://thesis.lib.nycu.edu.tw/items/f418cb06-72bf-43f9-ace0-bcfa5babf9cf" target="_blank">
                    三層電路下界之研究: 以多數決函數為例
                </a>
            </div>
            <div class="thesis-subtitle">
                Improved Depth-3 Circuit Lower Bounds for Majority with Local Enumeration
            </div>
            <div class="thesis-meta">
                <span><span style="color: var(--accent-blue);">Student:</span> Chen, Yu-Hung</span>
                <span>|</span>
                <span><span style="color: var(--accent-pink);">Advisor:</span> Dr. Tsai, Shi-Chun</span>
            </div>
            <span class="thesis-badge">🔒 論文保密中 / Embargoed</span>
        </div>
    </div>
    <div class="content-box">
        <div class="section-heading">📝 摘要 (Abstract)</div>
        <div style="margin-bottom: 30px; text-align: left;">
            &emsp;&emsp;$k$-SAT 的精確演算法與 Majority（多數決函數）等函數的電路下界之間的關係，是計算複雜度理論的一個核心研究領域。由 Gurumukhani 等人提出的『局部列舉範式』(local enumeration paradigm) 為推導電路下界提供了一個有前景的方法。他們定義了 $Enum(k,t)$ 問題：給定一個 $n$ 變數的 $k$-CNF 公式和一個初始指派 $\alpha$，要求輸出所有與 $\alpha$ 漢明距離 (Hamming distance) 為 $t$ 的滿足指派，並假設不存在與 $\alpha$ 漢明距離小於 $t$ 的滿足指派。他們證明了 $Enum(k,t)$ 複雜度的上界可以推導出 Majority 函數的深度為 3 的電路下界。
            <br><br>
            &emsp;&emsp;基於他們的工作，我們將他們的方法 <strong style="color: var(--accent-pink);">推廣到 $k>3$ 的情況</strong>。我們的框架為最困難的情況（$t=n/2$）證明了新的上界。在單調情況下，對於 $Enum(k,t)$ 問題，我們為 $k=4$ 和 $k=5$ 證明了新的上界，分別為 $O^*(1.7963276329^n)$ 與 $O^*(1.98671381726^n)$。對於 $NAEEnum(k,t)$，我們的分析為 $k=4$ 和 $k=5$ 確立了新的上界，分別為 $O^*(1.76460102831^n)$ 與 $O^*(1.95509096938^n)$。在一般情況下，我們推導出 $Enum(4,t)$ 的上界為 $O^*(1.80552514518^n)$。對於 $NAEEnum(k,t)$，我們得到了 $k=4$ 的上界 $O^*(1.77614766795^n)$ 以及 $k=5$ 的上界 $O^*(1.97227827013^n)$。作為直接的推論，我們推導出了更強的電路下界。對於單調情況，我們成功得出 $\Sigma_3^4(\mathrm{Maj}) \ge 1.11338263876^{n-o(n)}$ 和 $\Sigma_3^5(\mathrm{Maj}) \ge 1.00668751716^{n-o(n)}$；而在一般情況下，我們建立了 $\Sigma_3^4(\mathrm{Maj}) \ge 1.1077109645^{n-o(n)}$ 的下界。
        </div>
        <div class="milestone-box">
            <div class="milestone-header">
                <div style="color: var(--accent-green); font-weight: bold; font-size: 16px;">
                    🚀 總結與里程碑 (Summary)
                </div>
                <div class="milestone-tag">UNDER REVIEW: CCC</div>
            </div>
            <div class="milestone-content">
                <div style="margin-bottom: 15px;">
                    <div style="font-size: 13px; color: #888; margin-bottom: 5px;">📄 投稿論文標題 (Paper Title)</div>
                    <div style="color: #fff; font-size: 15px; font-weight: 600; padding-left: 10px; border-left: 3px solid var(--accent-blue);">
                        Depth-3 Circuit Lower Bounds for Majority with Local Enumeration
                    </div>
                </div>
                <div style="color: #ccc; font-size: 14px; margin-bottom: 15px;">
                    本研究曾投稿至 <strong style="color: #aaa;">ACM STOC</strong>，目前正於計算複雜度領域頂級會議 <strong style="color: var(--accent-blue);">CCC</strong> 進行審查。這顯示本研究持續接受該領域最高標準的檢驗，並具備挑戰核心難題的理論深度。
                </div>
                <div class="contribution-list">
                    <div style="color: var(--accent-yellow); font-weight: bold; font-size: 14px; margin-bottom: 8px;">
                        💡 具體貢獻 (Key Contributions):
                    </div>
                    <ul style="margin: 0; padding-left: 20px; color: #ccc; font-size: 14px; line-height: 1.7;">
                        <li>解決了 <a href="https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.CCC.2024.17" target="_blank" class="ref-link">[CCC'24] Local Enumeration</a> 提出的 $k>3$ 難題</li>
                        <li>解開了 <a href="https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.STACS.2025.42" target="_blank" class="ref-link">[STACS'25] Not-All-Equal Case</a> 提出的 $k>3$ 難題</li>
                        <li>給出了新的 <strong style="color: #fff;">多數決函數 (Majority) 的三層電路下界</strong></li>
                    </ul>
                </div>
            </div>
        </div>
        <div style="margin-top: 40px;">
    <div class="section-heading">📊 結果列表 (Results)</div>
        <div class="table-caption">Table 1: Summary of Algorithmic Upper Bounds and Resulting Circuit Lower Bounds.</div>
            <div class="table-wrapper">
                <table class="data-table">
                    <thead>
                        <tr>
                            <th>$\qquad k$</th>
                            <th>$\text{Enum}(k, n/2)$</th>
                            <th>$\text{NAE-Enum}(k, n/2)$</th>
                            <th>Lower Bound for $\Sigma_3^k(\text{Maj})$</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr><td><strong>3</strong></td><td>$O^*(1.59947866987^n)$</td><td>$O^*(1.57208202798^n)$</td><td>$1.25040742191^{n-o(n)}$</td></tr>
                        <tr><td><strong>4</strong></td><td>$O^*(1.7963276329^n)$</td><td>$O^*(1.76460102831^n)$</td><td>$1.11338263876^{n-o(n)}$</td></tr>
                        <tr><td><strong>5</strong></td><td>$O^*(1.98671381726^n)$</td><td>$O^*(1.95509096938^n)$</td><td>$1.00668751716^{n-o}$</td></tr>
                    </tbody>
                </table>
            </div>
            <div class="table-caption">Table 2: Summary for Arbitrary Case.</div>
                <div class="table-wrapper">
                    <table class="data-table">
                        <thead>
                            <tr>
                                <th>$\qquad k$</th>
                                <th>$\text{Enum}(k, n/2)$</th>
                                <th>$\text{NAE-Enum}(k, n/2)$</th>
                                <th>Lower Bound for $\Sigma_3^k(\text{Maj})$</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr><td><strong>3</strong></td><td>$O^*(1.60541280336^n)$</td><td>$O^*(1.58113883008^n)$</td><td>$1.24578550502^{n-o(n)}$</td></tr>
                            <tr><td><strong>4</strong></td><td>$O^*(1.80552514518^n)$</td><td>$O^*(1.77614766795^n)$</td><td>$1.1077109645^{n-o(n)}$</td></tr>
                            <tr><td><strong>5</strong></td><td>$-$</td><td>$O^*(1.97227827013^n)$</td><td>$-$</td></tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

<script>
    MathJax = {
      tex: {
        inlineMath: [['$', '$'], ['\\(', '\\)']],
        displayMath: [['$$','$$'], ['\\[', '\\]']],
        processEscapes: true,
        processEnvironments: true
      },
      options: {
        skipHtmlTags: ['script', 'noscript', 'style', 'textarea', 'pre']
      }
    };
</script>
<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>