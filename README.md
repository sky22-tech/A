<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>これって重い？判定シート</title>
    <style>
        body { font-family: sans-serif; padding: 20px; text-align: center; background: #fff0f5; color: #333; }
        .container { max-width: 500px; margin: 0 auto; background: white; padding: 30px 20px; border-radius: 15px; border: 3px solid #ff69b4; box-shadow: 0 4px 10px rgba(0,0,0,0.05); }
        h2 { color: #ff1493; font-size: 22px; margin-bottom: 20px; }
        #question-text { font-size: 18px; font-weight: bold; margin-bottom: 30px; line-height: 1.5; min-height: 60px; display: flex; align-items: center; justify-content: center; }
        .btn-container { display: flex; flex-direction: column; gap: 12px; margin-top: 20px; }
        button { padding: 14px; font-size: 16px; border: 2px solid #ffb6c1; border-radius: 8px; background: #fff; cursor: pointer; transition: 0.2s; font-weight: bold; color: #ff1493; }
        button:hover { background: #ffe4e1; border-color: #ff69b4; }
        .hidden { display: none; }
    </style>
</head>
<body>

<div class="container">
    <!-- 診断中の画面 -->
    <div id="quiz-screen">
        <h2 id="question-number">第 1 問</h2>
        <p id="question-text">読み込み中...</p>
        <div class="btn-container" id="answer-buttons"></div>
    </div>

    <!-- 結果画面 -->
    <div id="result-screen" class="hidden">
        <h2>判定結果</h2>
        <p id="result-text">あなたの合計点数は...</p>
        <button onclick="location.reload()" style="margin-top: 20px; background: #ff69b4; color: white;">もう一度やる</button>
    </div>
</div>

<script>
// 画像（1枚目〜3枚目）から抽出した全30問のデータ
const quizData = [
    // 1枚目
    { question: "毎日「好き」「愛してる」と言ってくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "毎朝「おはよう」、毎晩「おやすみ」の連絡をしてくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "LINEを数時間返さないと追いLINEしてくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "毎晩寝落ち通話をしたがる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "デートのたびに家まで送り届けたがる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "会うたびに「次いつ会える？」と聞いてくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分が何気なく話したことを細かく覚えている", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分の写真をスマホの待ち受けにしている", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "毎回デートプランを完璧に考えてくれる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "休日はできるだけ自分と一緒に過ごしたがる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    
    // 2枚目
    { question: "毎日「今どこ？誰といるの？」と聞いてくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分がほかの人を褒めると嫉妬して拗ねる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分のSNSの投稿に毎回いいね・コメントをする", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "SNSのアイコンを二人の写真にしたがる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "友達に会うたび、自分のことを恋人として紹介したがる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分が飲み会に行くと、帰宅するまで起きて待っている", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "お互いの予定を全部共有カレンダーに入れたがる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "お互いのスマホの位置情報を共有したがる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分のSNSのフォロー欄を定期的にチェックしている", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "「自分のことを誰よりも優先してほしい」と言ってくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "付き合って1か月で「結婚したい」と言ってくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    
    // 3枚目
    { question: "自分の誕生日に何か月も前からサプライズを準備している", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分に合わせて趣味や生活習慣を変えようとする", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分が落ち込んでいると、予定をキャンセルして会いに来ようとする", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "「あなたがない人生なんて考えられない」と言ってくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分と一緒にいるために引っ越しを考え始める", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分の家族や友達と積極的に仲良くなろうとする", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分との将来のために毎月貯金を始める", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "自分の好きな作品や趣味を全部知ろうとする", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] },
    { question: "「生まれ変わっても絶対にあなたと恋人になりたい」と言ってくる", answers: [{ text: "① 嬉しい", score: 0 }, { text: "② 嬉しいけど重い", score: 1 }, { text: "③ 重い", score: 2 }] }
];

let currentQuestionIndex = 0;
let totalScore = 0;

function showQuestion() {
    const currentQuestion = quizData[currentQuestionIndex];
    document.getElementById("question-number").innerText = `第 ${currentQuestionIndex + 1} 問 / 全 ${quizData.length} 問`;
    document.getElementById("question-text").innerText = currentQuestion.question;
    
    const buttonContainer = document.getElementById("answer-buttons");
    buttonContainer.innerHTML = "";
    
    currentQuestion.answers.forEach(answer => {
        const button = document.createElement("button");
        button.innerText = answer.text;
        button.addEventListener("click", () => selectAnswer(answer.score));
        buttonContainer.appendChild(button);
    });
}

function selectAnswer(score) {
    totalScore += score;
    currentQuestionIndex++;
    
    if (currentQuestionIndex < quizData.length) {
        showQuestion();
    } else {
        showResult();
    }
}

function showResult() {
    document.getElementById("quiz-screen").classList.add("hidden");
    document.getElementById("result-screen").classList.remove("hidden");
    
    let evaluation = "";
    // 30問×最大2点＝60点満点での簡易判定ロジック
    if (totalScore >= 40) {
        evaluation = "【超・重量級タイプ】<br>相手からの愛が少し負担に感じやすい、または相手の行動がかなりヘビーな状態かもしれません。しっかりお互いの距離感を話し合ってみるのも手です。";
    } else if (totalScore >= 15) {
        evaluation = "【愛されバランスタイプ】<br>嬉しさと重さを適度に感じられている、一般的な感覚の持ち主です。愛されている証拠として可愛く受け流せるレベルです。";
    } else {
        evaluation = "【ウェルカム超寛容タイプ】<br>どんなアプローチも「嬉しい！」と受け止められる、器の大きいタイプ。むしろこれくらい愛されたいと思っている理想的な関係かも？";
    }
    
    document.getElementById("result-text").innerHTML = `あなたの合計重い度スコアは <strong>${totalScore} 点</strong>（60点満点）です。<br><br>${evaluation}`;
}

showQuestion();
</script>

</body>
</html>
