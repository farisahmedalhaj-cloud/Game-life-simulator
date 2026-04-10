# Game-life-simulator
<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>🎮 Life Game Pro</title>

<style>
body {
  font-family: Arial;
  text-align: center;
  background: linear-gradient(135deg, #0f0f0f, #1f1f1f);
  color: white;
}

h1 {
  margin-top: 20px;
}

.card {
  background: #222;
  padding: 20px;
  border-radius: 15px;
  display: inline-block;
  margin-top: 20px;
  width: 300px;
}

button {
  margin: 5px;
  padding: 10px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}

button:hover {
  opacity: 0.8;
}

.bar {
  height: 10px;
  background: gray;
  border-radius: 10px;
  margin: 5px 0;
}

.fill {
  height: 10px;
  border-radius: 10px;
}

#event {
  margin-top: 15px;
  color: #ffd700;
}

#achievements {
  margin-top: 15px;
  font-size: 14px;
}
</style>

</head>
<body>

<h1>🎮 Life Game Pro</h1>

<div class="card">
  <h2 id="day"></h2>
  <h3>Level: <span id="level"></span></h3>

  <button onclick="choose('study')">📚</button>
  <button onclick="choose('play')">🎮</button>
  <button onclick="choose('gym')">🏃</button>
  <button onclick="choose('rest')">😴</button>

  <p>💪</p>
  <div class="bar"><div id="energyBar" class="fill"></div></div>

  <p>🧠</p>
  <div class="bar"><div id="smartBar" class="fill"></div></div>

  <p>😊</p>
  <div class="bar"><div id="happyBar" class="fill"></div></div>

  <p id="event"></p>
  <p id="achievements"></p>
</div>

<h2 id="result"></h2>
<button onclick="resetGame()">🔄 Restart</button>

<script>
let game = JSON.parse(localStorage.getItem("lifePro")) || {
  day: 1,
  energy: 50,
  smart: 50,
  happy: 50,
  level: 1,
  achievements: []
};

function save() {
  localStorage.setItem("lifePro", JSON.stringify(game));
}

function updateBars() {
  document.getElementById("day").textContent = "Day " + game.day;
  document.getElementById("level").textContent = game.level;

  setBar("energyBar", game.energy, "red");
  setBar("smartBar", game.smart, "blue");
  setBar("happyBar", game.happy, "green");

  document.getElementById("achievements").textContent =
    "🏆 " + game.achievements.join(" | ");
}

function setBar(id, value, color) {
  let bar = document.getElementById(id);
  bar.style.width = value + "%";
  bar.style.background = color;
}

function choose(action) {
  if (game.day > 10) return;

  if (action === "study") {
    game.smart += 10;
    game.energy -= 7;
  }

  if (action === "play") {
    game.happy += 10;
    game.energy -= 5;
  }

  if (action === "gym") {
    game.energy += 10;
    game.happy += 5;
  }

  if (action === "rest") {
    game.energy += 12;
  }

  randomEvent();
  checkAchievements();

  game.day++;
  game.level = Math.floor((game.smart + game.happy) / 50);

  clamp();
  save();
  updateBars();

  if (game.day > 10) endGame();
}

function randomEvent() {
  let events = [
    { text: "💰 لقيت فلوس!", effect: () => game.happy += 10 },
    { text: "😓 يوم سيء", effect: () => game.happy -= 10 },
    { text: "📚 فرصة تعلم!", effect: () => game.smart += 10 },
    { text: "🤒 تعبت", effect: () => game.energy -= 10 }
  ];

  let e = events[Math.floor(Math.random() * events.length)];
  e.effect();
  document.getElementById("event").textContent = e.text;
}

function checkAchievements() {
  if (game.smart >= 80 && !game.achievements.includes("عبقري")) {
    game.achievements.push("عبقري 🧠");
  }

  if (game.happy >= 80 && !game.achievements.includes("سعيد")) {
    game.achievements.push("سعيد 😎");
  }

  if (game.energy >= 80 && !game.achievements.includes("نشيط")) {
    game.achievements.push("نشيط 💪");
  }
}

function clamp() {
  game.energy = Math.max(0, Math.min(100, game.energy));
  game.smart = Math.max(0, Math.min(100, game.smart));
  game.happy = Math.max(0, Math.min(100, game.happy));
}

function endGame() {
  let r = "";

  if (game.smart > 90) r = "🔥 صرت عبقري عالمي!";
  else if (game.happy > 90) r = "🎉 حياتك حلم!";
  else if (game.energy < 20) r = "💀 انهار جسدك";
  else r = "😐 حياة متوسطة";

  document.getElementById("result").textContent = r;
}

function resetGame() {
  localStorage.removeItem("lifePro");
  location.reload();
}

updateBars();
</script>

</body>
</html>
