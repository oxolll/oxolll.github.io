---
date: '2026-01-23T03:59:26+08:00'
draft: false
title: '📚 文章與技術筆記'
---

<style>
    html, body {
        background-color: #111 !important;
        background-image: 
            linear-gradient(rgba(255, 255, 255, 0.1) 1px, transparent 1px),
            linear-gradient(90deg, rgba(255, 255, 255, 0.1) 1px, transparent 1px) !important;
        background-size: 40px 40px !important;
        background-attachment: fixed !important; 
        min-height: 100vh;
    }

    .main {
        background: transparent !important;
    }

    :root {
        --accent-blue: #66d9ef;
        --accent-yellow: #e6db74;
        --accent-pink: #f92672;
        --accent-green: #a6e22e;
        --text-muted: #aaa;
    }

    .intro-text {
        font-size: 16px;
        line-height: 1.8;
        color: #eee;
        margin-bottom: 30px;
    }

    /* 強調連結樣式 */
    a.highlight-link {
        color: var(--accent-green);
        text-decoration: none;
        font-weight: bold;
        border-bottom: 1px dashed var(--accent-green);
        transition: all 0.2s;
    }
    a.highlight-link:hover {
        background-color: rgba(166, 226, 46, 0.1);
    }

    /* 分類卡片容器 */
    .category-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
        gap: 20px;
        margin-top: 20px;
        margin-bottom: 40px;
    }

    /* 分類小卡 */
    .cat-card {
        background-color: #252526;
        border: 1px solid #3e3e42;
        padding: 20px;
        border-radius: 8px;
        transition: transform 0.2s, border-color 0.2s;
    }
    .cat-card:hover {
        transform: translateY(-3px);
        border-color: var(--accent-blue);
    }
    .cat-title {
        font-weight: bold;
        font-size: 18px;
        color: #fff;
        margin-bottom: 10px;
        display: flex;
        align-items: center;
        gap: 8px;
    }
    .cat-desc {
        font-size: 14px;
        color: var(--text-muted);
        line-height: 1.6;
    }

    /* 裝飾線條 */
    .divider {
        height: 1px;
        background: linear-gradient(90deg, #333 0%, #66d9ef 50%, #333 100%);
        margin: 30px 0;
        opacity: 0.5;
    }
    /* 針對這個頁面的卡片，強制改用「琥珀金」配色 */
    
    /* Hover 時的邊框與光暈 */
    .post-entry:hover {
        /* 琥珀金邊框 */
        border-color: rgba(255, 184, 108, 0.9) !important; 
        
        /* 溫暖的金色光暈 */
        box-shadow: 0 8px 30px rgba(255, 184, 108, 0.25) !important;
        
        transform: translateY(-4px) !important;
    }

    /* 標題文字也變金色 */
    .post-entry:hover .entry-title a {
        color: #ffb86c !important; /* Pastel Orange */
    }

    /* (選用) 讓專案卡片比文章卡片稍微大一點，更有份量感 */
    .post-entry {
        border-width: 1px;
    }
</style>

<div class="intro-text">
    <p>
        歡迎來到我的知識庫。這裡收錄了我在碩士生涯與技術探索過程中的<strong>深度筆記</strong>與<strong>心得文章</strong>。
    </p>
    <p>
        這裡的文章不同於 <a href="/algorithm/" class="highlight-link">💻 解題區</a> 的「單題實作紀錄」，而是更著重於 <strong>「演算法背後的理論脈絡」</strong>、<strong>「技術難點的解決思路」</strong> 以及 <strong>「系統性的學習筆記」</strong>。內容主要涵蓋以下三大領域：
    </p>
</div>

<div class="category-grid">
    <div class="cat-card">
        <div class="cat-title" style="color: var(--accent-pink);">
            📖 論文研讀 & TCS
        </div>
        <div class="cat-desc">
            收錄計算複雜度 (Complexity Theory)、演算法下界 (Lower Bounds) 相關的論文導讀，以及 $k$-SAT、ETH 等理論概念的白話文解析。
        </div>
    </div>
    <div class="cat-card">
        <div class="cat-title" style="color: var(--accent-blue);">
            🧠 演算法筆記 (Algo Notes)
        </div>
        <div class="cat-desc">
            針對特定的資料結構與演算法進行深度解析 (如 KMP 原理、紅黑樹機制)。
            <br>
            <span style="font-size: 12px; color: #888;">* 若尋找特定的刷題代碼與模板，請前往 <a href="/algorithm/" style="color: #888; text-decoration: underline;">LeetCode 解題區</a>。</span>
        </div>
    </div>
    <div class="cat-card">
        <div class="cat-title" style="color: var(--accent-yellow);">
            🎓 碩士修業 & 心得
        </div>
        <div class="cat-desc">
            記錄從找指導教授、修課策略、論文寫作到口試答辯的心路歷程。這裡有我在陽明交大這幾年最真實的生存筆記。
        </div>
    </div>

</div>

<div class="divider"></div>

<div style="text-align: center; color: #888; font-size: 14px; margin-bottom: 20px;">
    👇 以下為最新發布的文章 (Latest Posts) 👇
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