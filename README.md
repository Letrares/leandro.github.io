<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Protótipo — painel do micro-ondas</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#0F1113; --panel:#1A1D21; --panel2:#20242A; --ink:#F4F1EC; --dim:#8A9099;
    --amber:#FFB454; --amber2:#FF8A5C; --steel:#6B8A9A; --danger:#FF6B5C; --ok:#5CE0A6;
    box-sizing:border-box;
    padding-top:env(safe-area-inset-top,0px); padding-bottom:env(safe-area-inset-bottom,0px);
  }
  *{box-sizing:border-box;}
  html,body{margin:0;height:100%;color:var(--ink);
    font-family:'Space Grotesk',system-ui,sans-serif;display:flex;align-items:center;justify-content:center;
    background:
      radial-gradient(circle at 20% 15%, rgba(255,138,92,.10), transparent 45%),
      radial-gradient(circle at 85% 80%, rgba(92,224,166,.08), transparent 45%),
      var(--bg);}
 
  .device{width:330px;background:linear-gradient(165deg,var(--panel2),var(--panel));
    border-radius:28px;padding:22px;position:relative;
    box-shadow:0 30px 70px rgba(0,0,0,.55), inset 0 1px 0 rgba(255,255,255,.04);
    border:1px solid rgba(255,255,255,.06);}
 
  .ring-wrap{position:relative;margin-bottom:18px;border-radius:18px;padding:2px;
    background:conic-gradient(var(--amber) calc(var(--pct,0)*1%), rgba(255,255,255,.06) 0);
    transition:background .5s linear;}
  .display{background:#0B0C0E;border-radius:16px;padding:22px 18px;text-align:center;min-height:70px;
    position:relative;overflow:hidden;}
  .display::after{content:'';position:absolute;inset:0;
    background:radial-gradient(circle at 50% 0%, rgba(255,180,84,.08), transparent 60%);pointer-events:none;}
  .time{font-size:2.4rem;font-weight:700;letter-spacing:2px;color:var(--amber);
    text-shadow:0 0 18px rgba(255,180,84,.35);transition:opacity .15s ease;}
  .status{font-size:.72rem;color:var(--dim);margin-top:6px;letter-spacing:.3px;
    text-transform:uppercase;transition:opacity .2s ease;}
 
  .screen{display:none;}
  .screen.active{display:block;animation:rise .38s cubic-bezier(.22,1,.36,1);}
  @keyframes rise{from{opacity:0;transform:translateY(10px) scale(.98);}to{opacity:1;transform:translateY(0) scale(1);}}
 
  .grid{display:grid;grid-template-columns:1fr 1fr;gap:10px;}
  .keypad{display:grid;grid-template-columns:1fr 1fr 1fr;gap:10px;}
  .keypad button{font-size:1.15rem;padding:15px 0;}
 
  button{border:none;border-radius:13px;padding:14px 10px;font-size:.82rem;font-weight:600;
    background:#2A2E34;color:var(--ink);cursor:pointer;font-family:inherit;
    transition:transform .15s ease, background .2s ease, box-shadow .2s ease;
    box-shadow:0 1px 0 rgba(255,255,255,.04) inset;}
  button:hover{background:#32363D;}
  button:active{transform:scale(.95);}
  button.primary{background:linear-gradient(135deg,var(--amber),var(--amber2));color:#1A1206;
    box-shadow:0 6px 18px rgba(255,138,92,.28);}
  button.primary:hover{box-shadow:0 8px 22px rgba(255,138,92,.4);}
  button.ghost{background:transparent;border:1px solid rgba(255,255,255,.12);color:var(--dim);}
  button.ghost:hover{border-color:rgba(255,255,255,.24);color:var(--ink);}
  button.danger{background:linear-gradient(135deg,var(--danger),#c9503f);color:#fff;}
 
  .row{display:flex;gap:10px;margin-top:10px;}
  .row button{flex:1;}
  .list button{width:100%;text-align:left;margin-bottom:8px;display:flex;align-items:center;gap:10px;}
  .icon-svg{width:18px;height:18px;flex:0 0 auto;color:var(--amber);}
 
  .warn{background:rgba(255,107,92,.1);border:1px solid rgba(255,107,92,.4);border-radius:14px;padding:16px;
    text-align:center;color:var(--danger);font-weight:600;margin-bottom:14px;}
  .center{text-align:center;}
  .big-icon{font-size:2.6rem;margin:6px 0;animation:pop .4s cubic-bezier(.22,1.6,.4,1);}
  @keyframes pop{from{transform:scale(.4);opacity:0;}to{transform:scale(1);opacity:1;}}
 
  input[type=range]{width:100%;accent-color:var(--amber);}
  label{font-size:.78rem;color:var(--dim);}
  .name-picker{display:flex;align-items:center;gap:10px;}
  .name-picker button{flex:0 0 44px;padding:12px 0;font-size:1.1rem;}
  .name-display{flex:1;text-align:center;background:#0B0C0E;border-radius:10px;padding:12px;
    font-weight:600;border:1px solid rgba(255,255,255,.06);}
  .preset-row{display:flex;gap:8px;margin-bottom:8px;}
  .preset-row button{flex:1;text-align:left;display:flex;align-items:center;gap:10px;}
  .trash-btn{flex:0 0 auto !important;width:44px;padding:0 !important;display:flex;align-items:center;justify-content:center;}
  .trash-btn svg{color:var(--danger);}
  .empty-note{font-size:.78rem;color:var(--dim);text-align:center;padding:10px 0;}
</style>
</head>
<body>
<div class="device">
 
  <div class="ring-wrap" id="ringWrap">
    <div class="display">
      <div class="time" id="displayTime">00:00</div>
      <div class="status" id="displayStatus"></div>
    </div>
  </div>
 
  <!-- UA-1 Tela inicial -->
  <div class="screen active" id="ua1">
    <div class="keypad">
      <button onclick="pressDigit('1')">1</button>
      <button onclick="pressDigit('2')">2</button>
      <button onclick="pressDigit('3')">3</button>
      <button onclick="pressDigit('4')">4</button>
      <button onclick="pressDigit('5')">5</button>
      <button onclick="pressDigit('6')">6</button>
      <button onclick="pressDigit('7')">7</button>
      <button onclick="pressDigit('8')">8</button>
      <button onclick="pressDigit('9')">9</button>
      <button class="ghost" onclick="clearTyped()">Limpar</button>
      <button onclick="pressDigit('0')">0</button>
      <button class="primary" onclick="startTyped()">Iniciar</button>
    </div>
    <div class="grid" style="margin-top:10px">
      <button onclick="quickStart(30,'+30s')">30 seg</button>
      <button onclick="quickStart(60,'1 min')">1 min</button>
      <button class="ghost" style="grid-column:1 / -1" onclick="go('ua3')">Mais opções ›</button>
    </div>
  </div>
 
  <!-- UA-2 Ajuste de tempo -->
  <div class="screen" id="ua2">
    <label>Tempo (segundos): <b id="customVal" style="color:var(--ink)">60</b>s</label>
    <input type="range" min="10" max="600" step="10" value="60" oninput="customVal.textContent=this.value" id="customRange">
    <div class="row">
      <button class="primary" onclick="startCustom()">Iniciar</button>
      <button class="ghost" onclick="go('ua1')">Voltar</button>
    </div>
  </div>
 
  <!-- UA-3 Mais funções -->
  <div class="screen list" id="ua3">
    <button onclick="go('ua7')">
      <svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"><path d="M12 2v20M5 5l14 14M19 5L5 19M3 12h4M17 12h4M8 12h8"/></svg>
      Descongelar
    </button>
    <button onclick="quickStart(300,'Grill')">
      <svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M12 3c-2.6 3.2-4.3 5.3-4.3 7.8a4.3 4.3 0 008.6 0c0-1.2-.5-2-1.1-2.8.2 1.6-.6 2.4-1.6 2.4-1.2 0-1.6-1-1.2-2C12.8 6.8 12.6 5 12 3z"/></svg>
      Grill
    </button>
    <button onclick="quickStart(150,'Pipoca')">
      <svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M6.5 9h11l-1.1 10.2a1 1 0 01-1 .8H8.6a1 1 0 01-1-.8L6.5 9z"/><path d="M9.3 9v10.5M12 9v10.5M14.7 9v10.5"/><circle cx="9" cy="6.2" r="1.1"/><circle cx="12" cy="5.4" r="1.1"/><circle cx="15" cy="6.2" r="1.1"/></svg>
      Pipoca
    </button>
    <button onclick="quickStart(90,'Leite')">
      <svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M8.5 3.2h7l0.9 3.6v13.4a1 1 0 01-1 1h-5.8a1 1 0 01-1-1V6.8l0.9-3.6z"/><path d="M8.5 3.2L12 5.6l3.5-2.4M7.4 10.2h9.2"/></svg>
      Leite
    </button>
    <button onclick="quickStart(240,'Receita: frango')">
      <svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M4 11.2h16c.6 0 1 .5.9 1.1a8 8 0 01-7.9 6.9h-1a8 8 0 01-7.9-6.9c-.1-.6.3-1.1.9-1.1z"/><path d="M9 6c0 1-1 1.2-1 2.4M13.3 5c0 1-1 1.2-1 2.4M17 6.4c0 1-1 1.2-1 2.4"/></svg>
      Receita: frango
    </button>
    <button onclick="go('ua8')">
      <svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"><path d="M12 5v14M5 12h14"/></svg>
      Gerenciar presets
    </button>
    <button class="ghost" style="margin-top:6px" onclick="go('ua1')">Voltar</button>
  </div>
 
  <!-- UA-7 Descongelar por peso -->
  <div class="screen" id="ua7">
    <label style="display:flex;align-items:center;gap:8px;margin-bottom:10px">
      <svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"><path d="M12 2v20M5 5l14 14M19 5L5 19M3 12h4M17 12h4M8 12h8"/></svg>
      Peso do alimento (gramas)
    </label>
    <div class="keypad">
      <button onclick="pressGramDigit('1')">1</button>
      <button onclick="pressGramDigit('2')">2</button>
      <button onclick="pressGramDigit('3')">3</button>
      <button onclick="pressGramDigit('4')">4</button>
      <button onclick="pressGramDigit('5')">5</button>
      <button onclick="pressGramDigit('6')">6</button>
      <button onclick="pressGramDigit('7')">7</button>
      <button onclick="pressGramDigit('8')">8</button>
      <button onclick="pressGramDigit('9')">9</button>
      <button class="ghost" onclick="clearGrams()">Limpar</button>
      <button onclick="pressGramDigit('0')">0</button>
      <button class="primary" onclick="startDefrostTyped()">Iniciar</button>
    </div>
    <div class="row">
      <button class="ghost" style="flex:none;width:100%" onclick="go('ua3')">Voltar</button>
    </div>
  </div>
 
  <!-- UA-4 Executando -->
  <div class="screen" id="ua4">
    <div class="center" style="margin-bottom:10px;color:var(--dim);font-size:.85rem" id="execLabel">Aquecendo…</div>
    <div class="row">
      <button onclick="pauseResume()" id="pauseBtn">Pausar</button>
      <button class="danger" onclick="cancelRun()">Cancelar</button>
    </div>
    <div class="row">
      <button class="ghost" onclick="openDoor()">Simular: abrir porta</button>
    </div>
  </div>
 
  <!-- UA-8 Gerenciar presets -->
  <div class="screen" id="ua8">
    <label style="display:flex;align-items:center;gap:8px;margin-bottom:10px">
      <svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"><path d="M12 5v14M5 12h14"/></svg>
      Gerenciar presets (máx. 2)
    </label>
    <div id="presetList"></div>
    <button class="ghost" id="addPresetBtn" style="width:100%;justify-content:center;margin-top:4px" onclick="go('ua9')">+ Adicionar preset</button>
    <button class="ghost" style="width:100%;margin-top:10px" onclick="go('ua3')">‹ Voltar</button>
  </div>
 
  <!-- UA-9 Novo preset -->
  <div class="screen" id="ua9">
    <label style="margin-bottom:6px;display:block">Nome do preset</label>
    <div class="name-picker">
      <button class="ghost" onclick="cycleName(-1)">‹</button>
      <div class="name-display" id="presetNameDisplay">Café</div>
      <button class="ghost" onclick="cycleName(1)">›</button>
    </div>
    <label style="margin:14px 0 6px;display:block">Tempo (segundos)</label>
    <div class="keypad">
      <button onclick="pressPresetDigit('1')">1</button>
      <button onclick="pressPresetDigit('2')">2</button>
      <button onclick="pressPresetDigit('3')">3</button>
      <button onclick="pressPresetDigit('4')">4</button>
      <button onclick="pressPresetDigit('5')">5</button>
      <button onclick="pressPresetDigit('6')">6</button>
      <button onclick="pressPresetDigit('7')">7</button>
      <button onclick="pressPresetDigit('8')">8</button>
      <button onclick="pressPresetDigit('9')">9</button>
      <button class="ghost" onclick="clearPresetDigits()">Limpar</button>
      <button onclick="pressPresetDigit('0')">0</button>
      <button class="primary" onclick="savePreset()">Salvar</button>
    </div>
    <button class="ghost" style="width:100%;margin-top:10px" onclick="go('ua8')">‹ Cancelar</button>
  </div>
 
  <!-- UA-10 Confirmar exclusão -->
  <div class="screen" id="ua10">
    <div class="warn" id="deleteWarnText">Excluir este preset?</div>
    <div class="row">
      <button class="ghost" onclick="go('ua8')">Cancelar</button>
      <button class="danger" onclick="deletePresetConfirmed()">Excluir</button>
    </div>
  </div>
 
  <!-- UA-5 Concluído -->
  <div class="screen center" id="ua5">
    <div class="big-icon">✅</div>
    <p style="margin:0 0 14px;color:var(--dim)">Pronto! Retire o prato.</p>
    <button class="primary" style="width:100%" onclick="go('ua1')">OK</button>
  </div>
 
  <!-- UA-6 Porta aberta -->
  <div class="screen" id="ua6">
    <div class="warn">Porta aberta — execução pausada</div>
    <button class="primary" style="width:100%" onclick="closeDoor()">Fechar porta e continuar</button>
  </div>
 
</div>
 
<script>
let seconds = 0, total = 0, timer = null, paused = false, label = '';
let digitBuffer = '';
 
function fmt(s){
  const m = Math.floor(s/60), r = s%60;
  return String(m).padStart(2,'0')+':'+String(r).padStart(2,'0');
}
function setRing(pct){ ringWrap.style.setProperty('--pct', pct); }
function updateDisplay(status){
  displayTime.textContent = fmt(seconds);
  if(status) displayStatus.textContent = status;
  setRing(total ? Math.max(0,(seconds/total)*100) : 0);
}
function go(id){
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  if(id==='ua1'){ seconds=0; total=0; digitBuffer=''; displayTime.textContent='00:00'; displayStatus.textContent=''; setRing(0); }
  if(id==='ua7'){ gramsBuffer=''; displayTime.textContent='0 g'; displayStatus.textContent='Digite o peso e toque Iniciar'; }
  if(id==='ua8'){ renderPresetManager(); }
  if(id==='ua9'){ presetBuffer=''; displayTime.textContent='00:00'; displayStatus.textContent='Digite o tempo do preset'; setupNamePicker(); }
}
function pressDigit(d){
  digitBuffer += d;
  if(digitBuffer.length>4) digitBuffer = digitBuffer.slice(-4);
  const padded = digitBuffer.padStart(4,'0');
  displayTime.textContent = padded.slice(0,2)+':'+padded.slice(2,4);
}
function clearTyped(){
  digitBuffer = '';
  displayTime.textContent = '00:00';
  displayStatus.textContent = '';
}
function startTyped(){
  if(!digitBuffer) return;
  const padded = digitBuffer.padStart(4,'0');
  const mm = parseInt(padded.slice(0,2),10), ss = parseInt(padded.slice(2,4),10);
  const s = mm*60+ss;
  digitBuffer = '';
  if(s>0) quickStart(s);
}
function quickStart(s, name){
  label = name; seconds = s; total = s;
  go('ua4');
  execLabel.textContent = name + ' — em andamento';
  updateDisplay(name);
  runTimer();
}
let gramsBuffer = '';
function pressGramDigit(d){
  gramsBuffer += d;
  if(gramsBuffer.length>4) gramsBuffer = gramsBuffer.slice(-4);
  displayTime.textContent = parseInt(gramsBuffer,10) + ' g';
  displayStatus.textContent = 'Digite o peso e toque Iniciar';
}
function clearGrams(){
  gramsBuffer = '';
  displayTime.textContent = '0 g';
  displayStatus.textContent = 'Digite o peso e toque Iniciar';
}
function startDefrostTyped(){
  const grams = parseInt(gramsBuffer,10);
  if(!grams) return;
  gramsBuffer = '';
  const s = Math.round(grams/500*120); // ~2 min a cada 500g
  quickStart(s, 'Descongelando ' + grams + 'g');
}
function startCustom(){
  quickStart(parseInt(customRange.value,10));
}
function runTimer(){
  paused = false; pauseBtn.textContent = 'Pausar';
  clearInterval(timer);
  timer = setInterval(()=>{
    if(paused) return;
    seconds--;
    updateDisplay();
    if(seconds<=0){
      clearInterval(timer);
      go('ua5');
    }
  },1000);
}
function pauseResume(){
  paused = !paused;
  pauseBtn.textContent = paused ? 'Retomar' : 'Pausar';
  updateDisplay(paused ? 'Pausado' : label + ' — em andamento');
}
function cancelRun(){
  clearInterval(timer);
  go('ua1');
}
function openDoor(){
  paused = true;
  go('ua6');
}
function closeDoor(){
  go('ua4');
  paused = false;
  updateDisplay(label + ' — em andamento');
}
 
// ---- Presets personalizados (máx. 2) ----
let customPresets = [];
let presetBuffer = '';
let deleteIndex = null;
 
const trashIcon = '<svg viewBox="0 0 24 24" width="17" height="17" fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round"><path d="M4 7h16M9 7V5a1 1 0 011-1h4a1 1 0 011 1v2m-8 0l1 12.5A1 1 0 008 20.5h8a1 1 0 001-1.5L18 7"/></svg>';
const presetIcon = '<svg class="icon-svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path d="M7 3h10a1 1 0 011 1v16.2a.6.6 0 01-.94.5L12 17l-5.06 3.7A.6.6 0 016 20.2V4a1 1 0 011-1z"/></svg>';
 
function renderPresetManager(){
  const list = document.getElementById('presetList');
  list.innerHTML = '';
  customPresets.forEach((p, i)=>{
    const row = document.createElement('div');
    row.className = 'preset-row';
    row.innerHTML =
      '<button onclick="quickStart(' + p.seconds + ',\'' + p.name.replace(/'/g,"\\'") + '\')">' + presetIcon +
      '<span>' + p.name + ' · ' + fmt(p.seconds) + '</span></button>' +
      '<button class="trash-btn" onclick="askDelete(' + i + ')">' + trashIcon + '</button>';
    list.appendChild(row);
  });
  if(customPresets.length===0){
    list.innerHTML = '<div class="empty-note">Nenhum preset ainda</div>';
  }
  addPresetBtn.style.display = customPresets.length < 2 ? 'flex' : 'none';
}
function pressPresetDigit(d){
  presetBuffer += d;
  if(presetBuffer.length>4) presetBuffer = presetBuffer.slice(-4);
  const padded = presetBuffer.padStart(4,'0');
  displayTime.textContent = padded.slice(0,2)+':'+padded.slice(2,4);
}
function clearPresetDigits(){
  presetBuffer = '';
  displayTime.textContent = '00:00';
}
const presetNameOptions = ['Café','Sopa','Pizza','Massa','Lanche','Bebida'];
let nameOptions = [];
let nameIndex = 0;
function setupNamePicker(){
  nameOptions = presetNameOptions.filter(n=>!customPresets.some(p=>p.name===n));
  nameIndex = 0;
  updateNameDisplay();
}
function updateNameDisplay(){
  presetNameDisplay.textContent = nameOptions.length ? nameOptions[nameIndex] : '—';
}
function cycleName(dir){
  if(!nameOptions.length) return;
  nameIndex = (nameIndex + dir + nameOptions.length) % nameOptions.length;
  updateNameDisplay();
}
function savePreset(){
  const name = nameOptions[nameIndex];
  const padded = presetBuffer.padStart(4,'0');
  const s = parseInt(padded.slice(0,2),10)*60 + parseInt(padded.slice(2,4),10);
  if(!name || s<=0 || customPresets.length>=2) return;
  customPresets.push({name, seconds:s});
  go('ua8');
}
function askDelete(i){
  deleteIndex = i;
  deleteWarnText.textContent = 'Excluir "' + customPresets[i].name + '"?';
  go('ua10');
}
function deletePresetConfirmed(){
  if(deleteIndex!==null) customPresets.splice(deleteIndex,1);
  deleteIndex = null;
  go('ua8');
}
</script>
</body>
</html>
 

