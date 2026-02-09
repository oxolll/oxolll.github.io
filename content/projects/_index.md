---
title: "🗂️ 專案與實驗列表"
date: 2026-01-21T17:08:19+08:00
draft: false 
hidden: true
ShowToc: false
tags: ["profile"]
_build:
    list: never
    render: always
---

<style>
    :root {
        --accent-blue: #66d9ef;
        --accent-yellow: #e6db74;
        --text-muted: #aaa;
    }

    /* 簡介文字容器 */
    .intro-container {
        font-size: 16px;
        line-height: 1.7;
        color: #eee;
        margin-bottom: 30px; /* 與下方文章列表保持適當距離 */
    }

    /* CCC 提醒區塊 (改為深色低調風格) */
    .notice-block {
        margin-top: 15px;
        padding: 12px 15px;
        background-color: #252526;
        border-left: 4px solid var(--accent-blue);
        border-radius: 4px;
        font-size: 14px;
        color: #ccc;
    }
    
    .notice-tag {
        display: inline-block;
        background-color: #1e3a5f;
        color: var(--accent-blue);
        font-size: 12px;
        padding: 2px 6px;
        border-radius: 4px;
        font-weight: bold;
        margin-right: 6px;
        vertical-align: middle;
    }
</style>

<div class="intro-container">
    <p>
        這裡主要收錄我在碩士期間的兩類實作紀錄：
    </p>
    <ul style="margin: 0; padding-left: 20px; color: #ddd;">
        <li><strong>💻 修課實作 (Coursework)：</strong> 包含軟體測試、密碼學等課程的小型專案或作業紀錄。</li>
        <li><strong>🧪 研究實驗 (Research Experiments)：</strong> 論文研讀過程中的演算法驗證與探索性實驗。</li>
    </ul>
    <div class="notice-block">
        <span class="notice-tag">UNDER REVIEW: CCC</span>
        由於碩士論文核心成果正於 <strong>CCC (Computational Complexity Conference)</strong> 審查中，基於學術規範，部分實驗僅呈現結果摘要，完整證明細節將於論文發表後補上。
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