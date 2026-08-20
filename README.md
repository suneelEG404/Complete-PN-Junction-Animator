<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PN Junction — Complete Animator</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;600;700&family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#0a0e14; --panel:#0f151e; --panel-2:#131b26; --line:#1d2733; --line-soft:#161e29;
    --text:#e9eef4; --text-dim:#8b97a7; --text-faint:#57667a;
    --amber:#f5a623; --blue:#4fa8ff; --violet:#a78bfa; --green:#39ff88; --red:#ff5c6c;
    --radius:10px;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    background:
      radial-gradient(1200px 600px at 15% -10%, #14202e 0%, transparent 60%),
      radial-gradient(1000px 500px at 100% 0%, #1a1420 0%, transparent 55%),
      var(--bg);
    color:var(--text); font-family:'Inter',ui-sans-serif,system-ui,sans-serif;
    -webkit-font-smoothing:antialiased; min-height:100vh;
  }
  .mono{font-family:'JetBrains Mono',ui-monospace,monospace;}
  .wrap{max-width:1080px;margin:0 auto;padding:32px 20px 80px;}

  header{margin-bottom:8px;}
  .eyebrow{font-family:'JetBrains Mono',monospace;font-size:11.5px;letter-spacing:.14em;color:var(--text-faint);
    text-transform:uppercase;display:flex;align-items:center;gap:10px;margin-bottom:14px;}
  .eyebrow .dot{width:6px;height:6px;border-radius:50%;background:var(--green);box-shadow:0 0 8px var(--green);}
  h1{font-size:clamp(26px,4vw,38px);line-height:1.05;margin:0 0 10px;font-weight:800;letter-spacing:-0.02em;}
  h1 span{color:var(--text-dim);font-weight:600;}
  .sub{color:var(--text-dim);font-size:14.5px;max-width:700px;line-height:1.55;margin:0;}

  .tabnav{
    display:flex; gap:6px; margin-top:24px; padding:5px; background:var(--panel);
    border:1px solid var(--line); border-radius:100px; flex-wrap:wrap; position:sticky; top:10px; z-index:10;
    backdrop-filter:blur(6px);
  }
  .tabbtn{
    flex:1; min-width:130px; text-align:center; font-family:'JetBrains Mono',monospace; font-size:12px; font-weight:600;
    padding:10px 14px; border-radius:100px; border:none; background:transparent; color:var(--text-faint); cursor:pointer;
    transition:background .15s, color .15s;
  }
  .tabbtn:hover{ color:var(--text-dim); }
  .tabbtn.active{ background:var(--green); color:#052e16; }
  .tabpanel{ display:none; margin-top:22px; }
  .tabpanel.active{ display:block; }

  .section-label{
    font-family:'JetBrains Mono',monospace;font-size:11px;letter-spacing:.14em;color:var(--text-faint);
    text-transform:uppercase;margin:32px 0 14px;display:flex;align-items:center;gap:10px;
  }
  .section-label:first-child{margin-top:0;}
  .section-label::after{content:"";flex:1;height:1px;background:var(--line);}

  .stage-card{background:linear-gradient(180deg,var(--panel-2),var(--panel));border:1px solid var(--line);
    border-radius:var(--radius);padding:20px 20px 16px;position:relative;overflow:hidden;}
  .stage-head{display:flex;justify-content:space-between;align-items:flex-start;gap:20px;flex-wrap:wrap;margin-bottom:6px;}
  .stage-name{font-size:19px;font-weight:700;letter-spacing:-0.01em;}
  .stage-name .num{color:var(--text-faint);font-family:'JetBrains Mono',monospace;font-weight:500;font-size:14px;margin-right:8px;}
  .stage-desc{color:var(--text-dim);font-size:13.5px;max-width:600px;line-height:1.55;margin-top:4px;}
  .badge{font-family:'JetBrains Mono',monospace;font-size:11px;letter-spacing:.06em;padding:5px 10px;border-radius:100px;
    border:1px solid var(--line);color:var(--text-faint);white-space:nowrap;}
  .badge.on{color:var(--green);border-color:#1c4d33;background:#0e2118;}
  .badge.rev-on{color:var(--violet);border-color:#3d2f5e;background:#170f24;}
  canvas.stagecanvas{width:100%;height:auto;display:block;border-radius:8px;background-color:#0b1119;margin-top:12px;}

  .controls{display:flex;align-items:center;gap:14px;flex-wrap:wrap;margin-top:18px;padding-top:16px;border-top:1px solid var(--line-soft);}
  .btn{font-family:'JetBrains Mono',monospace;font-size:12.5px;font-weight:500;background:#141d29;border:1px solid var(--line);
    color:var(--text);padding:9px 16px;border-radius:7px;cursor:pointer;display:flex;align-items:center;gap:8px;
    transition:border-color .15s, background .15s;}
  .btn:hover{border-color:#324357;background:#182333;}
  .btn.primary{background:#12341f;border-color:#1d5a35;color:#a4f5c4;}
  .btn.primary:hover{background:#164227;}
  .btn svg{width:13px;height:13px;}
  .btn.mode.active{background:#12341f;border-color:#1d5a35;color:#a4f5c4;}
  .btn.mode.rev-active{background:#3a1420;border-color:#6b2338;color:#ffb0bd;}

  .stagebar{display:flex;align-items:center;gap:6px;flex:1;min-width:220px;}
  .stagebtn{flex:1;height:6px;border-radius:4px;background:var(--line);cursor:pointer;position:relative;border:none;padding:0;transition:background .2s;}
  .stagebtn.active{background:var(--green);}
  .stagebtn.passed{background:#2e6b48;}
  .stage-ticks{display:flex;justify-content:space-between;margin-top:6px;font-family:'JetBrains Mono',monospace;font-size:9.5px;color:var(--text-faint);}

  .eqformula{margin-top:16px;padding:14px 16px;border:1px solid var(--line);border-radius:8px;background:var(--panel);
    display:flex;align-items:center;justify-content:center;gap:10px;font-family:'JetBrains Mono',monospace;font-size:14.5px;
    color:var(--text-dim);transition:color .3s, border-color .3s;flex-wrap:wrap;text-align:center;}
  .eqformula.on{color:var(--green);border-color:#1c4d33;}
  .eqformula span.big{font-size:16px;color:var(--text);}
  .eqformula.on span.big{color:var(--green);}

  .toggles{display:flex;gap:16px;align-items:center;font-family:'JetBrains Mono',monospace;font-size:12px;color:var(--text-dim);flex-wrap:wrap;margin-top:14px;}
  .toggle{display:flex;align-items:center;gap:7px;cursor:pointer;user-select:none;}
  .toggle input{accent-color:var(--green);width:14px;height:14px;cursor:pointer;}
  .swatch{width:18px;height:3px;border-radius:2px;display:inline-block;}

  .slidewrap{display:flex;align-items:center;gap:10px;flex:1;min-width:200px;}
  .slidewrap input[type="range"]{flex:1;height:4px;-webkit-appearance:none;appearance:none;background:var(--line);border-radius:4px;outline:none;}
  .slidewrap input[type="range"]::-webkit-slider-thumb{-webkit-appearance:none;width:14px;height:14px;border-radius:50%;background:var(--green);cursor:pointer;box-shadow:0 0 0 3px rgba(57,255,136,0.15);}

  .current-panel{display:grid;grid-template-columns:1fr auto 1fr;gap:14px;align-items:center;margin-top:16px;
    padding:14px 16px;border:1px solid var(--line);border-radius:8px;background:var(--panel);}
  .current-col{display:flex;flex-direction:column;gap:4px;}
  .current-col.right{align-items:flex-end;text-align:right;}
  .current-label{font-family:'JetBrains Mono',monospace;font-size:11px;letter-spacing:.06em;color:var(--text-faint);text-transform:uppercase;}
  .current-val{font-family:'JetBrains Mono',monospace;font-size:15px;font-weight:600;}
  .current-val.diff{color:var(--amber);} .current-val.drift{color:var(--violet);}
  .eq-sign{font-family:'JetBrains Mono',monospace;font-size:20px;color:var(--text-faint);text-align:center;transition:color .3s;}
  .eq-sign.balanced{color:var(--green);}
  .bar-track{height:6px;background:var(--line-soft);border-radius:4px;overflow:hidden;margin-top:2px;width:100%;}
  .bar-fill{height:100%;border-radius:4px;transition:width .12s linear;}
  .bar-fill.diff{background:var(--amber);} .bar-fill.drift{background:var(--violet);margin-left:auto;}
  @media (max-width:760px){ .current-panel{grid-template-columns:1fr;text-align:left;} .current-col.right{align-items:flex-start;text-align:left;} }

  .graphs{display:grid;grid-template-columns:repeat(3,1fr);gap:14px;margin-top:14px;}
  @media (max-width:760px){.graphs{grid-template-columns:1fr;}}
  .graph-card{background:var(--panel);border:1px solid var(--line);border-radius:8px;padding:12px 14px 10px;}
  .graph-title{font-family:'JetBrains Mono',monospace;font-size:11px;letter-spacing:.05em;color:var(--text-dim);text-transform:uppercase;margin-bottom:2px;}
  .graph-sub{font-size:11px;color:var(--text-faint);margin-bottom:8px;}
  canvas.gcanvas{width:100%;height:auto;display:block;}

  .terms-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:14px;}
  @media (max-width:900px){.terms-grid{grid-template-columns:repeat(2,1fr);}}
  @media (max-width:620px){.terms-grid{grid-template-columns:1fr;}}
  .term-card{background:linear-gradient(180deg,var(--panel-2),var(--panel));border:1px solid var(--line);
    border-radius:var(--radius);padding:14px 14px 12px;display:flex;flex-direction:column;gap:8px;}
  .term-head{display:flex;justify-content:space-between;align-items:flex-start;gap:8px;}
  .term-title{font-size:14px;font-weight:700;letter-spacing:-0.01em;}
  .term-tag{font-family:'JetBrains Mono',monospace;font-size:9.5px;padding:2px 7px;border-radius:100px;border:1px solid var(--line);color:var(--text-faint);white-space:nowrap;}
  .term-card canvas{width:100%;height:auto;display:block;border-radius:7px;background:#0b1119;border:1px solid var(--line-soft);}
  .term-def{font-size:11.5px;color:var(--text-dim);line-height:1.55;margin:0;}
  .term-def b{color:var(--text);font-weight:600;}

  .table-wrap{overflow-x:auto;border:1px solid var(--line);border-radius:var(--radius);}
  table{width:100%;border-collapse:collapse;font-size:13px;min-width:640px;}
  thead th{background:#131b26;color:var(--text-faint);font-family:'JetBrains Mono',monospace;font-size:11px;
    text-transform:uppercase;letter-spacing:.05em;text-align:left;padding:12px 14px;border-bottom:1px solid var(--line);}
  tbody td{padding:13px 14px;border-bottom:1px solid var(--line-soft);color:var(--text-dim);vertical-align:top;}
  tbody tr:last-child td{border-bottom:none;}
  tbody tr{transition:background .15s;}
  tbody tr:hover{background:#111a26;}
  tbody td:first-child{color:var(--text);font-weight:600;font-family:'JetBrains Mono',monospace;font-size:12px;}
  .cell-p{color:var(--amber);} .cell-n{color:var(--blue);} .cell-d{color:var(--violet);}

  footer{margin-top:50px;padding-top:18px;border-top:1px solid var(--line-soft);
    font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--text-faint);
    display:flex;justify-content:space-between;flex-wrap:wrap;gap:8px;}
</style>
</head>
<body>
<div class="wrap">

  <header>
    <div class="eyebrow"><span class="dot"></span>SEMICONDUCTOR DEVICE PHYSICS · COMPLETE ANIMATOR</div>
    <h1>The complete <span>P–N junction</span> animator</h1>
    <p class="sub">Every stage in one tool: how the junction forms, the six core terms behind it, what happens under forward and reverse bias, the charge/field/potential graphs, and a quick-reference summary table.</p>
  </header>

  <div class="tabnav" id="tabnav">
    <button class="tabbtn active" data-tab="formation">1 · Formation</button>
    <button class="tabbtn" data-tab="terms">2 · Core Terms</button>
    <button class="tabbtn" data-tab="bias">3 · Forward / Reverse Bias</button>
    <button class="tabbtn" data-tab="graphs">4 · Charge, Field & Potential</button>
    <button class="tabbtn" data-tab="table">5 · Summary Table</button>
  </div>

  <div class="tabpanel active" id="tab-formation">
    <div class="stage-card">
      <div class="stage-head">
        <div>
          <div class="stage-name"><span class="num" id="stageNum">1</span><span id="stageName">Concentration gradient trigger</span></div>
          <div class="stage-desc" id="stageDesc">The moment the junction forms, a steep carrier gradient exists across the boundary: high hole concentration on the P-side and high electron concentration on the N-side.</div>
        </div>
        <div class="badge" id="eqBadge">GRADIENT ONLY</div>
      </div>

      <canvas class="stagecanvas" id="stage" width="900" height="300"></canvas>

      <div class="controls">
        <button class="btn primary" id="playBtn">
          <svg viewBox="0 0 24 24" fill="currentColor" id="playIcon"><path d="M8 5v14l11-7z"/></svg>
          <span id="playLabel">Play formation</span>
        </button>
        <button class="btn" id="resetBtn">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 12a9 9 0 1 0 3-6.7M3 4v5h5"/></svg>
          Reset
        </button>
        <div class="stagebar" id="stagebar">
          <button class="stagebtn" data-p="0"></button>
          <button class="stagebtn" data-p="0.18"></button>
          <button class="stagebtn" data-p="0.36"></button>
          <button class="stagebtn" data-p="0.54"></button>
          <button class="stagebtn" data-p="0.74"></button>
          <button class="stagebtn" data-p="1"></button>
        </div>
      </div>
      <div class="stage-ticks">
        <span>1. Gradient</span><span>2. Diffusion</span><span>3. Ions exposed</span><span>4. Field builds</span><span>5. Drift opposes</span><span>6. Equilibrium</span>
      </div>

      <div class="eqformula mono" id="eqFormula">
        I<sub>net</sub> = <span class="big" id="idiffLive">I_diff</span> − <span class="big" id="idriftLive">I_drift</span> <span id="eqResult">≠ 0</span>
      </div>
    </div>
  </div>

  <div class="tabpanel" id="tab-terms">
    <div class="terms-grid">

      <div class="term-card">
        <div class="term-head"><div class="term-title">Majority &amp; Minority Carriers</div><span class="term-tag mono">carrier types</span></div>
        <canvas id="cMajMin" width="280" height="150"></canvas>
        <p class="term-def">In the <b>P-region</b>, holes are <b>majority</b> carriers and electrons are <b>minority</b>. In the <b>N-region</b>, electrons are <b>majority</b> and holes are <b>minority</b>.</p>
      </div>

      <div class="term-card">
        <div class="term-head"><div class="term-title">Immobile Ions (Dopants)</div><span class="term-tag mono">N_A⁻ / N_D⁺</span></div>
        <canvas id="cIons" width="280" height="150"></canvas>
        <p class="term-def"><b>Acceptor ions (N_A⁻):</b> trivalent atoms that gain an electron, becoming fixed negative sites. <b>Donor ions (N_D⁺):</b> pentavalent atoms that lose an electron, becoming fixed positive sites.</p>
      </div>

      <div class="term-card">
        <div class="term-head"><div class="term-title">Diffusion Current</div><span class="term-tag mono">I_diff</span></div>
        <canvas id="cDiffusion" width="280" height="150"></canvas>
        <p class="term-def">Charge transport from <b>random thermal motion</b>, pushing carriers from high concentration toward low concentration — no field required.</p>
      </div>

      <div class="term-card">
        <div class="term-head"><div class="term-title">Drift Current</div><span class="term-tag mono">I_drift</span></div>
        <canvas id="cDrift" width="280" height="150"></canvas>
        <p class="term-def">Charge transport driven by an <b>electric field</b>: positive carriers move along E, negative carriers move opposite to E.</p>
      </div>

      <div class="term-card">
        <div class="term-head"><div class="term-title">Depletion Region</div><span class="term-tag mono">space-charge</span></div>
        <canvas id="cDepletion" width="280" height="150"></canvas>
        <p class="term-def">The zone at the junction <b>depleted of mobile carriers</b> — only the fixed, ionized dopants remain, forming a net space charge.</p>
      </div>

      <div class="term-card">
        <div class="term-head"><div class="term-title">Built-in Potential</div><span class="term-tag mono">V_bi</span></div>
        <canvas id="cVbi" width="280" height="150"></canvas>
        <p class="term-def">The electrostatic potential set up by the space-charge layer — <b>≈0.7&nbsp;V for Si</b>, <b>≈0.3&nbsp;V for Ge</b> at room temperature.</p>
      </div>

    </div>
  </div>

  <div class="tabpanel" id="tab-bias">
    <div class="stage-card">
      <div class="stage-head">
        <div>
          <div class="stage-name"><span class="num" id="biasNum">●</span><span id="biasName">No external voltage — equilibrium</span></div>
          <div class="stage-desc" id="biasDesc">Diffusion current and drift current are still happening every instant — they're just equal and opposite. No battery, no net current, depletion width stays fixed.</div>
        </div>
        <div class="badge" id="biasBadge">I ≈ 0</div>
      </div>

      <canvas class="stagecanvas" id="biasStage" width="900" height="300"></canvas>

      <div class="controls">
        <div class="btn-group" style="display:flex;gap:8px;">
          <button class="btn mode active" data-mode="none">Unbiased</button>
          <button class="btn mode" data-mode="forward">Forward bias</button>
          <button class="btn mode" data-mode="reverse">Reverse bias</button>
        </div>
        <div class="slidewrap">
          <label class="mono" style="font-size:11px;color:var(--text-faint);white-space:nowrap;">BIAS&nbsp;VOLTAGE</label>
          <input type="range" id="biasSlider" min="0" max="100" value="55">
          <span class="mono" id="biasSliderVal" style="font-size:11px;color:var(--text-dim);width:34px;">55%</span>
        </div>
      </div>

      <div class="current-panel">
        <div class="current-col">
          <div class="current-label">Depletion width / barrier</div>
          <div class="current-val mono" id="biasWidthVal">medium</div>
          <div class="bar-track"><div class="bar-fill" id="biasWidthBar" style="width:50%;background:var(--violet)"></div></div>
        </div>
        <div class="eq-sign mono" id="biasArrowIcon">≈</div>
        <div class="current-col right">
          <div class="current-label">Net current</div>
          <div class="current-val mono" id="biasCurrentVal">≈ 0</div>
          <div class="bar-track"><div class="bar-fill" id="biasCurrentBar" style="width:2%;background:var(--green);margin-left:auto;"></div></div>
        </div>
      </div>
    </div>

    <div class="terms-grid" style="grid-template-columns:repeat(2,1fr);margin-top:14px;">
      <div class="term-card">
        <div class="term-head"><div class="term-title">Forward bias</div><span class="term-tag mono">+ to P, − to N</span></div>
        <p class="term-def">The battery pushes holes toward the junction from P and electrons from N. Depletion narrows, the barrier shrinks, and majority carriers pour across and recombine right at the junction — a large current, P → N.</p>
      </div>
      <div class="term-card">
        <div class="term-head"><div class="term-title">Reverse bias</div><span class="term-tag mono">+ to N, − to P</span></div>
        <p class="term-def">The battery pulls holes and electrons away from the junction. Depletion widens and the barrier grows, almost blocking majority-carrier flow. Only a tiny leakage current of minority carriers survives, N → P.</p>
      </div>
    </div>
  </div>

  <div class="tabpanel" id="tab-graphs">
    <div class="stage-card">
      <div class="stage-head">
        <div>
          <div class="stage-name">Charge density, field &amp; potential across the junction</div>
          <div class="stage-desc">Drag the depletion-width slider to see how the space-charge profile, the built-in field, and the potential all change together — exactly as they do while the junction forms or gets biased.</div>
        </div>
      </div>
      <div class="controls" style="border-top:none;padding-top:4px;margin-top:6px;">
        <div class="slidewrap">
          <label class="mono" style="font-size:11px;color:var(--text-faint);white-space:nowrap;">DEPLETION WIDTH</label>
          <input type="range" id="graphSlider" min="0" max="100" value="75">
          <span class="mono" id="graphSliderVal" style="font-size:11px;color:var(--text-dim);width:34px;">75%</span>
        </div>
      </div>
      <div class="graphs">
        <div class="graph-card">
          <div class="graph-title">Space-charge density ρ(x)</div>
          <div class="graph-sub">Charge concentration across the junction</div>
          <canvas class="gcanvas" id="gRho" width="300" height="150"></canvas>
        </div>
        <div class="graph-card">
          <div class="graph-title">Electric field E(x)</div>
          <div class="graph-sub">Built-in field, points N → P</div>
          <canvas class="gcanvas" id="gField" width="300" height="150"></canvas>
        </div>
        <div class="graph-card">
          <div class="graph-title">Potential V(x)</div>
          <div class="graph-sub">Rises to built-in potential V<sub>bi</sub></div>
          <canvas class="gcanvas" id="gPot" width="300" height="150"></canvas>
        </div>
      </div>
    </div>
  </div>

  <div class="tabpanel" id="tab-table">
    <div class="table-wrap">
      <table>
        <thead>
          <tr><th>Parameter</th><th>P-Side (Edge)</th><th>Depletion Zone</th><th>N-Side (Edge)</th></tr>
        </thead>
        <tbody>
          <tr>
            <td>Mobile Charges</td>
            <td class="cell-p">Abundant Holes (+)</td>
            <td class="cell-d">Negligible free carriers</td>
            <td class="cell-n">Abundant Electrons (−)</td>
          </tr>
          <tr>
            <td>Space Charge</td>
            <td>Neutral</td>
            <td class="cell-d">Negative (Acceptors, −qN_A) / Positive (Donors, +qN_D)</td>
            <td>Neutral</td>
          </tr>
          <tr>
            <td>Internal Potential</td>
            <td>0 V (Reference)</td>
            <td class="cell-d">Rises non-linearly</td>
            <td>High (+V_bi)</td>
          </tr>
          <tr>
            <td>Conduction State</td>
            <td class="cell-p">Conductive</td>
            <td class="cell-d">Insulating barrier</td>
            <td class="cell-n">Conductive</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  <footer>
    <span>PN Junction — Complete Animator</span>
    <span>Idealized step-junction approximation (uniform doping, abrupt depletion edges)</span>
  </footer>
</div>

<script>
(function(){
  "use strict";
  function clamp01(x){return Math.max(0,Math.min(1,x));}
  function lerp(a,b,t){return a+(b-a)*t;}
  function smoothstep(t){t=clamp01(t);return t*t*(3-2*t);}
  function seeded(seed){let s=seed;return function(){s=(s*9301+49297)%233280;return s/233280;};}

  function drawParticle(ctx,x,y,type,op,r){
    if(op<=0.02) return;
    r=r||6;
    ctx.save(); ctx.globalAlpha=op;
    ctx.beginPath(); ctx.arc(x,y,r,0,Math.PI*2);
    const col = type==='h' ? '#f5a623' : '#4fa8ff';
    ctx.fillStyle=col; ctx.shadowColor=col+'88'; ctx.shadowBlur=5; ctx.fill(); ctx.shadowBlur=0;
    ctx.fillStyle = type==='h' ? '#241505' : '#031320';
    ctx.font='bold '+Math.max(7,r*1.3)+'px JetBrains Mono, monospace';
    ctx.textAlign='center'; ctx.textBaseline='middle';
    ctx.fillText(type==='h'?'+':'\u2212', x, y+0.5);
    ctx.restore();
  }
  function drawIon(ctx,x,y,sign,color,op,r){
    op = op===undefined?1:op; r=r||7;
    ctx.save(); ctx.globalAlpha=op;
    ctx.beginPath(); ctx.arc(x,y,r,0,Math.PI*2);
    ctx.strokeStyle=color; ctx.lineWidth=1.3; ctx.stroke();
    ctx.fillStyle=color; ctx.font='10px JetBrains Mono, monospace';
    ctx.textAlign='center'; ctx.textBaseline='middle';
    ctx.fillText(sign,x,y+0.5);
    ctx.restore();
  }
  function drawArrow(ctx,x1,y1,x2,y2,color,w){
    ctx.save(); ctx.strokeStyle=color; ctx.fillStyle=color; ctx.lineWidth=w;
    ctx.beginPath(); ctx.moveTo(x1,y1); ctx.lineTo(x2,y2); ctx.stroke();
    const ang=Math.atan2(y2-y1,x2-x1), ah=5;
    ctx.beginPath(); ctx.moveTo(x2,y2);
    ctx.lineTo(x2-ah*Math.cos(ang-0.5), y2-ah*Math.sin(ang-0.5));
    ctx.lineTo(x2-ah*Math.cos(ang+0.5), y2-ah*Math.sin(ang+0.5));
    ctx.closePath(); ctx.fill(); ctx.restore();
  }

  const tabbtns = Array.from(document.querySelectorAll('.tabbtn'));
  const panels = {
    formation: document.getElementById('tab-formation'),
    terms: document.getElementById('tab-terms'),
    bias: document.getElementById('tab-bias'),
    graphs: document.getElementById('tab-graphs'),
    table: document.getElementById('tab-table')
  };
  let activeTab = 'formation';
  tabbtns.forEach(b=>{
    b.addEventListener('click', ()=>{
      activeTab = b.dataset.tab;
      tabbtns.forEach(x=>x.classList.toggle('active', x===b));
      Object.keys(panels).forEach(k=> panels[k].classList.toggle('active', k===activeTab));
    });
  });

  const cvs = document.getElementById('stage');
  const ctx = cvs.getContext('2d');
  const W = cvs.width, H = cvs.height, CX = W/2;

  const fstate = { p:0, target:0, playing:false };
  const rndF = seeded(42);
  const N_H=46, N_E=46, PAD_TOP=34, PAD_BOT=26;
  const fholes=[], felectrons=[];
  for(let i=0;i<N_H;i++) fholes.push({x0:34+rndF()*(CX-34-18), y:PAD_TOP+rndF()*(H-PAD_TOP-PAD_BOT), ph:rndF()*Math.PI*2});
  for(let i=0;i<N_E;i++) felectrons.push({x0:CX+18+rndF()*(W-18-(CX+18)-34), y:PAD_TOP+rndF()*(H-PAD_TOP-PAD_BOT), ph:rndF()*Math.PI*2});

  const STAGES = [
    {at:0.00, num:'1', name:'Concentration gradient trigger',
     desc:'The moment the junction forms, a steep carrier gradient exists across the boundary: high hole concentration on the P-side and high electron concentration on the N-side.', badge:'GRADIENT ONLY'},
    {at:0.20, num:'2', name:'Diffusion & recombination',
     desc:'Majority electrons diffuse from N into P, majority holes diffuse from P into N. Near the boundary, crossing electrons and holes recombine, neutralizing each other.', badge:'DIFFUSING'},
    {at:0.40, num:'3', name:'Exposure of fixed ions',
     desc:'As mobile carriers recombine and disappear, they unmask the stationary ionized dopants: negative acceptor ions (\u2212) on the P-side and positive donor ions (+) on the N-side.', badge:'IONS EXPOSED'},
    {at:0.58, num:'4', name:'Built-in electric field establishment',
     desc:'The unmasked charges create an internal field E directed from N to P. This field opposes further diffusion of majority carriers.', badge:'FIELD BUILDING'},
    {at:0.76, num:'5', name:'Drift opposes diffusion',
     desc:'The built-in field sweeps thermally generated minority carriers across the junction \u2014 minority holes N\u2192P, minority electrons P\u2192N \u2014 creating a drift current opposite to diffusion.', badge:'DRIFT ACTIVE'},
    {at:0.92, num:'6', name:'Dynamic thermal equilibrium',
     desc:'As the depletion width grows, the field strengthens until drift current exactly equals diffusion current. Depletion width W and built-in potential V_bi lock in place.', badge:'EQUILIBRIUM'}
  ];
  function currentStage(p){ let s=STAGES[0]; for(const st of STAGES){ if(p>=st.at) s=st; } return s; }

  const MAX_HALF_W = 92, MAX_GAP = 150;
  function computeDerived(p){
    const gapProg = smoothstep(clamp01(p/0.16));
    const gap = lerp(MAX_GAP,0,gapProg);
    const deplProg = smoothstep(clamp01((p-0.16)/0.40));
    const hw = MAX_HALF_W*deplProg;
    const fieldProg = smoothstep(clamp01((p-0.30)/0.50));
    const driftProg = smoothstep(clamp01((p-0.55)/0.35));
    const eqProg = smoothstep(clamp01((p-0.88)/0.12));
    return {gap,hw,deplProg,fieldProg,driftProg,eqProg};
  }
  function mapHoleX(x0,d){
    const halfGap=d.gap/2, origMax=CX-34-18, t=(x0-34)/origMax, newRight=CX-halfGap;
    return 34 + t*(newRight-34);
  }
  function mapElecX(x0,d){
    const halfGap=d.gap/2, origLeft=CX+18, origMax=W-18-origLeft-34, t=(x0-origLeft)/origMax, newLeft=CX+halfGap;
    return newLeft + t*((W-34)-newLeft);
  }

  function drawFormation(p, now){
    const d = computeDerived(p);
    ctx.clearRect(0,0,W,H);
    const halfGap=d.gap/2, pRight=CX-halfGap, nLeft=CX+halfGap;

    const gradP = ctx.createLinearGradient(0,0,pRight,0);
    gradP.addColorStop(0,'#20140a'); gradP.addColorStop(1,'#2a1a0d');
    ctx.fillStyle=gradP; ctx.fillRect(0,0,pRight,H);
    const gradN = ctx.createLinearGradient(nLeft,0,W,0);
    gradN.addColorStop(0,'#0d1c2a'); gradN.addColorStop(1,'#0a1420');
    ctx.fillStyle=gradN; ctx.fillRect(nLeft,0,W-nLeft,H);

    ctx.save(); ctx.strokeStyle='rgba(255,255,255,0.028)'; ctx.lineWidth=1;
    for(let gx=0;gx<W;gx+=22){ ctx.beginPath(); ctx.moveTo(gx,0); ctx.lineTo(gx,H); ctx.stroke(); }
    for(let gy=0;gy<H;gy+=22){ ctx.beginPath(); ctx.moveTo(0,gy); ctx.lineTo(W,gy); ctx.stroke(); }
    ctx.restore();

    if(d.gap<4 && d.hw>0.5){
      const zx=CX-d.hw, zw=d.hw*2;
      const gradDep = ctx.createLinearGradient(zx,0,zx+zw,0);
      gradDep.addColorStop(0,'rgba(167,139,250,0.16)'); gradDep.addColorStop(0.5,'rgba(167,139,250,0.30)'); gradDep.addColorStop(1,'rgba(167,139,250,0.16)');
      ctx.fillStyle=gradDep; ctx.fillRect(zx,0,zw,H);
      ctx.strokeStyle='rgba(167,139,250,0.55)'; ctx.setLineDash([4,4]);
      ctx.beginPath(); ctx.moveTo(zx,0); ctx.lineTo(zx,H); ctx.stroke();
      ctx.beginPath(); ctx.moveTo(zx+zw,0); ctx.lineTo(zx+zw,H); ctx.stroke();
      ctx.setLineDash([]);
      ctx.fillStyle='rgba(233,238,244,0.55)'; ctx.font='10.5px JetBrains Mono, monospace'; ctx.textAlign='center';
      ctx.fillText('DEPLETION REGION', CX, 18);
    }

    if(d.hw>2){
      const ionOpacity=d.deplProg, spacing=17;
      ctx.font='11px JetBrains Mono, monospace'; ctx.textAlign='center'; ctx.textBaseline='middle';
      for(let x=CX-8; x>=CX-d.hw+6; x-=spacing){ for(let y=46;y<H-26;y+=46){ drawIon(ctx,x,y,'-','#f5a623',ionOpacity); } }
      for(let x=CX+8; x<=CX+d.hw-6; x+=spacing){ for(let y=46;y<H-26;y+=46){ drawIon(ctx,x,y,'+','#4fa8ff',ionOpacity); } }
    }

    fholes.forEach(h=>{
      const x = mapHoleX(h.x0,d);
      const edge = CX-d.hw;
      let op = d.gap<4 ? clamp01((edge-x)/18) : 1;
      if(x>pRight-6) return;
      drawParticle(ctx, x, h.y+Math.sin(now*0.0016+h.ph)*2.4, 'h', op, 6.5);
    });
    felectrons.forEach(e=>{
      const x = mapElecX(e.x0,d);
      const edge = CX+d.hw;
      let op = d.gap<4 ? clamp01((x-edge)/18) : 1;
      if(x<nLeft+6) return;
      drawParticle(ctx, x, e.y+Math.sin(now*0.0016+e.ph+1.7)*2.4, 'e', op, 6.5);
    });

    if(d.fieldProg>0.02 && d.hw>8){
      const n=5;
      for(let i=0;i<n;i++){
        const fx = CX-d.hw + (d.hw*2)*((i+0.5)/n);
        const distC = Math.abs(fx-CX)/d.hw;
        const mag = (1-distC)*d.fieldProg;
        const len = 12+mag*20;
        const y = 40+i*((H-70)/(n-1));
        drawArrow(ctx, fx+len/2, y, fx-len/2, y, `rgba(79,168,255,${0.25+0.55*mag})`, 1.6);
      }
    }

    if(d.gap<4 && d.hw>4){
      const y1=H-40;
      if(d.deplProg>0.05){
        const op=clamp01(d.deplProg);
        ctx.save(); ctx.globalAlpha=op;
        drawArrow(ctx, CX-70, y1, CX+70, y1, `rgba(245,166,35,${0.85*op})`, 2.2);
        ctx.fillStyle='#f5a623'; ctx.font='10px JetBrains Mono, monospace'; ctx.textAlign='center';
        ctx.fillText('I_diff  P \u2192 N', CX, y1-8);
        ctx.restore();
      }
      if(d.driftProg>0.05){
        const op=clamp01(d.driftProg);
        const y2=H-16;
        ctx.save(); ctx.globalAlpha=op;
        drawArrow(ctx, CX+70, y2, CX-70, y2, `rgba(167,139,250,${0.85*op})`, 2.2);
        ctx.fillStyle='#a78bfa'; ctx.font='10px JetBrains Mono, monospace'; ctx.textAlign='center';
        ctx.fillText('I_drift  N \u2192 P', CX, y2-8);
        ctx.restore();
      }
    }

    if(d.gap<4){
      ctx.strokeStyle='rgba(255,255,255,0.12)'; ctx.setLineDash([2,3]);
      ctx.beginPath(); ctx.moveTo(CX,0); ctx.lineTo(CX,H); ctx.stroke(); ctx.setLineDash([]);
    }
    if(d.gap>6){
      ctx.fillStyle='rgba(139,151,167,0.7)'; ctx.font='11px JetBrains Mono, monospace'; ctx.textAlign='center';
      ctx.fillText('no contact yet', CX, H/2);
    }
  }

  const stageNumEl=document.getElementById('stageNum');
  const stageNameEl=document.getElementById('stageName');
  const stageDescEl=document.getElementById('stageDesc');
  const eqBadge=document.getElementById('eqBadge');
  const stagebtns=Array.from(document.querySelectorAll('#stagebar .stagebtn'));
  const eqFormula=document.getElementById('eqFormula');
  const idiffLive=document.getElementById('idiffLive');
  const idriftLive=document.getElementById('idriftLive');
  const eqResult=document.getElementById('eqResult');
  const markersF=[0,0.18,0.36,0.54,0.74,1];
  function nearestMarkerF(p){ let best=markersF[0], bd=Infinity; for(const m of markersF){ const dd=Math.abs(p-m); if(dd<bd){bd=dd;best=m;} } return best; }

  function updateFormationUI(p,d){
    const st=currentStage(p);
    stageNumEl.textContent=st.num; stageNameEl.textContent=st.name; stageDescEl.textContent=st.desc;
    eqBadge.textContent=st.badge; eqBadge.classList.toggle('on', d.eqProg>0.9);

    idiffLive.textContent = 'I_diff('+d.deplProg.toFixed(2)+')';
    idriftLive.textContent = 'I_drift('+d.driftProg.toFixed(2)+')';
    const eq = d.eqProg>0.9;
    eqResult.textContent = eq ? '= 0  \u2705 equilibrium' : '\u2260 0';
    eqFormula.classList.toggle('on', eq);

    stagebtns.forEach(b=>{
      const bp=parseFloat(b.dataset.p);
      b.classList.toggle('active', Math.abs(bp-nearestMarkerF(p))<0.001);
      b.classList.toggle('passed', p>bp+0.001);
    });
  }

  const playBtn=document.getElementById('playBtn');
  const playLabel=document.getElementById('playLabel');
  const playIcon=document.getElementById('playIcon');
  const resetBtn=document.getElementById('resetBtn');
  function setPlayUI(playing){
    playLabel.textContent = playing ? 'Pause' : (fstate.p>=0.999 ? 'Replay' : 'Play formation');
    playIcon.innerHTML = playing ? '<path d="M6 5h4v14H6zM14 5h4v14h-4z"/>' : '<path d="M8 5v14l11-7z"/>';
  }
  playBtn.addEventListener('click', ()=>{
    if(fstate.p>=0.999 && !fstate.playing){ fstate.p=0; fstate.target=0; }
    fstate.playing=!fstate.playing;
    if(fstate.playing) fstate.target=1;
    setPlayUI(fstate.playing);
  });
  resetBtn.addEventListener('click', ()=>{ fstate.playing=false; fstate.target=0; setPlayUI(false); });
  stagebtns.forEach(b=>{
    b.addEventListener('click', ()=>{ fstate.playing=false; fstate.target=parseFloat(b.dataset.p); setPlayUI(false); });
  });
  setPlayUI(false);

  const registry = [];
  function reg(id, initFn, drawFn){
    const c = document.getElementById(id);
    if(!c) return;
    const cctx = c.getContext('2d');
    const data = initFn ? initFn(c.width,c.height) : {};
    registry.push({ctx:cctx, w:c.width, h:c.height, data, drawFn});
  }

  reg('cMajMin', function(w,h){
    const rnd = seeded(1);
    const holes=[], electrons=[];
    for(let i=0;i<9;i++) holes.push({x:14+rnd()*(w/2-28), y:18+rnd()*(h-36), ph:rnd()*7});
    for(let i=0;i<9;i++) electrons.push({x:w/2+14+rnd()*(w/2-28), y:18+rnd()*(h-36), ph:rnd()*7});
    return {holes, electrons};
  }, function(cctx,w,h,t,d){
    cctx.clearRect(0,0,w,h);
    cctx.fillStyle='#20140a'; cctx.fillRect(0,0,w/2,h);
    cctx.fillStyle='#0d1c2a'; cctx.fillRect(w/2,0,w/2,h);
    cctx.strokeStyle='rgba(255,255,255,0.1)'; cctx.beginPath(); cctx.moveTo(w/2,0); cctx.lineTo(w/2,h); cctx.stroke();
    cctx.font='9px JetBrains Mono, monospace'; cctx.fillStyle='rgba(233,238,244,0.55)'; cctx.textAlign='center';
    cctx.fillText('P-region', w*0.25, 12); cctx.fillText('N-region', w*0.75, 12);
    d.holes.forEach(p=> drawParticle(cctx, p.x, p.y+Math.sin(t*0.0015+p.ph)*2, 'h', 1, 6));
    d.electrons.forEach(p=> drawParticle(cctx, p.x, p.y+Math.sin(t*0.0015+p.ph)*2, 'e', 1, 6));
    const blink = (Math.sin(t*0.0022)+1)/2;
    drawParticle(cctx, w*0.32, h*0.55, 'e', 0.25+0.75*blink, 6);
    drawParticle(cctx, w*0.68, h*0.55, 'h', 0.25+0.75*blink, 6);
    if(blink>0.6){
      cctx.fillStyle='rgba(233,238,244,0.7)'; cctx.font='8px JetBrains Mono, monospace';
      cctx.fillText('minority', w*0.32, h*0.55+16);
      cctx.fillText('minority', w*0.68, h*0.55+16);
    }
  });

  reg('cIons', function(w,h){ return {period:3200}; }, function(cctx,w,h,t,d){
    cctx.clearRect(0,0,w,h);
    cctx.fillStyle='#20140a'; cctx.fillRect(0,0,w/2,h);
    cctx.fillStyle='#0d1c2a'; cctx.fillRect(w/2,0,w/2,h);
    const cyc = (t % d.period)/d.period;
    const ax = w*0.28, ay = h*0.55;
    let capture = clamp01((cyc-0.15)/0.15);
    let approach = clamp01(cyc/0.15);
    if(cyc<0.5){
      const ex = lerp(w*0.05, ax, approach);
      if(capture<1) drawParticle(cctx, ex, ay-22, 'e', 1, 6);
      cctx.beginPath(); cctx.arc(ax,ay,9,0,Math.PI*2);
      cctx.strokeStyle = capture>=1 ? '#f5a623' : 'rgba(233,238,244,0.4)'; cctx.lineWidth=1.6; cctx.stroke();
      cctx.fillStyle = capture>=1 ? '#f5a623' : 'rgba(233,238,244,0.6)';
      cctx.font='10px JetBrains Mono, monospace'; cctx.textAlign='center'; cctx.textBaseline='middle';
      cctx.fillText(capture>=1?'\u2212':'B', ax, ay+0.5);
    } else {
      cctx.beginPath(); cctx.arc(ax,ay,9,0,Math.PI*2);
      cctx.strokeStyle='#f5a623'; cctx.lineWidth=1.6; cctx.stroke();
      cctx.fillStyle='#f5a623'; cctx.font='10px JetBrains Mono, monospace'; cctx.textAlign='center'; cctx.textBaseline='middle';
      cctx.fillText('\u2212', ax, ay+0.5);
    }
    const dx = w*0.72, dy = h*0.55;
    if(cyc<0.5){
      cctx.beginPath(); cctx.arc(dx,dy,9,0,Math.PI*2);
      cctx.strokeStyle='rgba(233,238,244,0.4)'; cctx.lineWidth=1.6; cctx.stroke();
      cctx.fillStyle='rgba(233,238,244,0.6)'; cctx.font='10px JetBrains Mono, monospace'; cctx.textAlign='center'; cctx.textBaseline='middle';
      cctx.fillText('P', dx, dy+0.5);
    } else {
      const rel = clamp01((cyc-0.5)/0.5);
      const ey = lerp(dy-4, dy-30, rel);
      const ex = lerp(dx, w*0.95, rel);
      cctx.beginPath(); cctx.arc(dx,dy,9,0,Math.PI*2);
      cctx.strokeStyle='#4fa8ff'; cctx.lineWidth=1.6; cctx.stroke();
      cctx.fillStyle='#4fa8ff'; cctx.font='10px JetBrains Mono, monospace'; cctx.textAlign='center'; cctx.textBaseline='middle';
      cctx.fillText('+', dx, dy+0.5);
      drawParticle(cctx, ex, ey, 'e', 1, 6);
    }
    cctx.font='8px JetBrains Mono, monospace'; cctx.fillStyle='rgba(233,238,244,0.5)'; cctx.textAlign='center';
    cctx.fillText('acceptor \u2192 N_A\u207B', ax, h-8);
    cctx.fillText('donor \u2192 N_D\u207A', dx, h-8);
  });

  reg('cDiffusion', function(w,h){
    const rnd = seeded(3); const parts=[];
    for(let i=0;i<26;i++) parts.push({x:w*Math.pow(rnd(),1.6), y:16+rnd()*(h-40), vy:(rnd()-0.5)*0.4});
    return {parts};
  }, function(cctx,w,h,t,d){
    cctx.clearRect(0,0,w,h);
    cctx.fillStyle='#0b1119'; cctx.fillRect(0,0,w,h);
    const grad = cctx.createLinearGradient(0,0,w,0);
    grad.addColorStop(0,'rgba(245,166,35,0.12)'); grad.addColorStop(1,'rgba(245,166,35,0.0)');
    cctx.fillStyle=grad; cctx.fillRect(0,0,w,h-24);
    d.parts.forEach(p=>{
      p.x += 0.55; p.y += p.vy;
      if(p.y<16||p.y>h-30) p.vy*=-1;
      if(p.x>w+8){ p.x=-4; p.y=16+Math.random()*(h-40); }
      drawParticle(cctx,p.x,p.y,'h',1,5.5);
    });
    drawArrow(cctx, 16, h-12, w-16, h-12, 'rgba(245,166,35,0.85)', 2);
    cctx.font='9px JetBrains Mono, monospace'; cctx.fillStyle='#f5a623'; cctx.textAlign='center';
    cctx.fillText('high concentration \u2192 low concentration', w/2, h-2);
  });

  reg('cDrift', function(w,h){
    const rnd = seeded(4); const pos=[], neg=[];
    for(let i=0;i<7;i++) pos.push({x:rnd()*w, y:26+rnd()*(h-70)});
    for(let i=0;i<7;i++) neg.push({x:rnd()*w, y:26+rnd()*(h-70)});
    return {pos,neg};
  }, function(cctx,w,h,t,d){
    cctx.clearRect(0,0,w,h);
    cctx.fillStyle='#0b1119'; cctx.fillRect(0,0,w,h);
    cctx.font='9px JetBrains Mono, monospace'; cctx.fillStyle='rgba(79,168,255,0.8)'; cctx.textAlign='center';
    cctx.fillText('E field (N \u2192 P)', w/2, 12);
    for(let i=0;i<4;i++){
      const y = 22 + i*((h-70)/3);
      drawArrow(cctx, w-14, y, 14, y, 'rgba(79,168,255,0.55)', 1.4);
    }
    d.pos.forEach(p=>{ p.x -= 0.9; if(p.x<-8) p.x=w+6; drawParticle(cctx,p.x,p.y,'h',1,5.5); });
    d.neg.forEach(p=>{ p.x += 0.9; if(p.x>w+8) p.x=-6; drawParticle(cctx,p.x,p.y,'e',1,5.5); });
    cctx.font='8.5px JetBrains Mono, monospace'; cctx.fillStyle='rgba(233,238,244,0.55)'; cctx.textAlign='center';
    cctx.fillText('+ drifts with E, \u2212 drifts against E', w/2, h-6);
  });

  reg('cDepletion', function(w,h){ return {}; }, function(cctx,w,h,t,d){
    cctx.clearRect(0,0,w,h);
    cctx.fillStyle='#160e08'; cctx.fillRect(0,0,w*0.25,h);
    cctx.fillStyle='#0a121b'; cctx.fillRect(w*0.75,0,w*0.25,h);
    const zx=w*0.25, zw=w*0.5;
    const shimmer = 0.75 + 0.25*Math.sin(t*0.0025);
    const grad = cctx.createLinearGradient(zx,0,zx+zw,0);
    grad.addColorStop(0,'rgba(167,139,250,0.10)'); grad.addColorStop(0.5,'rgba(167,139,250,0.24)'); grad.addColorStop(1,'rgba(167,139,250,0.10)');
    cctx.fillStyle=grad; cctx.fillRect(zx,0,zw,h);
    cctx.strokeStyle='rgba(167,139,250,0.55)'; cctx.setLineDash([3,3]);
    cctx.beginPath(); cctx.moveTo(zx,0); cctx.lineTo(zx,h); cctx.moveTo(zx+zw,0); cctx.lineTo(zx+zw,h); cctx.stroke();
    cctx.setLineDash([]);
    for(let x=zx+10; x<w/2-4; x+=17){ for(let y=30;y<h-22;y+=34){ drawIon(cctx,x,y,'\u2212','#f5a623',shimmer,6); } }
    for(let x=w/2+10; x<zx+zw-4; x+=17){ for(let y=30;y<h-22;y+=34){ drawIon(cctx,x,y,'+','#4fa8ff',shimmer,6); } }
    drawParticle(cctx, w*0.10, h*0.6, 'h', 0.9, 6);
    drawParticle(cctx, w*0.90, h*0.6, 'e', 0.9, 6);
    cctx.font='9px JetBrains Mono, monospace'; cctx.fillStyle='rgba(233,238,244,0.6)'; cctx.textAlign='center';
    cctx.fillText('no free carriers here', w/2, 14);
  });

  reg('cVbi', function(w,h){ return {}; }, function(cctx,w,h,t,d){
    cctx.clearRect(0,0,w,h);
    cctx.fillStyle='#0b1119'; cctx.fillRect(0,0,w,h);
    const pad=18, x0=pad, x1=w-pad, y0=h-24, vbi=h-56;
    cctx.strokeStyle='#1d2733'; cctx.lineWidth=1;
    cctx.beginPath(); cctx.moveTo(x0,y0); cctx.lineTo(x1,y0); cctx.stroke();
    cctx.beginPath(); cctx.moveTo(x0,14); cctx.lineTo(x0,y0); cctx.stroke();
    cctx.beginPath();
    const steps=40;
    for(let i=0;i<=steps;i++){
      const xx = x0 + (x1-x0)*(i/steps);
      const yy = y0 - vbi*smoothstep(i/steps);
      if(i===0) cctx.moveTo(xx,yy); else cctx.lineTo(xx,yy);
    }
    cctx.strokeStyle='#39ff88'; cctx.lineWidth=2; cctx.stroke();
    cctx.strokeStyle='rgba(57,255,136,0.35)'; cctx.setLineDash([3,3]);
    cctx.beginPath(); cctx.moveTo(x0,y0-vbi); cctx.lineTo(x1,y0-vbi); cctx.stroke(); cctx.setLineDash([]);
    const cyc = (t%2600)/2600;
    const dx = x0 + (x1-x0)*cyc;
    const dy = y0 - vbi*smoothstep(cyc);
    cctx.beginPath(); cctx.arc(dx,dy,4,0,Math.PI*2); cctx.fillStyle='#39ff88'; cctx.shadowColor='#39ff88'; cctx.shadowBlur=6; cctx.fill(); cctx.shadowBlur=0;
    cctx.font='9px JetBrains Mono, monospace'; cctx.fillStyle='#39ff88'; cctx.textAlign='left';
    cctx.fillText('V_bi \u2248 0.7 V (Si)', x0+4, y0-vbi-6);
    cctx.fillStyle='rgba(233,238,244,0.45)'; cctx.fillText('P', x0, y0+14);
    cctx.textAlign='right'; cctx.fillText('N', x1, y0+14);
  });

  const bcvs = document.getElementById('biasStage');
  const bctx = bcvs.getContext('2d');
  const BW = bcvs.width, BH = bcvs.height, BCX = BW/2;

  const bstate = { mode:'none', bias:0.55 };
  const rndB = seeded(7);

  const N_MAJ = 16;
  const bholes = [], belectrons = [];
  for(let i=0;i<N_MAJ;i++){
    bholes.push({ x: 30 + rndB()*(BCX-60), y: 30+rndB()*(BH-60) });
    belectrons.push({ x: BCX+30 + rndB()*(BCX-60), y: 30+rndB()*(BH-60) });
  }
  const N_MIN = 4;
  const minElecInP = [], minHoleInN = [];
  for(let i=0;i<N_MIN;i++){
    minElecInP.push({ x: 40+rndB()*(BCX-80), y: 30+rndB()*(BH-60) });
    minHoleInN.push({ x: BCX+40+rndB()*(BCX-80), y: 30+rndB()*(BH-60) });
  }
  const bsparks = [];

  function depletionHalfWidth(){
    if(bstate.mode==='forward') return lerp(60, 14, bstate.bias);
    if(bstate.mode==='reverse') return lerp(60, 150, bstate.bias);
    return 60;
  }

  function drawIonBand(hw){
    if(hw<2) return;
    const zx = BCX-hw, zw = hw*2;
    const grad = bctx.createLinearGradient(zx,0,zx+zw,0);
    grad.addColorStop(0,'rgba(167,139,250,0.14)'); grad.addColorStop(0.5,'rgba(167,139,250,0.28)'); grad.addColorStop(1,'rgba(167,139,250,0.14)');
    bctx.fillStyle = grad; bctx.fillRect(zx,0,zw,BH);
    bctx.strokeStyle='rgba(167,139,250,0.5)'; bctx.setLineDash([4,4]);
    bctx.beginPath(); bctx.moveTo(zx,0); bctx.lineTo(zx,BH); bctx.stroke();
    bctx.beginPath(); bctx.moveTo(zx+zw,0); bctx.lineTo(zx+zw,BH); bctx.stroke();
    bctx.setLineDash([]);
    const spacing=17;
    for(let x=BCX-8; x>=zx+6; x-=spacing){ for(let y=44;y<BH-24;y+=54){ drawIon(bctx,x,y,'-','#f5a623'); } }
    for(let x=BCX+8; x<=zx+zw-6; x+=spacing){ for(let y=44;y<BH-24;y+=54){ drawIon(bctx,x,y,'+','#4fa8ff'); } }
  }

  function drawBattery(){
    if(bstate.mode==='none') return;
    const wireY = 4;
    const fwd = bstate.mode==='forward';
    bctx.save();
    bctx.strokeStyle = 'rgba(233,238,244,0.35)'; bctx.lineWidth = 1.6;
    bctx.beginPath(); bctx.moveTo(BCX-140, 26); bctx.lineTo(BCX-140, wireY); bctx.lineTo(BCX-18, wireY); bctx.stroke();
    bctx.beginPath(); bctx.moveTo(BCX+140, 26); bctx.lineTo(BCX+140, wireY); bctx.lineTo(BCX+18, wireY); bctx.stroke();
    bctx.strokeStyle = 'rgba(233,238,244,0.7)'; bctx.lineWidth = 3;
    bctx.beginPath(); bctx.moveTo(BCX-18,wireY-9); bctx.lineTo(BCX-18,wireY+9); bctx.stroke();
    bctx.lineWidth = 1.6;
    bctx.beginPath(); bctx.moveTo(BCX+18,wireY-5); bctx.lineTo(BCX+18,wireY+5); bctx.stroke();
    bctx.fillStyle = fwd ? '#f5a623' : '#a78bfa';
    bctx.font='11px JetBrains Mono, monospace'; bctx.textAlign='center';
    bctx.fillText(fwd ? '+' : '\u2212', BCX-30, wireY+4);
    bctx.fillText(fwd ? '\u2212' : '+', BCX+30, wireY+4);
    bctx.fillStyle='rgba(233,238,244,0.5)'; bctx.fillText('V', BCX, wireY-14);
    bctx.beginPath(); bctx.arc(BCX-140,26,2.5,0,Math.PI*2); bctx.fill();
    bctx.beginPath(); bctx.arc(BCX+140,26,2.5,0,Math.PI*2); bctx.fill();
    bctx.restore();
  }

  function updateBiasParticles(dt){
    const hw = depletionHalfWidth();
    const b = bstate.bias;
    if(bstate.mode==='forward'){
      const speed = lerp(0.35, 2.6, b) * dt;
      bholes.forEach(h=>{
        h.x += speed;
        if(h.x > BCX + Math.max(18, hw*0.35)){
          bsparks.push({x:h.x,y:h.y,life:1,color:'#ffd27a'});
          h.x = 20 + Math.random()*40; h.y = 30+Math.random()*(BH-60);
        }
      });
      belectrons.forEach(e=>{
        e.x -= speed;
        if(e.x < BCX - Math.max(18, hw*0.35)){
          bsparks.push({x:e.x,y:e.y,life:1,color:'#9fd6ff'});
          e.x = BW-20 - Math.random()*40; e.y = 30+Math.random()*(BH-60);
        }
      });
    } else if(bstate.mode==='reverse'){
      bholes.forEach(h=>{ const target = Math.min(h.x, BCX-hw-14); h.x += (target-h.x)*0.02; });
      belectrons.forEach(e=>{ const target = Math.max(e.x, BCX+hw+14); e.x += (target-e.x)*0.02; });
      const mspeed = lerp(0.12, 0.55, b) * dt;
      minElecInP.forEach(m=>{
        m.x += mspeed;
        if(m.x > BCX+30){ bsparks.push({x:m.x,y:m.y,life:1,color:'#9fd6ff'}); m.x = 40+Math.random()*(BCX-80); m.y = 30+Math.random()*(BH-60); }
      });
      minHoleInN.forEach(m=>{
        m.x -= mspeed;
        if(m.x < BCX-30){ bsparks.push({x:m.x,y:m.y,life:1,color:'#ffd27a'}); m.x = BCX+40+Math.random()*(BCX-80); m.y = 30+Math.random()*(BH-60); }
      });
    } else {
      if(Math.random()<0.02) bsparks.push({x:BCX-hw+6,y:40+Math.random()*(BH-80),life:1,color:'#ffd27a'});
      if(Math.random()<0.02) bsparks.push({x:BCX+hw-6,y:40+Math.random()*(BH-80),life:1,color:'#9fd6ff'});
    }
    for(let i=bsparks.length-1;i>=0;i--){
      bsparks[i].life -= dt*0.03;
      if(bsparks[i].life<=0) bsparks.splice(i,1);
    }
  }

  let biasLastNow = null;
  function drawBias(now){
    const dt = Math.min(2.2, biasLastNow ? (now-biasLastNow)/16.7 : 1);
    biasLastNow = now;
    const hw = depletionHalfWidth();

    bctx.clearRect(0,0,BW,BH);
    const gradP = bctx.createLinearGradient(0,0,BCX,0);
    gradP.addColorStop(0,'#20140a'); gradP.addColorStop(1,'#2a1a0d');
    bctx.fillStyle=gradP; bctx.fillRect(0,0,BCX,BH);
    const gradN = bctx.createLinearGradient(BCX,0,BW,0);
    gradN.addColorStop(0,'#0d1c2a'); gradN.addColorStop(1,'#0a1420');
    bctx.fillStyle=gradN; bctx.fillRect(BCX,0,BW-BCX,BH);

    bctx.save(); bctx.strokeStyle='rgba(255,255,255,0.028)'; bctx.lineWidth=1;
    for(let gx=0;gx<BW;gx+=22){ bctx.beginPath(); bctx.moveTo(gx,0); bctx.lineTo(gx,BH); bctx.stroke(); }
    for(let gy=0;gy<BH;gy+=22){ bctx.beginPath(); bctx.moveTo(0,gy); bctx.lineTo(BW,gy); bctx.stroke(); }
    bctx.restore();

    drawIonBand(hw);
    updateBiasParticles(dt||1);

    bholes.forEach(h=> drawParticle(bctx,h.x,h.y,'h',1,6.5));
    belectrons.forEach(e=> drawParticle(bctx,e.x,e.y,'e',1,6.5));
    if(bstate.mode==='reverse'){
      minElecInP.forEach(m=> drawParticle(bctx,m.x,m.y,'e', 0.85,6.5));
      minHoleInN.forEach(m=> drawParticle(bctx,m.x,m.y,'h', 0.85,6.5));
    }

    bsparks.forEach(s=>{
      bctx.save(); bctx.globalAlpha = clamp01(s.life);
      bctx.beginPath(); bctx.arc(s.x, s.y, 10*(1-s.life)+3, 0, Math.PI*2);
      bctx.strokeStyle = s.color; bctx.lineWidth = 1.6; bctx.stroke();
      bctx.restore();
    });

    bctx.strokeStyle='rgba(255,255,255,0.12)'; bctx.setLineDash([2,3]);
    bctx.beginPath(); bctx.moveTo(BCX,0); bctx.lineTo(BCX,BH); bctx.stroke(); bctx.setLineDash([]);

    drawBattery();

    bctx.font='10.5px JetBrains Mono, monospace'; bctx.textAlign='center';
    if(bstate.mode==='forward'){
      drawArrow(bctx, BCX-90,BH-14,BCX+90,BH-14,'rgba(245,166,35,0.9)',2.6);
      bctx.fillStyle='#f5a623'; bctx.fillText('I_F  (P \u2192 N, large)', BCX, BH-22);
    } else if(bstate.mode==='reverse'){
      drawArrow(bctx, BCX+70,BH-14,BCX-70,BH-14,'rgba(167,139,250,0.85)',1.6);
      bctx.fillStyle='#a78bfa'; bctx.fillText('I_R  (N \u2192 P, tiny leakage)', BCX, BH-22);
    } else {
      drawArrow(bctx, BCX-40,BH-14,BCX+8,BH-14,'rgba(245,166,35,0.55)',1.4);
      drawArrow(bctx, BCX+40,BH-14,BCX-8,BH-14,'rgba(167,139,250,0.55)',1.4);
      bctx.fillStyle='#8b97a7'; bctx.fillText('I_diff \u2248 I_drift  (balanced)', BCX, BH-22);
    }

    updateBiasUI(hw);
  }

  const bNameEl = document.getElementById('biasName');
  const bDescEl = document.getElementById('biasDesc');
  const bBadgeEl = document.getElementById('biasBadge');
  const bWidthVal = document.getElementById('biasWidthVal');
  const bWidthBar = document.getElementById('biasWidthBar');
  const bCurVal = document.getElementById('biasCurrentVal');
  const bCurBar = document.getElementById('biasCurrentBar');
  const bArrowIcon = document.getElementById('biasArrowIcon');
  const bSliderVal = document.getElementById('biasSliderVal');

  const BIAS_COPY = {
    none: { name:'No external voltage — equilibrium',
      desc:"Diffusion current and drift current are still happening every instant — they're just equal and opposite. No battery, no net current, depletion width stays fixed.",
      badge:'I ≈ 0' },
    forward: { name:'Forward bias — barrier lowered',
      desc:"The battery's positive terminal connects to P, negative to N. This pushes majority carriers toward the junction, narrows the depletion region, lowers the barrier, and lets a large current flow P → N.",
      badge:'I_F ↑ (mA range)' },
    reverse: { name:'Reverse bias — barrier raised',
      desc:"The positive terminal connects to N, negative to P. Majority carriers are pulled away from the junction, widening the depletion region and raising the barrier. Only a tiny drift current of minority carriers leaks through, N → P.",
      badge:'I_R ≈ 0 (µA leakage)' }
  };

  function updateBiasUI(hw){
    const c = BIAS_COPY[bstate.mode];
    bNameEl.textContent = c.name; bDescEl.textContent = c.desc; bBadgeEl.textContent = c.badge;
    bBadgeEl.classList.remove('on','rev-on');
    if(bstate.mode==='forward') bBadgeEl.classList.add('on');
    if(bstate.mode==='reverse') bBadgeEl.classList.add('rev-on');

    const widthPct = clamp01(hw/150);
    bWidthBar.style.width = (widthPct*100).toFixed(0)+'%';
    bWidthVal.textContent = bstate.mode==='forward' ? 'narrow ('+hw.toFixed(0)+'px)'
                          : bstate.mode==='reverse' ? 'wide ('+hw.toFixed(0)+'px)'
                          : 'medium ('+hw.toFixed(0)+'px)';
    bWidthBar.style.background = bstate.mode==='forward' ? 'var(--amber)' : bstate.mode==='reverse' ? 'var(--violet)' : 'var(--blue)';

    let curPct, curLabel, arrow;
    if(bstate.mode==='forward'){ curPct = lerp(4,96,bstate.bias); curLabel = curPct.toFixed(0)+'% of max (mA range)'; arrow='→'; }
    else if(bstate.mode==='reverse'){ curPct = lerp(1,4,bstate.bias); curLabel = '≈ '+curPct.toFixed(1)+'% (µA / nA leakage)'; arrow='←'; }
    else { curPct = 0; curLabel = '0 (I_diff = I_drift)'; arrow='='; }
    bCurBar.style.width = curPct.toFixed(0)+'%';
    bCurVal.textContent = curLabel;
    bArrowIcon.textContent = arrow;
    bArrowIcon.classList.toggle('balanced', bstate.mode==='none');
  }

  document.querySelectorAll('#tab-bias .mode').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      document.querySelectorAll('#tab-bias .mode').forEach(b=>b.classList.remove('active','rev-active'));
      bstate.mode = btn.dataset.mode;
      if(bstate.mode==='forward') btn.classList.add('active');
      else if(bstate.mode==='reverse') btn.classList.add('rev-active');
      else btn.classList.add('active');
    });
  });
  const biasSlider = document.getElementById('biasSlider');
  biasSlider.addEventListener('input', ()=>{
    bstate.bias = biasSlider.value/100;
    bSliderVal.textContent = biasSlider.value+'%';
  });

  const gRho = document.getElementById('gRho').getContext('2d');
  const gField = document.getElementById('gField').getContext('2d');
  const gPot = document.getElementById('gPot').getContext('2d');
  const GW=300, GH=150, GPAD=26;
  const MAX_HALF_GRAPH = 100;
  const graphSlider = document.getElementById('graphSlider');
  const graphSliderVal = document.getElementById('graphSliderVal');
  let graphW = 0.75;
  graphSlider.addEventListener('input', ()=>{
    graphW = graphSlider.value/100;
    graphSliderVal.textContent = graphSlider.value+'%';
  });

  function graphAxes(g){
    g.clearRect(0,0,GW,GH);
    g.strokeStyle = '#1d2733'; g.lineWidth = 1;
    g.beginPath(); g.moveTo(GPAD, GH/2); g.lineTo(GW-10, GH/2); g.stroke();
    g.beginPath(); g.moveTo(GPAD, 14); g.lineTo(GPAD, GH-14); g.stroke();
    g.fillStyle = '#57667a'; g.font = '9px JetBrains Mono, monospace'; g.textAlign='left';
    g.fillText('x', GW-16, GH/2-6);
  }

  function drawGraphs(){
    const deplProg = graphW;
    const cx = GPAD + (GW-GPAD-10)/2;
    const scaleX = (GW-GPAD-10)/2 / MAX_HALF_GRAPH;
    const hwpx = (MAX_HALF_GRAPH*deplProg)*scaleX;

    graphAxes(gRho);
    const amp = 42*deplProg;
    if(hwpx>1){
      gRho.fillStyle = 'rgba(245,166,35,0.8)'; gRho.fillRect(cx-hwpx, GH/2, hwpx, amp);
      gRho.fillStyle = 'rgba(79,168,255,0.8)'; gRho.fillRect(cx, GH/2-amp, hwpx, amp);
    }
    gRho.fillStyle = '#8b97a7'; gRho.font = '9px JetBrains Mono, monospace'; gRho.textAlign='center';
    gRho.fillText('−qNa', cx-hwpx/2 - 6, GH/2+amp+12);
    gRho.fillText('+qNd', cx+hwpx/2 + 6, GH/2-amp-6);

    graphAxes(gField);
    const peak = 52*deplProg;
    if(hwpx>1){
      gField.beginPath();
      gField.moveTo(cx-hwpx, GH/2); gField.lineTo(cx, GH/2+peak); gField.lineTo(cx+hwpx, GH/2); gField.closePath();
      gField.fillStyle = 'rgba(79,168,255,0.22)'; gField.fill();
      gField.strokeStyle = '#4fa8ff'; gField.lineWidth = 1.8;
      gField.beginPath(); gField.moveTo(cx-hwpx, GH/2); gField.lineTo(cx, GH/2+peak); gField.lineTo(cx+hwpx, GH/2); gField.stroke();
    }
    gField.fillStyle = '#57667a'; gField.font = '9px JetBrains Mono, monospace'; gField.textAlign='center';
    gField.fillText('E max (N→P)', cx, Math.min(GH-6, GH/2+peak+12));

    graphAxes(gPot);
    const vbi = 50*deplProg;
    gPot.beginPath();
    const steps = 40;
    for(let i=0;i<=steps;i++){
      const x = cx - hwpx + (2*hwpx)*(i/steps);
      const y = GH/2+18 - vbi*smoothstep(i/steps);
      if(i===0) gPot.moveTo(x,y); else gPot.lineTo(x,y);
    }
    gPot.strokeStyle = '#39ff88'; gPot.lineWidth = 2; gPot.stroke();
    gPot.strokeStyle = 'rgba(57,255,136,0.4)'; gPot.setLineDash([3,3]);
    gPot.beginPath(); gPot.moveTo(GPAD, GH/2+18); gPot.lineTo(cx-hwpx, GH/2+18); gPot.stroke();
    gPot.beginPath(); gPot.moveTo(cx+hwpx, GH/2+18-vbi); gPot.lineTo(GW-10, GH/2+18-vbi); gPot.stroke();
    gPot.setLineDash([]);
    gPot.fillStyle = '#57667a'; gPot.font = '9px JetBrains Mono, monospace'; gPot.textAlign='left';
    gPot.fillText('V_bi', GW-30, Math.max(12, GH/2+18-vbi-4));
  }

  function frame(now){
    if(activeTab==='formation'){
      const speed = fstate.playing ? 0.0035 : 0.006;
      if(fstate.p<fstate.target) fstate.p=Math.min(fstate.target, fstate.p+speed*16.7);
      else if(fstate.p>fstate.target) fstate.p=Math.max(fstate.target, fstate.p-speed*16.7);
      if(fstate.playing){
        fstate.target = Math.min(1, fstate.target + 0.00018*16.7*0.2);
        fstate.p = fstate.target;
        if(fstate.p>=1){ fstate.playing=false; setPlayUI(false); }
      }
      const d = computeDerived(fstate.p);
      drawFormation(fstate.p, now);
      updateFormationUI(fstate.p, d);
    } else if(activeTab==='terms'){
      registry.forEach(r=> r.drawFn(r.ctx, r.w, r.h, now, r.data));
    } else if(activeTab==='bias'){
      drawBias(now);
    } else if(activeTab==='graphs'){
      drawGraphs();
    }
    requestAnimationFrame(frame);
  }
  requestAnimationFrame(frame);
})();
</script>
</body>
</html>
