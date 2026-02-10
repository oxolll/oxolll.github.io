---
title: "作者簡介"
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
        --bg-card: #1e1e1e;
        --border-color: #333;
        --text-main: #f8f8f2;
        --text-muted: #aaa;
        --accent-blue: #66d9ef;
        --accent-green: #a6e22e;
        --accent-pink: #f92672;
        --accent-yellow: #e6db74;
    }

    /* 通用卡片容器 */
    .profile-section {
        margin-bottom: 40px;
    }

    .section-title {
        font-size: 22px;
        font-weight: bold;
        color: var(--text-main);
        border-left: 5px solid var(--accent-blue);
        padding-left: 15px;
        margin-bottom: 20px;
    }

    .content-card {
        background-color: var(--bg-card);
        border: 1px solid var(--border-color);
        border-radius: 8px;
        padding: 20px;
        margin-bottom: 15px;
        transition: transform 0.2s;
    }
    
    .content-card:hover {
        border-color: #555;
    }

    /* 技能標籤 */
    .skill-tag {
        display: inline-flex;
        align-items: center;
        gap: 10px;
        background-color: #2d2d30; /*稍微深一點的背景，讓Logo突出*/
        color: #f8f8f2;
        padding: 8px 14px;
        border-radius: 6px;
        font-size: 14px;
        margin: 0 8px 10px 0;
        border: 1px solid #444;
        transition: all 0.2s ease;
        text-decoration: none;
    }
    
    .skill-tag:hover {
        transform: translateY(-2px);
        background-color: #3e3e42;
        border-color: #666;
        box-shadow: 0 4px 8px rgba(0,0,0,0.3);
    }

    /* SVG 圖示統一樣式 */
    .skill-icon {
        width: 20px;
        height: 20px;
        flex-shrink: 0;
    }

    /* 分類標題樣式 */
    .skill-category {
        color: #fff; 
        margin-bottom: 12px; 
        font-size: 16px; 
        display: flex; 
        align-items: center; 
        gap: 8px;
    }

    /* 聯絡資訊列 */
    .contact-row {
        display: flex;
        align-items: center;
        gap: 12px;
        margin-bottom: 12px;
        font-size: 15px;
    }
    .contact-row a {
        color: var(--accent-blue);
        text-decoration: none;
    }
    .contact-row a:hover {
        text-decoration: underline;
    }
    
    /* 連結按鈕 */
    .action-btn {
        display: inline-flex;
        align-items: center;
        justify-content: center;
        gap: 10px; /* 圖示跟文字的距離 */
        
        padding: 12px 28px;     /* 更舒適的內距 */
        border-radius: 50px;    /* 膠囊形狀 */
        font-weight: 600;
        font-size: 15px;
        text-decoration: none !important;
        
        /* 基礎：半透明毛玻璃 */
        background: rgba(30, 30, 35, 0.6); 
        border: 1px solid rgba(255, 255, 255, 0.1);
        backdrop-filter: blur(5px);
        -webkit-backdrop-filter: blur(5px);
        color: #ccc; /* 預設灰字 */
        
        transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
    }
    .btn-home {
        background-color: transparent;
        color: var(--accent-blue);
        border: 2px solid var(--accent-blue);
    }
    .btn-home:hover {
        background: rgba(102, 217, 239, 0.15); /* 淡淡的藍底 */
        border-color: #66d9ef;                 /* 藍色邊框 */
        color: #fff;
        box-shadow: 0 5px 20px rgba(102, 217, 239, 0.3); /* 藍色光暈 */
        transform: translateY(-3px);
    }
    .btn-email {
        background-color: #e74c3c;
        color: #fff;
        border: 2px solid #e74c3c;
    }
    .btn-email:hover {
        background: rgba(231, 76, 60, 0.15); /* 淡淡的紅底 */
        border-color: #e74c3c;               /* 紅色邊框 */
        color: #fff;
        box-shadow: 0 5px 20px rgba(231, 76, 60, 0.3); /* 紅色光暈 */
        transform: translateY(-3px);
    }
</style>

