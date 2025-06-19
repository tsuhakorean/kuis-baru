<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Tebak Arti Kosakata Korea: Tema Sekolah</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f0f4f8;
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }
    .container {
      background: white;
      border-radius: 16px;
      padding: 24px;
      box-shadow: 0 0 15px rgba(0,0,0,0.1);
      width: 90%;
      max-width: 600px;
    }
    h1, h2 {
      text-align: center;
    }
    .question {
      font-size: 1.2em;
      margin-bottom: 16px;
    }
    .answers button {
      display: block;
      margin: 8px 0;
      padding: 10px;
      width: 100%;
      border: none;
      border-radius: 8px;
      background-color: #dbeafe;
      cursor: pointer;
      transition: background 0.3s;
    }
    .answers button:hover {
      background-color: #93c5fd;
    }
    .hidden {
      display: none;
    }
    .correct {
      background-color: #bbf7d0 !important;
    }
    .wrong {
      background-color: #fecaca !important;
    }
    #score-board {
      margin-top: 16px;
      text-align: center;
    }
    .word-list {
      margin-top: 24px;
      background: #f9fafb;
      padding: 16px;
      border-radius: 8px;
    }
    .word-list ul {
      padding-left: 20px;
    }
    .wrong-answer {
      color: red;
    }
    .correct-answer::before {
      content: "✅ ";
      color: green;
    }
    .incorrect-answer::before {
      content: "❌ ";
      color: red;
    }
  </style>
</head>
<body>
<div class="container">
  <div id="start-screen">
    <h1>Tebak Arti Kosakata Korea: Tema Sekolah</h1>
    <p>Masukkan namamu untuk mulai kuis!</p>
    <input type="text" id="player-name" placeholder="Nama kamu" style="padding:10px;width:100%;margin-bottom:16px;">
    <button onclick="startQuiz()">Mulai Kuis</button>
  </div>

  <div id="quiz-screen" class="hidden">
    <h2 id="player-display"></h2>
    <div id="question-box">
      <div class="question" id="question"></div>
      <div class="answers" id="answers"></div>
    </div>
    <div id="score-board"></div>
    <div class="word-list hidden" id="word-list">
      <h3>📚 Kosakata & Artinya:</h3>
      <ul id="word-list-ul"></ul>
    </div>
  </div>
</div>

<script>
const quizData = [
  { q: "학교", a: "Sekolah" },
  { q: "선생님", a: "Guru" },
  { q: "학생", a: "Siswa" },
  { q: "교실", a: "Ruang kelas" },
  { q: "책", a: "Buku" },
  { q: "공책", a: "Buku tulis" },
  { q: "연필", a: "Pensil" },
  { q: "지우개", a: "Penghapus" },
  { q: "칠판", a: "Papan tulis" },
  { q: "시험", a: "Ujian" },
  { q: "숙제", a: "PR" },
  { q: "공부", a: "Belajar" },
  { q: "교장선생님", a: "Kepala sekolah" },
  { q: "친구", a: "Teman" },
  { q: "수업", a: "Pelajaran" },
  { q: "시간표", a: "Jadwal pelajaran" },
  { q: "사전", a: "Kamus" },
  { q: "컴퓨터", a: "Komputer" },
  { q: "도서관", a: "Perpustakaan" },
  { q: "체육관", a: "Aula olahraga" },
];

let current = 0;
let score = 0;
let player = "";
let userAnswers = [];

function startQuiz() {
  const nameInput = document.getElementById("player-name");
  player = nameInput.value.trim();
  if (!player) return alert("Masukkan namamu dulu!");

  document.getElementById("start-screen").classList.add("hidden");
  document.getElementById("quiz-screen").classList.remove("hidden");
  document.getElementById("player-display").innerText = `Selamat datang, ${player}!`;
  showQuestion();
}

function showQuestion() {
  if (current >= quizData.length) {
    document.getElementById("question-box").innerHTML = `<h3>Kuis selesai!</h3><p>${player}, skor kamu: ${score} dari 100</p>`;
    localStorage.setItem("kuis-korea-" + player, score);
    showWordList();
    return;
  }
  const q = quizData[current];
  document.getElementById("question").innerText = `"${q.q}" artinya apa?`;

  const wrongOptions = quizData
    .filter(item => item.a !== q.a)
    .sort(() => 0.5 - Math.random())
    .slice(0, 3)
    .map(item => item.a);

  const options = [q.a, ...wrongOptions].sort(() => 0.5 - Math.random());

  const answersDiv = document.getElementById("answers");
  answersDiv.innerHTML = "";
  options.forEach(opt => {
    const btn = document.createElement("button");
    btn.innerText = opt;
    btn.onclick = () => checkAnswer(opt, q.a, btn);
    answersDiv.appendChild(btn);
  });
}

function checkAnswer(selected, correct, btn) {
  userAnswers.push({
    question: quizData[current].q,
    correct: correct,
    selected: selected
  });

  const allButtons = document.querySelectorAll("#answers button");
  allButtons.forEach(b => {
    b.disabled = true;
    if (b.innerText === correct) b.classList.add("correct");
    if (b.innerText === selected && selected !== correct) b.classList.add("wrong");
  });
  if (selected === correct) score += 5;

  setTimeout(() => {
    current++;
    showQuestion();
  }, 1000);
}

function showWordList() {
  const wordListDiv = document.getElementById("word-list");
  const ul = document.getElementById("word-list-ul");
  ul.innerHTML = "";
  userAnswers.forEach(item => {
    const li = document.createElement("li");
    if (item.selected !== item.correct) {
      li.classList.add("incorrect-answer");
      li.innerHTML = `${item.question} = ${item.correct} <span class="wrong-answer">(Jawaban kamu: ${item.selected})</span>`;
    } else {
      li.classList.add("correct-answer");
      li.innerText = `${item.question} = ${item.correct}`;
    }
    ul.appendChild(li);
  });
  wordListDiv.classList.remove("hidden");
}
</script>
</body>
</html>
