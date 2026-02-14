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
    /* ========================================================
       1. 基礎背景與字體設定 (維持原樣)
       ======================================================== */
    html, body {
        background-color: #111 !important;
        background-image: 
            linear-gradient(rgba(255, 255, 255, 0.1) 1px, transparent 1px),
            linear-gradient(90deg, rgba(255, 255, 255, 0.1) 1px, transparent 1px) !important;
        background-size: 40px 40px !important;
        background-attachment: fixed !important; 
        min-height: 100vh;
    }

    .main { background: transparent !important; }
    
    :root {
        --accent-blue: #66d9ef; /* 電光青 */
        --accent-yellow: #e6db74;
        --text-muted: #aaa;
    }

    /* ========================================================
       2. 專案頁面專屬：電光青光暈大典 (Project Glow)
       ======================================================== */

    /* 強制讓此頁面的所有卡片套用青色光暈 */
    .post-entry:hover {
        /* 1. 邊框全亮 */
        border-color: var(--accent-blue) !important; 
        
        /* 2. 🔥 雙層霓虹光暈 (光暈大典規格) */
        box-shadow: 
            0 0 20px rgba(102, 217, 239, 0.7),    /* 內層核心 */
            0 15px 60px rgba(102, 217, 239, 0.4)  /* 外層擴散 */
            !important;
        
        /* 3. 強烈上浮感 */
        transform: translateY(-6px) scale(1.01) !important;
        z-index: 10;
        transition: all 0.4s cubic-bezier(0.25, 0.8, 0.25, 1);
    }

    /* 4. 標題文字同步變色 */
    .post-entry:hover .entry-hint-parent span {
        color: #e0faff !important; /* 極淺青色 */
        text-shadow: 0 0 15px var(--accent-blue);
    }

    /* 5. 摘要與評分文字微調 */
    .post-entry:hover .entry-content {
        color: #eee !important;
    }

    /* ========================================================
       3. 其他元件樣式 (原本的內容)
       ======================================================== */
    .intro-container { font-size: 16px; line-height: 1.7; color: #eee; margin-bottom: 30px; }
    .notice-block {
        margin-top: 15px; padding: 12px 15px; background-color: #252526;
        border-left: 4px solid var(--accent-blue); border-radius: 4px;
        font-size: 14px; color: #ccc;
    }
    .notice-tag {
        display: inline-block; background-color: #1e3a5f; color: var(--accent-blue);
        font-size: 12px; padding: 2px 6px; border-radius: 4px; font-weight: bold; margin-right: 6px;
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