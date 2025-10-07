<!doctype html>
<html lang="id">  
<head>  
  <meta charset="utf-8" />  
  <meta name="viewport" content="width=device-width,initial-scale=1" />  
  <title>Mateketen froms</title>  
  <style>  
    /* Reset & base */  
    * { box-sizing: border-box; margin: 0; padding: 0; }  
    html,body { height: 100%; background:#000; font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; color:#dfffd6; -webkit-font-smoothing:antialiased; -moz-osx-font-smoothing:grayscale; }  /* Canvas fills screen */  
#cmatrix {  
  position: fixed;  
  inset: 0;  
  z-index: 0;  
  display: block;  
  width: 100%;  
  height: 100%;  
  background: #000;  
}  

/* Centered content card */  
.center {  
  position: relative;  
  z-index: 2;  
  min-height: 100vh;  
  display: flex;  
  align-items: center;  
  justify-content: center;  
  padding: 3rem;  
  pointer-events: none; /* prevent accidental selection */  
}  

.card {  
  pointer-events: auto;  
  max-width: 980px;  
  width: 100%;  
  padding: 2.4rem;  
  border-radius: 18px;  
  background: linear-gradient(180deg, rgba(10,20,12,0.55) 0%, rgba(6,8,6,0.35) 100%);  
  backdrop-filter: blur(8px) saturate(1.05);  
  box-shadow: 0 10px 40px rgba(0,0,0,0.6), inset 0 1px 0 rgba(255,255,255,0.02);  
  border: 1px solid rgba(50,255,80,0.06);  
  text-align: center;  
}  

h1 {  
  font-size: clamp(1.6rem, 3.8vw, 3rem);  
  letter-spacing: .02em;  
  margin-bottom: .6rem;  
  color: #c8ffb8;  
  text-shadow: 0 6px 30px rgba(86,255,118,0.06);  
}  

p.lead {  
  font-size: clamp(1rem, 1.6vw, 1.25rem);  
  color: #bfeeb0;  
  opacity: .95;  
  line-height: 1.6;  
  margin-bottom: 1.25rem;  
}  

.signature {  
  margin-top: 1.4rem;  
  font-style: italic;  
  color: rgba(190, 238, 176, 0.9);  
  letter-spacing: 0.08em;  
}  

.small {  
  font-size: .82rem;  
  color: rgba(190,238,176,0.7);  
  margin-top: .6rem;  
}  

/* subtle floating glow */  
.glow {  
  position: absolute;  
  inset: 0;  
  z-index: 1;  
  pointer-events: none;  
  background:  
    radial-gradient(800px 300px at 10% 10%, rgba(80,255,120,0.03), transparent 8%),  
    radial-gradient(600px 240px at 90% 80%, rgba(32,180,90,0.02), transparent 8%);  
  mix-blend-mode: screen;  
}  

/* animated line under title */  
.line {  
  height: 4px;  
  width: 64px;  
  margin: 10px auto 18px;  
  border-radius: 6px;  
  background: linear-gradient(90deg, rgba(100,255,110,0.95), rgba(60,200,90,0.6));  
  animation: pulse 2.6s ease-in-out infinite;  
  box-shadow: 0 8px 24px rgba(58,220,110,0.06);  
}  
@keyframes pulse {  
  0%,100% { transform: scaleX(1); opacity: 0.9; }  
  50% { transform: scaleX(1.4); opacity: 0.6; }  
}  

footer.info {  
  position: fixed;  
  bottom: 14px;  
  left: 14px;  
  z-index: 3;  
  color: rgba(190,238,176,0.55);  
  font-size: .82rem;  
  backdrop-filter: blur(6px);  
  padding: .35rem .6rem;  
  border-radius: 8px;  
  border: 1px solid rgba(80,255,120,0.03);  
}  

/* Responsive tweaks */  
@media (max-width:640px) {  
  .card { padding: 1.6rem; border-radius: 12px; }  
  .small { font-size:.78rem; }  
}

  </style>  
