<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Crash Replica UI</title>
  <style>
    /* ====== Reset & base ====== */
    * { box-sizing: border-box; margin:0; padding:0; font-family: Inter, "Helvetica Neue", Arial, sans-serif; }
    html,body { height:100%; background:#071020; color:#fff; }
    a { color:inherit; text-decoration:none; }
    .container { max-width:420px; margin:10px auto; padding:12px; }

    /* ====== Top bar ====== */
    .topbar { display:flex; align-items:center; gap:10px; }
    .back { width:36px; height:36px; display:grid; place-items:center; background:transparent; border-radius:8px; }
    .balance { margin-left:auto; background:#263346; padding:6px 10px; border-radius:20px; color:#cfe9d6; display:flex; align-items:center; gap:8px; }
    .bonus { width:36px; height:36px; display:grid; place-items:center; border-radius:18px; border:1px solid #3b556b; }

    /* ====== Graph card ====== */
    .card { margin-top:10px; background:#0e2130; border-radius:14px; padding:10px; box-shadow: 0 6px 18px rgba(0,0,0,0.5); }
    .tabs { display:flex; gap:8px; }
    .tab { padding:8px 12px; border-radius:8px; background:transparent; border:1px solid rgba(255,255,255,0.04); color:#f3a24a; font-weight:600; }
    .tab.active { background: linear-gradient(90deg,#ff7b2a,#ff5050); color:white; }

    .graph {
      position:relative;
      height:160px;
      margin-top:10px;
      background: linear-gradient(180deg,#0a1b2a,#0f2a3a);
      border-radius:10px;
      overflow:hidden;
      border:1px dashed rgba(255,255,255,0.03);
    }

    /* plane & multiplier */
    .plane { position:absolute; left:20px; bottom:24px; width:58px; transform-origin:left center; transition: transform 0.12s linear; }
    .multiplier {
      position:absolute;
      right:14px;
      bottom:12px;
      font-weight:800;
      font-size:44px;
      color:#ffffff;
      text-shadow:0 2px 8px rgba(0,0,0,0.6);
    }

    /* ====== Bet panels ====== */
    .bets { margin-top:12px; display:grid; gap:12px; }
    .bet-panel { display:flex; gap:12px; align-items:center; }
    .bet-left { flex:1; background:#0c2a3a; padding:8px; border-radius:10px; }
    .bet-left input { width:100%; padding:10px; border-radius:8px; border:0; background:transparent; color:#fff; font-weight:700; font-size:16px; }
    .quick { display:flex; gap:6px; margin-top:8px; flex-wrap:wrap; }
    .quick button { background:#0e2230; border:0; padding:8px 12px; border-radius:8px; color:#9fb0c0; font-weight:600; }

    .bet-actions { width:150px; display:flex; flex-direction:column; gap:8px; }
    .btn { background: linear-gradient(90deg,#ff7b2a,#ff5050); border:0; padding:12px; border-radius:10px; font-weight:800; color:#fff; cursor:pointer; }
    .btn.secondary { background:transparent; border:1px solid rgba(255,255,255,0.06); color:#f3a24a; font-weight:700; }

    /* ====== Stats bar ====== */
    .stats { display:flex; gap:8px; margin-top:12px; border-radius:10px; overflow:hidden; }
    .stat { flex:1; padding:10px; background:linear-gradient(180deg,#ff9b53,#ef6a6a); color:#fff; text-align:center; }
    .stat .label { font-size:12px; opacity:0.95; }
    .stat .value { font-weight:800; margin-top:6px; }

    /* ====== Users table ====== */
    .table { margin-top:10px; background:rgba(0,0,0,0.12); border-radius:10px; padding:10px; max-height:240px; overflow:auto; }
    table { width:100%; border-collapse:collapse; }
    th,td { padding:8px 6px; text-align:left; font-size:13px; color:#dfe9f2; }
    th { color:#9fb0c0; font-size:12px; font-weight:700; position:sticky; top:0; background:linear-gradient(180deg,rgba(0,0,0,0.15),transparent); }

    /* small screens */
    @media (max-width:420px){
      .container{ padding:10px; }
      .bet-actions{ width:140px; }
      .multiplier{ font-size:36px; }
    }

  </style>
</head>
<body>
  <div class="container">
    <div class="topbar">
      <div class="back" title="Retour">
        <!-- simple arrow -->
        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="#cfe9d6" stroke-width="2"><path d="M15 18l-6-6 6-6"/></svg>
      </div>

      <div style="font-weight:700;">0 €</div>

      <div class="balance">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="#cfe9d6" stroke-width="1.6"><circle cx="12" cy="12" r="9"/><path d="M12 7v6l3 3"/></svg>
        <div style="font-size:13px;">0 €</div>
      </div>

      <div class="bonus" title="Bonus">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="#9fb0c0" stroke-width="1.6"><path d="M12 2l2 5 5 2-5 2-2 5-2-5-5-2 5-2z"/></svg>
      </div>
    </div>

    <div class="card">
      <div class="tabs">
        <div class="tab">Règles</div>
        <div class="tab active">HISTORIQUE</div>
      </div>

      <div class="graph" id="graph">
        <!-- plane (SVG) -->
        <img src="" alt="plane" class="plane" id="plane" style="filter:drop-shadow(0 6px 8px rgba(0,0,0,0.6));">
        <div class="multiplier" id="mult">1.00x</div>
      </div>

      <div class="bets">
        <!-- first bet -->
        <div class="bet-panel">
          <div class="bet-left">
            <input type="number" id="bet1" value="0.4" step="0.1" min="0.1" />
            <div class="quick">
              <button onclick="setBet(1,1)">1</button>
              <button onclick="setBet(1,2)">2</button>
              <button onclick="setBet(1,5)">5</button>
              <button onclick="setBet(1,10)">10</button>
              <button onclick="setBet(1,50)">50</button>
              <button onclick="setBet(1,100)">100</button>
            </div>
          </div>
          <div class="bet-actions">
            <button class="btn secondary" id="auto1">ACTIVER LE JEU AUTOMATIQUE</button>
            <button class="btn" onclick="placeBet(1)">PLACER UN PARI</button>
          </div>
        </div>

        <!-- second bet -->
        <div class="bet-panel">
          <div class="bet-left">
            <input type="number" id="bet2" value="0.4" step="0.1" min="0.1" />
            <div class="quick">
              <button onclick="setBet(2,1)">1</button>
              <button onclick="setBet(2,2)">2</button>
              <button onclick="setBet(2,5)">5</button>
              <button onclick="setBet(2,10)">10</button>
              <button onclick="setBet(2,50)">50</button>
              <button onclick="setBet(2,100)">100</button>
            </div>
          </div>
          <div class="bet-actions">
            <button class="btn secondary" id="auto2">ACTIVER LE JEU AUTOMATIQUE</button>
            <button class="btn" onclick="placeBet(2)">PLACER UN PARI</button>
          </div>
        </div>

        <div class="stats">
          <div class="stat">
            <div class="label">Nombre de paris</div>
            <div class="value" id="count">441</div>
          </div>
          <div class="stat" style="background:linear-gradient(180deg,#ff8b55,#ff5050);">
            <div class="label">Total des paris</div>
            <div class="value" id="total">390.84 EUR</div>
          </div>
          <div class="stat" style="background:linear-gradient(180deg,#ffb36a,#ffd07a); color:#222;">
            <div class="label">Gains totaux</div>
            <div class="value" id="wins">0 EUR</div>
          </div>
        </div>

        <div class="table">
          <table id="usersTable">
            <thead>
              <tr><th>NOM D'UTILISAT...</th><th>COTE</th><th>PARI</th><th>GAIN</th></tr>
            </thead>
            <tbody>
              <!-- rows injected by JS -->
            </tbody>
          </table>
        </div>

      </div>
    </div>
  </div>

  <script>
    /* ====== Simple UI data & behaviour to mimic the screenshot ====== */

    // inject dummy user rows
    const users = [
      {name:'*******35', cote:'x0', pari:'20.37 EUR', gain:'0 EUR'},
      {name:'*******55', cote:'x0', pari:'12.58 EUR', gain:'0 EUR'},
      {name:'*******51', cote:'x0', pari:'4.72 EUR', gain:'0 EUR'},
      {name:'*******69', cote:'x0', pari:'4.04 EUR', gain:'0 EUR'},
      {name:'*******67', cote:'x0', pari:'3.64 EUR', gain:'0 EUR'},
      {name:'*******83', cote:'x0', pari:'3.54 EUR', gain:'0 EUR'},
      {name:'*******07', cote:'x0', pari:'3.23 EUR', gain:'0 EUR'},
      {name:'*******47', cote:'x0', pari:'3.18 EUR', gain:'0 EUR'},
      {name:'*******99', cote:'x0', pari:'4.32 EUR', gain:'0 EUR'},
    ];
    const tbody = document.querySelector('#usersTable tbody');
    users.forEach(u=>{
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>${u.name}</td><td>${u.cote}</td><td>${u.pari}</td><td>${u.gain}</td>`;
      tbody.appendChild(tr);
    });

    // plane image: embed small svg as data URL to ensure standalone file
    const planeSvg = encodeURIComponent(`<svg viewBox="0 0 512 512" xmlns="http://www.w3.org/2000/svg"><g fill="#ffb84d"><path d="M55 256c0 0 60 30 150 30s150-30 150-30 0-22-50-40-100-32-150-32S105 174 55 192z"/><path d="M480 80c-6-6-16-7-22-2l-80 60 24 14 110-72c6-4 8-12 2-18z"/></g></svg>`);
    document.getElementById('plane').src = 'data:image/svg+xml;utf8,' + planeSvg;

    // multiplier animation simulation
    const multEl = document.getElementById('mult');
    const planeEl = document.getElementById('plane');
    let running=false;
    let currentMult = 1.00;
    let speed = 0.005; // exponential growth rate
    let crashAt = null;
    let raf = null;

    function startRound(){
      // reset
      currentMult = 1.00;
      multEl.textContent = currentMult.toFixed(2) + 'x';
      crashAt = randomCrash(); // e.g., between 1.0 and 10.0 (heavier tail)
      running = true;
      // visual reset
      planeEl.style.left = '20px';
      planeEl.style.bottom = '24px';
      planeEl.style.transform = 'rotate(0deg) scale(1)';
      cancelAnimationFrame(raf);
      raf = requestAnimationFrame(tick);
    }

    function tick(){
      if(!running) return;
      // accelerate multiplier exponentially but with small randomness
      currentMult *= (1 + speed + Math.random()*0.002);
      if(currentMult > 100) currentMult = 100; // cap for visual
      multEl.textContent = currentMult.toFixed(2) + 'x';

      // move plane along x & rotate slightly
      const containerWidth = document.getElementById('graph').clientWidth;
      const progress = Math.log(currentMult) / Math.log(crashAt || 2); // rough progress
      const left = 20 + Math.min(containerWidth - 100, progress * (containerWidth - 120));
      planeEl.style.left = left + 'px';
      planeEl.style.transform = `rotate(${Math.min(25, progress*25)}deg) scale(${1+progress*0.12})`;

      // crash check
      if(currentMult >= crashAt){
        // simulate crash
        running = false;
        multEl.textContent = currentMult.toFixed(2) + 'x';
        planeEl.style.transform += ' translateY(20px) rotate(120deg)';
        // flash red multiplier briefly
        multEl.animate([{color:'#fff'},{color:'#ff4444'},{color:'#fff'}], {duration:700});
        // schedule next round after short delay
        setTimeout(startRound, 2200);
        return;
      }

      raf = requestAnimationFrame(tick);
    }

    function randomCrash(){
      // heavy-tailed distribution so many small crashes and some long runs.
      // returns value >= 1.0
      const r = Math.random();
      if(r < 0.6) return 1 + Math.random()*1.5;      // most rounds small (1.0 - 2.5)
      if(r < 0.88) return 2.5 + Math.random()*5;     // medium rounds (2.5 - 7.5)
      return 8 + Math.random()*60;                   // rare long runs
    }

    // bets (UI only)
    function setBet(panel, val){
      const el = document.getElementById('bet' + panel);
      el.value = val;
    }

    function placeBet(panel){
      const val = Number(document.getElementById('bet' + panel).value || 0);
      if(!val || val <= 0) { alert('Entrez un montant valide'); return; }
      // add to users table top as mock behaviour
      const tr = document.createElement('tr');
      tr.innerHTML = `<td>*******` + (Math.floor(Math.random()*90)+10) + `</td><td>x0</td><td>${val.toFixed(2)} EUR</td><td>0 EUR</td>`;
      tbody.insertBefore(tr, tbody.firstChild);
      // update stats
      const countEl = document.getElementById('count');
      const totalEl = document.getElementById('total');
      let count = parseInt(countEl.textContent) || 0;
      count++; countEl.textContent = count;
      // parse total
      const t = (document.getElementById('total').textContent || '0').replace(/[^\d\.\-]/g,'') || '0';
      const newTotal = (parseFloat(t) + val).toFixed(2);
      totalEl.textContent = newTotal + ' EUR';
      // feedback
      // (in real app would add bet to server for next round)
    }

    // start initial round
    startRound();

    // attach auto buttons (toggle visual state only)
    document.getElementById('auto1').addEventListener('click', e=>{
      e.target.textContent = e.target.textContent.includes('ACTIVER') ? 'DÉSACTIVER LE JEU' : 'ACTIVER LE JEU AUTOMATIQUE';
      e.target.classList.toggle('secondary');
    });
    document.getElementById('auto2').addEventListener('click', e=>{
      e.target.textContent = e.target.textContent.includes('ACTIVER') ? 'DÉSACTIVER LE JEU' : 'ACTIVER LE JEU AUTOMATIQUE';
      e.target.classList.toggle('secondary');
    });

  </script>
</body>
</html>
