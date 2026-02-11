---
date: 2026-01-23T04:28:06+08:00
draft: false
title: "🧟 軟體測試-2D遊戲"
categories: ["Life & Misc"]
tags: ["Software Testing", "Portfolio", "Master"]
keywords: ["Pygame", "Unit Testing", "CACC", "軟體測試", "碩士紀錄"]
weight: 3
ShowToc: false       # 👈 關鍵 1：這行設為 false 就能關掉上方的目錄
# _build:
#     list: never
#     render: always
---

<style><style>
    .project-container {
        background-color: #1e1e1e;
        border: 1px solid #333;
        border-radius: 10px;
        padding: 30px;
        margin-top: 20px;
        box-shadow: 0 4px 10px rgba(0,0,0,0.3);
    }
    
    .project-header {
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
        flex-wrap: wrap;
        gap: 20px;
        border-bottom: 2px solid #444;
        padding-bottom: 20px;
        margin-bottom: 20px;
    }

    .tech-badge {
        background-color: #3e3e42;
        color: #66d9ef;
        padding: 4px 10px;
        border-radius: 4px;
        font-size: 13px;
        font-weight: bold;
        margin-right: 5px;
        border: 1px solid #66d9ef;
    }

    .section-title {
        font-size: 18px;
        font-weight: bold;
        color: #fff;
        margin-top: 30px;
        margin-bottom: 15px;
        border-left: 4px solid #a6e22e;
        padding-left: 10px;
    }

    /* 圖片網格系統 */
    .image-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); /* 稍微加寬最小寬度 */
        gap: 20px;
        align-items: stretch; /* 強制讓同一列的卡片高度拉伸到一樣高 */
        margin-bottom: 20px;
    }

    /* 圖片卡片樣式 */
    .image-card {
        background-color: #252526;
        padding: 10px;
        border-radius: 6px;
        border: 1px solid #444;
        text-align: center;
        display: flex;           /* 改用 Flex 排版 */
        flex-direction: column;  /* 垂直排列 */
        justify-content: space-between; /* 內容上下撐開，確保文字對齊底部 */
        transition: transform 0.3s ease;
    }
    .image-card:hover {
        transform: translateY(-5px);
        border-color: #66d9ef;
    }

    /* 👇 關鍵修改：強制固定圖片高度，確保對齊 */
    .image-card img {
        width: 100%;
        height: 220px;       /* 設定固定高度 */
        object-fit: cover;   /* 裁切模式：填滿框框 (如果要完整顯示不裁切，改成 contain) */
        object-position: center;
        border-radius: 4px;
        background-color: #000; /* 若圖片有透明背景或使用 contain 時的底色 */
    }

    .image-caption {
        color: #aaa;
        font-size: 13px;
        margin-top: 10px;    /* 增加一點上方間距 */
        font-weight: 500;
    }

    .test-method-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
        gap: 15px;
        margin-bottom: 20px;
    }

    .method-card {
        background-color: #252526;
        padding: 15px;
        border-radius: 6px;
        border: 1px solid #444;
    }

    .method-title {
        color: #fd971f;
        font-weight: bold;
        margin-bottom: 5px;
        display: block;
    }

    .stat-row {
        display: flex;
        justify-content: space-between;
        padding: 8px 0;
        border-bottom: 1px solid #333;
        color: #ccc;
    }
    
    .cta-btn {
        display: inline-block;
        background-color: #a6e22e;
        color: #111;
        padding: 10px 20px;
        border-radius: 5px;
        text-decoration: none;
        font-weight: bold;
        transition: transform 0.2s;
    }
    .cta-btn:hover {
        transform: translateY(-2px);
        background-color: #8cbe27;
    }
</style>

