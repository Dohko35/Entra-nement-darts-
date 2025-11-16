<!doctype html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Darts IA V14 – Finish interactif</title>
<style>
body{font-family:-apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Arial;padding:16px;max-width:760px;margin:auto;background:#f0f0f0;}
h1{font-size:22px;margin-bottom:10px;color:#333;}
.row{display:flex;gap:10px;flex-wrap:wrap;align-items:center;margin-bottom:10px;}
button{padding:10px 14px;font-size:16px;border-radius:8px;border:1px solid #ccc;background:#fff;cursor:pointer;}
select,input{padding:8px;border-radius:8px;border:1px solid #ccc;font-size:16px;}
#log{margin-top:12px;background:#e0f7fa;padding:10px;border-radius:8px;color:#00796b;font-weight:500;}
#aiThrows{margin-top:10px;padding:10px;background:#fff;border-radius:8px;box-shadow:0 0 5px rgba(0,0,0,0.1);}
.scoreBox{display:flex;justify-content:space-between;background:#fff;padding:10px;border-radius:8px;box-shadow:0 0 5px rgba(0,0,0,0.1);margin-top:10px;font-weight:600;font-size:16px;}
</style>
</head>
<body>
<h1>Darts IA V14 – Finish interactif</h1>

<div class="row">
<label>Niveau IA:
<select id="difficulty">
<option value="pro">Pro</option>
<option value="standard" selected>Standard</option>
<option value="unstable">Instable</option>
</select>
</label>
<button id="newGameBtn">Nouvelle partie</button>
<button id="aiThrowBtn">Lancer IA</button>
</div>

<div class="scoreBox">
<div>Score : <span id="score">501</span></div>
<div>Tour : <span id="turn">0</span></div>
</div>

<div id="aiThrows"></div>
<div id="log"></div>

<h3>Ton action</h3>
<div class="row" id="playerAction">
<label id="sumLabel">Somme du tour: <input type="number" id="userSum" placeholder="ex: 81"></label>
<label id="newScoreLabel">Nouveau score: <input type="number" id="userNew" placeholder="ex: 420"></label>
<select id="playerAim" style="display:none;"></select>
<button id="playerThrowBtn">Valider</button>
</div>

<script>
(function(){
const allDarts = [];
for(let i=1;i<=20;i++){allDarts.push({name:'S'+i,v:i},{name:'D'+i,v:i*2},{name:'T'+i,v:i*3});}
allDarts.push({name:'SB',v:25},{name:'DB',v:50});

let score=501,last=[],turn=0,gameOver=false,currentAITurn=[];

function updateState(){document.getElementById('score').textContent=score; document.getElementById('turn').textContent=turn;}

document.getElementById('newGameBtn').onclick=()=>{
  score=501; last=[]; turn=0; gameOver=false; currentAITurn=[];
  document.getElementById('log').innerHTML='Nouvelle partie.';
  document.getElementById('aiThrows').innerHTML='';
  document.getElementById('playerAim').style.display='none';
  document.getElementById('userSum').style.display='inline';
  document.getElementById('userNew').style.display='inline';
  updateState();
};

function logicalAI(score,diff){
  let throws=[],remaining=score;
  const errorRate = diff==='pro'?0.05:diff==='standard'?0.15:0.3;
  for(let i=0;i<3;i++){
    let dart = allDarts[Math.floor(Math.random()*allDarts.length)];
    if(dart.v>remaining) dart={name:'S'+remaining,v:remaining};
    if(Math.random()<errorRate){dart = allDarts[Math.floor(Math.random()*allDarts.length)];}
    remaining-=dart.v;
    throws.push(dart);
    if(remaining<=0) break;
  }
  return throws;
}

function launchAITurn(){
  if(gameOver) return;
  turn++;
  const diff=document.getElementById('difficulty').value;
  if(score>170){
    currentAITurn = logicalAI(score,diff);
    let aiText = currentAITurn.map(d=>d.name).join(", "); // Affiche seulement le nom, pas la valeur
    document.getElementById('aiThrows').innerHTML = "IA a lancé : " + aiText;
    document.getElementById('log').textContent = "Saisis la somme et le nouveau score pour valider.";
    document.getElementById('userSum').style.display='inline';
    document.getElementById('userNew').style.display='inline';
    document.getElementById('playerAim').style.display='none';
  } else {
    // score ≤170 : mode finish interactif
    document.getElementById('log').textContent="Choisis ta flèche pour finir.";
    const select=document.getElementById('playerAim');
    select.innerHTML='';
    allDarts.forEach(d=>{const opt=document.createElement('option'); opt.value=d.name; opt.textContent=d.name; select.appendChild(opt);});
    select.style.display='inline';
    document.getElementById('userSum').style.display='none';
    document.getElementById('userNew').style.display='none';
    currentAITurn=[]; // IA attend que joueur joue
    document.getElementById('aiThrows').innerHTML='';
  }
}

document.getElementById('aiThrowBtn').onclick=()=>{launchAITurn();};

document.getElementById('playerThrowBtn').onclick=()=>{
  if(gameOver){document.getElementById('log').textContent="La partie est terminée."; return;}

  if(score>170){
    // saisie somme classique
    const us = Number(document.getElementById('userSum').value);
    const un = Number(document.getElementById('userNew').value);
    const realSum = currentAITurn.reduce((s,d)=>s+d.v,0);
    const expectedScore = score - realSum;

    if(us===realSum && un===expectedScore){
      score = expectedScore;
      updateState();
      last = [];
      document.getElementById('log').textContent="✔️ Correct. Lancement automatique du prochain tour.";
      setTimeout(()=>{launchAITurn();},700);
    } else {
      document.getElementById('log').textContent=`❌ Mauvais calcul. Somme réelle=${realSum}, score réel=${expectedScore}.`;
    }

  } else {
    // mode finish interactif
    const aim=document.getElementById('playerAim').value;
    let dart = allDarts.find(d=>d.name===aim);
    if(!dart){document.getElementById('log').textContent="Flèche invalide !"; return;}

    const diff=document.getElementById('difficulty').value;
    const errorRate = diff==='pro'?0.05:diff==='standard'?0.15:0.3;
    if(Math.random()<errorRate){
      dart = allDarts[Math.floor(Math.random()*allDarts.length)];
      document.getElementById('log').textContent="IA a manqué la cible !";
    } else {
      document.getElementById('log').textContent="Flèche jouée.";
    }

    score -= dart.v;
    if(score<0 || (score===0 && !(dart.name.startsWith('D')||dart.name==='DB'))){
      score += dart.v;
      document.getElementById('log').textContent="Bust ! Score reste à "+score;
    }

    last.push(dart);
    updateState();
    if(score===0){document.getElementById('log').textContent=`🎉 Partie terminée en ${turn} tours !`; gameOver=true;}
  }
};
})();
</script>
</body>
</html>