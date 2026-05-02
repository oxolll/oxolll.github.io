---
title: "🛤️ 碩士生存指南：我的迷惘、撞牆與進化之路"
date: 2026-02-18T10:00:00+08:00
draft: false
weight: 3
categories: ["Life & Misc"]
tags: ["Career", "Reflection", "Master", "NYCU", "Research"]
keywords: ["陽明交通大學", "碩士生存", "CCC", "STOC", "k-SAT", "研究心得", "畢業感言"]
description: "從瘋狂修課到題目卡關，再到挑戰頂級會議 STOC 與 CCC。這是我在陽明交大三年半，從迷惘中找到方向的真實生存紀錄。"
---

<style>
    :root {
        --accent-blue: #66d9ef;
        --accent-pink: #f92672;
        --accent-yellow: #e6db74;
        --accent-green: #a6e22e;
        --text-main: #e6e6e6;
        --bg-card: #252526;
        --timeline-line: #444;
    }

    /* 引言區塊 */
    .intro-box {
        border-left: 5px solid var(--accent-blue);
        background: linear-gradient(90deg, rgba(30, 58, 95, 0.3) 0%, rgba(0,0,0,0) 100%);
        padding: 20px;
        margin-bottom: 40px;
        font-style: italic;
        color: #ccc;
        border-radius: 0 5px 5px 0;
    }

    /* 章節標題 - 改為並排顯示，不重疊 */
    .chapter-title {
        font-size: 24px;
        font-weight: bold;
        color: #fff;
        margin-top: 60px;
        margin-bottom: 25px;
        display: flex;
        align-items: baseline; /* 讓文字底部對齊 */
        gap: 15px;
        border-bottom: 1px solid #333;
        padding-bottom: 10px;
    }
    
    /* 章節數字 - 改為亮色並排 */
    .chapter-number {
        font-size: 32px; /* 稍微縮小 */
        color: var(--accent-blue); /* 改用醒目的藍色 */
        font-weight: 900;
        opacity: 0.9;
        /* 移除原本的絕對定位 (absolute)，避免重疊 */
        position: static;
        transform: none;
    }

    /* 時間軸樣式 */
    .timeline {
        position: relative;
        margin: 30px 0;
        padding-left: 30px;
        border-left: 2px solid var(--timeline-line);
    }
    .timeline-item {
        position: relative;
        margin-bottom: 30px;
    }
    .timeline-dot {
        position: absolute;
        left: -37px;
        top: 5px;
        width: 14px;
        height: 14px;
        border-radius: 50%;
        background-color: var(--accent-blue);
        border: 3px solid #1e1e1e;
        box-shadow: 0 0 5px rgba(0,0,0,0.5);
    }
    .timeline-dot.fail { background-color: var(--accent-pink); }
    .timeline-dot.success { background-color: var(--accent-green); }
    
    .timeline-date {
        font-size: 0.85em;
        color: #888;
        margin-bottom: 5px;
        font-family: monospace;
        text-transform: uppercase;
        letter-spacing: 1px;
    }
    .timeline-content {
        background-color: var(--bg-card);
        padding: 18px;
        border-radius: 8px;
        border-left: 3px solid transparent;
        box-shadow: 0 4px 6px rgba(0,0,0,0.2);
        transition: transform 0.2s ease;
    }
    .timeline-content:hover {
        border-left-color: var(--accent-yellow);
        transform: translateX(5px);
    }

    /* 對比卡片 */
    .vs-container {
        display: flex;
        gap: 20px;
        margin: 30px 0;
        flex-wrap: wrap;
    }
    .vs-card {
        flex: 1;
        min-width: 280px;
        background-color: var(--bg-card);
        padding: 20px;
        border-radius: 8px;
        border-top: 4px solid #555;
        box-shadow: 0 4px 6px rgba(0,0,0,0.2);
    }
    .vs-card.reality { border-top-color: var(--accent-pink); }
    .vs-card.expectation { border-top-color: var(--accent-green); }
    .vs-title {
        font-weight: bold;
        font-size: 18px;
        margin-bottom: 15px;
        display: block;
        padding-bottom: 10px;
        border-bottom: 1px dashed #444;
    }
    .vs-card ul {
        padding-left: 20px;
        margin: 0;
    }
    .vs-card li {
        margin-bottom: 8px;
    }

    /* 金句語錄 */
    .quote-box {
        text-align: center;
        margin: 50px auto;
        padding: 30px;
        border: 1px dashed #555;
        border-radius: 10px;
        background-color: rgba(255, 255, 255, 0.02);
        max-width: 80%;
    }
    .quote-text {
        font-size: 22px;
        font-weight: bold;
        color: var(--accent-yellow);
        font-family: "Times New Roman", serif;
        font-style: italic;
    }

    /* 手機版適配 */
    @media (max-width: 768px) {
        .vs-container { flex-direction: column; }
        .quote-box { max-width: 100%; padding: 20px; }
        .chapter-title { font-size: 20px; flex-direction: row; }
    }