<div align="center" style="margin-bottom: 40px;">
    <img src="/images/cyhIcon.png" width="180" height="180" style="border-radius: 50%; object-fit: cover; border: 4px solid #333; margin-bottom: 15px;" alt="cyh">
    <h1 style="margin: 0; font-size: 2.5rem;">cyh</h1>
    <p style="color: var(--text-muted); font-size: 1.1rem;">
        M.S. in Data Science and Engineering, NYCU
    </p>
    <div style="max-width: 600px; margin: 20px auto; line-height: 1.6; color: #ccc; text-align: left;">
        你好，我是 <strong>cyh</strong>。我畢業於 <strong>國立陽明交通大學</strong> 數據科學與工程研究所。
        我的研究興趣主要集中在 <strong style="color: var(--accent-blue);">理論計算機科學 (TCS)</strong>，特別是演算法分析與計算複雜度理論。
        <br><br>
        碩士期間，我致力於探索電路複雜度下界 (Circuit Lower Bounds) 與 k-SAT 問題的關聯。這份網站主要用於記錄我的學習歷程、論文研讀筆記以及技術解決方案。
    </div>
</div>

<hr style="border-color: #333; margin: 30px 0;">

<div class="profile-section">
    <div class="section-title">🎓 學歷 (Education)</div>
    <div class="content-card" style="display: flex; gap: 20px; align-items: flex-start;">
        <div style="flex-shrink: 0; width: 60px; height: 60px; background-color: #fff; border-radius: 8px; padding: 5px; display: flex; align-items: center; justify-content: center;">
            <img src="/images/nycu.png" alt="NYCU Logo" style="width: 100%; height: auto; object-fit: contain;">
        </div>
        <div style="flex: 1;">
            <div style="display: flex; justify-content: space-between; flex-wrap: wrap; margin-bottom: 5px;">
                <strong style="font-size: 18px; color: #fff;">國立陽明交通大學 (NYCU)</strong>
                <span style="color: var(--accent-green); font-weight: bold; font-size: 14px;">2023 - 2026</span>
            </div>
            <div style="color: var(--text-muted); font-size: 15px; margin-bottom: 8px;">
                數據科學與工程研究所 (Institute of Data Science and Engineering)
            </div>
            <ul style="margin: 0; padding-left: 20px; color: #ccc; font-size: 14px; line-height: 1.6;">
                <li>學位：碩士 (Master of Science)</li>
                <li>研究領域：Algorithms, Computational Complexity</li>
            </ul>
        </div>
    </div>
    <div class="content-card" style="display: flex; gap: 20px; align-items: flex-start;">
        <div style="flex-shrink: 0; width: 60px; height: 60px; background-color: #fff; border-radius: 8px; padding: 5px; display: flex; align-items: center; justify-content: center;">
            <img src="/images/nqu.png" alt="NQU Logo" style="width: 100%; height: auto; object-fit: contain;">
        </div>
        <div style="flex: 1;">
            <div style="display: flex; justify-content: space-between; flex-wrap: wrap; margin-bottom: 5px;">
                <strong style="font-size: 18px; color: #fff;">國立金門大學 (NQU)</strong>
                <span style="color: #888; font-size: 14px;">2019 - 2023</span>
            </div>
            <div style="color: var(--text-muted); font-size: 15px; margin-bottom: 8px;">
                資訊工程學系 (Computer Science and Information Engineering)
            </div>
            <ul style="margin: 0; padding-left: 20px; color: #ccc; font-size: 14px; line-height: 1.6;">
                <li>學位：學士 (Bachelor of Science)</li>
                <!-- <li>系排：13 / 43 </li>  -->
            </ul>
        </div>
    </div>
</div>

