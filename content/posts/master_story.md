---
title: "🛤️ 碩士生存指南：我的迷惘與進化之路"
date: 2026-01-23T10:00:00+08:00
draft: false
tags: ["Master's Life", "Reflection"]
description: "從瘋狂修課到論文卡關，再到挑戰頂級會議 CCC。這是我在陽明交大兩年半的真實生存紀錄。"
---

<style>
    :root {
        --accent-blue: #66d9ef;
        --accent-pink: #f92672;
        --accent-yellow: #e6db74;
        --accent-green: #a6e22e;
        --text-main: #e6e6e6;
        --bg-card: #252526;
    }

    body {
        font-size: 16px;
        line-height: 1.8;
        color: var(--text-main);
    }

    /* 引言區塊 */
    .intro-box {
        border-left: 5px solid var(--accent-blue);
        background: linear-gradient(90deg, rgba(30, 58, 95, 0.3) 0%, rgba(0,0,0,0) 100%);
        padding: 20px;
        margin-bottom: 40px;
        font-style: italic;
        color: #ccc;
    }

    /* 章節標題 */
    .chapter-title {
        font-size: 24px;
        font-weight: bold;
        color: #fff;
        margin-top: 50px;
        margin-bottom: 20px;
        display: flex;
        align-items: center;
        gap: 10px;
    }
    .chapter-number {
        font-size: 40px;
        color: rgba(255, 255, 255, 0.1);
        font-weight: 900;
        position: absolute;
        transform: translateY(-10px);
        z-index: -1;
    }

    /* 對比卡片 (Expectation vs Reality) */
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
    }
    .vs-card.reality {
        border-top-color: var(--accent-pink);
    }
    .vs-card.expectation {
        border-top-color: var(--accent-green);
    }
    .vs-title {
        font-weight: bold;
        font-size: 18px;
        margin-bottom: 10px;
        display: block;
    }

    /* 金句語錄 */
    .quote-box {
        text-align: center;
        margin: 40px 0;
        padding: 30px;
        border: 1px dashed #444;
        border-radius: 10px;
    }
    .quote-text {
        font-size: 20px;
        font-weight: bold;
        color: var(--accent-yellow);
        font-family: "Times New Roman", serif;
    }

    /* 重點標記 */
    .highlight {
        color: var(--accent-blue);
        font-weight: bold;
    }
</style>

<div class="intro-box">
    「研究不是一條直線，而是在迷霧中不斷撞牆，直到你找到那扇門的過程。」
    <br>
    這篇文章不講艱澀的數學證明，只想聊聊這兩年半來，我在理想與現實的夾縫中，如何從一個瘋狂修課的碩士生，轉變為挑戰頂級會議的研究者。
</div>

<div style="position: relative;">
    <span class="chapter-number">01</span>
    <div class="chapter-title">
        🔥 地獄開局：33 個作業的修煉
    </div>
</div>

剛進入陽明交大時，我抱著一個單純（且天真）的想法：「碩一趕快把課修完，碩二就能專心做研究。」於是我在碩一上學期做了一個瘋狂的決定：**修 5 門硬課**。

其中包括了那門傳說中的「機器學習」。雖然當時沒選上只能旁聽，但我為了學到東西，堅持跟著做所有的作業。

<div class="vs-container">
    <div class="vs-card expectation">
        <span class="vs-title" style="color: var(--accent-green);">😌 理想中的碩一</span>
        <ul>
            <li>白天優雅地聽課</li>
            <li>晚上悠閒地讀 Paper</li>
            <li>週末還有時間去新竹巨城逛街</li>
        </ul>
    </div>
    <div class="vs-card reality">
        <span class="vs-title" style="color: var(--accent-pink);">😱 現實中的碩一</span>
        <ul>
            <li><strong>33 個作業</strong> 無情轟炸</li>
            <li>每天寫 Code 寫到凌晨 3 點</li>
            <li>期末考週甚至直接睡在實驗室</li>
            <li>成績：雖然沒拿卷，但活下來了</li>
        </ul>
    </div>
</div>

這段經歷雖然痛苦，卻意外地磨練了我的 **實作能力 (Coding)** 與 **時間管理**。這也解釋了為什麼我的修課成績看起來「還行」——因為每一分都是用肝換來的。

<br>

<div style="position: relative;">
    <span class="chapter-number">02</span>
    <div class="chapter-title">
        🌫️ 迷霧時期：題目不是一次到位
    </div>
</div>

碩一結束後，修課壓力沒了，隨之而來的是更大的焦慮：**「我的題目在哪裡？」**

很多人以為研究生是進來就確定題目的，但我的情況完全相反。這中間經歷了好幾次波折：

1.  **最初的嘗試**：原本想做某個熱門的 AI 應用題目，但發現自己對「調參數」沒有太大的熱情。
2.  **轉折點**：在修了「難解計算問題專論」與「計算複雜度」後，我發現自己對 **理論推導 (Theoretical Derivation)** 更有感覺。那種「用數學證明這件事絕對做不到」的優雅感深深吸引了我。
3.  **定錨**：最終鎖定 **Circuit Lower Bounds** 與 **k-SAT**。這是一個硬核的領域，但我知道這就是我想挑戰的。

<div class="quote-box">
    <div class="quote-text">
        "Research is what I'm doing when I don't know what I'm doing."
    </div>
    <div style="margin-top: 10px; color: #888;">— Wernher von Braun</div>
</div>

<br>

<div style="position: relative;">
    <span class="chapter-number">03</span>
    <div class="chapter-title">
        🚀 衝刺與突破：挑戰 CCC
    </div>
</div>

確定方向後，挑戰才真正開始。

我的碩論題目涉及將 `Local Enumeration` 演算法推廣到 $k>3$ 的情形。這在數學上非常困難，因為 $k=3$ 有很多特殊的幾何性質，一旦 $k$ 變大，很多原本成立的假設都會失效。

那段時間我的日常是：
* 早上：讀 Paper，試圖理解前人到底是怎麼證出來的。
* 下午：在白板上推導公式，然後發現推錯了，擦掉重來。
* 晚上：寫 C++ 程式跑模擬實驗，看 Hamming Distance 的分佈圖，尋找數學規律的蛛絲馬跡。

<div style="background-color: #252526; border-left: 4px solid var(--accent-yellow); padding: 15px; margin: 20px 0; color: #ddd;">
    <strong>💡 關鍵時刻 (The Aha! Moment)：</strong><br>
    記得某個深夜，我看著實驗跑出來的數據曲線，發現它跟理論預測有一個固定的偏差值。那個瞬間我意識到：<strong>「我們可以修正上界公式！」</strong>。
</div>

最終，我們成功推導出了 $k=4$ 和 $k=5$ 的新上界，並趕在截止日期前將論文投稿至 **CCC (Computational Complexity Conference)**。雖然目前還在審查中，但無論結果如何，這段從「無」到「有」的過程，才是我碩士生涯最珍貴的資產。

<br>
<hr style="border-color: #333; margin: 40px 0;">

### 結語

碩士兩年半，我學到的不只是如何證明一個定理，更重要的是學會了**如何面對「未知」**。

如果你也是正在迷惘的研究生，我想對你說：**「題目換了沒關係，實驗失敗也沒關係。只要你還在思考，你就是在前進。」**

<br>
<div style="text-align: center;">
    <a href="/projects/" style="
        display: inline-block;
        background-color: var(--accent-blue);
        color: #111;
        padding: 10px 25px;
        border-radius: 50px;
        text-decoration: none;
        font-weight: bold;
        transition: transform 0.2s;
    ">
        👉 看看我的研究成果 (Project List)
    </a>
</div>