</style>

<div class="intro-box">
    「研究不是一條直線，而是在迷霧中不斷撞牆，直到你找到那扇門的過程。」
    <br><br>
    這篇文章不講艱澀的數學證明，只想聊聊這三年半來，我在理想與現實的夾縫中，如何從一個瘋狂修課的碩士生，經歷題目卡關、實驗失敗，最終轉變為挑戰頂級會議的研究者。
</div>

<div class="chapter-title">
    <span class="chapter-number">01</span>
    🔥 地獄開局：33 個作業的修煉
</div>

剛進入陽明交大時，我抱著一個單純（且天真）的想法：「碩一趕快把課修完，碩二就能專心做研究。」於是我在碩一上學期做了一個瘋狂的決定：**修 5 門課**。

其中包括了那門 Loading 重到出名的「機器學習」。雖然當時沒選上只能旁聽，但老師給旁聽生的條件是：**「想聽課？那就得做所有修課學生做的一切！」**

<div class="vs-container">
    <div class="vs-card expectation">
        <span class="vs-title" style="color: var(--accent-green);">😌 理想中的碩一</span>
        <ul>
            <li>白天優雅地聽課</li>
            <li>晚上悠閒地讀 Paper</li>
            <li>週末還能去新竹巨城逛街看電影</li>
        </ul>
    </div>
    <div class="vs-card reality">
        <span class="vs-title" style="color: var(--accent-pink);">😱 現實中的碩一</span>
        <ul>
            <li><strong>33 個作業</strong> 無情轟炸</li>
            <li>每天寫 Code 寫到天亮，看著日出睡覺</li>
            <li>期末考週直接睡在實驗室地板</li>
            <li>成績：雖然都 Pass 了，但每一分都是用肝換來的</li>
        </ul>
    </div>
</div>

這段經歷雖然痛苦，卻意外地磨練了我的 **實作能力 (Coding)** 與 **極限時間管理**。這也解釋了為什麼我的成績單看起來「還行」，但背後全是血淚。

<br>

<div class="chapter-title">
    <span class="chapter-number">02</span>
    🌫️ 迷霧撞牆：題目在哪裡？
</div>

碩一下結束後，修課壓力暫緩，隨之而來的是研究生最大的夢魘：**「我的題目是什麼？」**

很多人以為進了實驗室就會有題目，但在我們這組（演算法組），題目幾乎得**自己找**。這中間我經歷了長達兩年的試錯與撞牆期。

