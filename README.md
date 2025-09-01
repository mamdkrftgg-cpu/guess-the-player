<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>لعبة أحزر اللاعب ⚽</title>
<style>
:root {
  --primary-color: #6a11cb;
  --secondary-color: #2575fc;
  --accent-color: #ff4757;
  --text-color: #fff;
  --card-bg: #fff;
  --card-text: #333;
  --button-bg: #4CAF50;
  --button-hover: #45a049;
}
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  margin:0;padding:0;
  display:flex;flex-direction:column;align-items:center;justify-content:center;
  min-height:100vh;background:linear-gradient(135deg,var(--primary-color),var(--secondary-color));
  color: var(--text-color);text-align:center;
}
.screen{display:none;flex-direction:column;align-items:center;width:100%;max-width:600px;padding:20px;box-sizing:border-box;}
.active{display:flex;}
h1{text-align:center;margin-bottom:20px;text-shadow:2px 2px 4px rgba(0,0,0,0.3);}
button{margin:10px;padding:12px 24px;border:none;border-radius:50px;font-size:18px;cursor:pointer;background-color:var(--button-bg);color:white;transition:all 0.3s ease;box-shadow:0 4px 6px rgba(0,0,0,0.1);width:200px;}
button:hover{background-color:var(--button-hover);transform:translateY(-2px);box-shadow:0 6px 8px rgba(0,0,0,0.15);}
button:active{transform:translateY(0);}
.card{background:var(--card-bg);color:var(--card-text);border-radius:20px;padding:20px;width:90%;max-width:400px;text-align:center;margin-bottom:20px;box-shadow:0 10px 20px rgba(0,0,0,0.2);transition: transform 0.3s ease;}
.card:hover{transform:translateY(-5px);}
.card img{max-width:100%;border-radius:15px;margin-bottom:15px;cursor:pointer;transition: transform 0.3s ease;}
.card img:hover{transform:scale(1.02);}
#answer{display:none;margin:10px 0;font-weight:bold;color:var(--primary-color);font-size:1.2em;}
#overlay{position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.9);display:none;align-items:center;justify-content:center;z-index:1000;cursor:pointer;}
#overlay img{max-width:95%;max-height:95%;border-radius:10px;}
#score,#timer{margin:5px 0;font-size:20px;font-weight:bold;}
.ad-bar{width:100%;text-align:center;padding:10px;background:rgba(0,0,0,0.3);margin-top:20px;border-radius:10px;}
.hints-container{margin:15px 0;text-align:right;direction:rtl;}
.hint{margin:8px 0;padding:8px;background:#f1f1f1;border-radius:8px;font-size:16px;}
.difficulty-buttons{display:flex;flex-direction:column;align-items:center;}
.difficulty-buttons button{width:150px;}
.easy-btn{background-color:#4CAF50;}
.medium-btn{background-color:#FF9800;}
.hard-btn{background-color:#f44336;}
.progress-bar{width:100%;background-color:#f1f1f1;border-radius:10px;margin:10px 0;}
.progress{height:10px;background-color:var(--primary-color);border-radius:10px;width:100%;transition:width 1s linear;}
@keyframes pulse{0%{transform:scale(1);}50%{transform:scale(1.05);}100%{transform:scale(1);}}
.pulse{animation:pulse 0.5s infinite;}
</style>
</head>
<body>

<div id="homeScreen" class="screen active">
  <h1>🎮 لعبة أحزر اللاعب ⚽</h1>
  <button onclick="goDifficulty()">ابدأ اللعب 🚀</button>
  <button onclick="openSettings()">⚙️ الإعدادات</button>
  <div class="ad-bar">🔔 مساحة إعلانية</div>
</div>

<div id="settingsScreen" class="screen">
  <h1>⚙️ الإعدادات</h1>
  <button onclick="playStadiumSounds()">تشغيل أصوات الملعب</button>
  <button onclick="goHome()">🏠 العودة للرئيسية</button>
  <div class="ad-bar">🔔 مساحة إعلانية</div>
</div>

<div id="difficultyScreen" class="screen">
  <h1>اختر مستوى الصعوبة</h1>
  <div class="difficulty-buttons">
    <button class="easy-btn" onclick="startGame('easy')">سهل ⭐</button>
    <button class="medium-btn" onclick="startGame('medium')">متوسط ⭐⭐</button>
    <button class="hard-btn" onclick="startGame('hard')">صعب ⭐⭐⭐</button>
  </div>
  <div class="ad-bar">🔔 مساحة إعلانية</div>
</div>

<div id="gameScreen" class="screen">
  <div id="score">النقاط: 0</div>
  <div id="timer">الوقت: 05:00</div>
  <div class="progress-bar">
    <div id="timeProgress" class="progress"></div>
  </div>
  <div class="card">
    <img id="playerImg" src="" alt="صورة اللاعب" onclick="showFullScreen(this.src)">
    <div class="hints-container" id="playerHints"></div>
    <p id="answer"></p>
    <button onclick="showAnswer()">كشف الإجابة</button>
    <button onclick="nextPlayer()">اللاعب التالي</button>
    <button onclick="restartGame()">إعادة اللعبة</button>
  </div>
  <div class="ad-bar">🔔 مساحة إعلانية</div>
</div>

<div id="endScreen" class="screen">
  <h1>🏁 نهاية اللعبة</h1>
  <p id="endMessage"></p>
  <button onclick="restartGame()">إعادة اللعب</button>
  <button onclick="goHome()">العودة للرئيسية</button>
  <div class="ad-bar">🔔 مساحة إعلانية</div>
</div>

<div id="overlay" onclick="hideOverlay()">
  <img src="" alt="صورة مكبرة للاعب">
</div>

<script>
// ===== اللاعبين =====
const players={
easy:[{name:"محمد صلاح",img:"https://upload.wikimedia.org/wikipedia/commons/2/2f/Mohamed_Salah_2018.jpg",hints:["🇪🇬 مصري","ليفربول","جناح أيمن","الفرعون"]},{name:"ليونيل ميسي",img:"https://upload.wikimedia.org/wikipedia/commons/8/89/Lionel_Messi_WC2022.jpg",hints:["🇦🇷 أرجنتيني","إنتر ميامي","أفضل لاعب في التاريخ","7 جوائز بلون دور"]},{name:"كريستيانو رونالدو",img:"https://upload.wikimedia.org/wikipedia/commons/8/8c/Cristiano_Ronaldo_2018.jpg",hints:["🇵🇹 برتغالي","النصر","هداف تاريخي","سي آر 7"]},{name:"نيمار",img:"https://upload.wikimedia.org/wikipedia/commons/0/0c/Neymar_2018.jpg",hints:["🇧🇷 برازيلي","الهلال","مهارات المراوغة","رقم 10 برشلونة"]},{name:"كيليان مبابي",img:"https://upload.wikimedia.org/wikipedia/commons/a/a8/Kylian_Mbappe_WC2022.jpg",hints:["🇫🇷 فرنسي","باريس سان جيرمان","سريع","كأس العالم 2018"]}],
medium:[{name:"فيرجيل فان دايك",img:"https://upload.wikimedia.org/wikipedia/commons/2/2e/Virgil_van_Dijk_2020.jpg",hints:["🇳🇱 هولندي","ليفربول","قلب دفاع","أفضل لاعب أوروبا 2019"]},{name:"كيفين دي بروين",img:"https://upload.wikimedia.org/wikipedia/commons/0/05/Kevin_De_Bruyne_2018.jpg",hints:["🇧🇪 بلجيكي","مانشستر سيتي","صانع ألعاب","أفضل لاعب إنجليزي 2020"]},{name:"روبرت ليفاندوفسكي",img:"https://upload.wikimedia.org/wikipedia/commons/9/9a/Robert_Lewandowski_2018.jpg",hints:["🇵🇱 بولندي","برشلونة","مهاجم","41 هدف موسم واحد"]},{name:"حكيم زياش",img:"https://upload.wikimedia.org/wikipedia/commons/6/6d/Hakim_Ziyech_2018.jpg",hints:["🇲🇦 مغربي","غلطة سراي","جناح أيسر","نصف نهائي كأس العالم 2022"]},{name:"توماس مولر",img:"https://upload.wikimedia.org/wikipedia/commons/5/5e/Thomas_M%C3%BCller_2018.jpg",hints:["🇩🇪 ألماني","بايرن ميونخ","مهاجم ثان","راصد المساحات"]}],
hard:[{name:"جورجينيو",img:"https://upload.wikimedia.org/wikipedia/commons/e/ee/Jorginho_2021.jpg",hints:["🇮🇹 إيطالي","أرسنال","وسط مدافع","أمم أوروبا 2020"]},{name:"روجيريو سيني",img:"https://upload.wikimedia.org/wikipedia/commons/6/6e/Rogerio_Ceni_2012.jpg",hints:["🇧🇷 برازيلي","حارس سابق","131 هدف","قائد ساو باولو"]},{name:"دافيد ألابا",img:"https://upload.wikimedia.org/wikipedia/commons/0/0d/David_Alaba_2020.jpg",hints:["🇦🇹 نمساوي","ريال مدريد","متعدد المراكز","منتج بايرن ميونخ"]},{name:"رايس نيلسون",img:"https://upload.wikimedia.org/wikipedia/commons/4/4c/Reiss_Nelson_2018.jpg",hints:["🏴 إنجليزي","أرسنال","مهاجم","منتج أكاديمية أرسنال"]},{name:"نيكولو باريلا",img:"https://upload.wikimedia.org/wikipedia/commons/2/2d/Nicolo_Barella_2020.jpg",hints:["🇮🇹 إيطالي","إنتر ميلان","وسط ميدان","أفضل لاعب أمم أوروبا 2020"]}]
};

// ===== الأصوات =====
const stadiumSound=new Audio("https://www.soundjay.com/misc/sounds/crowd-cheer-01.mp3");
const halfSound=new Audio("https://www.soundjay.com/misc/sounds/referee-whistle-1.mp3");
const soundAnswer=new Audio("https://www.soundjay.com/buttons/sounds/button-3.mp3");
const soundNext=new Audio("https://www.soundjay.com/buttons/sounds/button-09.mp3");
const timesUpSound=new Audio("https://www.soundjay.com/misc/sounds/fail-trombone-03.mp3");

// ===== متغيرات اللعبة =====
let difficulty="easy",usedPlayers=[],currentPlayer=null,score=0,timeLeft=5*60,totalTime=5*60,timerInterval;

// ===== وظائف التنقل =====
function switchScreen(s){document.querySelectorAll(".screen").forEach(e=>e.classList.remove("active"));document.getElementById(s).classList.add("active");}
function goDifficulty(){switchScreen("difficultyScreen");}
function openSettings(){switchScreen("settingsScreen");}
function goHome(){switchScreen("homeScreen");clearInterval(timerInterval);}
function playStadiumSounds(){stadiumSound.currentTime=0;stadiumSound.play();setTimeout(()=>{halfSound.currentTime=0;halfSound.play();},3000);}
function startGame(level){difficulty=level;score=0;timeLeft=totalTime;usedPlayers=[];document.getElementById("score").innerText="النقاط: 0";updateTimerDisplay();switchScreen("gameScreen");startTimer();loadPlayer();}
function getRandomPlayer(){const list=players[difficulty];if(usedPlayers.length>=list.length)usedPlayers=[];let player;do{player=list[Math.floor(Math.random()*list.length)];}while(usedPlayers.includes(player.name));usedPlayers.push(player.name);return player;}
function loadPlayer(){currentPlayer=getRandomPlayer();document.getElementById("playerImg").src=currentPlayer.img;const h=document.getElementById("playerHints");h.innerHTML="";currentPlayer.hints.forEach(t=>{const d=document.createElement("div");d.className="hint";d.textContent=t;h.appendChild(d);});document.getElementById("answer").style.display="none";}
function showAnswer(){document.getElementById("answer").style.display="block";document.getElementById("answer").innerText="✅ اللاعب هو: "+currentPlayer.name;score+=difficulty==="easy"?1:difficulty==="medium"?2:3;document.getElementById("score").innerText="النقاط: "+score;soundAnswer.currentTime=0;soundAnswer.play();}
function nextPlayer(){loadPlayer();soundNext.currentTime=0;soundNext.play();}
function startTimer(){clearInterval(timerInterval);timerInterval=setInterval(()=>{timeLeft--;updateTimerDisplay();if(timeLeft<=0){clearInterval(timerInterval);timesUpSound.currentTime=0;timesUpSound.play();endGame();}},1000);}
function updateTimerDisplay(){const m=Math.floor(timeLeft/60).toString().padStart(2,'0');const s=(timeLeft%60).toString().padStart(2,'0');document.getElementById("timer").innerText=`الوقت: ${m}:${s}`;const p=(timeLeft/totalTime)*100;document.getElementById("timeProgress").style.width=p+"%";if(timeLeft<60){document.getElementById("timeProgress").style.backgroundColor= "var(--accent-color)";document.getElementById("timer").classList.add("pulse");}else{document.getElementById("timeProgress").style.backgroundColor="var(--primary-color)";document.getElementById("timer").classList.remove("pulse");}}
function endGame(){switchScreen("endScreen");let m=`لقد حصلت على ${score} نقاط! `;if(score>=15)m+="🎉 أداء رائع!";else if(score>=10)m+="👍 أداء جيد!";else if(score>=5)m+="👏 ليس سيئاً!";else m+="💪 لا تستسلم!";document.getElementById("endMessage").innerText=m;}
function restartGame(){startGame(difficulty);}
function showFullScreen(src){document.querySelector("#overlay img").src=src;document.getElementById("overlay").style.display="flex";}
function hideOverlay(){document.getElementById("overlay").style.display="none";}
</script>

</body>
</html>
