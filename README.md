import React, { useState, useEffect } from 'react';
import { X, ArrowRight, BookOpen, Sparkles, Loader2 } from 'lucide-react';

// A realistic, glowing lily illustration
const RealisticLily = ({ className, isDetail = false }) => (
  <svg 
    viewBox="0 0 200 200" 
    className={`${className} ${isDetail ? 'animate-bloom' : ''}`}
    style={{ filter: 'drop-shadow(0px 10px 20px rgba(212,175,55,0.15))' }}
  >
    <defs>
      <radialGradient id="lilyGrad" cx="50%" cy="50%" r="50%">
        <stop offset="0%" stopColor="#d4af37" />
        <stop offset="25%" stopColor="#fff8e7" />
        <stop offset="100%" stopColor="#ffffff" />
      </radialGradient>
      <radialGradient id="leafGrad" cx="50%" cy="50%" r="50%">
        <stop offset="0%" stopColor="#2a3b22" />
        <stop offset="100%" stopColor="#142010" />
      </radialGradient>
    </defs>
    
    <path d="M100 180 Q110 140 100 80" fill="none" stroke="#2a3b22" strokeWidth="4" strokeLinecap="round" />
    <path d="M102 150 Q130 140 140 110 Q115 135 102 145" fill="url(#leafGrad)" />
    <path d="M98 160 Q60 150 50 120 Q80 145 98 155" fill="url(#leafGrad)" />

    <g>
      <path d="M100 80 Q140 20 170 80 Q130 90 100 80" fill="url(#lilyGrad)" opacity="0.95" />
      <path d="M100 80 Q60 20 30 80 Q70 90 100 80" fill="url(#lilyGrad)" opacity="0.95" />
      <path d="M100 80 Q100 10 130 40 Q115 70 100 80" fill="url(#lilyGrad)" opacity="0.9" />
      <path d="M100 80 Q150 100 130 150 Q90 110 100 80" fill="url(#lilyGrad)" />
      <path d="M100 80 Q50 100 70 150 Q110 110 100 80" fill="url(#lilyGrad)" />
      <path d="M100 80 Q100 160 70 120 Q90 100 100 80" fill="url(#lilyGrad)" />
      <path d="M100 80 Q100 160 130 120 Q110 100 100 80" fill="url(#lilyGrad)" />
    </g>

    <g stroke="#d4af37" strokeWidth="1.5">
      <line x1="100" y1="80" x2="110" y2="50" />
      <line x1="100" y1="80" x2="90" y2="50" />
      <line x1="100" y1="80" x2="100" y2="45" />
    </g>
    <circle cx="110" cy="50" r="3" fill="#8a6d1c" />
    <circle cx="90" cy="50" r="3" fill="#8a6d1c" />
    <circle cx="100" cy="45" r="3" fill="#8a6d1c" />
  </svg>
);

const toRoman = (num) => {
  const roman = ['I', 'II', 'III', 'IV', 'V', 'VI', 'VII', 'VIII', 'IX'];
  return roman[num] || num;
};