<div class="timeline">
    <div class="timeline-item">
        <div class="timeline-dot"></div>
        <div class="timeline-date">碩二上 (Year 2, Sem 1)</div>
        <div class="timeline-content">
            <strong>⛔ 初次嘗試：Analysis of Boolean Functions</strong><br>
            嘗試了一個書中的猜想，但研究半年後發現沒有顯著突破，加上老師興趣缺缺，只能止損終止。這是我第一次感受到研究的無力感。
        </div>
    </div>
    <div class="timeline-item">
        <div class="timeline-dot"></div>
        <div class="timeline-date">碩二下 (Year 2, Sem 2)</div>
        <div class="timeline-content">
            <strong>😶 沉默蟄伏期</strong><br>
            實驗室還有 7 位學長姐排隊等畢業，資源有限。我轉而大量閱讀論文，試圖從 <i>"Local Enumeration and Majority Lower bound"</i> 這篇論文中尋找靈感，並開始實作 TreeSearch 演算法。
        </div>
    </div>
    <div class="timeline-item">
        <div class="timeline-dot"></div>
        <div class="timeline-date">碩三上 (Year 3, Sem 1)</div>
        <div class="timeline-content">
            <strong>🛠️ 工程師魂爆發</strong><br>
            試圖設計一套基於 TreeSearch 改良的演算法。這段期間我像個工程師一樣瘋狂優化：雙向搜尋、DLX 模式、甚至試圖融入 CDCL（後來證明這條路不通）。這也是我專案中 SAT-Solver 的雛形。
        </div>
    </div>
    <div class="timeline-item">
        <div class="timeline-dot fail"></div>
        <div class="timeline-date">碩三下 (Year 3, Sem 2)</div>
        <div class="timeline-content">
            <strong>📉 重大挫折：DPLL vs CDCL</strong><br>
            我的演算法雖然能跑，但在與現代主流的 CDCL 比較效能時，幾乎被碾壓。
            <br><br>
            <em>「如果不比效能，那貢獻在哪？」</em>
            <br><br>
            老師這句話雖然殘酷，但也點醒了我。這個耗時一年的專案，最終因為無法證明其學術價值，只能含淚封存。看著同屆同學紛紛畢業，那是我最迷惘的時刻。
        </div>
    </div>
</div>

<div class="quote-box">
    <div class="quote-text">
        "Research is what I'm doing when I don't know what I'm doing."
    </div>
    <div style="margin-top: 15px; color: #888; font-size: 0.9em;">— Wernher von Braun (火箭科學家)</div>
</div>

<div class="chapter-title">
    <span class="chapter-number">03</span>
    🚀 轉折與突破：STOC 與 CCC 的挑戰
</div>

在碩三下暑假前，老師給了一個關鍵建議：**「不要只做實驗，試著去推廣 $k > 3$ 的數學情況。」**

這句話成了轉捩點。我暫時放下了鍵盤，拿起了紙筆。我不再執著於寫出更快的 Code，而是試圖證明更廣的定理。

### 📅 碩四上：最後的衝刺與完美的句點

這學期是我碩士生涯最高壓、也最充實的時光。

從暑假開始，我每天都在與論文鬥爭。原本 $k=3$ 的特殊性質在 $k > 3$ 時完全失效，我必須建立新的數學框架。老師回國後，看見了初稿的潛力，我們開始了高強度的數學檢驗與校正。

* **投遞 STOC**：我們將這份成果投遞至 **STOC (Symposium on Theory of Computing)**。這代表了我們對研究品質的最高信心。
* **畢業口試**：雖然當時 STOC 的結果尚未出爐，但我帶著這份充滿自信的研究成果站上了口試台。

最終，我拿到了 **A+ (95/95/98)** 的高分。那一刻，三年半的迷惘與焦慮，終於化為了踏實的學位證書。

<br>

### 🔄 畢業後的延續：轉戰 CCC

故事並沒有因為拿到畢業證書而結束。

畢業後，我們收到了 STOC 的審查結果——雖然遺憾未被錄取，但 Reviewers 給予了非常有價值的反饋。

我們沒有就此停手，而是**根據建議進一步完善了論文細節，讓原先推導出的 $k=4$ 與 $k=5$ 上界論述更為嚴謹**，並轉戰投稿至 **CCC (Computational Complexity Conference)**。

這段經歷證明了：**「畢業是碩士生涯的終點，但對真理的追求沒有終點。」**

<br>
<hr style="border-color: #333; margin: 40px 0;">

### 結語：給還在迷惘的你

碩士三年半，我學到的不只是如何證明一個定理，更重要的是學會了**如何面對「未知」**。

如果你也是正在迷惘的研究生，看著別人發 Paper、畢業，而自己還在原地打轉，我想對你說：

> **「題目換了沒關係，實驗失敗也沒關係。只要你還在思考，你就是在前進。」**

<br>
<div style="text-align: center;">
    <a href="/projects/" style="
        display: inline-block;
        background-color: var(--accent-blue);
        color: #111;
        padding: 12px 30px;
        border-radius: 50px;
        text-decoration: none;
        font-weight: bold;
        transition: transform 0.2s;
        box-shadow: 0 4px 15px rgba(102, 217, 239, 0.3);
    ">
        👉 看看我的研究成果 (Project List)
    </a>
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