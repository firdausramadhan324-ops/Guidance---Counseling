<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GC Services Ultimate</title>

<style>
body{
  margin:0;
  font-family:'Segoe UI',sans-serif;
  background:#eef2f7;
}
.screen{display:none;padding:20px;}
.active{display:block;}
#welcome{
  background:#0d47a1;
  color:white;
  height:100vh;
  display:flex;
  justify-content:center;
  align-items:center;
  flex-direction:column;
  font-size:26px;
}
.header{
  text-align:center;
  font-size:22px;
  font-weight:bold;
  color:#0d47a1;
  margin-bottom:20px;
}
.card{
  background:white;
  padding:15px;
  margin:10px 0;
  border-radius:15px;
  box-shadow:0 4px 12px rgba(0,0,0,0.1);
  cursor:pointer;
}
button{
  width:100%;
  padding:10px;
  margin-top:10px;
  border:none;
  border-radius:10px;
  background:#0d47a1;
  color:white;
  cursor:pointer;
}
input, textarea, select{
  width:100%;
  padding:8px;
  margin-top:8px;
  border-radius:8px;
  border:1px solid #ccc;
}
#chatBox{
  background:white;
  height:250px;
  overflow:auto;
  padding:10px;
  border-radius:10px;
  margin-bottom:10px;
}
.user{text-align:right;color:#333;}
.bot{color:#0d47a1;}
.small{font-size:12px;color:gray;}
</style>
</head>
<body>

<!-- WELCOME -->
<div id="welcome" class="screen active">
  <div>GC Services</div>
  <div class="small">Smart Guidance & Counseling System</div>
</div>

<!-- HOME -->
<div id="home" class="screen">
  <div class="header">Dashboard Siswa</div>

  <div class="card" onclick="openScreen('chatMenu')">💬 Konseling AI</div>
  <div class="card" onclick="openScreen('assessment')">📊 Self Assessment Emosi</div>
  <div class="card" onclick="openScreen('jadwal')">📅 Penjadwalan</div>
  <div class="card" onclick="openScreen('kotak')">📦 Kotak Masalah Anonim</div>
  <div class="card" onclick="openScreen('history')">🗂 Riwayat Konseling</div>
</div>

<!-- CHAT MENU -->
<div id="chatMenu" class="screen">
  <div class="header">Pilih Jenis Konseling</div>
  <div class="card" onclick="startChat('akademik')">📚 Akademik</div>
  <div class="card" onclick="startChat('pribadi')">❤️ Pribadi</div>
  <div class="card" onclick="startChat('sosial')">👥 Sosial</div>
  <button onclick="openScreen('home')">Kembali</button>
</div>

<!-- CHAT SCREEN -->
<div id="chat" class="screen">
  <div class="header">Konseling AI</div>
  <div id="chatBox"></div>
  <input type="text" id="chatInput" placeholder="Tulis pesan...">
  <button onclick="sendMessage()">Kirim</button>
  <button onclick="saveChat()">Simpan Sesi</button>
  <button onclick="openScreen('chatMenu')">Kembali</button>
</div>

<!-- ASSESSMENT -->
<div id="assessment" class="screen">
  <div class="header">Self Assessment</div>
  <label>Bagaimana perasaanmu hari ini?</label>
  <select id="mood">
    <option>Senang</option>
    <option>Cemas</option>
    <option>Sedih</option>
    <option>Marah</option>
  </select>
  <button onclick="checkMood()">Lihat Saran</button>
  <div id="result"></div>
  <button onclick="openScreen('home')">Kembali</button>
</div>

<!-- JADWAL -->
<div id="jadwal" class="screen">
  <div class="header">Penjadwalan Konseling</div>
  <input type="text" placeholder="Nama">
  <select>
    <option>Tatap Muka</option>
    <option>Virtual</option>
  </select>
  <input type="date">
  <button onclick="alert('Jadwal terkirim!')">Kirim</button>
  <button onclick="openScreen('home')">Kembali</button>
</div>

<!-- KOTAK ANONIM -->
<div id="kotak" class="screen">
  <div class="header">Kotak Masalah (Anonim)</div>
  <textarea id="anonText" rows="5"></textarea>
  <button onclick="saveAnon()">Kirim</button>
  <button onclick="openScreen('home')">Kembali</button>
</div>

<!-- HISTORY -->
<div id="history" class="screen">
  <div class="header">Riwayat Konseling</div>
  <div id="historyList"></div>
  <button onclick="openScreen('home')">Kembali</button>
</div>

<script>
setTimeout(()=>{openScreen('home')},2000);

function openScreen(id){
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}

/* ===== CHAT SYSTEM ===== */
let currentCategory = "";
let chatHistory = [];

function startChat(category){
  currentCategory = category;
  chatHistory = [];
  document.getElementById("chatBox").innerHTML="";
  openScreen("chat");
}

function getAIResponse(msg){
  msg = msg.toLowerCase();

  if(currentCategory==="akademik"){
    if(msg.includes("nilai")) return "Nilai bisa ditingkatkan dengan strategi belajar teratur. Sudah coba buat jadwal belajar?";
    if(msg.includes("tidak paham")) return "Bagian mana yang paling sulit dipahami?";
  }

  if(currentCategory==="pribadi"){
    if(msg.includes("sedih")) return "Aku ikut prihatin kamu merasa sedih. Apa penyebab utamanya?";
    if(msg.includes("cemas")) return "Rasa cemas itu wajar. Apa yang membuatmu merasa seperti itu?";
  }

  if(currentCategory==="sosial"){
    if(msg.includes("teman")) return "Apakah terjadi konflik dengan teman?";
    if(msg.includes("dibully")) return "Itu situasi serius. Kamu tidak sendiri, ceritakan lebih lanjut ya.";
  }

  return "Terima kasih sudah berbagi. Bisa ceritakan lebih detail?";
}

function sendMessage(){
  let input = document.getElementById("chatInput");
  if(input.value.trim()==="") return;

  chatHistory.push("Kamu: "+input.value);
  document.getElementById("chatBox").innerHTML += `<p class="user">${input.value}</p>`;

  let reply = getAIResponse(input.value);

  setTimeout(()=>{
    chatHistory.push("AI: "+reply);
    document.getElementById("chatBox").innerHTML += `<p class="bot">${reply}</p>`;
  },600);

  input.value="";
}

function saveChat(){
  let all = JSON.parse(localStorage.getItem("sessions")||"[]");
  all.push(chatHistory);
  localStorage.setItem("sessions",JSON.stringify(all));
  alert("Sesi disimpan!");
}

/* ===== HISTORY ===== */
function loadHistory(){
  let list = document.getElementById("historyList");
  list.innerHTML="";
  let data = JSON.parse(localStorage.getItem("sessions")||"[]");
  data.forEach((session,i)=>{
    list.innerHTML += `<div class="card"><b>Sesi ${i+1}</b><br>${session.join("<br>")}</div>`;
  });
}
document.getElementById("history").addEventListener("click",loadHistory);

/* ===== ANON ===== */
function saveAnon(){
  let text=document.getElementById("anonText").value;
  let data=JSON.parse(localStorage.getItem("anon")||"[]");
  data.push(text);
  localStorage.setItem("anon",JSON.stringify(data));
  alert("Pesan anonim tersimpan!");
}

/* ===== ASSESSMENT ===== */
function checkMood(){
  let mood=document.getElementById("mood").value;
  let result="";
  if(mood==="Cemas") result="Cobalah teknik pernapasan dan tulis apa yang membuatmu cemas.";
  if(mood==="Sedih") result="Ceritakan ke orang terpercaya atau gunakan fitur chat.";
  if(mood==="Marah") result="Ambil waktu tenang sebelum bereaksi.";
  if(mood==="Senang") result="Pertahankan energi positifmu hari ini!";
  document.getElementById("result").innerHTML=result;
}
</script>

</body>
</html>