<div class="profile-section">
    <div class="section-title">🔬 研究與專案 (Research & Projects)</div>
    <div style="display: flex; flex-direction: column; gap: 20px;">
        <div style="background-color: var(--bg-card); border: 1px solid #444; border-radius: 10px; padding: 25px;">
            <div style="display: flex; align-items: flex-start; gap: 15px;"> 
                <div style="font-size: 40px; line-height: 1;">📜</div>
                <div style="flex: 1;">            
                    <div style="font-size: 20px; font-weight: bold; color: #fff; margin-bottom: 5px;">
                        <a href="/projects/master_thesis" style="color: #fff; text-decoration: none;">
                            三層電路下界之研究: 以多數決函數為例
                        </a> 
                    </div>
                    <div style="font-size: 14px; color: var(--text-muted); font-style: italic; margin-bottom: 8px;">
                        Improved Depth-3 Circuit Lower Bounds for Majority with Local Enumeration
                    </div>
                    <div style="font-size: 14px; color: #888;">
                        <span style="color: var(--accent-blue);">Student:</span> Chen, Yu-Hung 
                        &nbsp;|&nbsp; <span style="color: var(--accent-pink);">Advisor:</span> Dr. Tsai, Shi-Chun
                    </div>
                    <div style="margin-top: 10px; display: flex; gap: 10px; flex-wrap: wrap;">
                        <span style="background-color: #333; color: var(--accent-yellow); padding: 2px 8px; border-radius: 4px; font-size: 12px; border: 1px solid var(--accent-yellow);">
                            🔒 論文保密中
                        </span>
                        <span style="background-color: #1e3a5f; color: var(--accent-blue); padding: 2px 8px; border-radius: 4px; font-size: 12px; border: 1px solid var(--accent-blue); font-weight: bold;">
                            🚀 Under Review: CCC
                        </span>
                    </div>
                </div>
            </div>
            <div style="margin-top: 20px; border-left: 4px solid #555; padding-left: 15px; color: #ccc; line-height: 1.6;">
                本研究探討了 $k$-SAT 演算法與 Majority 函數下界之間的關聯。核心成果經整理後以 
                <strong style="color: #fff;">『[Depth-3 Circuit Lower Bounds for Majority with Local Enumeration]』</strong>
                為題，曾投稿至 <strong style="color: #ccc;">ACM STOC</strong>，目前正於計算複雜度頂級會議 <strong style="color: var(--accent-blue);">CCC</strong> 進行審查，顯示本研究具備挑戰領域前沿難題的規格與深度。
                <br>
                <a href="/projects/master_thesis" style="color: var(--accent-blue); font-size: 14px; margin-top: 5px; display: inline-block;">👉 閱讀完整論文介紹</a>
            </div>
        </div>
        <div class="content-card" style="display: flex; align-items: center; gap: 15px;">
            <div style="font-size: 30px;">🧠</div>
            <div>
                <strong style="font-size: 18px; color: #fff; display: block; margin-bottom: 5px;">k-SAT Lower Bounds</strong>
                <div style="color: #ccc; font-size: 15px; margin-bottom: 8px;">
                    深入研讀並整理關於 SAT 問題在不同計算模型下的下界證明 (Survey & Study)。
                </div>
                <div>
                    <span class="skill-tag green">Literature Review</span>
                    <span class="skill-tag blue">Complexity Theory</span>
                </div>
            </div>
        </div>
    </div>
</div>

