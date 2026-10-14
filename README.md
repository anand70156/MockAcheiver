# MockAcheiver<!DOCTYPE html>
<html lang="hi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>SKAU Clerk Mock Test</title>
  <style>
    :root {
      --primary: #1a73e8;
      --success: #28a745;
      --danger: #dc3545;
      --bg: #f8f9fa;
    }
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: var(--bg);
      margin: 0;
      padding: 20px;
      color: #333;
    }
    .container {
      max-width: 800px;
      margin: auto;
      background: #fff;
      padding: 25px;
      border-radius: 10px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.08);
    }
    .header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 2px solid #eee;
      padding-bottom: 15px;
      margin-bottom: 20px;
    }
    .timer {
      font-size: 1.2rem;
      font-weight: bold;
      color: #d93025;
      background: #ffebe6;
      padding: 6px 14px;
      border-radius: 20px;
    }
    .q-card {
      margin-bottom: 25px;
      padding: 16px;
      border: 1px solid #e0e0e0;
      border-radius: 8px;
    }
    .q-title {
      font-weight: 600;
      font-size: 1.05rem;
      margin-bottom: 12px;
    }
    .option-label {
      display: block;
      padding: 10px 14px;
      margin: 6px 0;
      background: #f1f3f4;
      border-radius: 6px;
      cursor: pointer;
      transition: background 0.2s;
    }
    .option-label:hover {
      background: #e8eaed;
    }
    .option-label input {
      margin-right: 10px;
    }
    .btn-submit {
      display: block;
      width: 100%;
      background: var(--primary);
      color: white;
      border: none;
      padding: 14px;
      font-size: 1.1rem;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
    }
    .btn-submit:hover {
      background: #1557b0;
    }
    /* रिजल्ट और समरी स्टाइल */
    .summary-box {
      display: none;
      margin-top: 12px;
      padding: 12px;
      border-radius: 6px;
      font-size: 0.95rem;
      line-height: 1.5;
    }
    .correct-ans {
      background: #e6f4ea;
      border-left: 4px solid var(--success);
      color: #137333;
    }
    .wrong-ans {
      background: #fce8e6;
      border-left: 4px solid var(--danger);
      color: #c5221f;
    }
    .explanation {
      margin-top: 6px;
      color: #444;
      font-style: normal;
      background: #fff;
      padding: 8px;
      border-radius: 4px;
      border: 1px dashed #ccc;
    }
    .score-banner {
      display: none;
      text-align: center;
      padding: 20px;
      background: #e8f0fe;
      border-radius: 8px;
      margin-bottom: 25px;
    }
  </style>
</head>
<body>

<div class="container">
  <div class="header">
    <h2 style="margin:0; font-size:1.3rem;">SKAU Clerk / Typist Practice Test</h2>
    <div class="timer" id="timer">Time: 10:00</div>
  </div>

  <div id="scoreBanner" class="score-banner">
    <h2 id="scoreText" style="margin:0; color:#1a73e8;"></h2>
    <p style="margin:8px 0 0 0; color:#555;">नीचे सभी सवालों के सही उत्तर और विस्तृत समरी (Explanation) देखें:</p>
  </div>

  <form id="quizForm">
    <div id="quizContainer"></div>
    <button type="button" class="btn-submit" id="submitBtn" onclick="submitQuiz()">Test Submit Karein</button>
  </form>
</div>

