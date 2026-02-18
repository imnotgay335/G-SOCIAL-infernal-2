<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>G-SOCIAL INFERNAL 2</title>
<style>
body{margin:0;font-family:Arial;background:#0f2027;color:white;text-align:center;}
#intro{position:fixed;top:0;left:0;width:100%;height:100%;background:black;display:flex;flex-direction:column;justify-content:center;align-items:center;z-index:9999;}
#intro h1{font-size:4em;color:red;text-shadow:0 0 20px red;animation:glow 1s infinite alternate;}
@keyframes glow{from{opacity:0.7;}to{opacity:1;}}
.card{background:rgba(255,255,255,0.1);margin:20px auto;padding:20px;border-radius:15px;max-width:500px;}
button{padding:8px 15px;border:none;border-radius:10px;background:#00ffcc;cursor:pointer;margin:5px;font-weight:bold;}
input{padding:8px;border-radius:8px;border:none;text-align:center;}
#comments{background:#111;padding:10px;border-radius:10px;max-height:200px;overflow-y:auto;margin-top:10px;text-align:left;}
.stickerBtn{margin:3px;font-size:20px;}
.ultimate{font-size:30px;color:gold;text-shadow:0 0 15px gold;}
.userGallery{display:flex;flex-wrap:wrap;justify-content:center;}
.userGallery img{width:80px;height:80px;margin:5px;border-radius:10px;}
#dashboard{display:none;position:fixed;top:0;left:0;width:100%;height:100%;background:#111;color:#00ffcc;overflow-y:auto;z-index:9999;padding:20px;}
#dashboard h2{color:red;}
#tutorial{position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.95);color:#00ffcc;z-index:10000;display:flex;flex-direction:column;justify-content:center;align-items:center;display:none;}
#tutorial div{max-width:500px;text-align:left;}
#tutorial button{margin-top:15px;}
</style>
</head>
<body>

<div id="intro">
<h1>G-SOCIAL INFERNAL 2</h1>
<p style="font-size:2em;color:#00ffcc;">Créé par Vassili Stockmans</p>
</div>

<h2 id="playerName">Non connecté</h2>
<p>G-Coins : <span id="coins">0</span></p>

<div class="card">
<h3>Créer Profil</h3>
<input type="text" id="pseudoInput" placeholder="Pseudo">
<button onclick="createProfile()">Créer</button>
</div>

<div class="card">
<h3>Mini Jeu</h3>
<button onclick="gainCoins()">Gagner 100 G</button>
</div>

<div class="card">
<h3>Posts / Commentaires</h3>
<div id="comments"></div>
<input type="text" id="commentInput" placeholder="Commentaire">
<button onclick="addComment()">Envoyer</button>
<br><br>
<button onclick="toggleShop()">Boutique Stickers</button>
<div id="shop"></div>
</div>

<div class="card">
<h3>Galerie Utilisateur</h3>
<div id="userGallery" class="userGallery"></div>
</div>

<div class="card">
<h3>Chat Privé</h3>
<select id="chatUserSelect"></select>
<input type="text" id="chatMessage" placeholder="Écris un message">
<button onclick="sendMessage()">Envoyer</button>
<div id="chatBox" style="max-height:200px;overflow-y:auto;text-align:left;"></div>
</div>

<div id="dashboard">
<h2>💀 Dashboard SuperAdmin 💀</h2>
<p>Utilisateur connecté : <span id="adminName"></span></p>
<button onclick="showAllUsers()">Voir tous les utilisateurs</button>
<button onclick="showAllPosts()">Voir tous les posts</button>
<button onclick="giveCoins()">Donner des G-Coins</button>
<button onclick="simulateAI()">Générer Script IA</button>
<button onclick="hideDashboard()">Fermer Dashboard</button>
<div id="adminContent"></div>
</div>

<div id="tutorial">
<div>
<h2>Tutoriel G-SOCIAL INFERNAL 2</h2>
<p>Bienvenue sur ton nouveau jeu ! Voici comment l’utiliser :</p>
<ol>
<li>Crée ton pseudo pour commencer à jouer.</li>
<li>Gagne des G-Coins avec le mini-jeu.</li>
<li>Achete des stickers dans la boutique et utilise-les dans les commentaires.</li>
<li>Poste des commentaires, photos ou vidéos dans ta galerie.</li>
<li>Chatte avec d’autres utilisateurs et suis-les pour voir leur contenu.</li>
<li>SuperAdmin (Vassiliboss1) peut tout gérer et donner des G-Coins.</li>
<li>Tu peux relancer ce tutoriel à tout moment depuis le menu.</li>
</ol>
</div>
<button onclick="nextTutorial()">Suivant</button>
<button onclick="closeTutorial()">Fermer</button>
</div>

<script>
// Intro
setTimeout(()=>{document.getElementById("intro").style.display="none"; showTutorial();},4000);

// Données
let coins = parseInt(localStorage.getItem("coins"))||0;
let pseudo = localStorage.getItem("pseudo")||null;
let ownedStickers = JSON.parse(localStorage.getItem("ownedStickers"))||[];
let users = JSON.parse(localStorage.getItem("users"))||{};
let chatData = JSON.parse(localStorage.getItem("chatData"))||{};
let currentAdmin = null;

document.getElementById("coins").textContent = coins;
if(pseudo){document.getElementById("playerName").textContent = pseudo;}

// Stickers et prix
const stickers = ["😀","🔥","💎","👑","⚡","🚀","💰","❤️","😈","💀","🌟","🎯","🛡","🐉","👽","🧨","🌈","🪙","🌀","✨"];
const prices = [50,100,250,500,1000,2000,3000,5000,7000,10000,12000,14000,16000,17000,18000,19000,19500,19800,19900,20000];

// Fonctions principales
function createProfile(){
    let p=document.getElementById("pseudoInput").value;
    if(!p)return;
    p = sanitize(p);
    pseudo=p;
    localStorage.setItem("pseudo",pseudo);
    document.getElementById("playerName").textContent=pseudo;
    if(!users[pseudo]) users[pseudo]={coins:0,stickers:[],photos:[],messages:[],role:"user"};
    localStorage.setItem("users",JSON.stringify(users));
    updateUserSelect();
    alert("Profil créé !");
}

function gainCoins(){coins+=100; saveCoins();}
function saveCoins(){if(coins>100000000)coins=100000000;localStorage.setItem("coins",coins);document.getElementById("coins").textContent=coins;if(pseudo){users[pseudo].coins=coins;localStorage.setItem("users",JSON.stringify(users));}}

function toggleShop(){
    let shop=document.getElementById("shop"); shop.innerHTML="";
    stickers.forEach((s,i)=>{
        let btn=document.createElement("button"); btn.className="stickerBtn"; btn.innerHTML=s+" - "+prices[i]+" G";
        if(i===19)btn.classList.add("ultimate");
        btn.onclick=()=>buySticker(i);
        shop.appendChild(btn);
    });
}
function buySticker(i){
    if(coins<prices[i]){alert("Pas assez de G !");return;}
    coins-=prices[i];saveCoins();
    if(!ownedStickers.includes(i)) ownedStickers.push(i);
    localStorage.setItem("ownedStickers",JSON.stringify(ownedStickers));
    alert("Sticker acheté !");
}

function addComment(){
    let input=document.getElementById("commentInput").value;
    input = sanitize(input);
    if(!input) return;
    let div=document.createElement("div"); div.textContent=pseudo+" : "+input;
    document.getElementById("comments").appendChild(div); 
    document.getElementById("commentInput").value="";
}

function updateUserGallery(){
    let gallery=document.getElementById("userGallery"); gallery.innerHTML="";
    if(!pseudo) return;
    let photos=users[pseudo]?.photos||[];
    photos.forEach(p=>{let img=document.createElement("img"); img.src=p; gallery.appendChild(img);});
}

function updateUserSelect(){
    let select=document.getElementById("chatUserSelect"); select.innerHTML="";
    Object.keys(users).forEach(u=>{if(u!==pseudo){let opt=document.createElement("option"); opt.value=u; opt.textContent=u; select.appendChild(opt);}});
}
updateUserSelect();

function sendMessage(){
    let to=document.getElementById("chatUserSelect").value;
    let msg=document.getElementById("chatMessage").value;
    msg = sanitize(msg);
    if(!to||!msg) return;
    if(!chatData[to]) chatData[to]=[];
    chatData[to].push(pseudo+": "+msg);
    localStorage.setItem("chatData",JSON.stringify(chatData));
    displayChat(to); document.getElementById("chatMessage").value="";
}
function displayChat(user){
    let box=document.getElementById("chatBox"); box.innerHTML="";
    let msgs=chatData[user]||[];
    msgs.forEach(m=>{let div=document.createElement("div"); div.textContent=m; box.appendChild(div);});
}

// Admin
function checkAdmin(){
    let code=prompt("Entrez le code admin :");
    if(code==="Vassiliboss1"){currentAdmin="Vassili Stockmans"; showDashboard();}
    else if(code==="boss2"){currentAdmin="frankortisboss2"; showDashboard();}
    else{alert("Code incorrect");}
}
function showDashboard(){document.getElementById("dashboard").style.display="block"; document.getElementById("adminName").textContent=currentAdmin;}
function hideDashboard(){document.getElementById("dashboard").style.display="none";}
function showAllUsers(){document.getElementById("adminContent").innerHTML=JSON.stringify(users,null,2);}
function showAllPosts(){document.getElementById("adminContent").innerHTML="Fonction posts à implémenter";}
function giveCoins(){let u=prompt("Quel utilisateur ?"); let amount=parseInt(prompt("Combien de G ?")); if(users[u]){users[u].coins+=amount; localStorage.setItem("users",JSON.stringify(users)); alert("G-Coins donnés !");}}
function simulateAI(){let task=prompt("Que veux-tu générer ?"); if(task)alert("IA générerait ceci : "+task+" (simulation)");}

// Anti-hack / antivirus simple
function sanitize(input){ let div=document.createElement('div'); div.textContent = input; return div.innerHTML;}

// Tutoriel
let tutorialStep = 0;
function showTutorial(){document.getElementById("tutorial").style.display="flex"; tutorialStep=0; updateTutorial();}
function updateTutorial(){document.getElementById("tutorial").querySelector("div").scrollTop=0;}
function nextTutorial(){alert("Passez à la prochaine étape");}
function closeTutorial(){document.getElementById("tutorial").style.display="none";}

// Shortcut admin
document.addEventListener("keydown",(e)=>{if(e.ctrlKey && e.key==="a") checkAdmin();});
</script>
</body>
</html>
