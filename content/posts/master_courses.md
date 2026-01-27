---
title: "🎓 碩士修課簡介"
date: 2026-01-21T17:08:19+08:00
draft: false 
hidden: true
ShowToc: false       # 👈 關鍵 1：這行設為 false 就能關掉上方的目錄
# categories: [""]
tags: ["posts"]
weight: 1
# _build:
#     list: never
#     render: always
---

<style>
    /* 容器設定 */
    .course-container {
        display: flex;
        flex-direction: column;
        gap: 20px;
    }

    /* 課程卡片 */
    .course-card {
        background-color: #252526; /* 卡片背景色 */
        border: 1px solid #3e3e42;
        border-radius: 8px;
        padding: 20px;
        transition: transform 0.2s, border-color 0.2s;
    }
    .course-card:hover {
        border-color: #66d9ef; /* 滑鼠懸停變成亮藍色框 */
        transform: translateY(-2px); /* 微微浮起 */
    }

    /* 課程標題列 */
    .course-header {
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
        flex-wrap: wrap;
        gap: 10px;
        margin-bottom: 10px;
        border-bottom: 1px solid #333;
        padding-bottom: 10px;
    }

    /* 課程名稱連結 */
    .course-title {
        font-size: 18px;
        font-weight: bold;
        color: #66d9ef !important; /* 強制藍色 */
        text-decoration: none;
    }
    .course-title:hover {
        text-decoration: underline;
    }

    /* 教授與分數區域 */
    .course-meta {
        display: flex;
        align-items: center;
        gap: 10px;
        font-size: 14px;
    }

    /* 分數標籤 (Badge) */
    .grade-badge {
        background-color: #a6e22e; /* 預設綠色 (A/A+) */
        color: #1e1e1e;
        padding: 2px 8px;
        border-radius: 4px;
        font-weight: bold;
        font-size: 12px;
    }
    .grade-badge.B {
        background-color: #fd971f; /* 橘色 (B系列) */
    }

    /* 教授名字 */
    .prof-name {
        color: #ccc;
        font-style: italic;
    }
    .prof-name::before {
        content: "👨‍🏫 "; /* 加個小圖示 */
    }

    /* 課程心得內文 */
    .course-desc {
        color: #ddd;
        font-size: 15px;
        line-height: 1.6;
    }

    /* 故事區塊 (那一大段文字) */
    .story-block {
        background-color: #1e3a5f; /* 深藍色背景 */
        border-left: 5px solid #66d9ef;
        padding: 20px;
        border-radius: 5px;
        color: #e6f7ff;
        line-height: 1.8;
        margin: 30px 0;
        box-shadow: 0 4px 6px rgba(0,0,0,0.2);
    }

    /* 按鈕樣式 (最後的連結) */
    .cta-button {
        display: inline-block;
        background-color: #333;
        color: #fff !important;
        padding: 12px 24px;
        border-radius: 50px;
        text-decoration: none;
        border: 2px solid #66d9ef;
        transition: all 0.3s ease;
        font-weight: bold;
    }
    .cta-button:hover {
        background-color: #66d9ef;
        color: #111 !important;
    }
</style>

以下為我碩士生涯前半段在陽明交通大學的修課紀錄：
<br><br>

