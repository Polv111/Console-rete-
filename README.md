<!doctype html>
<html lang="it">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Console - Open Rete</title>
<style>
:root{--bg:#0b1220;--panel:#0f1724;--text:#d7e6ff;--muted:#9fb0d8;--accent:#6ee7b7;
font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, "Roboto Mono", "Courier New", monospace;}
html,body{height:100%; margin:0; background:#071023; color:var(--text);}
.wrap{max-width:900px;margin:36px auto;padding:20px;}
header{display:flex;gap:12px;align-items:center;margin-bottom:12px;}
.logo{width:48px;height:48px;border-radius:8px;background:linear-gradient(135deg,#123 0%, #346 100%);
display:flex;align-items:center;justify-content:center;color:#fff;font-weight:700;}
h1{font-size:18px;margin:0;font-weight:600;}
.panel{background:var(--panel);border-radius:10px;box-shadow:0 10px 30px rgba(2,8,23,0.6), inset 0 1px 0 rgba(255,255,255,0.02);}
.console{padding:18px;min-height:320px;max-height:60vh;overflow:auto;font-size:14px;line-height:1.45;}
.line{white-space:pre-wrap;margin:6px 0;}
.prompt{color: var(--muted);}
.ok{color: var(--accent); font-weight:600;}
.err{color:#ff6b6b;font-weight:700;}
.input-area{display:flex;gap:12px;align-items:center;padding:12px 18px;border-top:1px solid rgba(255,255,255,0.03);}
.cmd-prefix{color:var(--muted);min-width:80px;text-align:right;font-size:13px;}
.cmd-input{flex:1;background:transparent;border:0;outline:0;color:var(--text);font-size:14px;padding:8px 10px;border-radius:6px;}
.btn{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted);padding:8px 10px;border-radius:6px;cursor:pointer;}
.help{padding:12px 18px;color:var(--muted);font-size:13px;border-top:1px dashed rgba(255,255,255,0.02);}
.dots{display:inline-block;width:1.2em;letter-spacing:-0.12em;}
.modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,0.6);display:none;align-items:center;justify-content:center;z-index:1000;}
.modal{background:#1a2334;border-radius:10px;padding:20px;width:300px;color:var(--text);}
.modal h2{margin-top:0;font-size:16px;margin-bottom:12px;}
.modal ul{list-style:none;padding:0;margin:0;}
.modal li{padding:6px 8px;margin:4px 0;background:#2a3246;border-radius:6px;cursor:pointer;}
.modal li:hover{background:#374056;}
.ip{display:block;margin-top:4px;font-size:12px;color:var(--accent);cursor:pointer;}
</style>
</head>
<body>
<div class="wrap">
<header>
<div class="logo">OR</div>
<div>
<h1>Console — Open Rete</h1>
<div style="color:var(--muted); font-size:13px;">Digita <code>help</code> per i comandi</div>
</div>
</header>

<div class="panel">
<div id="console" class="console"></div>
<div class="input-area">
<div class="cmd-prefix">utente &gt;</div>
<input id="cmd" class="cmd-input" placeholder="es. open rete" autocomplete="off" />
<button id="send" class="btn">Invia</button>
</div>
<div class="help">Comandi: <code>open rete</code>, <code>risposta testo</code>, <code>clear</code>.</div>
</div>
</div>

<div class="modal-overlay" id="modal">
<div class="modal">
<h2>Host trovati</h2>
<ul id="hostList"></ul>
<button style="margin-top:12px;" onclick="document.getElementById('modal').style.display='none'">Chiudi</button>
</div>
</div>

<script>
(function(){
const consoleEl=document.getElementById('console');
const input=document.getElementById('cmd');
const btn=document.getElementById('send');
const modal=document.getElementById('modal');
const hostList=document.getElementById('hostList');
const hosts=["Ensiteboss","Ensiteryse","Ensiterobert","Ensitelong","Ensiteeric","Pso","Alfa","Parcouy"];

function appendLine(html){const el=document.createElement('div');el.className='line';el.innerHTML=html;consoleEl.appendChild(el);consoleEl.scrollTop=consoleEl.scrollHeight;}
function shortPause(ms){return new Promise(r=>setTimeout(r,ms));}

async function runCommand(cmd){
if(!cmd.trim()) return;
appendLine('<span class="prompt">utente &gt;</span> '+cmd);
if(cmd.toLowerCase()==='open rete'){await seqInizializzazione(); return;}
if(cmd.startsWith('risposta ')){appendLine('<span class="prompt">risposta:</span> '+cmd.slice(9)); return;}
if(cmd==='clear'){consoleEl.innerHTML=''; return;}
appendLine('<span class="err">Comando sconosciuto</span>');
}

async function seqInizializzazione(){
const l=appendLine('<span class="prompt">inizializzazione</span><span class="dots">.</span>');
await animateDots(document.querySelector('.dots'),1500);
document.querySelector('.dots').parentElement.innerHTML='<span class="prompt">inizializzazione</span> <span class="ok">completata</span>';
await shortPause(300);
const c=appendLine('<span class="prompt">Connessione</span><span class="dots">...</span>');
await animateDots(document.querySelector('.dots'),1500);
document.querySelector('.dots').parentElement.innerHTML='<span class="prompt">Connessione</span> <span class="ok">stabilita</span>';
await shortPause(300);
appendLine('<span class="ok">rete aperta</span>');
showHostModal();
}

function animateDots(node,duration){
return new Promise(res=>{
if(!node) return res();
let start=performance.now();
const id=setInterval(()=>{
if(performance.now()-start>=duration){clearInterval(id); node.textContent=''; res();}
else{const n=Math.floor(((performance.now()-start)/200)%4); node.textContent='.'.repeat(n+1);}
},120);
});
}

function showHostModal(){
hostList.innerHTML='';
hosts.forEach(h=>{
const li=document.createElement('li');
li.textContent=h;
li.addEventListener('click',()=>{
if(li.querySelector('.ip')) return;
const ip=`192.168.${Math.floor(Math.random()*255)}.${Math.floor(Math.random()*255)}`;
const ipEl=document.createElement('span');
ipEl.className='ip';
ipEl.textContent=ip;
ipEl.addEventListener('click',()=>{
const status=h==="Alfa"?"Ip attivo 2 minuti fa":h==="Parcouy"?"Ip attivo ora":"Ip attivo 65 ore fa";
const w=window.open("","_blank","width=400,height=200");
w.document.write(`<body style="background:#0b1220;color:#d7e6ff;font-family:monospace;padding:20px;font-size:16px;">
connessione....<br>connessioneeee.<br><span style="color:#6ee7b7;">${status}</span></body>`);
});
li.appendChild(ipEl);
});
hostList.appendChild(li);
});
modal.style.display='flex';
}

btn.addEventListener('click',()=>{runCommand(input.value); input.value='';});
input.addEventListener('keydown',e=>{if(e.key==='Enter'){runCommand(input.value); input.value='';}});

appendLine('<span class="ok">Benvenuto nella console — simulazione rete.</span>');
appendLine('Digita <code>open rete</code> per iniziare.');
})();
</script>
</body>
</html>