<div class="profile-section">
    <div class="section-title">🛠️ 技術技能 (Skills)</div>
    <div class="content-card">
        <div style="margin-bottom: 25px;">
            <div class="skill-category">
                💻 Programming Languages
            </div>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 128 128">
                    <path fill="#00599C" d="M117.5 33.5l-10-17.5c-3-5.5-9-7.5-14.5-4.5L82.5 17c-5.5 3-7.5 9-4.5 14.5l2 3.5h-10c-6.5 0-11.5 5-11.5 11.5v23H37v-6.5c0-13 10.5-23.5 23.5-23.5h2.5c4.5 0 8-3.5 8-8V10.5c0-4.5-3.5-8-8-8h-2.5C33.5 2.5 11 25 11 52.5v23C11 103 33.5 125.5 61 125.5h2.5c4.5 0 8-3.5 8-8v-20.5c0-4.5-3.5-8-8-8h-2.5c-13 0-23.5-10.5-23.5-23.5v-6.5h21.5v23c0 6.5 5 11.5 11.5 11.5h10l-2 3.5c-3 5.5-1 11.5 4.5 14.5l10.5 5.5c5.5 3 11.5 1 14.5-4.5l10-17.5c3-5.5 1-11.5-4.5-14.5L103 69.5c-3.5-2-7.5-2-11 0v-11c3.5 2 7.5 2 11 0l10-5.5c5.5-3 7.5-9 4.5-14.5z"></path>
                </svg>
                C++
            </span>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 128 128">
                    <path fill="#4584b6" d="M64 12c-15.9 0-29.3 5.5-29.3 16.5v8.5h29.5a5 5 0 0 1 5 5v13h-40c-15.9 0-29.3 10.6-29.3 26.5 0 14.8 11.4 24.9 25.5 26.3v-4.8c0-11.9 9.9-21.5 21.8-21.5h15.5v-13h-25a5 5 0 0 1-5-5V47.5c0-4.7 6.4-8.5 14.2-8.5h23.1c7.8 0 14.2 3.8 14.2 8.5v5h8.5c4.7 0 8.5-3.8 8.5-8.5V28.5C101.3 17.5 87.9 12 64 12z"></path>
                    <path fill="#ffde57" d="M64 116.5c15.9 0 29.3-5.5 29.3-16.5v-8.5H63.8a5 5 0 0 1-5-5v-13h40c15.9 0 29.3-10.6 29.3-26.5 0-14.8-11.4-24.9-25.5-26.3v4.8c0 11.9-9.9 21.5-21.8 21.5H65.3v13h25a5 5 0 0 1 5 5V81c0 4.7-6.4 8.5-14.2 8.5H58c-7.8 0-14.2-3.8-14.2-8.5v-5h-8.5c-4.7 0-8.5 3.8-8.5 8.5V100c0 11 13.4 16.5 37.2 16.5z"></path>
                </svg>
                Python
            </span>
        </div>
        <div style="margin-bottom: 25px;">
            <div class="skill-category">
                🧠 Core Knowledge (CS Theory)
            </div>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 24 24" fill="none" stroke="#f92672" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M18 6L6 18M6 6l12 12"></path>
                    <rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect> 
                    <circle cx="6" cy="6" r="3"></circle>
                    <circle cx="18" cy="18" r="3"></circle>
                    <line x1="8.12" y1="8.12" x2="15.88" y2="15.88"></line>
                </svg>
                Algorithms
            </span>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 24 24" fill="none" stroke="#f92672" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <circle cx="12" cy="12" r="10"></circle>
                    <polyline points="12 6 12 12 16 14"></polyline>
                </svg>
                Complexity Theory
            </span>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 24 24" fill="none" stroke="#f92672" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <rect x="9" y="3" width="6" height="6"></rect>
                    <path d="M5 21h14M12 9v12M5 21v-4a2 2 0 0 1 2-2h10a2 2 0 0 1 2 2v4"></path>
                </svg>
                Data Structures
            </span>
        </div>
        <div>
            <div class="skill-category">
                🔧 Tools & Platforms
            </div>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 128 128">
                    <circle cx="64" cy="64" r="64" fill="#E95420"/>
                    <path fill="#FFF" d="M85.5 24.8c-2.3-4-7.4-5.4-11.4-3.1-4 2.3-5.4 7.4-3.1 11.4 2.3 4 7.4 5.4 11.4 3.1 4-2.3 5.4-7.4 3.1-11.4zm-42.9 78.4c-2.3-4-7.4-5.4-11.4-3.1-4 2.3-5.4 7.4-3.1 11.4 2.3 4 7.4 5.4 11.4 3.1 4.1-2.3 5.4-7.4 3.1-11.4zm-9.3-59.9c2.6 0 5-1.2 6.6-3.3l10.9 6.3c-2.2 4-2.2 9 0 13l-10.9 6.3c-1.6-2.1-4-3.3-6.6-3.3-4.6 0-8.4 3.8-8.4 8.4s3.8 8.4 8.4 8.4c4.6 0 8.4-3.8 8.4-8.4 0-1-.2-2-.5-2.9l10.9-6.3c2.5 4.5 7.3 7.6 12.8 7.6 8.1 0 14.6-6.6 14.6-14.6 0-8.1-6.6-14.6-14.6-14.6-5.5 0-10.3 3.1-12.8 7.6l-10.9-6.3c.3-1 .5-2 .5-2.9 0-4.6-3.8-8.4-8.4-8.4s-8.4 3.8-8.4 8.4c-.1 4.6 3.7 8.4 8.4 8.4z"/>
                </svg>
                Ubuntu (Linux)
            </span>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 128 128">
                    <path fill="#F05032" d="M116.3 54.3L73.7 11.7c-4.7-4.7-12.3-4.7-17 0L11.7 56.7c-4.7 4.7-4.7 12.3 0 17l42.6 42.6c4.7 4.7 12.3 4.7 17 0l45-45c4.7-4.7 4.7-12.3 0-17zM64.6 93c-6.8 0-12.3-5.5-12.3-12.3 0-2.3.6-4.5 1.7-6.3l-9.8-13.8c-1.3.4-2.6.6-4 .6-6.8 0-12.3-5.5-12.3-12.3S33.4 36.6 40.2 36.6s12.3 5.5 12.3 12.3c0 2.2-.6 4.3-1.6 6.1l9.8 13.8c1.3-.4 2.6-.6 4-.6 1.8 0 3.5.4 5.1 1.1l7.6-13.2c-.4-1.3-.6-2.6-.6-4 0-6.8 5.5-12.3 12.3-12.3s12.3 5.5 12.3 12.3-5.5 12.3-12.3 12.3c-2.3 0-4.5-.6-6.3-1.7l-7.5 13.1c.4 1.3.6 2.6.6 4-.2 6.7-5.7 12.2-12.3 12.2z"></path>
                </svg>
                Git
            </span>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 512 512">
                   <path fill="#FF4088" d="M59.7 130.3c0-38.4 31.4-69.8 69.8-69.8 16 0 30.7 5.4 42.5 14.5L256 160l84-84.9c11.8-9.1 26.5-14.5 42.5-14.5 38.4 0 69.8 31.4 69.8 69.8 0 16-5.4 30.7-14.5 42.5L256 356 74.2 172.8c-9.1-11.8-14.5-26.5-14.5-42.5z"/>
                </svg>
                Hugo
            </span>
            <span class="skill-tag">
                <svg class="skill-icon" viewBox="0 0 24 24" fill="none" stroke="#2ecc71" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path>
                    <polyline points="14 2 14 8 20 8"></polyline>
                    <line x1="16" y1="13" x2="8" y2="13"></line>
                    <line x1="16" y1="17" x2="8" y2="17"></line>
                    <polyline points="10 9 9 9 8 9"></polyline>
                </svg>
                LaTeX
            </span>
        </div>
    </div>