const App = () => {
  const [view, setView] = useState('intro'); 
  const [activeIndex, setActiveIndex] = useState(null);
  const [mounted, setMounted] = useState(false);
  
  const [aiExpansion, setAiExpansion] = useState('');
  const [isExpanding, setIsExpanding] = useState(false);
  const [aiPoem, setAiPoem] = useState('');
  const [isPoemGenerating, setIsPoemGenerating] = useState(false);
  const [showPoemModal, setShowPoemModal] = useState(false);

  useEffect(() => {
    setMounted(true);
  }, []);

  const messages = [
    "Love you gyanakam", "Az guri te bm jwankilakam", "Evînê te germiya min e", 
    "Az hayrana cave te bm", "you are so cute", "i like your eyebrows", 
    "All i need is just a paddle", "i became a poet for you", "xoda tum le nastene"
  ];

  const handleOpenDetail = (index) => {
    setActiveIndex(index);
    setAiExpansion('');
    setView('detail');
  };

  const handleCloseDetail = () => {
    setView('archive');
    setTimeout(() => {
      setActiveIndex(null);
      setAiExpansion('');
    }, 500); 
  };

  const fetchGeminiWithBackoff = async (prompt) => {
    const apiKey = "YOUR_GEMINI_API_KEY_HERE"; // INSERT KEY HERE
    const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${apiKey}`;
    const payload = {
      contents: [{ parts: [{ text: prompt }] }],
      generationConfig: {
        temperature: 0.7,
        topK: 40,
        topP: 0.95,
        maxOutputTokens: 200,
      }
    };
    
    try {
      const response = await fetch(url, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });
      const data = await response.json();
      return data.candidates?.[0]?.content?.parts?.[0]?.text || 'The archive remains silent...';
    } catch (error) {
      return "The connection to the garden was lost.";
    }
  };

  const handleExpandMessage = async (message) => {
    setIsExpanding(true);
    const prompt = `Write a short, deeply romantic expansion (2 sentences max) for: "${message}"`;
    const response = await fetchGeminiWithBackoff(prompt);
    setAiExpansion(response);
    setIsExpanding(false);
  };

  const handleGeneratePoem = async () => {
    setIsPoemGenerating(true);
    setShowPoemModal(true);
    const prompt = `Write a 4-line romantic poem for Silavm about glowing lilies in a dark garden.`;
    const response = await fetchGeminiWithBackoff(prompt);
    setAiPoem(response);
    setIsPoemGenerating(false);
  };

  return (
    <div className="min-h-screen bg-[#050505] text-[#e8e4d9] font-serif overflow-hidden relative">
      
      {/* Background */}
      <div className="fixed inset-0 pointer-events-none z-0 opacity-20">
        <div className="absolute inset-0 bg-[radial-gradient(circle_at_center,rgba(212,175,55,0.05)_0%,transparent_70%)]" />
      </div>

      {/* Intro */}
      {view === 'intro' && (
        <div className="absolute inset-0 flex flex-col items-center justify-center z-10 p-8 text-center">
          <h1 className={`text-6xl transition-all duration-1000 ${mounted ? 'opacity-100' : 'opacity-0'}`}>
            For Silavm
          </h1>
          <button 
            onClick={() => setView('archive')}
            className="mt-12 px-8 py-4 border border-[#d4af37] text-[#d4af37] hover:bg-[#d4af37] hover:text-black transition-all"
          >
            ENTER ARCHIVE
          </button>
        </div>
      )}

      {/* Archive Grid */}
      {view === 'archive' && (
        <div className="p-8 max-w-6xl mx-auto relative z-10 h-screen overflow-y-auto">
          <div className="flex justify-between items-center mb-12 border-b border-[#1a1a1a] pb-4">
            <h2 className="tracking-widest text-xl uppercase">Garden of My Heart</h2>
            <button onClick={handleGeneratePoem} className="flex items-center gap-2 text-[#d4af37]">
               <Sparkles size={16}/> Synthesize Poem
            </button>
          </div>
          
          <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
            {messages.map((_, i) => (
              <div key={i} onClick={() => handleOpenDetail(i)} className="aspect-square border border-[#1a1a1a] flex flex-col items-center justify-center cursor-pointer hover:border-[#d4af37] transition-all group">
                <span className="text-xs text-[#555] mb-4">FIG. {toRoman(i)}</span>
                <RealisticLily className="w-24 h-24 group-hover:scale-110 transition-transform" />
              </div>
            ))}
          </div>
        </div>
      )}

      {/* Detail View */}
      {view === 'detail' && activeIndex !== null && (
        <div className="fixed inset-0 z-50 bg-[#050505] flex flex-col md:flex-row">
          <button onClick={handleCloseDetail} className="absolute top-8 right-8 text-white z-[60]"><X size={32}/></button>
          <div className="w-full md:w-1/2 flex items-center justify-center border-r border-[#1a1a1a]">
            <RealisticLily isDetail={true} className="w-64 h-64" />
          </div>
          <div className="w-full md:w-1/2 p-12 flex flex-col justify-center">
            <p className="text-4xl italic mb-8">"{messages[activeIndex]}"</p>
            <button 
              onClick={() => handleExpandMessage(messages[activeIndex])}
              className="text-[#d4af37] flex items-center gap-2 uppercase tracking-tighter"
            >
              {isExpanding ? <Loader2 className="animate-spin" /> : <Sparkles />} Extrapolate Sentiment
            </button>
            {aiExpansion && <p className="mt-6 text-lg text-[#8a8575] animate-in fade-in">{aiExpansion}</p>}
          </div>
        </div>
      )}

      {/* Poem Modal */}
      {showPoemModal && (
        <div className="fixed inset-0 bg-black/90 z-[100] flex items-center justify-center p-6">
          <div className="max-w-md text-center">
            <button onClick={() => setShowPoemModal(false)} className="mb-8 text-[#d4af37]">CLOSE</button>
            {isPoemGenerating ? <Loader2 className="animate-spin mx-auto text-[#d4af37]" /> : <p className="text-2xl italic leading-relaxed">{aiPoem}</p>}
          </div>
        </div>
      )}

      <style>{`
        .animate-bloom { animation: bloom 2s ease-out; }
        @keyframes bloom { from { opacity: 0; transform: scale(0.8); } to { opacity: 1; transform: scale(1); } }
      `}</style>
    </div>
  );
};

export default App;
