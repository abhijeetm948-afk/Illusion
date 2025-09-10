<!DOCTYPE html>
<html lang="mr">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>PyShop AI — Single File (Browser Only)</title>
<style>
  :root{--bg:#0f1720;--card:#0b1220;--muted:#9aa7b2;--accent:#2b90d9;--accent2:#e15252;--panel:#0f1a26}
  html,body{height:100%;margin:0;font-family:Inter,Segoe UI,Arial,sans-serif;background:
    linear-gradient(180deg,#071022 0%, #0f1728 100%);color:#e6eef6}
  .wrap{max-width:1200px;margin:18px auto;padding:18px}
  header{display:flex;align-items:center;justify-content:space-between;gap:12px;margin-bottom:14px}
  h1{font-size:1.3rem;margin:0;background:linear-gradient(90deg,var(--accent),var(--accent2));
      -webkit-background-clip:text;-webkit-text-fill-color:transparent}
  .sub{color:var(--muted);font-size:0.95rem}
  .layout{display:grid;grid-template-columns:300px 1fr 320px;gap:16px}
  .panel{background:rgba(255,255,255,0.03);border-radius:12px;padding:14px;box-shadow:0 6px 20px rgba(2,6,23,0.6)}
  .left .group{margin-bottom:14px}
  .tool-btn{display:block;width:100%;text-align:left;padding:10px;border-radius:8px;border:0;background:rgba(255,255,255,0.02);color:inherit;cursor:pointer;margin-bottom:8px}
  .tool-btn:hover{transform:translateX(6px)}
  .center{display:flex;flex-direction:column;align-items:center;gap:10px;padding:18px}
  .canvas-wrap{width:100%;height:600px;border-radius:10px;border:2px dashed rgba(255,255,255,0.04);display:flex;align-items:center;justify-content:center;position:relative;overflow:hidden;background:#08101a}
  canvas{max-width:100%;max-height:100%}
  .controls{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
  .btn{padding:10px 12px;border-radius:8px;border:0;background:var(--accent);color:white;cursor:pointer;font-weight:600}
  .btn.alt{background:transparent;border:1px solid rgba(255,255,255,0.06)}
  .right .section{margin-bottom:14px}
  label{display:block;font-size:0.85rem;margin-bottom:6px;color:var(--muted)}
  textarea{width:100%;height:90px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit;padding:8px}
  input[type="file"]{width:100%}
  .small{font-size:0.82rem;color:var(--muted)}
  .feature{background:rgba(255,255,255,0.02);padding:10px;border-radius:8px;margin-bottom:8px}
  footer{margin-top:14px;color:var(--muted);font-size:0.82rem;text-align:center}
  /* approval modal */
  .modal{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:linear-gradient(0deg,rgba(2,6,23,0.6),rgba(2,6,23,0.6));z-index:999}
  .modal .box{width:420px;background:linear-gradient(180deg,#06101a,#071421);padding:18px;border-radius:12px;box-shadow:0 12px 40px rgba(0,0,0,0.6)}
  .muted{color:var(--muted)}
  .ok{background:linear-gradient(90deg,var(--accent),var(--accent2));border:0;padding:10px;border-radius:8px;color:white;cursor:pointer}
  .linklike{color:#93d6ff;cursor:pointer}
  .hr{height:1px;background:rgba(255,255,255,0.03);margin:10px 0;border-radius:2px}
  .flex{display:flex;gap:8px;align-items:center}
  .drawer{max-height:420px;overflow:auto;padding:6px}
  .chip{display:inline-block;padding:6px 8px;border-radius:999px;background:rgba(255,255,255,0.03);margin-right:6px;font-size:0.82rem}
  .note{background:rgba(255,255,255,0.02);padding:10px;border-radius:8px;color:var(--muted);font-size:0.88rem}
  .right .feature h3{margin:6px 0 4px}
  .kbd{background:rgba(255,255,255,0.04);padding:4px 8px;border-radius:6px;font-weight:700}
  @media(max-width:1000px){.layout{grid-template-columns:1fr;}.center{order:3}.left{order:1}.right{order:2}.canvas-wrap{height:420px}}
</style>
</head>
<body>
<div class="wrap">
  <header>
    <div>
      <h1>PyShop AI — Browser Single File</h1>
      <div class="sub">Editor + Approval panel + demo AI actions — सर्व काही ब्राउझरमध्ये (कोणताही server नाही).</div>
    </div>
    <div class="small muted">Local demo • Save as <span class="kbd">pyshop-single.html</span> and open in browser</div>
  </header>

  <div class="layout">
    <!-- LEFT: Tools & Upload -->
    <aside class="panel left">
      <div class="group">
        <label>Upload / Image</label>
        <input id="imageUpload" type="file" accept="image/*">
        <div id="sizeNote" class="small muted">40 MB पेक्षा मोठी फाईल वापरू नका (ब्राउझर लिमिट्स).</div>
      </div>

      <div class="group">
        <label>Tools</label>
        <button class="tool-btn" onclick="selectTool('move')">Move</button>
        <button class="tool-btn" onclick="selectTool('brush')">AI Brush (demo)</button>
        <button class="tool-btn" onclick="selectTool('erase')">Erase (demo)</button>
        <button class="tool-btn" onclick="selectTool('crop')">Crop (demo)</button>
      </div>

      <div class="group">
        <label>Quick actions</label>
        <button class="tool-btn" onclick="applyGenerativeFill()">Generative Fill (placeholder)</button>
        <button class="tool-btn" onclick="applyColorize()">Colorize (B&W → color demo)</button>
        <button class="tool-btn" onclick="applySkyReplace()">Sky Replace (simple)</button>
        <button class="tool-btn" onclick="downloadImage()">Download Edited Image</button>
      </div>

      <div class="group">
        <label>About (फिचर्स)</label>
        <div class="feature"><strong>Generative Fill:</strong> टेक्स्ट-आधारित सामग्री भरण्याचे डेमो चिन्ह.</div>
        <div class="feature"><strong>Colorize:</strong> B&W इमेजवर रंग टाकण्याचा सोपा फिल्टर.</div>
        <div class="feature"><strong>Sky Replacement:</strong> वरच्या भागात साधा रंगीत आकाश भरणे.</div>
      </div>
    </aside>

    <!-- CENTER: Canvas / Editor -->
    <main class="panel center">
      <div class="canvas-wrap" id="canvasWrap">
        <canvas id="mainCanvas"></canvas>
        <div id="placeholder" style="position:absolute;color:rgba(255,255,255,0.12);font-size:18px;">इमेज अपलोड करा किंवा ड्रॅग & ड्रॉप करा</div>
      </div>

      <div class="controls">
        <button class="btn" onclick="resetCanvas()">Reset</button>
        <button class="btn alt" onclick="toggleGrid()">Toggle Grid</button>
        <button class="btn alt" onclick="fitToScreen()">Fit to Screen</button>
        <div style="flex:1"></div>
        <div class="small muted">Tool: <span id="activeTool">move</span></div>
      </div>

      <div style="width:100%;margin-top:10px" class="small muted">
        Tips: ब्रशसाठी क्लिक & ड्रॅग करा. Sky Replace ने खालील भागात आकाश रंगेल. Generative Fill हे placeholder — वास्तविक AI जोडण्यासाठी server/मॉडेल लागेल.
      </div>
    </main>

    <!-- RIGHT: AI Prompt / Approval -->
    <aside class="panel right">
      <div class="section">
        <h3>AI Prompt (प्रॉम्प्ट)</h3>
        <label>Prompt (English / Marathi)</label>
        <textarea id="aiPrompt" placeholder="उदा: 'Add a distant mountain on the right'"></textarea>
        <div class="flex" style="margin-top:8px">
          <button class="btn" onclick="runPrompt()">Run Prompt (demo)</button>
          <button class="btn alt" onclick="clearPrompt()">Clear</button>
        </div>
      </div>

      <div class="section">
        <h3>Approval / OTP (Local Demo)</h3>
        <div class="note">
          तुम्हाला server नको असल्यास खालीलपैकी एक पध्दत वापरा:
          <ul>
            <li><strong>Local OTP:</strong> Generate करा → कॉपी करा → स्वतःच्या ईमेलवर पेस्ट करा → Enter करून verify करा.</li>
            <li><strong>Server OTP:</strong> Nodemailer वापरून पाठवायचे असल्यास, Node.js backend लागेल — खाली comment मध्ये snippet आहे.</li>
          </ul>
        </div>

        <div style="margin-top:10px">
          <div class="flex">
            <button class="btn" onclick="generateLocalOTP()">Generate OTP</button>
            <button class="btn alt" onclick="copyOTP()">Copy OTP</button>
          </div>

          <div style="margin-top:8px">
            <input id="otpInput" placeholder="Enter OTP to verify" style="width:100%;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit" />
            <div style="margin-top:8px" class="flex">
              <button class="btn" onclick="verifyOTP()">Verify</button>
              <button class="btn alt" onclick="clearOTP()">Clear</button>
            </div>
            <div id="otpStatus" class="small muted" style="margin-top:8px"></div>
          </div>
        </div>
      </div>

      <div class="section">
        <h3>Helpful Notes</h3>
        <div class="feature">
          <strong>Save:</strong> क्लिक करून इमेज डाउनलोड करा. <br>
          <strong>Server OTP टिप:</strong> खालील Node.js snippet वापरा — Gmail App Password तयार करा.
        </div>

        <div class="hr"></div>
        <div class="small muted drawer">
          <strong>Node.js (nodemailer) snippet (use on your server):</strong>
<pre style="white-space:pre-wrap;font-size:12px;background:rgba(0,0,0,0.05);padding:8px;border-radius:6px;color:#cfeaff">
const nodemailer = require('nodemailer');
const transporter = nodemailer.createTransport({
  service: 'gmail',
  auth: { user: 'your@gmail.com', pass: 'YOUR_APP_PASSWORD' }
});
const otp = Math.floor(100000 + Math.random()*900000);
transporter.sendMail({
  from: 'your@gmail.com',
  to: 'user@example.com',
  subject: 'Your OTP',
  text: Your OTP is: ${otp}
});
</pre>
        </div>
      </div>
    </aside>
  </div>

  <footer>PyShop AI — Single file demo • For production OTP/email use a backend (Node.js / SendGrid / Brevo)</footer>
</div>

<script>
/* ==========================
   Main single-file JS logic
   ========================== */
const canvas = document.getElementById('mainCanvas');
const ctx = canvas.getContext('2d');
const placeholder = document.getElementById('placeholder');
let imgObj = null;
let activeTool = 'move';
let isDrawing = false;
let lastPos = null;
let gridOn = false;

/* --- init size --- */
function setCanvasSize(w,h){
  canvas.width = w;
  canvas.height = h;
  redraw();
}

/* --- redraw --- */
function redraw(){
  ctx.clearRect(0,0,canvas.width,canvas.height);
  if(imgObj){
    ctx.drawImage(imgObj,0,0,canvas.width,canvas.height);
  } else {
    // placeholder visual
  }
  if(gridOn) drawGrid();
}

/* --- grid --- */
function drawGrid(){
  ctx.save();
  ctx.strokeStyle = 'rgba(255,255,255,0.03)';
  ctx.lineWidth = 1;
  const step = 50;
  for(let x=0;x<canvas.width;x+=step){
    ctx.beginPath(); ctx.moveTo(x,0); ctx.lineTo(x,canvas.height); ctx.stroke();
  }
  for(let y=0;y<canvas.height;y+=step){
    ctx.beginPath(); ctx.moveTo(0,y); ctx.lineTo(canvas.width,y); ctx.stroke();
  }
  ctx.restore();
}

/* --- file upload / dragdrop --- */
const upload = document.getElementById('imageUpload');
upload.addEventListener('change', handleUpload);
function handleUpload(e){
  const f = e.target.files[0];
  if(!f) return;
  if(f.size > 40*1024*1024){ alert('File > 40MB — use smaller image'); return; }
  const r = new FileReader();
  r.onload = function(ev){
    const im = new Image();
    im.onload = function(){
      imgObj = im;
      // fit canvas to image (limit max width to 1000)
      const maxW = Math.min(im.width, 1000);
      const ratio = im.height/im.width;
      setCanvasSize(maxW, Math.round(maxW*ratio));
      placeholder.style.display = 'none';
    }
    im.src = ev.target.result;
  }
  r.readAsDataURL(f);
}

/* --- tools --- */
function selectTool(t){
  activeTool = t;
  document.getElementById('activeTool').textContent = t;
}
canvas.addEventListener('pointerdown', (e)=>{
  if(!imgObj) return;
  isDrawing = true;
  lastPos = getPos(e);
  if(activeTool === 'brush'){
    ctx.save();
    ctx.lineWidth = Math.max(4, Math.min(canvas.width/150, 20));
    ctx.lineCap = 'round';
    ctx.strokeStyle = '#ff7a7a';
    ctx.beginPath(); ctx.moveTo(lastPos.x,lastPos.y);
  }
});
canvas.addEventListener('pointermove', (e)=>{
  if(!isDrawing || !imgObj) return;
  const p = getPos(e);
  if(activeTool === 'brush'){
    ctx.lineTo(p.x,p.y); ctx.stroke();
  } else if(activeTool === 'erase'){
    ctx.clearRect(p.x-12,p.y-12,24,24);
  }
  lastPos = p;
});
canvas.addEventListener('pointerup', (e)=>{
  if(isDrawing && activeTool === 'brush'){ ctx.closePath(); ctx.restore(); }
  isDrawing = false;
  lastPos = null;
});
function getPos(e){
  const r = canvas.getBoundingClientRect();
  return {x: (e.clientX - r.left) * (canvas.width / r.width), y: (e.clientY - r.top) * (canvas.height / r.height)};
}

/* --- simple actions --- */
function resetCanvas(){
  if(!imgObj) return;
  const im = new Image(); im.src = imgObj.src;
  im.onload = ()=>{ imgObj = im; setCanvasSize(im.width,im.height); placeholder.style.display='none'; };
}
function fitToScreen(){
  if(!imgObj) return;
  const maxW = Math.min(900, imgObj.width);
  const ratio = imgObj.height / imgObj.width;
  setCanvasSize(maxW, Math.round(maxW*ratio));
}
function toggleGrid(){ gridOn = !gridOn; redraw(); }

/* --- Generative Fill (placeholder demo) --- */
async function applyGenerativeFill(){
  if(!imgObj){ alert('इमेज अपलोड करा.'); return; }
  alert('Generative Fill — demo: small vignette + pattern added (placeholder).');
  // placeholder effect: vignette + small pattern
  ctx.save();
  ctx.globalCompositeOperation = 'source-over';
  // subtle vignette
  const g = ctx.createRadialGradient(canvas.width/2,canvas.height/2,canvas.width*0.2,canvas.width/2,canvas.height/2,canvas.width*0.9);
  g.addColorStop(0,'rgba(0,0,0,0)');
  g.addColorStop(1,'rgba(0,0,0,0.35)');
  ctx.fillStyle = g; ctx.fillRect(0,0,canvas.width,canvas.height);
  // small decorative dot pattern
  ctx.fillStyle = 'rgba(255,255,255,0.02)';
  for(let y=20;y<canvas.height;y+=60){
    for(let x=20;x<canvas.width;x+=60){
      ctx.fillRect(x,y,3,3);
    }
  }
  ctx.restore();
}

/* --- Colorize (simple tint demo for B&W) --- */
function applyColorize(){
  if(!imgObj){ alert('इमेज अपलोड करा.'); return; }
  // sample: apply warm color overlay blending
  ctx.save();
  ctx.globalCompositeOperation = 'multiply';
  ctx.fillStyle = 'rgba(235,190,160,0.12)'; // warm tint
  ctx.fillRect(0,0,canvas.width,canvas.height);
  ctx.globalCompositeOperation = 'soft-light';
  ctx.fillStyle = 'rgba(200,220,255,0.06)';
  ctx.fillRect(0,0,canvas.width,canvas.height);
  ctx.restore();
  alert('Colorize (demo) लागू झाले.');
}

/* --- Sky Replace (simple top rectangle with gradient) --- */
function applySkyReplace(){
  if(!imgObj){ alert('इमेज अपलोड करा.'); return; }
  const h = Math.round(canvas.height * 0.33);
  // draw sky gradient at top
  const g = ctx.createLinearGradient(0,0,0,h);
  g.addColorStop(0,'#ffd6a5'); g.addColorStop(0.5,'#ffb4a2'); g.addColorStop(1,'#8ec5ff');
  ctx.save();
  ctx.globalCompositeOperation = 'source-over';
  ctx.fillStyle = g;
  ctx.fillRect(0,0,canvas.width,h);
  ctx.restore();
  alert('Sky replaced (demo).');
}

/* --- crop (simple center crop 80%) --- */
function cropCanvas(){
  if(!imgObj) return;
  const nw = Math.round(canvas.width * 0.8);
  const nh = Math.round(canvas.height * 0.8);
  const sx = Math.round((canvas.width - nw)/2);
  const sy = Math.round((canvas.height - nh)/2);
  const data = ctx.getImageData(sx,sy,nw,nh);
  canvas.width = nw; canvas.height = nh;
  ctx.putImageData(data,0,0);
}

/* connect crop tool */
function selectTool(t){
  activeTool = t;
  document.getElementById('activeTool').textContent = t;
  if(t==='crop') cropCanvas();
}

/* download */
function downloadImage(){
  if(!imgObj){ alert('Nothing to download'); return; }
  const link = document.createElement('a');
  link.download = 'pyshop-edited.png';
  link.href = canvas.toDataURL('image/png');
  link.click();
}

/* --- AI Prompt (placeholder) --- */
function runPrompt(){
  const txt = document.getElementById('aiPrompt').value.trim();
  if(!txt){ alert('Prompt भरा'); return; }
  // Demo: if prompt contains 'mountain' draw a mountain shape on right
  if(/mountain/i.test(txt)){
    ctx.save();
    ctx.fillStyle = 'rgba(20,30,40,0.6)';
    ctx.beginPath();
    ctx.moveTo(canvas.width*0.6, canvas.height*0.7);
    ctx.lineTo(canvas.width*0.75, canvas.height*0.3);
    ctx.lineTo(canvas.width*0.9, canvas.height*0.7);
    ctx.closePath(); ctx.fill();
    ctx.restore();
    alert('Prompt processed (demo): mountain added');
  } else {
    alert('Prompt processed (demo): no recognized command — it is a placeholder.');
  }
}
function clearPrompt(){ document.getElementById('aiPrompt').value=''; }

/* ============================
   LOCAL OTP (Client-only demo)
   ============================ */
let currentOTP = null;
function generateLocalOTP(){
  currentOTP = Math.floor(100000 + Math.random()*900000).toString();
  document.getElementById('otpStatus').textContent = 'OTP generated (copy & email to yourself) — ' + currentOTP;
  // For security: we do not auto-send email from browser (requires server)
}
function copyOTP(){
  if(!currentOTP){ alert('पहिले Generate करा'); return; }
  navigator.clipboard?.writeText(currentOTP).then(()=> alert('OTP कॉपी झाला — आता स्वतःच्या ईमेलवर पेस्ट करा.'));
}
function verifyOTP(){
  const v = document.getElementById('otpInput').value.trim();
  if(!currentOTP){ document.getElementById('otpStatus').textContent = 'कृपया प्रथम Generate करा.'; return; }
  if(v === currentOTP){ document.getElementById('otpStatus').textContent = 'Verified ✅ — Access granted.'; localStorage.setItem('pyshop_verified','1'); closeModalIfOpen(); }
  else { document.getElementById('otpStatus').textContent = 'चुकीचा OTP. पुन्हा तपासा.'; }
}
function clearOTP(){ currentOTP = null; document.getElementById('otpInput').value=''; document.getElementById('otpStatus').textContent=''; }

/* --- modal behaviour (simple) --- */
function closeModalIfOpen(){
  const m = document.getElementById('approvalModal');
  if(m) m.style.display = 'none';
}

/* On load: if not verified show modal */
window.addEventListener('load', ()=>{
  if(localStorage.getItem('pyshop_verified') !== '1'){
    showApprovalModal();
  } else {
    // already verified
  }
});

/* Create approval modal element */
function showApprovalModal(){
  // build DOM
  const modal = document.createElement('div'); modal.className='modal'; modal.id='approvalModal';
  modal.innerHTML = `
    <div class="box">
      <h2>PyShop Approval (Local Demo)</h2>
      <p class="muted">ही एक ब्राउझर-ओनली Approval आहे. तुम्ही खालीलपैकी वापरू शकता:</p>
      <ol>
        <li><strong>Local OTP:</strong> Generate OTP → Copy → Send to your email manually → Enter to verify.</li>
        <li><strong>Server OTP:</strong> Use Node.js snippet from the panel and run on your server (preferred for real emails).</li>
      </ol>
      <div class="hr"></div>
      <div style="display:flex;gap:8px">
        <button class="ok" onclick="generateLocalOTP();document.getElementById('otpStatus').textContent='OTP generated. Use panel on right to paste & verify.'">Generate Local OTP</button>
        <button class="linklike" onclick="document.getElementById('approvalModal').style.display='none';">Skip (demo only)</button>
      </div>
      <div class="hr"></div>
      <div class="small muted">टीप: खऱ्या ईमेलसाठी server वापरा — Nodemailer / SendGrid इत्यादी.</div>
    </div>
  `;
  document.body.appendChild(modal);
}

/* small UX: drag & drop */
const cwrap = document.getElementById('canvasWrap');
cwrap.addEventListener('dragover', e=>{ e.preventDefault(); cwrap.style.outline='2px dashed rgba(255,255,255,0.08)'; });
cwrap.addEventListener('dragleave', e=>{ cwrap.style.outline='none'; });
cwrap.addEventListener('drop', e=>{ e.preventDefault(); cwrap.style.outline='none'; const f = e.dataTransfer.files[0]; if(f){ const dt = new DataTransfer(); upload.files = f ? [f] : []; /* workaround not allowed in all browsers */ handleFileFromDrop(f); }});
function handleFileFromDrop(f){
  if(!f) return;
  if(f.size > 40*1024*1024){ alert('File too large'); return; }
  const r = new FileReader();
  r.onload = ev=>{
    const im = new Image();
    im.onload = ()=>{ imgObj = im; setCanvasSize(Math.min(im.width,1000), Math.round(Math.min(im.width,1000)*im.height/im.width)); placeholder.style.display='none'; };
    im.src = ev.target.result;
  };
  r.readAsDataURL(f);
}

</script>
</body>
</html>