</head>  
<body>  
  <canvas id="cmatrix"></canvas>    <div class="center">  
    <div class="card" role="region" aria-label="Kartu pesan generasi anonymous">  
      <h1>MATEKETEN GENERATION</h1>  
      <div class="line" aria-hidden="true"></div>  
      <p class="lead">  
        Kami adalah suara yang tak bernama, menyulam gagasan di ruang gelap.  
        Bukan untuk merusak, melainkan untuk mengingatkan: kekuatan ide tanpa wajah tetap  
        mampu menyalakan perubahan yang halus namun meluas.  
      </p>  <p class="small">  
    Ketika kata tak perlu nama, tindakan berlandaskan etikalah yang menjadi bukti.  
    Berkreasi. Berbagi. Bertanggung jawab.  
  </p>  

  <div class="signature">by administrator ┆manusia serupa jin┆iswanto pheal┆babhul</div>  
</div>

  </div>    <div class="glow" aria-hidden="true"></div>    <footer class="info" aria-hidden="true">see you next time</footer>    <script>  
    /* CMatrix-style falling characters on canvas */  
    (function () {  
      const canvas = document.getElementById('cmatrix');  
      const ctx = canvas.getContext('2d');  
      let width = canvas.width = innerWidth;  
      let height = canvas.height = innerHeight;  
  
      // character set: katakana + ASCII digits/symbols for variety  
      const katakana = Array.from('ｱｲｳｴｵｶｷｸｹｺｻｼｽｾｿﾀﾁﾂﾃﾄﾅﾆﾇﾈﾉﾊﾋﾌﾍﾎﾏﾐﾑﾒﾓﾔﾕﾖﾗﾘﾙﾚﾛﾜｦﾝ');  
      const ascii = Array.from('ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789@#$%&*+-=');  
      const charset = katakana.concat(ascii);  
  
      const fontSizeBase = Math.max(12, Math.floor(Math.min(width, height) / 64)); // responsive  
      let columns = Math.floor(width / fontSizeBase);  
      let drops = [];  
  
      function initDrops() {  
        columns = Math.floor(width / fontSizeBase);  
        drops = new Array(columns).fill(0).map(() => ({  
          y: Math.random() * -1000, // start above  
          speed: (Math.random() * 1.8 + 0.6) * (height / 900),  
          length: Math.floor(Math.random() * 18) + 6  
        }));  
      }  
      initDrops();  
  
      function draw() {  
        // translucent background for trail effect  
        ctx.fillStyle = 'rgba(0, 0, 0, 0.12)';  
        ctx.fillRect(0, 0, width, height);  
  
        ctx.font = fontSizeBase + 'px monospace';  
        for (let i = 0; i < drops.length; i++) {  
          const x = i * fontSizeBase;  
          let drop = drops[i];  
          let y = drop.y;  
  
          // draw a handful of characters per stream to create vertical variation  
          for (let k = 0; k < drop.length; k++) {  
            const char = charset[Math.floor(Math.random() * charset.length)];  
            const yy = y - k * fontSizeBase;  
  
            // fade head brighter  
            if (k === 0) {  
              ctx.fillStyle = 'rgba(196,255,180,0.95)';  
              ctx.fillText(char, x, yy);  
            } else {  
              // gradient fade  
              const opacity = Math.max(0, 0.8 - (k / drop.length) * 0.9);  
              ctx.fillStyle = 'rgba(80,220,120,' + opacity.toFixed(3) + ')';  
              ctx.fillText(char, x, yy);  
            }  
          }  
  
          drop.y += drop.speed;  
          // reset if passed bottom + random gap  
          if (drop.y > height + Math.random() * 1000) {  
            drop.y = -Math.random() * 200;  
            drop.speed = (Math.random() * 1.8 + 0.6) * (height / 900);  
            drop.length = Math.floor(Math.random() * 18) + 6;  
          }  
        }  
      }  
  
      let rafId;  
      function loop() {  
        draw();  
        rafId = requestAnimationFrame(loop);  
      }  
      loop();  
  
      // handle resize  
      function onResize() {  
        cancelAnimationFrame(rafId);  
        width = canvas.width = innerWidth;  
        height = canvas.height = innerHeight;  
        initDrops();  
        loop();  
      }  
      addEventListener('resize', onResize, { passive: true });  
  
      // subtle dimming when page not visible to save cycles  
      document.addEventListener('visibilitychange', () => {  
        if (document.hidden) cancelAnimationFrame(rafId);  
        else loop();  
      });  
    })();  
  </script>  </body>  
</html>
