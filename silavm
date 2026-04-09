<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Ahmad's Heart</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;500;700&display=swap" rel="stylesheet">
  <style>
    :root{
      --bg1:#ffdde1; /* soft pink */
      --bg2:#c7ffd8; /* mint */
      --accent:#ff6b81;
      --glass: rgba(255,255,255,0.18);
      --card-shadow: 0 10px 30px rgba(15,15,25,0.12);
    }
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;font-family:'Poppins',system-ui,Segoe UI,Roboto,'Helvetica Neue',Arial}
    body{
      background: radial-gradient(1200px 600px at 10% 10%, rgba(255,255,255,0.35), transparent),linear-gradient(135deg,var(--bg1),var(--bg2));
      display:flex;align-items:center;justify-content:center;overflow:hidden;padding:24px;
    }

    .stage{
      width:100%;max-width:720px;height:520px;border-radius:28px;backdrop-filter: blur(6px);background:linear-gradient(180deg,rgba(255,255,255,0.14),rgba(255,255,255,0.06));box-shadow:var(--card-shadow);position:relative;overflow:hidden;padding:32px;display:flex;align-items:center;justify-content:center;flex-direction:column;
    }

    /* floating emoji layer */
    .float-layer{position:absolute;inset:0;pointer-events:none}
    .emoji{
      position:absolute;font-size:20px;opacity:0;transform:translateY(20px) scale(0.9);filter:drop-shadow(0 6px 10px rgba(0,0,0,0.08));
      will-change:transform,opacity;
    }

    /* central button */
    .heart-btn{
      appearance:none;border:0;padding:18px 36px;border-radius:999px;background:linear-gradient(90deg,var(--accent),#ff9ab8);color:white;font-weight:700;font-size:20px;cursor:pointer;box-shadow:0 8px 30px rgba(255,107,129,0.28);display:inline-flex;gap:12px;align-items:center;justify-content:center;z-index:5;backdrop-filter: blur(4px);
      transition:transform .25s ease,box-shadow .25s ease;
    }
    .heart-btn:active{transform:translateY(2px) scale(.99)}

    .heart-icon{font-size:22px;filter:drop-shadow(0 3px 6px rgba(0,0,0,0.12))}

    /* message page */
    .message{
      position:absolute;inset:24px;border-radius:18px;padding:36px;background:linear-gradient(180deg,rgba(255,255,255,0.9),rgba(255,255,255,0.82));display:flex;flex-direction:column;align-items:center;justify-content:center;gap:12px;transform:translateY(18px) scale(.98);opacity:0;pointer-events:none;transition:all .6s cubic-bezier(.2,.9,.2,1);
    }
    .stage.revealed .message{opacity:1;transform:translateY(0) scale(1);pointer-events:auto}
    .stage.revealed .center-area{opacity:0;transform:scale(.94);pointer-events:none}

    .message h1{margin:0;font-size:30px;color:#c81b5a}
    .message p{margin:0;font-size:18px;color:#222;line-height:1.5;text-align:center}

    .little-row{display:flex;gap:8px;margin-top:12px}

    .close-btn{position:absolute;right:18px;top:18px;border:0;background:transparent;font-weight:700;color:#944;cursor:pointer}

    /* small responsive tweaks */
    @media (max-width:520px){.stage{height:86vh;padding:20px}.heart-btn{font-size:18px;padding:14px 26px}}
  </style>
</head>
<body>
  <div class="stage" role="main" aria-label="Ahmad's Heart card">
    <div class="float-layer" id="floatLayer"></div>

    <div class="center-area" style="text-align:center;z-index:4">
      <button class="heart-btn" id="openBtn"><span class="heart-icon">❤️</span> Ahmad's Heart</button>
      <div style="margin-top:18px;color:#5d5d66;">Click the heart — open my message 💌</div>
    </div>

    <div class="message" id="messageCard" aria-hidden="true">
      <button class="close-btn" id="closeBtn">✕</button>
      <h1>For my Silav</h1>
      <p>I love you Silave mn, shirinakam, xatuni malakam.<br> You have no idea how much I love you — only Allah knows about it because you are there in all my duas.<br> xoda tom lo bheli.</p>
      <div class="little-row">💖 🍦 💎 ✨</div>
    </div>
  </div>

  <script>
    const emojis = ['❤️','💕','💖','🍦','🍰','💎','✨','🌸','😍','🥰'];
    const layer = document.getElementById('floatLayer');

    function spawnEmoji(){
      const el = document.createElement('span');
      el.className = 'emoji';
      el.textContent = emojis[Math.floor(Math.random()*emojis.length)];
      const size = 12 + Math.random()*30; // px
      el.style.fontSize = size + 'px';
      const left = Math.random()*100; // percent
      el.style.left = left + '%';
      el.style.top = (70 + Math.random()*30) + '%';
      layer.appendChild(el);

      // animate using Web Animations
      const translateY = - (120 + Math.random()*220);
      const rotate = (Math.random()*60 - 30);
      el.animate([
        {transform: `translateY(0px) rotate(0deg) scale(.9)`, opacity:0},
        {transform: `translateY(${translateY}px) rotate(${rotate}deg) scale(1)`, opacity:1},
        {transform: `translateY(${translateY-40}px) rotate(${rotate*1.6}deg) scale(.95)`, opacity:0}
      ],{duration:4000 + Math.random()*3000, easing:'cubic-bezier(.2,.8,.2,1)'}).onfinish = ()=> el.remove();
    }

    // spawn continuously
    setInterval(spawnEmoji, 450);

    // reveal message on click
    const openBtn = document.getElementById('openBtn');
    const stage = document.querySelector('.stage');
    const closeBtn = document.getElementById('closeBtn');

    openBtn.addEventListener('click', ()=>{
      stage.classList.add('revealed');
      document.getElementById('messageCard').setAttribute('aria-hidden','false');
    });
    closeBtn.addEventListener('click', ()=>{
      stage.classList.remove('revealed');
      document.getElementById('messageCard').setAttribute('aria-hidden','true');
    });

    // subtle floating motion for the whole stage
    let t = 0;
    function bob(){
      t += 0.01;
      stage.style.transform = `translateY(${Math.sin(t)*4}px)`;
      requestAnimationFrame(bob);
    }
    requestAnimationFrame(bob);

    // accessibility: allow Enter to activate
    openBtn.addEventListener('keyup', (e)=>{ if(e.key === 'Enter') openBtn.click(); });
  </script>
</body>
</html>
