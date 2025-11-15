 <!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Crash Game</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      margin: 0;
      padding: 0;
      background: #0f172a;
      color: white;
      font-family: system-ui, -apple-system, sans-serif;
    }
    .animate-pulse {
      animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
    }
    @keyframes pulse {
      0%, 100% { opacity: 1; }
      50% { opacity: .5; }
    }
  </style>
</head>
<body>
  <div id="app" class="min-h-screen bg-slate-900 text-white p-4 max-w-md mx-auto"></div>

  <script>
    const state = {
      balance: 0,
      activeTab: 'historique',
      bet1: '0.4',
      bet2: '0.4',
      isPlaying: false,
      multiplier: 1.00,
      hasCrashed: false,
      crashPoint: 0,
      history: [
        { id: 1, user: '*******35', odds: 'x0', bet: '20.37 EUR', gain: '0 EUR' },
        { id: 2, user: '*******67', odds: 'x0', bet: '3.64 EUR', gain: '0 EUR' },
        { id: 3, user: '*******49', odds: 'x0', bet: '2.62 EUR', gain: '0 EUR' },
        { id: 4, user: '*******77', odds: 'x0', bet: '1.43 EUR', gain: '0 EUR' },
        { id: 5, user: '*******51', odds: 'x0', bet: '1.4 EUR', gain: '0 EUR' },
        { id: 6, user: '*******23', odds: 'x0', bet: '1.35 EUR', gain: '0 EUR' },
        { id: 7, user: '*******31', odds: 'x0', bet: '1.31 EUR', gain: '0 EUR' },
        { id: 8, user: '*******11', odds: 'x0', bet: '1.22 EUR', gain: '0 EUR' },
      ],
      totalBets: 156,
      totalAmount: 143.07,
      rocketPosition: { x: 10, y: 80 },
      animationId: null
    };

    const quickAmounts = [1, 2, 5, 10, 50, 100];

    function startGame() {
      state.crashPoint = 1 + Math.random() * 5;
      state.multiplier = 1.00;
      state.hasCrashed = false;
      state.isPlaying = true;
      state.rocketPosition = { x: 10, y: 80 };

      const bet1Amount = parseFloat(state.bet1) || 0;
      const bet2Amount = parseFloat(state.bet2) || 0;
      state.totalBets += 2;
      state.totalAmount += bet1Amount + bet2Amount;

      const startTime = Date.now();
      
      function animate() {
        const elapsed = (Date.now() - startTime) / 1000;
        const newMultiplier = 1 + elapsed * 0.5;

        if (newMultiplier >= state.crashPoint) {
          state.multiplier = state.crashPoint;
          state.hasCrashed = true;
          state.isPlaying = false;
          state.rocketPosition = { x: 50, y: 20 };
          render();
          return;
        }

        state.multiplier = newMultiplier;
        const progress = Math.min((newMultiplier - 1) / 5, 1);
        state.rocketPosition = {
          x: 10 + progress * 70,
          y: 80 - progress * 60
        };

        render();
        state.animationId = requestAnimationFrame(animate);
      }

      state.animationId = requestAnimationFrame(animate);
      render();
    }

    function resetGame() {
      if (state.animationId) {
        cancelAnimationFrame(state.animationId);
      }
      state.isPlaying = false;
      state.hasCrashed = false;
      state.multiplier = 1.00;
      state.rocketPosition = { x: 10, y: 80 };
      render();
    }

    function render() {
      const app = document.getElementById('app');
      app.innerHTML = `
        <div class="space-y-4">
          <!-- Header -->
          <div class="flex items-center justify-between bg-slate-800 rounded-lg p-4">
            <span class="text-2xl">←</span>
            <div class="flex items-center gap-2">
              <span class="text-xl font-bold">${state.balance} €</span>
              <button class="bg-green-500 px-3 py-1 rounded text-sm font-bold">+</button>
            </div>
          </div>

          <!-- Tabs -->
          <div class="flex gap-2">
            <button 
              onclick="state.activeTab='regles'; render()"
              class="flex-1 py-3 rounded-lg border-2 ${state.activeTab === 'regles' ? 'border-orange-500 text-orange-500' : 'border-slate-600 text-slate-400'}"
            >
              Règles
            </button>
            <button 
              onclick="state.activeTab='historique'; render()"
              class="flex-1 py-3 rounded-lg ${state.activeTab === 'historique' ? 'bg-gradient-to-r from-orange-500 to-red-500' : 'bg-slate-700'}"
            >
              🕐 HISTORIQUE
            </button>
          </div>

          <!-- Game Canvas -->
          <div class="bg-slate-800 rounded-lg p-6 h-48 relative overflow-hidden">
            <div class="absolute inset-0 opacity-20">
              ${[...Array(6)].map((_, i) => `<div class="border-b border-slate-600" style="margin-top: ${i * 40}px"></div>`).join('')}
            </div>
            
            ${!state.isPlaying && !state.hasCrashed ? `
              <div class="absolute inset-0 flex items-center justify-center">
                <div class="flex gap-4">
                  ${[...Array(7)].map(() => '<div class="w-3 h-3 bg-slate-600 rounded-full"></div>').join('')}
                </div>
              </div>
            ` : ''}
            
            ${state.isPlaying || state.hasCrashed ? `
              <div class="absolute transition-all duration-100" style="left: ${state.rocketPosition.x}%; top: ${state.rocketPosition.y}%">
                <div class="text-4xl" style="transform: rotate(-45deg)">🚀</div>
              </div>
              
              <div class="absolute top-4 right-4">
                <div class="text-5xl font-bold ${state.hasCrashed ? 'text-red-500' : 'text-white'}">
                  ${state.hasCrashed ? state.crashPoint.toFixed(2) : state.multiplier.toFixed(2)}x
                </div>
              </div>

              ${state.hasCrashed ? `
                <div class="absolute inset-0 flex items-center justify-center">
                  <div class="text-red-500 text-6xl font-bold animate-pulse">CRASH!</div>
                </div>
              ` : ''}
            ` : ''}
          </div>

          <!-- Bet Controls - First Set -->
          <div class="bg-slate-800 rounded-lg p-4">
            <div class="flex items-center gap-2 mb-3">
              <input
                type="text"
                value="${state.bet1}"
                oninput="state.bet1=this.value; render()"
                class="flex-1 bg-white text-slate-900 rounded-lg px-4 py-3 text-lg font-bold"
              />
              <button onclick="state.bet1=''; render()" class="text-slate-400 text-2xl p-2">✕</button>
            </div>
            
            <div class="grid grid-cols-3 gap-2 mb-3">
              ${quickAmounts.map(amount => `
                <button
                  onclick="state.bet1='${amount}'; render()"
                  class="bg-slate-700 hover:bg-slate-600 py-3 rounded-lg font-bold"
                >
                  ${amount}
                </button>
              `).join('')}
            </div>
            
            <button class="w-full bg-gradient-to-r from-orange-500 to-red-500 py-4 rounded-lg font-bold text-lg border-2 border-orange-600">
              ACTIVER LE JEU AUTOMATIQUE
            </button>
          </div>

          <!-- Bet Controls - Second Set -->
          <div class="bg-slate-800 rounded-lg p-4">
            <div class="flex items-center gap-2 mb-3">
              <input
                type="text"
                value="${state.bet2}"
                oninput="state.bet2=this.value; render()"
                class="flex-1 bg-white text-slate-900 rounded-lg px-4 py-3 text-lg font-bold"
              />
              <button onclick="state.bet2=''; render()" class="text-slate-400 text-2xl p-2">✕</button>
            </div>
            
            <div class="grid grid-cols-3 gap-2 mb-3">
              ${quickAmounts.map(amount => `
                <button
                  onclick="state.bet2='${amount}'; render()"
                  class="bg-slate-700 hover:bg-slate-600 py-3 rounded-lg font-bold"
                >
                  ${amount}
                </button>
              `).join('')}
            </div>
            
            <button 
              onclick="${state.hasCrashed ? 'resetGame()' : 'startGame()'}"
              ${state.isPlaying ? 'disabled' : ''}
              class="w-full bg-gradient-to-r from-orange-500 to-red-500 py-4 rounded-lg font-bold text-lg disabled:opacity-50"
            >
              ${state.hasCrashed ? 'NOUVEAU TOUR' : state.isPlaying ? 'EN COURS...' : 'PLACER UN PARI'}
            </button>
          </div>

          <!-- Stats Bar -->
          <div class="bg-gradient-to-r from-orange-500 to-red-500 rounded-lg p-4 grid grid-cols-3 gap-4">
            <div class="text-center">
              <div class="text-sm opacity-90">Nombre de paris</div>
              <div class="text-xl font-bold flex items-center justify-center gap-1">
                👤 ${state.totalBets}
              </div>
            </div>
            <div class="text-center">
              <div class="text-sm opacity-90">Total des paris</div>
              <div class="text-xl font-bold">💶 ${state.totalAmount.toFixed(2)} EUR</div>
            </div>
            <div class="text-center">
              <div class="text-sm opacity-90">Gains totaux</div>
              <div class="text-xl font-bold">💰 0 EUR</div>
            </div>
          </div>

          <!-- History Table -->
          <div class="bg-slate-800 rounded-lg overflow-hidden">
            <div class="grid grid-cols-4 gap-2 p-3 bg-slate-700 text-xs text-slate-400">
              <div>NOM D'UTILISAT...</div>
              <div>COTE</div>
              <div>PARI</div>
              <div>GAIN</div>
            </div>
            ${state.history.map(item => `
              <div class="grid grid-cols-4 gap-2 p-3 text-sm border-b border-slate-700">
                <div class="truncate">${item.user}</div>
                <div>${item.odds}</div>
                <div>${item.bet}</div>
                <div>${item.gain}</div>
              </div>
            `).join('')}
          </div>
        </div>
      `;
    }

    // Initial render
    render();
  </script>
</body>
</html>