<h2 style="border-left: 5px solid #a6e22e; padding-left: 10px; color: #fff;">111 學年度上學期</h2>
<div class="course-container">
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Biostatistics/" class="course-title">生物統計 (Biostatistics)</a>
            <div class="course-meta">
                <span class="prof-name">王維菁</span>
                <span class="grade-badge">A-</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            這門課程是開在生資碩底下，同時也是數據所的必修課程。課程內容涵蓋基礎的統計，大部分都是跟生物相關的例子。課程可以使用 R 語言或是 Python 去做作業，一共有 7 個作業，作業難度不高，大部分都可以手寫計算，有些作業需要畫圖，我都使用 Python 來處理，整體難度偏輕鬆。
        </div>
    </div>
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Graph_Theory/" class="course-title">圖形理論 (Graph Theory)</a>
            <div class="course-meta">
                <span class="prof-name">林政寬</span>
                <span class="grade-badge">A</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            這門課我大學並沒有修過，僅有在考研究所時在演算法裡面章節碰到，於是便打算在碩士修課學習看看，也當作複習。課程內容基本上都和考研究所會遇到的差不多，作業有 6 個，都是交紙本，修課感覺順暢，準備過程也挺輕鬆的。最有印象的是老師說過：糾正別人論文錯誤可能可以發表。
        </div>
    </div>
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Algorithms/" class="course-title">演算法 (Algorithms)</a>
            <div class="course-meta">
                <span class="prof-name">荊宇泰</span>
                <span class="grade-badge">A-</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            這門課我大學沒怎麼認真學習，是在考研所時才特別仔細學習的，想在碩士中好好學習並複習。課程內容跟考研究所的時候唯一差異是多了一些證明，有些時候一些簡單的性質 (比如 Greedy) 用證明卻落落長，不過整體也是十分有趣。作業的部分是三個手寫證明跟一個程式作業，既有理論又有實作，loading 還算輕鬆。
        </div>
    </div>
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Game_Theory/" class="course-title">賽局理論 (Game Theory)</a>
            <div class="course-meta">
                <span class="prof-name">嚴力行</span>
                <span class="grade-badge">A-</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            賽局理論這門課印象中就是投影片非常多，考試前的準備就是一直翻投影片。修課的理由一方面是教授鼓勵另一方面是自己也感興趣。課程內容蠻有趣的，是在講各種性質的賽局，以及平衡策略之類的相關，作業的部分有兩個程式作業，整體 loading 不重，是一門值得修的課。
        </div>
    </div>
</div>

<div class="story-block">
    <div style="font-weight: bold; font-size: 18px; margin-bottom: 10px; display: flex; align-items: center;">
        💡 關於修課與成績背後的故事...
    </div>
    看到這裡肯定有人會覺得奇怪，我碩一上這四門課聽起來都很輕鬆，但怎麼沒一個特別突出的成績呢？
    <br><br>
    這是因為我碩一剛進來，目標是<strong>盡快修完 24 學分必修</strong>，多留點時間專心做研究。所以我碩一上其實修了 <strong>5 門課程</strong>（其中一門為旁聽的「機器學習」）。
    <br><br>
    那時候為了旁聽下學期才會選到的「機器學習」（很多人修，但我沒搶到），我必須做所有修課生該做的事：作業、考試，但拿不到學分(課程旁聽的要求)。那學期總共被 33 個作業轟炸，忙得焦頭爛額，導致成績受到影響。
    <br><br>
    慶幸的是，下學期去加簽時，老師直接破例讓我沿用上學期的成績。雖然老師說「你再修一次成績會更好」，但我為了研究考量，選擇直接沿用，這也是為什麼成績看起來比較普通的原因。
</div>

<br>

<h2 style="border-left: 5px solid #fd971f; padding-left: 10px; color: #fff;">111 學年度下學期</h2>
<div class="course-container">
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Theory_of_cryptology/" class="course-title">密碼理論 (Theory of Cryptology)</a>
            <div class="course-meta">
                <span class="prof-name">曾文貴</span>
                <span class="grade-badge B">B</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            修課理由是指導教授推薦。作業有五個，使用 Python 等工具實作，作業分數拿得挺高，但期中期末考不太理想，有點可惜。不過課程內容沒問題，扎實且有挑戰。
        </div>
    </div>
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Machine_Learning/" class="course-title">機器學習 (Machine Learning)</a>
            <div class="course-meta">
                <span class="prof-name">洪瑞鴻、邱維辰</span>
                <span class="grade-badge B">B+</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            這門課就是上面故事提到的那一門。課程扎實，作業一共有七次，每次都需要 Coding，Loading 偏重。
        </div>
    </div>
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Software_Testing/" class="course-title">軟體測試 (Software Testing)</a>
            <div class="course-meta">
                <span class="prof-name">黃世昆</span>
                <span class="grade-badge">A+</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            我有興趣的課程。每一學期作業會更改使用的語言，這次是 Python。Homework 有 4 個，Lab 有 8 個，期中有上機考，期末有個專題，算是滿滿 Coding 的課程。Loading 偏重，但獲益良多，學會以測試角度來開發，很有趣！
        </div>
    </div>
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Selected_Topics_in_Intractable_Problems/" class="course-title">難解計算問題專論(Selected Topics in Intractable Problems)</a>
            <div class="course-meta">
                <span class="prof-name">高孟駿</span>
                <span class="grade-badge">A+</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            <br>
            這門課程是在講一些比較難的問題，不過老師課程規劃可能不完善，所以給分很甜。
        </div>
    </div>
