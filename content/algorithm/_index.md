---
title: "⚔️ 競程與演算法實作"
description: "收錄 LeetCode、Codeforces、AtCoder 解題紀錄與演算法模板。"
date: 2026-02-10
draft: false
layout: "list"
---

<style>
    /* =========================================
       1. 背景設定 (維持你喜歡的細緻網格)
       ========================================= */
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

    /* =========================================
       2. 平台卡片設計 (重點美化區)
       ========================================= */
    
    /* 容器：強制三欄均分 */
    .platform-grid {
        display: grid;
        grid-template-columns: repeat(3, 1fr); /* 強制三等份 */
        gap: 20px;
        margin-top: 30px;
        margin-bottom: 50px;
    }

    /* RWD: 手機版自動變直排，不然會太擠 */
    @media (max-width: 768px) {
        .platform-grid {
            grid-template-columns: 1fr;
        }
    }

    /* 卡片本體：精緻科技風 */
    .platform-card {
        /* 深色漸層背景，增加立體感 */
        background: linear-gradient(145deg, #252529 0%, #1a1a1c 100%);
        border: 1px solid #333;
        border-radius: 12px;
        padding: 18px 20px;
        
        display: flex;
        align-items: center; /* 垂直置中 */
        gap: 15px;           /* 圖文間距 */
        
        text-decoration: none;
        position: relative;
        overflow: hidden;
        transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
        
        /* 預設給一個微弱的底部光條 */
        box-shadow: 0 4px 6px rgba(0,0,0,0.3);
    }

    /* 懸停特效：卡片上浮 + 邊框發光 + 陰影加深 */
    .platform-card:hover {
        transform: translateY(-4px);
        background: linear-gradient(145deg, #2a2a2e 0%, #202022 100%);
    }

    /* --- 針對不同品牌的配色 --- */

    /* LeetCode (黃) */
    .platform-card.lc:hover {
        border-color: rgba(255, 161, 22, 0.6);
        box-shadow: 0 8px 20px rgba(255, 161, 22, 0.15); /* 黃色光暈 */
    }
    .platform-card.lc .p-name { color: #ffa116; }

    /* Codeforces (紅) */
    .platform-card.cf:hover {
        border-color: rgba(220, 53, 69, 0.6);
        box-shadow: 0 8px 20px rgba(220, 53, 69, 0.15); /* 紅色光暈 */
    }
    .platform-card.cf .p-name { color: #dc3545; }

    /* AtCoder (白) */
    .platform-card.at:hover {
        border-color: rgba(255, 255, 255, 0.5);
        box-shadow: 0 8px 20px rgba(255, 255, 255, 0.1); /* 白色光暈 */
    }
    .platform-card.at .p-name { color: #eee; }


    /* Logo 容器 */
    .p-logo {
        width: 48px;
        height: 48px;
        background-color: #fff; /* 白底讓 logo 更跳 */
        border-radius: 10px;    /* 稍微圓潤一點 */
        padding: 6px;
        display: flex;
        align-items: center;
        justify-content: center;
        flex-shrink: 0;         /* 防止 Logo 被壓扁 */
        box-shadow: 0 2px 5px rgba(0,0,0,0.2);
    }
    .p-logo img {
        width: 100%;
        height: 100%;
        object-fit: contain;
    }

    /* 文字區塊 */
    .p-info {
        display: flex;
        flex-direction: column;
        justify-content: center;
    }
    .p-name {
        font-weight: 800;
        font-size: 1.1rem;
        line-height: 1.2;
        margin-bottom: 4px;
        transition: color 0.3s;
    }
    .p-desc {
        font-size: 13px;
        color: #888;
        line-height: 1.3;
        font-weight: 400;
    }
    /* 懸停時讓描述文字稍微變亮 */
    .platform-card:hover .p-desc {
        color: #bbb;
    }


    /* =========================================
       3. 其他樣式 (標題、Filter)
       ========================================= */
    :root {
        --accent-blue: #66d9ef;
    }

    .post-title {
        position: relative;
        display: inline-block;
        margin-bottom: 20px;
    }
    .post-title::after {
        content: "";
        position: absolute;
        bottom: -5px;
        left: 0;
        width: 60px;
        height: 4px;
        background: linear-gradient(90deg, var(--accent-blue), transparent);
        border-radius: 2px;
    }

    .topic-filter-bar {
        background-color: transparent; /* 透明背景 */
        border: none;                  /* 移除邊框 */
        backdrop-filter: none;         /* 移除毛玻璃 */
        padding: 0;                    /* 移除內距 */
        border-radius: 0;

        display: flex;
        flex-wrap: wrap;
        gap: 10px;
        margin-bottom: 40px;
    }
    
    .filter-label {
        width: 100%;
        font-size: 14px;
        color: #fff;
        font-weight: bold;
        margin-bottom: 10px;
        display: flex;
        align-items: center;
        gap: 5px;
    }

    .mini-tag {
        font-size: 13px;
        color: #ccc;
        text-decoration: none;
        padding: 6px 14px;
        border-radius: 20px;
        border: 1px solid #444;
        transition: all 0.2s;
        background-color: #1e1e1e;
    }
    .mini-tag:hover {
        color: #111;
        border-color: #66d9ef;
        background-color: #66d9ef;
    }

    /* 👇 新增：讓這裡的文章卡片變成「駭客綠」 */
    /* 修改 index.md 內的這段 */
    .post-entry:hover {
        /* 將原本的 rgba(166, 226, 46, ...) 改成 var(--diff-color) */
        border-color: var(--diff-color) !important; 
        box-shadow: 0 8px 25px var(--diff-color) !important;
        opacity: 0.9;

        /* 卡片上浮感 */
        transform: translateY(-6px) !important;
        transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
    }

    .post-entry:hover .entry-hint-parent span {
        color: var(--diff-color) !important;
        text-shadow: 0 0 10px var(--diff-color);
    }
</style>

<p style="color: #aaa; margin-top: 5px; font-size: 1rem;">
    這裡是我鍛鍊演算法思維的競技場。收錄了各大平台的解題紀錄、比賽檢討 (Post-contest Analysis) 以及常用的演算法模板。
</p>

<div class="platform-grid">
    <a href="https://leetcode.com/u/oxolll/" target="_blank" class="platform-card lc">
        <div class="p-logo">
            <img src="/images/leetcode2.png" alt="LeetCode">
        </div>
        <div class="p-info">
            <span class="p-name">LeetCode</span>
            <span class="p-desc">面試高頻題庫</span>
        </div>
    </a>
    <a href="https://codeforces.com/profile/oxolll58x85" target="_blank" class="platform-card cf">
        <div class="p-logo">
            <img src="/images/codeforces.png" alt="Codeforces">
        </div>
        <div class="p-info">
            <span class="p-name">Codeforces</span>
            <span class="p-desc">高強度線上競技</span>
        </div>
    </a>
    <a href="https://atcoder.jp/users/oxolll" target="_blank" class="platform-card at">
        <div class="p-logo">
            <img src="/images/atcoder.png" alt="AtCoder">
        </div>
        <div class="p-info">
            <span class="p-name">AtCoder</span>
            <span class="p-desc">數學與構造題</span>
        </div>
    </a>

</div>

<div class="topic-filter-bar">
    <div class="filter-label">
        <span>🧭 快速篩選 (Tags):</span>
    </div> 
    <a href="/tags/leetcode/" class="mini-tag">Leetcode</a>
    <a href="/tags/codeforce/" class="mini-tag">Codeforces</a>
    <a href="/tags/atcoder/" class="mini-tag">AtCoder</a>
    <a href="/tags/dynamic-programming/" class="mini-tag">Dynamic Programming</a>
    <a href="/tags/graph/" class="mini-tag">Graph</a>
    <a href="/tags/math/" class="mini-tag">Math</a>
    <a href="/tags/segment-tree/" class="mini-tag">Segment Tree</a>
    <a href="/tags/constructive/" class="mini-tag">Constructive</a>
    <a href="/tags/greedy/" class="mini-tag">Greedy</a>
    <a href="/tags/binary-search/" class="mini-tag">Binary Search</a>
    <a href="/tags/two-pointers/" class="mini-tag">Two Pointers</a>
    <a href="/tags/simulation/" class="mini-tag">Simulation</a>
    <a href="/tags/string/" class="mini-tag">String</a>
    <a href="/tags/prefix-sum/" class="mini-tag">Prefix Sum</a>
    <a href="/tags/hash-table/" class="mini-tag">Hash Table</a>
    <a href="/tags/case-analysis/" class="mini-tag">Case Analysis</a>
    <a href="/tags/binary-indexed-tree-fenwick-tree/" class="mini-tag">Binary Indexed Tree (Fenwick Tree)</a>
    <a href="/tags/design/" class="mini-tag">design</a>
    <a href="/tags/counting/" class="mini-tag">counting</a>
    <a href="/tags/segment-tree/" class="mini-tag">Segment Tree</a>
    <a href="/tags/bit-manipulation/" class="mini-tag">Bit Manipulation</a>
    <a href="/tags/enumeration/" class="mini-tag">Enumeration</a>
    <a href="/tags/divide-and-conquer/" class="mini-tag">Divide and Conquer</a>
    <a href="/tags/array/" class="mini-tag">Array</a>
</div>

<div style="text-align: center; color: #666; font-size: 13px; margin-bottom: 20px;">
    👇 最新發布的題解 (Latest Solutions)
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