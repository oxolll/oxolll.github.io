---
date: 2026-01-23T04:27:18+08:00
draft: false
title: "🎓 碩士論文"
weight: 1
# categories: [""]
# tags: ["posts"]
# _build:
#     list: never
#     render: always
---

<div style="display: flex; flex-direction: column; gap: 15px;">
    <div style="display: flex; align-items: flex-start;"> 
        <img src="/images/thesis.png" width="50" height="50" style="margin-right: 15px; flex-shrink: 0; margin-top: 5px;">
        <div style="display: flex; flex-direction: column; gap: 4px;">            
            <div style="font-size: 20px; font-weight: bold; color: #fff;">
                <a href="https://thesis.lib.nycu.edu.tw/items/f418cb06-72bf-43f9-ace0-bcfa5babf9cf" style="color: #fff; font-weight: bold;">
                    三層電路下界之研究: 以多數決函數為例
                </a> 
            </div>
            <div style="font-size: 15px; color: #aaa; font-style: italic;">
                Improved Depth-3 Circuit Lower Bounds for Majority with Local Enumeration
            </div>
            <div style="font-size: 14px; color: #888; margin-top: 5px;">
                <span style="color: #66d9ef;">Student:</span> Chen, Yu-Hung 
                &nbsp;|&nbsp; <span style="color: #f92672;">Advisor:</span> Dr. Tsai, Shi-Chun
            </div>
            <div style="margin-top: 5px;">
                <span style="background-color: #333; color: #e6db74; padding: 2px 8px; border-radius: 4px; font-size: 12px; border: 1px solid #e6db74;">
                    🔒 論文保密中 / Embargoed
                </span>
            </div>
        </div>
    </div>
    <div style="display: flex; flex-direction: column; border: 2px dashed #aaa; border-radius: 10px; padding: 30px 60px 6px 60px; background-color: #1e1e1e; margin-bottom: 20px;">
        <div style="display: flex; flex-direction: column; gap: 20px; width: 100%;">
            <div style="font-size: 20px; font-weight: bold; color: #fff; display: flex; align-items: center;">
                &ensp;📝 摘要:
            </div>
            <div style="font-size: 16px; color: #eee7e7; margin-bottom: 5px;">
                &emsp;&emsp;$k$-SAT 的精確演算法與 Majority（多數決函數）等函數的電路下界之間的關係，是計算複雜度理論的一個核心研究領域。由 Gurumukhani 等人提出的『局部列舉範式』(local enumeration paradigm) 為推導電路下界提供了一個有前景的方法。他們定義了 $Enum(k,t)$ 問題：給定一個 $n$ 變數的 $k$-CNF 公式和一個初始指派 $\alpha$，要求輸出所有與 $\alpha$ 漢明距離 (Hamming distance) 為 $t$ 的滿足指派，並假設不存在與 $\alpha$ 漢明距離小於 $t$ 的滿足指派。他們證明了 $Enum(k,t)$ 複雜度的上界可以推導出 Majority 函數的深度為 3 的電路下界。
                <br>
                &emsp;&emsp;基於他們的工作，我們將他們的方法${\color{red}推廣到 k>3 的情況}$。我們的框架為最困難的情況（$t=n/2$）證明了新的上界。在單調情況下，對於 $Enum(k,t)$ 問題，我們為 $k=4$ 和 $k=5$ 證明了新的上界，分別為 $O^*(1.7963276329^n)$ 與 $O^*(1.9867138$$1726^n)$。對於 $NAEEnum(k,t)$，我們的分析為 $k=4$ 和 $k=5$ 確立了新的上界，分別為 $O^*(1.76460102831^n)$ 與 $O^*(1.95509096938^n)$。在一般情況下，我們推導出 $Enum(4,t)$ 的上界為 $O^*(1.805$\\$52514518^n)$。對於 $NAEEnum(k,t)$，我們得到了 $k=4$ 的上界 $O^*(1.77614766795^n)$ 以及 $k=5$ 的上界 $O^*(1.97227827013^n)$。作為直接的推論，我們推導出了更強的電路下界。對於單調情況，我們成功得出 $\Sigma_3^4(\mathrm{Maj}) \ge 1.11338263876^{n-o(n)}$ 和 $\Sigma_3^5(\mathrm{Maj}) \ge 1.00668751716^{n-o(n)}$；而在一般情況下，我們建立了 $\Sigma_3^4(\mathrm{Maj}) \ge 1.1077109645^{n-o(n)}$ 的下界。
                <br>
                <div style="background-color: #1e1e1e; border-left: 5px solid #a6e22e; padding: 15px 20px; border-radius: 4px; margin-top: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.3);">
                    <div style="font-size: 13px; line-height: 1.8; color: #f8f8f2;">
                        <strong style="color: #a6e22e; font-size: 18px;">🚀 總結來說：</strong>
                        <br>
                        我們解決了 
                        <a href="https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.CCC.2024.17" target="_blank" style="color: #66d9ef; text-decoration: underline; font-weight: bold;">
                            [Local Enumeration and Majority Lower Bounds]
                        </a> 
                        所提出的 $k>3$ 的問題，
                        <br>
                        也解開了 
                        <a href="https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.STACS.2025.42" target="_blank" style="color: #66d9ef; text-decoration: underline; font-weight: bold;">
                            [Local Enumeration: The Not-All-Equal Case]
                        </a> 
                        所提出的 $k>3$ 的問題，
                        <br>
                        並給出了新的 <strong style="color: #e6db74;">多數決函數的三層電路下界！</strong>
                    </div>
                </div>
            </div>
            <div style="text-align: center; margin-bottom: 10px; color: #fffefe; font-weight: bold;">
                Table 1: Summary of Algorithmic Upper Bounds and Resulting Circuit Lower Bounds.
            </div>
            <div style="overflow-x: auto; width: 100%;"> 
                <table style="width: 100%; border-collapse: collapse; text-align: center; color: #ddd; border: 1px solid #555; margin-bottom: 0px;">
                    <thead>
                        <tr style="background-color: #333; color: #fff;">
                            <th style="padding: 12px; border: 1px solid #555;">$\quad k$</th>
                            <th style="padding: 12px; border: 1px solid #555;">Bound for $\text{Enum}(k, n/2)$</th>
                            <th style="padding: 12px; border: 1px solid #555;">Bound for $\text{NAE-Enum}(k, n/2)$</th>
                            <th style="padding: 12px; border: 1px solid #555;">Lower Bound for $\Sigma_3^k(\text{Maj})$</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td style="padding: 10px; border: 1px solid #555;"><strong>3</strong></td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.59947866987^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.57208202798^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$1.25040742191^{n-o(n)}$</td>
                        </tr>
                        <tr>
                            <td style="padding: 10px; border: 1px solid #555;"><strong>4</strong></td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.7963276329^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.76460102831^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$1.11338263876^{n-o(n)}$</td>
                        </tr>
                        <tr>
                            <td style="padding: 10px; border: 1px solid #555;"><strong>5</strong></td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.98671381726^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.95509096938^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$1.00668751716^{n-o}$</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div style="text-align: center; margin-bottom: 10px; color: #faf9f9; font-weight: bold;">
                Table 2: Summary of Algorithmic Upper Bounds and Resulting Circuit Lower Bounds for Arbitrary Case.
            </div>
            <div style="overflow-x: auto; width: 100%;"> 
                <table style="width: 100%; border-collapse: collapse; text-align: center; color: #ddd; border: 1px solid #555; margin-bottom: 0px;">
                    <thead>
                        <tr style="background-color: #333; color: #fff;">
                            <th style="padding: 12px; border: 1px solid #555;">$\quad k$</th>
                            <th style="padding: 12px; border: 1px solid #555;">Bound for $\text{Enum}(k, n/2)$</th>
                            <th style="padding: 12px; border: 1px solid #555;">Bound for $\text{NAE-Enum}(k, n/2)$</th>
                            <th style="padding: 12px; border: 1px solid #555;">Lower Bound for $\Sigma_3^k(\text{Maj})$</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td style="padding: 10px; border: 1px solid #555;"><strong>3</strong></td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.60541280336^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.58113883008^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$1.24578550502^{n-o(n)}$</td>
                        </tr>
                        <tr>
                            <td style="padding: 10px; border: 1px solid #555;"><strong>4</strong></td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.80552514518^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.77614766795^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$1.1077109645^{n-o(n)}$</td>
                        </tr>
                        <tr>
                            <td style="padding: 10px; border: 1px solid #555;"><strong>5</strong></td>
                            <td style="padding: 10px; border: 1px solid #555;">$-$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$O^*(1.97227827013^n)$</td>
                            <td style="padding: 10px; border: 1px solid #555;">$-$</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</div>


<!-- | $k$ | Bound for $\text{Enum}(k, n/2)$ | Bound for $\text{NAE-Enum}(k, n/2)$ | Lower Bound for $\Sigma_3^k(\text{Maj})$ |
| :---: | :---: | :---: | :---: |
| **3** | $O^*(1.60541280336^n)$ | $O^*(1.58113883008^n)$ | $1.24578550502^{n-o(n)}$ |
| **4** | $O^*(1.80552514518^n)$ | $O^*(1.77614766795^n)$ | $1.1077109645^{n-o(n)}$ |
| **5** | $-$ | $O^*(1.97227827013^n)$ | $-$ | -->

<br>
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