</div>

<br><br>

<h2 style="border-left: 5px solid #66d9ef; padding-left: 10px; color: #fff;">112 學年度上學期</h2>
<div class="course-container">
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Formal_Languages_and_Theory_of_Computation/" class="course-title">正規語言與計算複雜度</a>
            <div class="course-meta">
                <span class="prof-name">曾文貴</span>
                <span class="grade-badge">A-</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            指導教授推薦的課程。這是計算複雜度的上半節，介紹自動機跟語言。課程內容非常有趣，完善了考研究所時沒那麼清楚的概念！作業有 4 次，都是紙本作答，Loading 算輕鬆，很值得修。
        </div>
    </div>
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Computational_Complexity/" class="course-title">計算複雜度 (Computational Complexity)</a>
            <div class="course-meta">
                <span class="prof-name">蔡錫鈞</span>
                <span class="grade-badge">A</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            我指導教授的課程，計算複雜度的下半節。著重在各種時間與空間複雜度類別。課程內容蠻多證明，很有挑戰性！作業 3 次，都是紙本證明，Loading 正常，值得修。
        </div>
    </div>
    <div class="course-card">
        <div class="course-header">
            <a href="/courses/Data_Science_Project/" class="course-title">數據科學專題 (Data Science Project)</a>
            <div class="course-meta">
                <span class="prof-name">彭文志</span>
                <span class="grade-badge">A</span>
                <span style="font-size: 12px; color: #888;">✅ 已修畢</span>
            </div>
        </div>
        <div class="course-desc">
            數據所必修。要求將自己的研究包裝成論文並報告。由於碩論題目未定，我以當時研究的猜想進行實驗呈現。算是一門引導做論文的課程，過渡期還算不錯。
        </div>
    </div>
</div>

<br><br>

<div style="text-align: center; margin-top: 40px; margin-bottom: 40px;">
    <p style="color: #aaa; margin-bottom: 20px;">
        * 研究生除了修課，最重要的就是碩士論文！
    </p>
    <a href="/posts/master_studies/" class="cta-button">
        🚀 前往：碩士研究簡介 (Master's Research)
    </a>
</div>

<div align="center">
    <a href="/" style="
        background-color: var(--primary); /* 使用主題色，或改成 #007bff (藍色) */
        color: var(--theme);              /* 文字顏色會自動跟隨深淺模式 */
        padding: 12px 25px;               /* 按鈕大小 */
        text-decoration: none;            /* 去除底線 */
        border-radius: 8px;               /* 圓角 */
        border: 2px solid var(--primary); /* 邊框 */
        font-weight: bold;                /* 粗體 */
        transition: transform 0.2s;       /* 點擊特效 */
    ">
        🏠 回到首頁
    </a>
    <!-- &emsp;&emsp;
    <a href="https://mail.google.com/mail/?view=cm&fs=1&to=oxolll58x85@gmail.com&su=來自網站的聯絡&body=Hi cyh,%0D%0A%0D%0A我想請問關於..." style="
        background-color: #e74c3c; /* 紅色背景 */
        color: white;              /* 白色文字 */
        padding: 12px 25px;
        text-decoration: none;
        border-radius: 30px;       /* 橢圓形按鈕 */
        font-weight: bold;
        box-shadow: 0 4px 6px rgba(0,0,0,0.1); /* 陰影 */
    ">
        ✉️ 寄信聯絡我
    </a> -->

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