<script>
  // 1. यहाँ आप अपने सवाल, ऑप्शन्स और समरी जोड़ सकते हैं
  const questions = [
    {
      q: "Q1. MS Word में Text को Center Align करने के लिए किस शॉर्टकट कुंजी का उपयोग किया जाता है?",
      options: ["Ctrl + C", "Ctrl + E", "Ctrl + L", "Ctrl + J"],
      answer: 1, // 'Ctrl + E' 0-indexed (2nd option)
      summary: "Ctrl + E का उपयोग सेलेक्टेड टेक्स्ट को सेंटर अलाइन करने के लिए किया जाता है। (Ctrl + C = Copy, Ctrl + L = Left Align, Ctrl + J = Justify)।"
    },
    {
      q: "Q2. कंप्यूटर की मुख्य मेमोरी (Main Memory) किसे कहा जाता है?",
      options: ["Hard Disk", "RAM", "CD-ROM", "Pen Drive"],
      answer: 1,
      summary: "RAM (Random Access Memory) कंप्यूटर की प्राथमिक (Primary/Main) मेमोरी होती है, जो वोलेटाइल (अस्थायी) होती है।"
    },
    {
      q: "Q3. हरियाणा का पहला आयुष विश्वविद्यालय (SKAU) किस जिले में स्थित है?",
      options: ["हिसार", "कुरुक्षेत्र", "करनाल", "रोहतक"],
      answer: 1,
      summary: "श्री कृष्णा आयुष विश्वविद्यालय (SKAU) कुरुक्षेत्र, हरियाणा में स्थित है। यह राज्य का पहला आयुष विश्वविद्यालय है।"
    }
  ];

  // 2. क्विज लोड करना
  const container = document.getElementById("quizContainer");
  questions.forEach((item, index) => {
    let optsHtml = "";
    item.options.forEach((opt, oIndex) => {
      optsHtml += `
        <label class="option-label" id="opt_lbl_${index}_${oIndex}">
          <input type="radio" name="q${index}" value="${oIndex}"> ${opt}
        </label>
      `;
    });

    container.innerHTML += `
      <div class="q-card" id="card_${index}">
        <div class="q-title">${item.q}</div>
        ${optsHtml}
        <div class="summary-box" id="summary_${index}"></div>
      </div>
    `;
  });

  // 3. टाइमर लॉजिक (10 मिनट)
  let totalTime = 10 * 60;
  const timerEl = document.getElementById("timer");
  const countdown = setInterval(() => {
    let min = Math.floor(totalTime / 60);
    let sec = totalTime % 60;
    timerEl.innerText = `Time: ${min < 10 ? '0' : ''}${min}:${sec < 10 ? '0' : ''}${sec}`;
    totalTime--;
    if (totalTime < 0) {
      clearInterval(countdown);
      alert("समय समाप्त हो गया है! आपका टेस्ट स्वतः सबमिट हो रहा है।");
      submitQuiz();
    }
  }, 1000);

  // 4. टेस्ट सबमिशन और रिजल्ट/समरी दिखाना
  function submitQuiz() {
    clearInterval(countdown);
    let score = 0;
    const form = document.getElementById("quizForm");

    questions.forEach((item, index) => {
      const selected = form.querySelector(`input[name="q${index}"]:checked`);
      const summaryBox = document.getElementById(`summary_${index}`);
      summaryBox.style.display = "block";

      // रेडियो बटन डिसेबल करें ताकि सबमिट के बाद बदल न सकें
      const allRadios = form.querySelectorAll(`input[name="q${index}"]`);
      allRadios.forEach(r => r.disabled = true);

      if (selected) {
        const val = parseInt(selected.value);
        if (val === item.answer) {
          score++;
          summaryBox.className = "summary-box correct-ans";
          summaryBox.innerHTML = `<strong>✔ आपका उत्तर सही है!</strong><div class="explanation"><strong>व्याख्या/समरी:</strong> ${item.summary}</div>`;
        } else {
          summaryBox.className = "summary-box wrong-ans";
          summaryBox.innerHTML = `<strong>✖ आपका उत्तर गलत है।</strong><br>सही उत्तर: <b>${item.options[item.answer]}</b><div class="explanation"><strong>व्याख्या/समरी:</strong> ${item.summary}</div>`;
        }
      } else {
        summaryBox.className = "summary-box wrong-ans";
        summaryBox.innerHTML = `<strong>आपने यह सवाल हल नहीं किया।</strong><br>सही उत्तर: <b>${item.options[item.answer]}</b><div class="explanation"><strong>व्याख्या/समरी:</strong> ${item.summary}</div>`;
      }
    });

    // स्कोर बैनर दिखाना
    document.getElementById("scoreBanner").style.display = "block";
    document.getElementById("scoreText").innerText = `आपका कुल स्कोर: ${score} / ${questions.length}`;
    document.getElementById("submitBtn").style.display = "none";
    window.scrollTo({ top: 0, behavior: 'smooth' });
  }
</script>

</body>
</html>
