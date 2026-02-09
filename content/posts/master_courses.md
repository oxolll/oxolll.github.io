---
title: "🎓 碩士修課簡介"
date: 2026-01-21T17:08:19+08:00
draft: false 
hidden: true
ShowToc: false
tags: ["posts"]
weight: 1
---

<style>
    :root {
        --bg-card: #1e1e1e;
        --border-color: #333;
        --text-main: #f8f8f2;
        --text-muted: #aaa;
        --accent-blue: #66d9ef;
        --accent-green: #a6e22e;
        --accent-orange: #fd971f;
        --accent-pink: #f92672;
        --card-hover-border: #66d9ef;
    }

    /* 通用容器 */
    .page-container {
        display: flex;
        flex-direction: column;
        gap: 40px;
        font-size: 16px;
        line-height: 1.6;
        color: var(--text-main);
    }

    /* 學期區塊標題 */
    .semester-header {
        display: flex;
        align-items: center;
        gap: 10px;
        font-size: 22px;
        font-weight: bold;
        color: #fff;
        border-left: 5px solid;
        padding-left: 15px;
        margin-bottom: 20px;
    }
    .semester-header.green { border-color: var(--accent-green); }
    .semester-header.orange { border-color: var(--accent-orange); }
    .semester-header.blue { border-color: var(--accent-blue); }

    /* 課程容器 (Grid Layout for RWD) */
    .course-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
        gap: 20px;
    }

    /* 課程卡片 */
    .course-card {
        background-color: #252526;
        border: 1px solid #3e3e42;
        border-radius: 8px;
        padding: 20px;
        transition: transform 0.2s, border-color 0.2s, box-shadow 0.2s;
        display: flex;
        flex-direction: column;
    }
    .course-card:hover {
        border-color: var(--card-hover-border);
        transform: translateY(-3px);
        box-shadow: 0 5px 15px rgba(0,0,0,0.3);
    }

    /* 課程標題 */
    .course-title {
        font-size: 18px;
        font-weight: bold;
        color: var(--accent-blue) !important;
        text-decoration: none;
        margin-bottom: 10px;
        display: block;
    }
    .course-title:hover {
        text-decoration: underline;
    }

    /* 課程資訊列 (教授、成績) */
    .course-meta {
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 15px;
        font-size: 14px;
        color: var(--text-muted);
        border-bottom: 1px dashed #444;
        padding-bottom: 10px;
    }
    
    .prof-name {
        display: flex;
        align-items: center;
        gap: 5px;
    }
    .prof-name::before {
        content: "👨‍🏫"; 
    }

    /* 成績標籤 */
    .grade-badge {
        background-color: var(--accent-green);
        color: #1e1e1e;
        padding: 2px 8px;
        border-radius: 4px;
        font-weight: bold;
        font-size: 12px;
    }
    .grade-badge.B { background-color: var(--accent-orange); }
    .grade-badge.A-plus { background-color: var(--accent-pink); color: #fff; }

    /* 課程描述 */
    .course-desc {
        color: #ddd;
        font-size: 15px;
        line-height: 1.6;
        flex-grow: 1; /* 讓內容填滿剩餘空間 */
    }

    /* 故事區塊 */
    .story-block {
        background: linear-gradient(135deg, #1e3a5f 0%, #162a45 100%);
        border-left: 5px solid var(--accent-blue);
        padding: 25px;
        border-radius: 8px;
        color: #e6f7ff;
        line-height: 1.8;
        margin: 10px 0;
        box-shadow: 0 4px 10px rgba(0,0,0,0.2);
    }
    .story-title {
        font-weight: bold;
        font-size: 18px;
        margin-bottom: 15px;
        display: flex;
        align-items: center;
        gap: 10px;
        color: var(--accent-blue);
    }

    /* 按鈕樣式 */
    .action-btn {
        display: inline-flex;
        align-items: center;
        justify-content: center;
        padding: 10px 24px;
        border-radius: 50px;
        font-weight: bold;
        text-decoration: none !important;
        transition: all 0.2s;
        margin: 0 5px;
    }
    .btn-outline {
        background-color: transparent;
        color: var(--accent-blue);
        border: 2px solid var(--accent-blue);
    }
    .btn-outline:hover {
        background-color: var(--accent-blue);
        color: #111;
    }
    .btn-primary {
        background-color: #333;
        color: #fff;
        border: 2px solid var(--accent-blue);
    }
    .btn-primary:hover {
        background-color: var(--accent-blue);
        color: #111;
    }
</style>

<div class="page-container">
    <div style="text-align: center; margin-bottom: 20px;">
        <p style="color: var(--text-muted); font-size: 1.1rem;">
            以下為我碩士生涯在國立陽明交通大學的修課紀錄與心得分享。
        </p>
    </div>
    <div>
        <div class="semester-header green">
            📅 111 學年度上學期
        </div>
        <div class="course-grid">
            <div class="course-card">
                <a href="/courses/Biostatistics/" class="course-title">📊 生物統計 (Biostatistics)</a>
                <div class="course-meta">
                    <span class="prof-name">王維菁</span>
                    <span class="grade-badge">A-</span>
                </div>
                <div class="course-desc">
                    這門課程是開在生資碩底下，同時也是數據所的必修課程。課程內容涵蓋基礎的統計，大部分都是跟生物相關的例子。課程可以使用 R 語言或是 Python 去做作業，一共有 7 個作業，作業難度不高，大部分都可以手寫計算，有些作業需要畫圖，我都使用 Python 來處理，整體難度偏輕鬆。
                </div>
            </div>
            <div class="course-card">
                <a href="/courses/Graph_Theory/" class="course-title">🕸️ 圖形理論 (Graph Theory)</a>
                <div class="course-meta">
                    <span class="prof-name">林政寬</span>
                    <span class="grade-badge">A</span>
                </div>
                <div class="course-desc">
                    這門課我大學並沒有修過，僅有在考研究所時在演算法裡面章節碰到，於是便打算在碩士修課學習看看，也當作複習。課程內容基本上都和考研究所會遇到的差不多，作業有 6 個，都是交紙本，修課感覺順暢，準備過程也挺輕鬆的。最有印象的是老師說過：糾正別人論文錯誤可能可以發表。
                </div>
            </div>
            <div class="course-card">
                <a href="/courses/Algorithms/" class="course-title">⚡ 演算法 (Algorithms)</a>
                <div class="course-meta">
                    <span class="prof-name">荊宇泰</span>
                    <span class="grade-badge">A-</span>
                </div>
                <div class="course-desc">
                    這門課我大學沒怎麼認真學習，是在考研所時才特別仔細學習的，想在碩士中好好學習並複習。課程內容跟考研究所的時候唯一差異是多了一些證明，有些時候一些簡單的性質 (比如 Greedy) 用證明卻落落長，不過整體也是十分有趣。作業的部分是三個手寫證明跟一個程式作業，既有理論又有實作，loading 還算輕鬆。
                </div>
            </div>
            <div class="course-card">
                <a href="/courses/Game_Theory/" class="course-title">♟️ 賽局理論 (Game Theory)</a>
                <div class="course-meta">
                    <span class="prof-name">嚴力行</span>
                    <span class="grade-badge">A-</span>
                </div>
                <div class="course-desc">
                    賽局理論這門課印象中就是投影片非常多，考試前的準備就是一直翻投影片。修課的理由一方面是教授鼓勵另一方面是自己也感興趣。課程內容蠻有趣的，是在講各種性質的賽局，以及平衡策略之類的相關，作業的部分有兩個程式作業，整體 loading 不重，是一門值得修的課。
                </div>
            </div>
        </div>
    </div>
    <div class="story-block">
        <div class="story-title">
            💡 關於修課與成績背後的故事...
        </div>
        <div>
            看到這裡肯定有人會覺得奇怪，我碩一上這四門課聽起來都很輕鬆，但怎麼沒一個特別突出的成績呢？
            <br><br>
            這是因為我碩一剛進來，目標是<strong>盡快修完 24 學分必修</strong>，多留點時間專心做研究。所以我碩一上其實修了 <strong>5 門課程</strong>（其中一門為旁聽的「機器學習」）。
            <br><br>
            那時候為了旁聽下學期才會選到的「機器學習」（很多人修，但我沒搶到），我必須做所有修課生該做的事：作業、考試，但拿不到學分(課程旁聽的要求)。那學期總共被 33 個作業轟炸，忙得焦頭爛額，導致成績受到影響。
            <br><br>
            慶幸的是，下學期去加簽時，老師直接破例讓我沿用上學期的成績。雖然老師說「你再修一次成績會更好」，但我為了研究考量，選擇直接沿用，這也是為什麼成績看起來比較普通的原因。
        </div>
    </div>
    <div>
        <div class="semester-header orange">
            📅 111 學年度下學期
        </div>
        <div class="course-grid">
            <div class="course-card">
                <a href="/courses/Theory_of_cryptology/" class="course-title">🔐 密碼理論 (Theory of Cryptology)</a>
                <div class="course-meta">
                    <span class="prof-name">曾文貴</span>
                    <span class="grade-badge B">B</span>
                </div>
                <div class="course-desc">
                    修課理由是指導教授推薦。作業有五個，使用 Python 等工具實作，作業分數拿得挺高，但期中期末考不太理想，有點可惜。不過課程內容沒問題，扎實且有挑戰。
                </div>
            </div>
            <div class="course-card">
                <a href="/courses/Machine_Learning/" class="course-title">🤖 機器學習 (Machine Learning)</a>
                <div class="course-meta">
                    <span class="prof-name">洪瑞鴻、邱維辰</span>
                    <span class="grade-badge B">B+</span>
                </div>
                <div class="course-desc">
                    這門課就是上面故事提到的那一門。課程扎實，作業一共有七次，每次都需要 Coding，Loading 偏重。
                </div>
            </div>
            <div class="course-card">
                <a href="/courses/Software_Testing/" class="course-title">🐞 軟體測試 (Software Testing)</a>
                <div class="course-meta">
                    <span class="prof-name">黃世昆</span>
                    <span class="grade-badge A-plus">A+</span>
                </div>
                <div class="course-desc">
                    我有興趣的課程。每一學期作業會更改使用的語言，這次是 Python。Homework 有 4 個，Lab 有 8 個，期中有上機考，期末有個專題，算是滿滿 Coding 的課程。Loading 偏重，但獲益良多，學會以測試角度來開發，很有趣！
                </div>
            </div>
            <div class="course-card">
                <a href="/courses/Selected_Topics_in_Intractable_Problems/" class="course-title">🧩 難解計算問題專論</a>
                <div class="course-meta">
                    <span class="prof-name">高孟駿</span>
                    <span class="grade-badge A-plus">A+</span>
                </div>
                <div class="course-desc">
                    全名 Selected Topics in Intractable Problems。這門課程是在講一些比較難的問題，不過老師課程規劃可能不完善，所以給分很甜。
                </div>
            </div>
        </div>
    </div>
    <div>
        <div class="semester-header blue">
            📅 112 學年度上學期
        </div>
        <div class="course-grid">
            <div class="course-card">
                <a href="/courses/Formal_Languages_and_Theory_of_Computation/" class="course-title">🔣 正規語言與計算複雜度</a>
                <div class="course-meta">
                    <span class="prof-name">曾文貴</span>
                    <span class="grade-badge">A-</span>
                </div>
                <div class="course-desc">
                    指導教授推薦的課程。這是計算複雜度的上半節，介紹自動機跟語言。課程內容非常有趣，完善了考研究所時沒那麼清楚的概念！作業有 4 次，都是紙本作答，Loading 算輕鬆，很值得修。
                </div>
            </div>
            <div class="course-card">
                <a href="/courses/Computational_Complexity/" class="course-title">⏱️ 計算複雜度 (Computational Complexity)</a>
                <div class="course-meta">
                    <span class="prof-name">蔡錫鈞</span>
                    <span class="grade-badge">A</span>
                </div>
                <div class="course-desc">
                    我指導教授的課程，計算複雜度的下半節。著重在各種時間與空間複雜度類別。課程內容蠻多證明，很有挑戰性！作業 3 次，都是紙本證明，Loading 正常，值得修。
                </div>
            </div>
            <div class="course-card">
                <a href="/courses/Data_Science_Project/" class="course-title">📈 數據科學專題 (Data Science Project)</a>
                <div class="course-meta">
                    <span class="prof-name">彭文志</span>
                    <span class="grade-badge">A</span>
                </div>
                <div class="course-desc">
                    數據所必修。要求將自己的研究包裝成論文並報告。由於碩論題目未定，我以當時研究的猜想進行實驗呈現。算是一門引導做論文的課程，過渡期還算不錯。
                </div>
            </div>
        </div>
    </div>
    <div style="text-align: center; margin-top: 40px; border-top: 1px solid #333; padding-top: 30px;">
        <p style="color: var(--text-muted); margin-bottom: 25px;">
            * 研究生除了修課，最重要的就是碩士論文！
        </p>
        <div style="display: flex; justify-content: center; gap: 15px; flex-wrap: wrap;">
            <a href="/" class="action-btn btn-outline">
                🏠 回到首頁 (Home)
            </a>
            <a href="/posts/master_studies/" class="action-btn btn-primary">
                🚀 前往：碩士研究簡介 (Research)
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