<div class="project-container">
    <div class="project-header">
        <div>
            <h2 style="margin: 0 0 10px 0; color: #fff;">🧟 2D Zombie Shooting Game - Unit Testing</h2>
            <div style="margin-bottom: 10px;">
                <span class="tech-badge">Python</span>
                <span class="tech-badge">Pygame</span>
                <span class="tech-badge">Unittest</span>
                <span class="tech-badge">Coverage Analysis</span>
            </div>
            <div style="color: #aaa; font-size: 14px;">
                <strong>Team 21:</strong> 陳昱宏 (Me), 謝秉錦, 肇綺筠 
            </div>
        </div>
        <a href="https://github.com/CherylChaoNYCU/unittesting-zombie-game" target="_blank" class="cta-btn">
            View on GitHub 🔗
        </a>
    </div>
    <div style="color: #ddd; line-height: 1.6; margin-bottom: 20px;">
        本專案目標為針對一個基於 Pygame 開發的 <strong>2D 殭屍射擊遊戲</strong> 建構完整的單元測試框架。
        我們針對遊戲中的 Initialization, Update, Collision 等核心機制進行了獨立測試，確保各模組功能在重構或開發過程中能穩定運行。
    </div>
    <div class="image-grid">
        <div class="image-card">
            <img src="/images/zombie_game_demo.png" alt="Game Demo Screen">
            <div class="image-caption">🎮 Game Demo / 遊戲實機畫面</div>
        </div>
        <div class="image-card">
            <img src="/images/project_architecture.png" alt="Project Architecture" style="object-fit: contain;"> 
            <div class="image-caption">🏗️ System Architecture / 系統架構圖</div>
        </div>
    </div>
    <div class="section-title">🛠️ 測試策略 (Testing Strategy)</div>
    <div class="test-method-grid">
        <div class="method-card">
            <span class="method-title">🧪 Spy (間諜物件)</span>
            <div style="color: #bbb; font-size: 13px;">
                用於監控物件的行為。我們對遊戲中的 Objects (如 Player, Zombie) 進行 Spy，確認函數是否被正確呼叫。
            </div>
        </div>
        <div class="method-card">
            <span class="method-title">🎭 Stub (樁物件)</span>
            <div style="color: #bbb; font-size: 13px;">
                模擬回傳固定的資料。主要用於 User Input 和 Character Position，隔離外部依賴的不確定性。
            </div>
        </div>
        <div class="method-card">
            <span class="method-title">🤡 Mock (模擬物件)</span>
            <div style="color: #bbb; font-size: 13px;">
                模擬複雜的外部系統。我們 Mock 了 Pygame Library 和 Collision Events，專注於測試自身的邏輯。
            </div>
        </div>
    </div>
    <div class="section-title">📊 邏輯覆蓋率 (Logic Coverage: PC & CACC)</div>
    <div style="color: #ccc; margin-bottom: 15px; line-height: 1.6;">
        為了驗證射擊機制的穩定性，我們針對 <code style="background: #333; padding: 2px 5px; border-radius: 4px; color: #e6db74;">shoot()</code> 函數中的判定條件進行了 <strong>CACC</strong> 分析。我們設計了多組 Test Case 來滿足 Predicate Coverage，確保射擊冷卻時間邏輯無誤。
    </div>
    <div style="margin-bottom: 20px;">
        <div class="image-card" style="max-width: 100%;">
            <img src="/images/code_coverage_example.png" alt="Code Coverage Logic Example" style="height: auto; min-height: 150px;">
            <div class="image-caption">📝 Code Example / 邏輯覆蓋測試代碼範例</div>
        </div>
    </div>
    <div class="section-title">📈 測試成果 (Test Scope & Status)</div>
    <div style="background-color: #252526; padding: 15px; border-radius: 6px; border: 1px solid #444;">
        <div class="stat-row">
            <span><strong>Component</strong></span>
            <span><strong>Status</strong></span>
        </div>
        <div class="stat-row">
            <span>Main Loop Logic (main.py)</span>
            <span style="color: #a6e22e;">✅ Verified</span>
        </div>
        <div class="stat-row">
            <span>Zombie Behavior (zombie.py)</span>
            <span style="color: #a6e22e;">✅ Verified</span>
        </div>
        <div class="stat-row" style="border-bottom: none;">
            <span>Menu System (menu.py)</span>
            <span style="color: #fd971f;">⚠️ UI Dependent</span>
        </div>
    </div>
</div>