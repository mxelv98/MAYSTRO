import React, { useState, useEffect, useRef } from 'react';
import { TrendingUp, Clock, User } from 'lucide-react';

export default function CrashGame() {
  const [balance, setBalance] = useState(0);
  const [activeTab, setActiveTab] = useState('historique');
  const [bet1, setBet1] = useState('0.4');
  const [bet2, setBet2] = useState('0.4');
  const [isPlaying, setIsPlaying] = useState(false);
  const [multiplier, setMultiplier] = useState(1.00);
  const [hasCrashed, setHasCrashed] = useState(false);
  const [crashPoint, setCrashPoint] = useState(0);
  const [history, setHistory] = useState([
    { id: 1, user: '*******35', odds: 'x0', bet: '20.37 EUR', gain: '0 EUR' },
    { id: 2, user: '*******67', odds: 'x0', bet: '3.64 EUR', gain: '0 EUR' },
    { id: 3, user: '*******49', odds: 'x0', bet: '2.62 EUR', gain: '0 EUR' },
    { id: 4, user: '*******77', odds: 'x0', bet: '1.43 EUR', gain: '0 EUR' },
    { id: 5, user: '*******51', odds: 'x0', bet: '1.4 EUR', gain: '0 EUR' },
    { id: 6, user: '*******23', odds: 'x0', bet: '1.35 EUR', gain: '0 EUR' },
    { id: 7, user: '*******31', odds: 'x0', bet: '1.31 EUR', gain: '0 EUR' },
    { id: 8, user: '*******11', odds: 'x0', bet: '1.22 EUR', gain: '0 EUR' },
  ]);
  const [totalBets, setTotalBets] = useState(156);
  const [totalAmount, setTotalAmount] = useState(143.07);
  const [rocketPosition, setRocketPosition] = useState({ x: 10, y: 80 });
  const animationRef = useRef(null);

  const quickAmounts = [1, 2, 5, 10, 50, 100];

  useEffect(() => {
    if (isPlaying && !hasCrashed) {
      const startTime = Date.now();
      const animate = () => {
        const elapsed = (Date.now() - startTime) / 1000;
        const newMultiplier = 1 + elapsed * 0.5;
        
        if (newMultiplier >= crashPoint) {
          setMultiplier(crashPoint);
          setHasCrashed(true);
          setIsPlaying(false);
          setRocketPosition({ x: 50, y: 20 });
          return;
        }
        
        setMultiplier(newMultiplier);
        const progress = Math.min((newMultiplier - 1) / 5, 1);
        setRocketPosition({
          x: 10 + progress * 70,
          y: 80 - progress * 60
        });
        
        animationRef.current = requestAnimationFrame(animate);
      };
      
      animationRef.current = requestAnimationFrame(animate);
      
      return () => {
        if (animationRef.current) {
          cancelAnimationFrame(animationRef.current);
        }
      };
    }
  }, [isPlaying, hasCrashed, crashPoint]);

  const startGame = () => {
    const newCrashPoint = 1 + Math.random() * 5;
    setCrashPoint(newCrashPoint);
    setMultiplier(1.00);
    setHasCrashed(false);
    setIsPlaying(true);
    setRocketPosition({ x: 10, y: 80 });
    
    const bet1Amount = parseFloat(bet1) || 0;
    const bet2Amount = parseFloat(bet2) || 0;
    setTotalBets(prev => prev + 2);
    setTotalAmount(prev => prev + bet1Amount + bet2Amount);
  };

  const resetGame = () => {
    setIsPlaying(false);
    setHasCrashed(false);
    setMultiplier(1.00);
    setRocketPosition({ x: 10, y: 80 });
  };

  return (
    <div className="min-h-screen bg-gradient-to-b from-slate-900 via-slate-800 to-slate-900 text-white p-4">
      {/* Header */}
      <div className="flex items-center justify-between mb-4">
        <button className="text-blue-400 text-2xl">←</button>
        <div className="flex items-center gap-2 bg-slate-700 rounded-full px-4 py-2">
          <span className="text-xl font-bold">{balance} €</span>
          <button className="bg-green-500 rounded-full w-8 h-8 flex items-center justify-center text-xl font-bold">
            +
          </button>
        </div>
        <button className="text-blue-400">
          <svg className="w-8 h-8" fill="currentColor" viewBox="0 0 24 24">
            <circle cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="2" fill="none"/>
          </svg>
        </button>
      </div>

      {/* Tabs */}
      <div className="flex gap-2 mb-4">
        <button
          onClick={() => setActiveTab('regles')}
          className={`flex-1 py-3 rounded-lg border-2 ${
            activeTab === 'regles' ? 'border-orange-500 text-orange-500' : 'border-slate-600 text-slate-400'
          }`}
        >
          Règles
        </button>
        <button
          onClick={() => setActiveTab('historique')}
          className={`flex-1 py-3 rounded-lg ${
            activeTab === 'historique' ? 'bg-gradient-to-r from-orange-500 to-red-500' : 'bg-slate-700'
          }`}
        >
          <Clock className="inline mr-2" size={20} />
          HISTORIQUE
        </button>
      </div>

      {/* Game Canvas */}
      <div className="bg-slate-800 rounded-lg p-6 mb-4 h-48 relative overflow-hidden">
        <div className="absolute inset-0 opacity-20">
          {[...Array(6)].map((_, i) => (
            <div key={i} className="border-b border-slate-600" style={{ marginTop: `${i * 40}px` }} />
          ))}
        </div>
        
        {!isPlaying && !hasCrashed && (
          <div className="absolute inset-0 flex items-center justify-center">
            <div className="flex gap-4">
              {[...Array(7)].map((_, i) => (
                <div key={i} className="w-3 h-3 bg-slate-600 rounded-full" />
              ))}
            </div>
          </div>
        )}
        
        {(isPlaying || hasCrashed) && (
          <>
            <div 
              className="absolute transition-all duration-100"
              style={{ left: `${rocketPosition.x}%`, top: `${rocketPosition.y}%` }}
            >
              <div className="text-4xl transform -rotate-45">🚀</div>
            </div>
            
            <div className="absolute top-4 right-4">
              <div className={`text-5xl font-bold ${hasCrashed ? 'text-red-500' : 'text-white'}`}>
                {hasCrashed ? `${crashPoint.toFixed(2)}x` : `${multiplier.toFixed(2)}x`}
              </div>
            </div>

            {hasCrashed && (
              <div className="absolute inset-0 flex items-center justify-center">
                <div className="text-red-500 text-6xl font-bold animate-pulse">CRASH!</div>
              </div>
            )}
          </>
        )}
      </div>

      {/* Bet Controls - First Set */}
      <div className="bg-slate-800 rounded-lg p-4 mb-3">
        <div className="flex items-center gap-2 mb-3">
          <input
            type="text"
            value={bet1}
            onChange={(e) => setBet1(e.target.value)}
            className="flex-1 bg-white text-slate-900 rounded-lg px-4 py-3 text-lg font-bold"
          />
          <button 
            onClick={() => setBet1('')}
            className="text-slate-400 text-2xl p-2"
          >
            ✕
          </button>
        </div>
        
        <div className="grid grid-cols-3 gap-2 mb-3">
          {quickAmounts.map((amount) => (
            <button
              key={amount}
              onClick={() => setBet1(amount.toString())}
              className="bg-slate-700 hover:bg-slate-600 py-3 rounded-lg font-bold"
            >
              {amount}
            </button>
          ))}
        </div>
        
        <button className="w-full bg-gradient-to-r from-orange-500 to-red-500 py-4 rounded-lg font-bold text-lg border-2 border-orange-600">
          ACTIVER LE JEU AUTOMATIQUE
        </button>
      </div>

      {/* Bet Controls - Second Set */}
      <div className="bg-slate-800 rounded-lg p-4 mb-4">
        <div className="flex items-center gap-2 mb-3">
          <input
            type="text"
            value={bet2}
            onChange={(e) => setBet2(e.target.value)}
            className="flex-1 bg-white text-slate-900 rounded-lg px-4 py-3 text-lg font-bold"
          />
          <button 
            onClick={() => setBet2('')}
            className="text-slate-400 text-2xl p-2"
          >
            ✕
          </button>
        </div>
        
        <div className="grid grid-cols-3 gap-2 mb-3">
          {quickAmounts.map((amount) => (
            <button
              key={amount}
              onClick={() => setBet2(amount.toString())}
              className="bg-slate-700 hover:bg-slate-600 py-3 rounded-lg font-bold"
            >
              {amount}
            </button>
          ))}
        </div>
        
        <button 
          onClick={hasCrashed ? resetGame : startGame}
          disabled={isPlaying}
          className="w-full bg-gradient-to-r from-orange-500 to-red-500 py-4 rounded-lg font-bold text-lg disabled:opacity-50"
        >
          {hasCrashed ? 'NOUVEAU TOUR' : isPlaying ? 'EN COURS...' : 'PLACER UN PARI'}
        </button>
      </div>

      {/* Stats Bar */}
      <div className="bg-gradient-to-r from-orange-500 to-red-500 rounded-lg p-4 mb-4 grid grid-cols-3 gap-4">
        <div className="text-center">
          <div className="text-sm opacity-90">Nombre de paris</div>
          <div className="text-xl font-bold flex items-center justify-center gap-1">
            <User size={16} />
            {totalBets}
          </div>
        </div>
        <div className="text-center">
          <div className="text-sm opacity-90">Total des paris</div>
          <div className="text-xl font-bold">💶 {totalAmount.toFixed(2)} EUR</div>
        </div>
        <div className="text-center">
          <div className="text-sm opacity-90">Gains totaux</div>
          <div className="text-xl font-bold">💰 0 EUR</div>
        </div>
      </div>

      {/* History Table */}
      <div className="bg-slate-800 rounded-lg overflow-hidden">
        <div className="grid grid-cols-4 gap-2 p-3 bg-slate-700 text-xs text-slate-400">
          <div>NOM D'UTILISAT...</div>
          <div>COTE</div>
          <div>PARI</div>
          <div>GAIN</div>
        </div>
        {history.map((item) => (
          <div key={item.id} className="grid grid-cols-4 gap-2 p-3 text-sm border-b border-slate-700">
            <div className="truncate">{item.user}</div>
            <div>{item.odds}</div>
            <div>{item.bet}</div>
            <div>{item.gain}</div>
          </div>
        ))}
      </div>
    </div>
  );
}