</div>

<div class="profile-section">
    <div class="section-title">📫 聯絡我 (Contact)</div>
    <div class="content-card" style="display: flex; flex-wrap: wrap; align-items: center; justify-content: space-between; gap: 20px;">
        <div style="flex: 1; min-width: 260px;">
            <div class="contact-row">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#ccc" stroke-width="2"><path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path><polyline points="22,6 12,13 2,6"></polyline></svg>
                <span>
                    <a href="mailto:oxolll58x85@gmail.com">oxolll58x85@gmail.com</a>
                </span>
            </div>
            <div class="contact-row">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#ccc" stroke-width="2"><path d="M9 19c-5 1.5-5-2.5-7-3m14 6v-3.87a3.37 3.37 0 0 0-.94-2.61c3.14-.35 6.44-1.54 6.44-7A5.44 5.44 0 0 0 20 4.77 5.07 5.07 0 0 0 19.91 1S18.73.65 16 2.48a13.38 13.38 0 0 0-7 0C6.27.65 5.09 1 5.09 1A5.07 5.07 0 0 0 5 4.77a5.44 5.44 0 0 0-1.5 3.78c0 5.42 3.3 6.61 6.44 7A3.37 3.37 0 0 0 9 18.13V22"></path></svg>
                <span><a href="https://github.com/oxolll" target="_blank">github.com/oxolll</a></span>
            </div>
            <div class="contact-row">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="#ccc" stroke-width="2"><path d="M16 8a6 6 0 0 1 6 6v7h-4v-7a2 2 0 0 0-2-2 2 2 0 0 0-2 2v7h-4v-7a6 6 0 0 1 6-6z"></path><rect x="2" y="9" width="4" height="12"></rect><circle cx="4" cy="4" r="2"></circle></svg>
                <span><a href="https://www.linkedin.com/in/你的ID" target="_blank">LinkedIn Profile</a></span>
            </div>
        </div>
        <div>
             <a href="https://mail.google.com/mail/?view=cm&fs=1&to=oxolll58x85@gmail.com&su=來自網站的聯絡" target="_blank" class="action-btn btn-email">
                <span>✉️</span> 
                <span>Email Me</span>
            </a>
        </div>
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