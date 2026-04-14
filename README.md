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
    
    {/* Stem */}
    <path d="M100 180 Q110 140 100 80" fill="none" stroke="#2a3b22" strokeWidth="4" strokeLinecap="round" />
    
    {/* Leaves */}
    <path d="M102 150 Q130 140 140 110 Q115 135 102 145" fill="url(#leafGrad)" />
    <path d="M98 160 Q60 150 50 120 Q80 145 98 155" fill="url(#leafGrad)" />

    {/* Petals - Layered */}
    <g>
      {/* Back petals */}
      <path d="M100 80 Q140 20 170 80 Q130 90 100 80" fill="url(#lilyGrad)" opacity="0.95" />
      <path d="M100 80 Q60 20 30 80 Q70 90 100 80" fill="url(#lilyGrad)" opacity="0.95" />
      <path d="M100 80 Q100 10 130 40 Q115 70 100 80" fill="url(#lilyGrad)" opacity="0.9" />
      
      {/* Front petals */}
      <path d="M100 80 Q150 100 130 150 Q90 110 100 80" fill="url(#lilyGrad)" />
      <path d="M100 80 Q50 100 70 150 Q110 110 100 80" fill="url(#lilyGrad)" />
      <path d="M100 80 Q100 160 70 120 Q90 100 100 80" fill="url(#lilyGrad)" />
      <path d="M100 80 Q100 160 130 120 Q110 100 100 80" fill="url(#lilyGrad)" />
    </g>

    {/* Anthers */}
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
  const [view, setView] = useState('intro'); // 'intro', 'archive', 'detail'
  const [activeIndex, setActiveIndex] = useState(null);
  const [mounted, setMounted] = useState(false);
  
  // State for Gemini AI Features
  const [aiExpansion, setAiExpansion] = useState('');
  const [isExpanding, setIsExpanding] = useState(false);
  const [aiPoem, setAiPoem] = useState('');
  const [isPoemGenerating, setIsPoemGenerating] = useState(false);
  const [showPoemModal, setShowPoemModal] = useState(false);

  useEffect(() => {
    setMounted(true);
  }, []);

  const messages = [
    "Love you gyanakam",
    "Az guri te bm jwankilakam",
    "Evînê te germiya min e، Tu xewn û hêvîya min î",
    "Az hayrana cave te bm",
    "you are so cute",
    "i like your eyebrows",
    "All i need is just a paddle",
    "i became a poet for you",
    "xoda tum le nastene"
  ];

  const handleOpenDetail = (index) => {
    setActiveIndex(index);
    setAiExpansion(''); // Reset expansion
    setView('detail');
  };

  const handleCloseDetail = () => {
    setView('archive');
    setTimeout(() => {
      setActiveIndex(null);
      setAiExpansion('');
    }, 500); 
  };

  // Gemini API Logic
  const fetchGeminiWithBackoff = async (prompt) => {
    const apiKey = ""; 
    const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
    const payload = {
      contents: [{ parts: [{ text: prompt }] }],
      systemInstruction: { parts: [{ text: "You are a deeply romantic, elegant poet. Keep your responses beautiful, brief, and perfectly suited to an academic garden of love. No intro/outro text, just the verse." }] }
    };
    
    const delays = [1000, 2000, 4000, 8000, 16000];
    for (let retries = 0; retries < 5; retries++) {
      try {
        const response = await fetch(url, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload)
        });
        if (!response.ok) throw new Error('API Error');
        const data = await response.json();
        return data.candidates?.[0]?.content?.parts?.[0]?.text || '';
      } catch (error) {
        if (retries === 4) return "The archive is quiet right now. Please try again in a moment.";
        await new Promise(resolve => setTimeout(resolve, delays[retries]));
      }
    }
  };

  const handleExpandMessage = async (message) => {
    setIsExpanding(true);
    const prompt = `Write a short, deeply romantic continuation (maximum 2 sentences) expanding beautifully on this message meant for a girl named Silavm: "${message}"`;
    const response = await fetchGeminiWithBackoff(prompt);
    setAiExpansion(response);
    setIsExpanding(false);
  };

  const handleGeneratePoem = async () => {
    setIsPoemGenerating(true);
    setShowPoemModal(true);
    const prompt = `Write a deeply romantic 4-line poem for a girl named Silavm. The theme is a dark, magical botanical garden where glowing white lilies bloom to represent eternal love.`;
    const response = await fetchGeminiWithBackoff(prompt);
    setAiPoem(response);
    setIsPoemGenerating(false);
  };

  return (
    <div className="min-h-screen bg-[#050505] text-[#e8e4d9] font-sans selection:bg-[#d4af37] selection:text-black overflow-hidden relative">
      
      {/* Background Texture/Gradient */}
      <div className="fixed inset-0 pointer-events-none z-0 opacity-20">
        <div className="absolute inset-0 bg-[radial-gradient(circle_at_center,rgba(212,175,55,0.05)_0%,transparent_70%)]" />
        <div className="absolute inset-0 bg-[url('https://www.transparenttextures.com/patterns/stardust.png')] opacity-10" />
      </div>

      {/* --- INTRO VIEW --- */}
      {view === 'intro' && (
        <div className="absolute inset-0 flex flex-col items-center justify-center z-10 p-8">
          <div className={`transition-all duration-1000 delay-300 transform ${mounted ? 'translate-y-0 opacity-100' : 'translate-y-10 opacity-0'} flex flex-col items-center text-center mt-8`}>
            
            {/* The Classic Painting */}
            <div className="relative mb-10 group cursor-pointer" onClick={() => setView('archive')}>
              <div className="absolute inset-0 bg-[#d4af37] opacity-10 blur-3xl group-hover:opacity-30 transition-opacity duration-1000" />
              <div className="relative p-2 md:p-3 border border-[#1a1a1a] bg-[#050505] shadow-2xl">
                <img 
                  src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/6e/Henri_Fantin-Latour_-_White_Lilies.jpg/506px-Henri_Fantin-Latour_-_White_Lilies.jpg" 
                  alt="White Lilies" 
                  className="w-40 h-56 md:w-64 md:h-96 object-cover opacity-80 group-hover:opacity-100 transition-opacity duration-700 filter contrast-125"
                />
              </div>
            </div>
            
            <h1 className="text-5xl md:text-7xl font-cinzel font-light tracking-[0.15em] mb-4 text-white">
              For Silavm
            </h1>
            
            <p className="uppercase tracking-[0.4em] text-xs text-[#d4af37] mb-12 font-light">
              Vol. I — The Archive
            </p>

            <button 
              onClick={() => setView('archive')}
              className="group relative px-8 py-4 bg-transparent overflow-hidden border border-[#2a2a2a] hover:border-[#d4af37] transition-colors duration-500 rounded-sm"
            >
              <div className="absolute inset-0 bg-[#d4af37] translate-y-[100%] group-hover:translate-y-0 transition-transform duration-500 ease-in-out" />
              <span className="relative z-10 flex items-center gap-3 text-sm tracking-widest uppercase text-[#8a8575] group-hover:text-black transition-colors duration-500 font-medium">
                Enter Archive <ArrowRight className="w-4 h-4" />
              </span>
            </button>
          </div>
        </div>
      )}

      {/* --- ARCHIVE GRID VIEW --- */}
      <div 
        className={`absolute inset-0 z-10 flex flex-col transition-all duration-1000 ease-in-out ${view === 'archive' ? 'opacity-100 translate-y-0 pointer-events-auto' : 'opacity-0 translate-y-12 pointer-events-none'}`}
      >
        <header className="flex flex-col md:flex-row items-center justify-between p-8 border-b border-[#1a1a1a] bg-[#050505]/90 backdrop-blur-sm z-20">
          <div className="flex items-center gap-4">
            <BookOpen className="w-5 h-5 text-[#d4af37]" />
            <h2 className="font-cinzel tracking-[0.2em] text-lg text-white">GARDEN OF MY HEART</h2>
          </div>
          <div className="flex flex-col md:flex-row items-center gap-4 md:gap-8 mt-4 md:mt-0">
            <button 
              onClick={handleGeneratePoem}
              disabled={isPoemGenerating}
              className="group flex items-center gap-2 text-[#d4af37] hover:text-white transition-colors text-xs tracking-widest uppercase border border-[#d4af37]/30 hover:border-[#d4af37] px-4 py-2 rounded-sm bg-[#d4af37]/5"
            >
              {isPoemGenerating ? <Loader2 className="w-4 h-4 animate-spin" /> : <Sparkles className="w-4 h-4 group-hover:animate-pulse" />}
              Synthesize Poem
            </button>
            <p className="font-serif italic text-[#8a8575] text-sm hidden md:block">
              Catalogued Specimens: 09
            </p>
          </div>
        </header>

        <main className="flex-1 overflow-y-auto p-4 md:p-8 custom-scrollbar">
          <div className="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-3 gap-px bg-[#1a1a1a] border border-[#1a1a1a]">
            {messages.map((_, index) => (
              <div 
                key={index}
                onClick={() => handleOpenDetail(index)}
                className="group relative bg-[#050505] aspect-square flex flex-col items-center justify-center p-8 cursor-pointer overflow-hidden"
              >
                {/* Hover Glow Effect */}
                <div className="absolute inset-0 bg-[radial-gradient(circle_at_center,rgba(212,175,55,0.08)_0%,transparent_100%)] opacity-0 group-hover:opacity-100 transition-opacity duration-700" />
                
                <div className="absolute top-6 left-6 font-cinzel text-xs text-[#555] group-hover:text-[#d4af37] transition-colors duration-500">
                  FIG. {toRoman(index)}
                </div>
                <div className="absolute bottom-6 right-6 font-mono text-[10px] tracking-widest text-[#333] group-hover:text-[#666] transition-colors duration-500">
                  REF-{index + 1}00
                </div>

                <div className="w-24 h-24 transition-all duration-700 transform group-hover:scale-110">
                  <RealisticLily className="w-full h-full" />
                </div>
                
                <div className="absolute bottom-6 left-0 w-full text-center opacity-0 group-hover:opacity-100 transition-all duration-500 translate-y-4 group-hover:translate-y-0">
                  <span className="text-[10px] uppercase tracking-[0.3em] text-[#8a8575]">Examine Specimen</span>
                </div>
              </div>
            ))}
          </div>
          
          <div className="text-center mt-12 mb-8">
            <button 
              onClick={() => setView('intro')}
              className="text-[#555] hover:text-[#d4af37] text-xs tracking-[0.2em] uppercase transition-colors"
            >
              Close Archive
            </button>
          </div>
        </main>

        {/* AI Poem Modal - Positioned absolutely to overlay the Archive View properly */}
        {showPoemModal && (
          <div className="absolute inset-0 z-50 flex items-center justify-center p-4 bg-[#050505]/80 backdrop-blur-sm animate-in fade-in duration-500">
            <div className="bg-[#0a0a0a] border border-[#1a1a1a] p-8 md:p-12 max-w-lg w-full relative shadow-2xl">
              <button 
                onClick={() => setShowPoemModal(false)}
                className="absolute top-6 right-6 text-[#8a8575] hover:text-white transition-colors"
              >
                <X className="w-5 h-5" />
              </button>
              <div className="text-center">
                <Sparkles className="w-6 h-6 text-[#d4af37] mx-auto mb-6" />
                <h3 className="font-cinzel text-sm text-[#d4af37] tracking-[0.3em] uppercase mb-8 border-b border-[#1a1a1a] pb-4 inline-block">
                  Synthesized Verse
                </h3>
                {isPoemGenerating ? (
                  <div className="flex flex-col items-center justify-center py-8">
                    <Loader2 className="w-8 h-8 animate-spin text-[#d4af37] mb-4" />
                    <p className="font-serif italic text-[#8a8575]">Gathering petals of thought...</p>
                  </div>
                ) : (
                  <div className="font-serif text-xl md:text-2xl leading-loose text-white italic whitespace-pre-line animate-in fade-in zoom-in duration-700">
                    {aiPoem}
                  </div>
                )}
              </div>
            </div>
          </div>
        )}
      </div>

      {/* --- DETAIL VIEW (THE REVEAL) --- */}
      <div 
        className={`fixed inset-0 z-[60] bg-[#050505] flex transition-all duration-1000 ease-[cubic-bezier(0.22,1,0.36,1)] ${view === 'detail' ? 'opacity-100 translate-y-0 pointer-events-auto' : 'opacity-0 translate-y-[100%] pointer-events-none'}`}
      >
        {activeIndex !== null && (
          <div className="flex flex-col md:flex-row w-full h-full relative">
            
            <button 
              onClick={handleCloseDetail}
              className="absolute top-8 right-8 z-[70] text-[#8a8575] hover:text-white transition-colors"
            >
              <X className="w-8 h-8" strokeWidth={1} />
            </button>

            {/* Left Panel: The Illustration */}
            <div className="w-full md:w-1/2 h-1/2 md:h-full border-b md:border-b-0 md:border-r border-[#1a1a1a] flex flex-col items-center justify-center relative bg-[#080808]">
              <div className="absolute top-8 left-8 font-cinzel text-sm text-[#d4af37]">
                FIG. {toRoman(activeIndex)}
              </div>
              <div className="w-48 h-48 md:w-80 md:h-80">
                <RealisticLily isDetail={true} className="w-full h-full" />
              </div>
              <div className="absolute bottom-8 right-8 font-mono text-xs text-[#444] uppercase tracking-widest">
                Genus: Lilium
              </div>
            </div>

            {/* Right Panel: The Message */}
            <div className="w-full md:w-1/2 h-1/2 md:h-full flex flex-col items-center justify-center p-8 md:p-20 relative bg-gradient-to-br from-[#050505] to-[#0a0a0a] overflow-y-auto">
              
              <div className="absolute top-0 left-0 w-full h-full overflow-hidden opacity-5 pointer-events-none">
                <div className="absolute top-[-50%] left-[-50%] w-[200%] h-[200%] bg-[radial-gradient(circle_at_center,rgba(255,255,255,1)_0%,transparent_50%)] bg-[length:10px_10px]" />
              </div>

              <div className="max-w-md w-full relative z-10 animate-in fade-in slide-in-from-bottom-10 duration-1000 delay-500 my-auto">
                <h3 className="uppercase tracking-[0.3em] text-xs text-[#8a8575] mb-8 border-b border-[#1a1a1a] pb-4">
                  Recorded Extract
                </h3>
                
                <p className="font-serif text-3xl md:text-5xl leading-tight text-white mb-12 italic text-glow">
                  "{messages[activeIndex]}"
                </p>
                
                <div className="flex items-center gap-4 mb-8">
                  <div className="w-8 h-px bg-[#d4af37]" />
                  <span className="font-cinzel text-sm text-[#d4af37] tracking-widest">A.K.</span>
                </div>

                {/* AI Expansion Feature */}
                <div className="mt-8 border-t border-[#1a1a1a] pt-8 min-h-[120px]">
                  {!aiExpansion && !isExpanding && (
                    <button 
                      onClick={() => handleExpandMessage(messages[activeIndex])}
                      className="group flex items-center gap-3 text-[#8a8575] hover:text-[#d4af37] transition-colors text-xs uppercase tracking-[0.2em]"
                    >
                      <Sparkles className="w-4 h-4 group-hover:animate-pulse" />
                      Extrapolate Sentiment
                    </button>
                  )}
                  {isExpanding && (
                    <div className="flex items-center gap-3 text-[#8a8575] text-xs uppercase tracking-[0.2em] animate-pulse">
                      <Loader2 className="w-4 h-4 animate-spin text-[#d4af37]" />
                      Consulting the Archive...
                    </div>
                  )}
                  {aiExpansion && (
                    <div className="animate-in fade-in slide-in-from-top-4 duration-1000">
                      <h4 className="uppercase tracking-[0.2em] text-[10px] text-[#d4af37] mb-3 flex items-center gap-2">
                        <Sparkles className="w-3 h-3" /> Extrapolated Sentiment
                      </h4>
                      <p className="font-serif italic text-[#a39e8e] text-lg leading-relaxed">
                        {aiExpansion}
                      </p>
                    </div>
                  )}
                </div>
              </div>
            </div>

          </div>
        )}
      </div>

      <style dangerouslySetInnerHTML={{ __html: `
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;500;600&family=Cormorant+Garamond:ital,wght@0,300;0,400;1,400;1,600&family=JetBrains+Mono:wght@100;200&display=swap');
        
        body { 
          font-family: 'Cormorant Garamond', serif; 
          background-color: #050505;
        }
        
        .font-cinzel { font-family: 'Cinzel', serif; }
        .font-serif { font-family: 'Cormorant Garamond', serif; }
        .font-mono { font-family: 'JetBrains Mono', monospace; }

        /* Custom Scrollbar for the archive */
        .custom-scrollbar::-webkit-scrollbar {
          width: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
          background: #050505; 
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
          background: #1a1a1a; 
          border-radius: 10px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover {
          background: #333; 
        }

        /* Bloom Animation for Realistic Lily */
        .animate-bloom {
          animation: bloom 1.5s cubic-bezier(0.22, 1, 0.36, 1) forwards;
          transform-origin: bottom center;
        }

        @keyframes bloom {
          0% { transform: scale(0.6) translateY(20px); opacity: 0; filter: brightness(0.5); }
          100% { transform: scale(1) translateY(0); opacity: 1; filter: brightness(1); }
        }

        .text-glow {
          text-shadow: 0 0 40px rgba(255,255,255,0.1);
        }
      `}} />
    </div>
  );
};

export default App;
