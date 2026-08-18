<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Complete PN Junction Animator</title>
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
  html{scroll-behavior:smooth;}
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
  .wrap{max-width:1080px;margin:0 auto;padding:36px 20px 80px;}

  header{margin-bottom:8px;}
  .eyebrow{font-family:'JetBrains Mono',monospace;font-size:11.5px;letter-spacing:.14em;color:var(--text-faint);
    text-transform:uppercase;display:flex;align-items:center;gap:10px;margin-bottom:14px;}
  .eyebrow .dot{width:6px;height:6px;border-radius:50%;background:var(--green);box-shadow:0 0 8px var(--green);}
  h1{font-size:clamp(26px,4vw,40px);line-height:1.05;margin:0 0 10px;font-weight:800;letter-spacing:-0.02em;}
  h1 span{color:var(--text-dim);font-weight:600;}
  .sub{color:var(--text-dim);font-size:15px;max-width:700px;line-height:1.55;margin:0 0 18px;}

  /* quick nav */
  .quicknav{display:flex;flex-wrap:wrap;gap:8px;margin-top:6px;}
  .quicknav a{
    font-family:'JetBrains Mono',monospace;font-size:11.5px;color:var(--text-dim);text-decoration:none;
    padding:7px 13px;border:1px solid var(--line);border-radius:100px;background:var(--panel);transition:all .15s;
  }
  .quicknav a:hover{border-color:#324357;color:var(--text);background:#141d29;}

  .section-label{
    font-family:'JetBrains Mono',monospace;font-size:11.5px;letter-spacing:.14em;color:var(--text-faint);
    text-transform:uppercase;margin:46px 0 14px;display:flex;align-items:center;gap:10px;scroll-margin-top:20px;
  }
  .section-label::after{content:"";flex:1;height:1px;background:var(--line);}
  .section-sub{color:var(--text-dim);font-size:13.5px;margin:-8px 0 14px;max-width:680px;line-height:1.5;}

  /* ---------- term cards ---------- */
  .terms-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:14px;}
  @media (max-width:900px){.terms-grid{grid-template-columns:repeat(2,1fr);}}
  @media (max-width:620px){.terms-grid{grid-template-columns:1fr;}}
  .term-card{background:linear-gradient(180deg,var(--panel-2),var(--panel));border:1px solid var(--line);
    border-radius:var(--radius);padding:14px 14px 12px;display:flex;flex-direction:column;gap:8px;}
  .term-head{display:flex;justify-content:space-between;align-items:flex-start;gap:8px;}
  .term-title{font-size:14.5px;font-weight:700;letter-spacing:-0.01em;}
  .term-tag{font-family:'JetBrains Mono',monospace;font-size:9.5px;padding:2px 7px;border-radius:100px;
    border:1px solid var(--line);color:var(--text-faint);white-space:nowrap;}
  .term-card canvas{width:100%;height:auto;display:block;border-radius:7px;background:#0b1119;border:1px solid var(--line-soft);}
  .term-def{font-size:12px;color:var(--text-dim);line-height:1.55;margin:0;}
  .term-def b{color:var(--text);font-weight:600;}

  /* ---------- generic stage card ---------- */
  .stage-card{background:linear-gradient(180deg,var(--panel-2),var(--panel));border:1px solid var(--line);
    border-radius:var(--radius);padding:20px 20px 16px;margin-top:8px;position:relative;overflow:hidden;}
  .stage-head{display:flex;justify-content:space-between;align-items:flex-start;gap:20px;flex-wrap:wrap;margin-bottom:6px;}
  .stage-name{font-size:19px;font-weight:700;letter-spacing:-0.01em;}
  .stage-name .num{color:var(--text-faint);font-family:'JetBrains Mono',monospace;font-weight:500;font-size:14px;margin-right:8px;}
  .stage-desc{color:var(--text-dim);font-size:13.5px;max-width:600px;line-height:1.55;margin-top:4px;}
  .badge{font-family:'JetBrains Mono',monospace;font-size:11px;letter-spacing:.06em;padding:5px 10px;border-radius:100px;
    border:1px solid var(--line);color:var(--text-faint);white-space:nowrap;}
  .badge.on{color:var(--green);border-color:#1c4d33;background:#0e2118;}
  .badge.rev{color:var(--red);border-color:#5a2330;background:#26141a;}
  canvas#stage,canvas#biasStage{width:100%;height:auto;display:block;border-radius:8px;background-color:#0b1119;margin-top:12px;}
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
    display:flex;align-items:center;justify-content:center;gap:10px;font-family:'JetBrains Mono',monospace;font-size:15px;
    color:var(--text-dim);transition:color .3s, border-color .3s;flex-wrap:wrap;text-align:center;}
  .eqformula.on{color:var(--green);border-color:#1c4d33;}
  .eqformula span.big{font-size:17px;color:var(--text);}
  .eqformula.on span.big{color:var(--green);}

  /* ---------- graphs row ---------- */
  .graphs{display:grid;grid-template-columns:repeat(3,1fr);gap:14px;margin-top:14px;}
  @media (max-width:760px){.graphs{grid-template-columns:1fr;}}
  .graph-card{background:var(--panel);border:1px solid var(--line);border-radius:8px;padding:12px 14px 10px;}
  .graph-title{font-family:'JetBrains Mono',monospace;font-size:11px;letter-spacing:.05em;color:var(--text-dim);text-transform:uppercase;margin-bottom:2px;}
  .graph-sub{font-size:11px;color:var(--text-faint);margin-bottom:8px;}
  canvas.gcanvas{width:100%;height:auto;display:block;}

  /* ---------- bias controls ---------- */
  .biasmodes{display:flex;gap:8px;flex-wrap:wrap;}
  .slidewrap{display:flex;align-items:center;gap:10px;flex:1;min-width:220px;}
  .slidewrap input[type="range"]{flex:1;height:4px;-webkit-appearance:none;appearance:none;background:var(--line);border-radius:4px;outline:none;accent-color:var(--green);}
  .slidewrap input[type="range"]::-webkit-slider-thumb{-webkit-appearance:none;width:14px;height:14px;border-radius:50%;background:var(--green);cursor:pointer;box-shadow:0 0 0 3px rgba(57,255,136,0.15);}

  .current-panel{display:grid;grid-template-columns:1fr auto 1fr;gap:14px;align-items:center;
    margin-top:16px;padding:14px 16px;border:1px solid var(--line);border-radius:8px;background:var(--panel);}
  .current-col{display:flex;flex-direction:column;gap:4px;}
  .current-col.right{align-items:flex-end;text-align:right;}
  .current-label{font-family:'JetBrains Mono',monospace;font-size:11px;letter-spacing:.06em;color:var(--text-faint);text-transform:uppercase;}
  .current-val{font-family:'JetBrains Mono',monospace;font-size:15px;font-weight:600;}
  .eq-sign{font-family:'JetBrains Mono',monospace;font-size:22px;color:var(--text-faint);text-align:center;transition:color .3s;}
  .eq-sign.balanced{color:var(--green);}
  .bar-track{height:6px;background:var(--line-soft);border-radius:4px;overflow:hidden;margin-top:2px;width:100%;}
  .bar-fill{height:100%;border-radius:4px;transition:width .12s linear;}
  @media (max-width:760px){.current-panel{grid-template-columns:1fr;text-align:left;}.current-col.right{align-items:flex-start;text-align:left;}.eq-sign{transform:rotate(90deg);}}

  .plain-caption{font-size:13.5px;line-height:1.6;color:var(--text);
    background:#0d1420;border:1px solid var(--line);border-left:3px solid var(--green);
    border-radius:8px;padding:12px 14px;margin-top:14px;}
  .plain-caption b{color:var(--blue);}

  .metrics-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;margin-top:14px;}
  @media (max-width:700px){.metrics-grid{grid-template-columns:repeat(2,1fr);}}
  .metric-box{background:var(--panel);border:1px solid var(--line);border-radius:8px;padding:10px 12px;text-align:center;}
  .metric-label{font-family:'JetBrains Mono',monospace;font-size:9.5px;color:var(--text-faint);text-transform:uppercase;}
  .metric-value{font-family:'JetBrains Mono',monospace;font-size:14px;font-weight:700;margin-top:4px;color:var(--green);}

  .charts-row{display:grid;grid-template-columns:1.15fr 0.85fr;gap:14px;margin-top:14px;}
  @media (max-width:760px){.charts-row{grid-template-columns:1fr;}}
  canvas#bandCanvas,canvas#ivCanvas{width:100%;height:auto;display:block;border-radius:8px;background:#06090e;}

  /* ---------- table ---------- */
  .table-wrap{overflow-x:auto;border:1px solid var(--line);border-radius:var(--radius);}
  table{width:100%;border-collapse:collapse;font-size:13px;min-width:640px;}
  thead th{background:#131b26;color:var(--text-faint);font-family:'JetBrains Mono',monospace;font-size:11px;
    text-transform:uppercase;letter-spacing:.05em;text-align:left;padding:12px 14px;border-bottom:1px solid var(--line);}
  tbody td{padding:13px 14px;border-bottom:1px solid var(--line-soft);color:var(--text-dim);vertical-align:top;}
  tbody tr:last-child td{border-bottom:none;}
  tbody tr{transition:background .15s;}
  tbody tr:hover{background:#111a26;}
  tbody td:first-child{color:var(--text);font-weight:600;font-family:'JetBrains Mono',monospace;font-size:12px;}
  .cell-p{color:var(--amber);}
  .cell-n{color:var(--blue);}
  .cell-d{color:var(--violet);}

  footer{margin-top:50px;padding-top:18px;border-top:1px solid var(--line-soft);
    font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--text-faint);
    display:flex;justify-content:space-between;flex-wrap:wrap;gap:8px;}
</style>
</head>
<body>
<div class="wrap">

  <header>
    <div class="eyebrow"><span class="dot"></span>SEMICONDUCTOR DEVICE PHYSICS · COMPLETE INTERACTIVE ANIMATOR</div>
    <h1>The complete <span>P–N junction</span> story</h1>
    <p class="sub">Every core term, the full six-step formation process, live charge/field/potential graphs, forward &amp; reverse bias with continuous carrier flow, the energy band picture, and the diode I–V curve — all in one animated walkthrough.</p>
    <div class="quicknav">
      <a href="#terms">Core Terms</a>
      <a href="#formation">Formation (6 Steps)</a>
      <a href="#graphs">Charge &amp; Field Graphs</a>
      <a href="#bias">Forward / Reverse Bias</a>
      <a href="#bands">Energy Bands &amp; I–V</a>
      <a href="#summary">Summary Table</a>
    </div>
  </header>

  <!-- ============ 1. CORE TERMS ============ -->
  <div class="section-label" id="terms">1 · core terms</div>
  <p class="section-sub">Six small live animations — the vocabulary you need before the full process makes sense.</p>
  <div class="terms-grid">

    <div class="term-card">
      <div class="term-head"><div class="term-title">Majority &amp; Minority Carriers</div><span class="term-tag mono">carrier types</span></div>
      <canvas id="cMajMin" width="280" height="150"></canvas>
      <p class="term-def">In the <b>P-region</b>, holes are <b>majority</b> and electrons are <b>minority</b>. In the <b>N-region</b>, electrons are <b>majority</b> and holes are <b>minority</b>. Watch the rare minority carrier blink on each side.</p>
    </div>

    <div class="term-card">
      <div class="term-head"><div class="term-title">Immobile Ions (Dopants)</div><span class="term-tag mono">N_A⁻ / N_D⁺</span></div>
      <canvas id="cIons" width="280" height="150"></canvas>
      <p class="term-def"><b>Acceptor ions:</b> trivalent atoms that gain an electron, becoming fixed negative sites. <b>Donor ions:</b> pentavalent atoms that lose an electron, becoming fixed positive sites.</p>
    </div>

    <div class="term-card">
      <div class="term-head"><div class="term-title">Diffusion Current</div><span class="term-tag mono">I_diff</span></div>
      <canvas id="cDiffusion" width="280" height="150"></canvas>
      <p class="term-def">Charge transport from <b>random thermal motion</b>, pushing carriers from high concentration toward low concentration.</p>
    </div>

    <div class="term-card">
      <div class="term-head"><div class="term-title">Drift Current</div><span class="term-tag mono">I_drift</span></div>
      <canvas id="cDrift" width="280" height="150"></canvas>
      <p class="term-def">Charge transport driven by an <b>electric field</b>: positive carriers move along E, negative carriers move opposite to E.</p>
    </div>

    <div class="term-card">
      <div class="term-head"><div class="term-title">Depletion Region</div><span class="term-tag mono">space-charge</span></div>
      <canvas id="cDepletion" width="280" height="150"></canvas>
      <p class="term-def">The zone at the junction <b>depleted of mobile carriers</b> — only fixed, ionized dopants remain.</p>
    </div>

    <div class="term-card">
      <div class="term-head"><div class="term-title">Built-in Potential</div><span class="term-tag mono">V_bi</span></div>
      <canvas id="cVbi" width="280" height="150"></canvas>
      <p class="term-def">The electrostatic potential set up by the space charge — <b>≈0.7&nbsp;V for Si</b>, <b>≈0.3&nbsp;V for Ge</b> at room temperature.</p>
    </div>

  </div>

  <!-- ============ 2. STEP-BY-STEP FORMATION ============ -->
  <div class="section-label" id="formation">2 · step-by-step process of formation</div>
  <p class="section-sub">Play through all six steps in order, or jump to any step using the dots below the animation.</p>

  <div class="stage-card">
    <div class="stage-head">
      <div>
        <div class="stage-name"><span class="num" id="stageNum">1</span><span id="stageName">Concentration gradient trigger</span></div>
        <div class="stage-desc" id="stageDesc">The moment the junction forms, a steep carrier gradient exists across the boundary: high hole concentration on the P-side and high electron concentration on the N-side.</div>
      </div>
      <div class="badge" id="eqBadge">GRADIENT ONLY</div>
    </div>

    <canvas id="stage" width="900" height="300"></canvas>

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

  <!-- ============ 3. CHARGE / FIELD / POTENTIAL GRAPHS ============ -->
  <div class="section-label" id="graphs">3 · charge concentration &amp; field graphs</div>
  <p class="section-sub">These three graphs are wired to the same formation animation above — they grow and settle exactly in sync with it.</p>
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

  <!-- ============ 4. BIAS EXPLORER ============ -->
  <div class="section-label" id="bias">4 · apply a voltage: forward vs reverse bias</div>
  <p class="section-sub">Now connect a battery. Carriers move continuously and recombine or leak in real time as you change the bias.</p>

  <div class="stage-card">
    <div class="stage-head">
      <div>
        <div class="stage-name"><span class="num">●</span><span id="biasName">No external voltage — equilibrium</span></div>
        <div class="stage-desc" id="biasDesc">Diffusion current and drift current are still happening every instant — they're just equal and opposite. No net current, depletion width stays fixed.</div>
      </div>
      <div class="badge" id="biasBadge">I ≈ 0</div>
    </div>

    <canvas id="biasStage" width="900" height="300"></canvas>

    <div class="controls">
      <div class="biasmodes">
        <button class="btn mode active" data-mode="none">Unbiased</button>
        <button class="btn mode" data-mode="reverse">Reverse (−2.0V)</button>
        <button class="btn mode" data-mode="forward">Forward (+0.7V)</button>
      </div>
      <div class="slidewrap">
        <label class="mono" style="font-size:11px;color:var(--text-faint);white-space:nowrap;">V<sub>a</sub></label>
        <input type="range" id="biasSlider" min="-3.0" max="1.0" step="0.02" value="0.0">
        <span class="mono" id="biasSliderVal" style="font-size:11.5px;color:var(--text);width:56px;">0.00 V</span>
      </div>
    </div>

    <p class="plain-caption" id="plainCaption">At <b>0.00 V</b>, nothing is pushing the carriers. Holes and electrons quietly diffuse toward the junction and drift back at the same rate — no net current flows.</p>

    <div class="metrics-grid">
      <div class="metric-box"><div class="metric-label">Depletion Width (W)</div><div class="metric-value" id="mWidth">120 nm</div></div>
      <div class="metric-box"><div class="metric-label">Barrier q(V_bi − V_a)</div><div class="metric-value" id="mBarrier">0.70 eV</div></div>
      <div class="metric-box"><div class="metric-label">Diffusion Current</div><div class="metric-value" id="mIdiff">1.0 µA</div></div>
      <div class="metric-box"><div class="metric-label">Net Current</div><div class="metric-value" id="mInet" style="color:var(--text);">0.00 mA</div></div>
    </div>
  </div>

  <!-- ============ 5. ENERGY BANDS + I-V ============ -->
  <div class="section-label" id="bands">5 · energy bands &amp; diode I–V curve</div>
  <p class="section-sub">Same bias slider from above — watch the band bending shrink/grow and the operating point slide along the diode curve.</p>
  <div class="charts-row">
    <div class="graph-card">
      <div class="graph-title">Energy Band Diagram (E vs x)</div>
      <canvas id="bandCanvas" width="500" height="180"></canvas>
      <p class="plain-caption" style="margin-top:10px;">The conduction band (blue) and valence band (orange) bend at the junction. The vertical gap is the barrier carriers must climb — smaller under forward bias, larger under reverse bias.</p>
    </div>
    <div class="graph-card">
      <div class="graph-title">Diode I–V Characteristic</div>
      <canvas id="ivCanvas" width="360" height="180"></canvas>
      <p class="plain-caption" style="margin-top:10px;">The orange dot is your current operating point. Current stays near zero until V<sub>a</sub> passes ~0.6–0.7V, then rises steeply — the classic diode "knee".</p>
    </div>
  </div>

  <!-- ============ 6. SUMMARY TABLE ============ -->
  <div class="section-label" id="summary">6 · parameter summary</div>
  <div class="table-wrap">
    <table>
      <thead><tr><th>Parameter</th><th>P-Side (Edge)</th><th>Depletion Zone</th><th>N-Side (Edge)</th></tr></thead>
      <tbody>
        <tr><td>Mobile Charges</td><td class="cell-p">Abundant Holes (+)</td><td class="cell-d">Negligible free carriers</td><td class="cell-n">Abundant Electrons (−)</td></tr>
        <tr><td>Space Charge</td><td>Neutral</td><td class="cell-d">Negative (Acceptors, −qN_A) / Positive (Donors, +qN_D)</td><td>Neutral</td></tr>
        <tr><td>Internal Potential</td><td>0 V (Reference)</td><td class="cell-d">Rises non-linearly</td><td>High (+V_bi)</td></tr>
        <tr><td>Conduction State</td><td class="cell-p">Conductive</td><td class="cell-d">Insulating barrier</td><td class="cell-n">Conductive</td></tr>
      </tbody>
    </table>
  </div>

  <footer>
    <span>Complete PN Junction Animator</span>
    <span>Idealized step-junction approximation (uniform doping, abrupt depletion edges)</span>
  </footer>
</div>

<script>
/* =====================================================================
   SCRIPT A — Core term mini animations
===================================================================== */
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
    op=op===undefined?1:op; r=r||7;
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

  const registry = [];
  function reg(id, initFn, drawFn){
    const cvs = document.getElementById(id);
    if(!cvs) return;
    const ctx = cvs.getContext('2d');
    const data = initFn ? initFn(cvs.width,cvs.height) : {};
    registry.push({ctx, w:cvs.width, h:cvs.height, data, drawFn});
  }

  reg('cMajMin', function(w,h){
    const rnd=seeded(1); const holes=[], electrons=[];
    for(let i=0;i<9;i++) holes.push({x:14+rnd()*(w/2-28), y:18+rnd()*(h-36), ph:rnd()*7});
    for(let i=0;i<9;i++) electrons.push({x:w/2+14+rnd()*(w/2-28), y:18+rnd()*(h-36), ph:rnd()*7});
    return {holes, electrons};
  }, function(ctx,w,h,t,d){
    ctx.clearRect(0,0,w,h);
    ctx.fillStyle='#20140a'; ctx.fillRect(0,0,w/2,h);
    ctx.fillStyle='#0d1c2a'; ctx.fillRect(w/2,0,w/2,h);
    ctx.strokeStyle='rgba(255,255,255,0.1)'; ctx.beginPath(); ctx.moveTo(w/2,0); ctx.lineTo(w/2,h); ctx.stroke();
    ctx.font='9px JetBrains Mono, monospace'; ctx.fillStyle='rgba(233,238,244,0.55)'; ctx.textAlign='center';
    ctx.fillText('P-region', w*0.25, 12); ctx.fillText('N-region', w*0.75, 12);
    d.holes.forEach(p=> drawParticle(ctx, p.x, p.y+Math.sin(t*0.0015+p.ph)*2, 'h', 1, 6));
    d.electrons.forEach(p=> drawParticle(ctx, p.x, p.y+Math.sin(t*0.0015+p.ph)*2, 'e', 1, 6));
    const blink=(Math.sin(t*0.0022)+1)/2;
    drawParticle(ctx, w*0.32, h*0.55, 'e', 0.25+0.75*blink, 6);
    drawParticle(ctx, w*0.68, h*0.55, 'h', 0.25+0.75*blink, 6);
    if(blink>0.6){
      ctx.fillStyle='rgba(233,238,244,0.7)'; ctx.font='8px JetBrains Mono, monospace';
      ctx.fillText('minority', w*0.32, h*0.55+16);
      ctx.fillText('minority', w*0.68, h*0.55+16);
    }
  });

  reg('cIons', function(){ return {period:3200}; }, function(ctx,w,h,t,d){
    ctx.clearRect(0,0,w,h);
    ctx.fillStyle='#20140a'; ctx.fillRect(0,0,w/2,h);
    ctx.fillStyle='#0d1c2a'; ctx.fillRect(w/2,0,w/2,h);
    const cyc=(t%d.period)/d.period;
    const ax=w*0.28, ay=h*0.55;
    let capture=clamp01((cyc-0.15)/0.15), approach=clamp01(cyc/0.15);
    if(cyc<0.5){
      const ex=lerp(w*0.05, ax, approach);
      if(capture<1) drawParticle(ctx, ex, ay-22, 'e', 1, 6);
      ctx.beginPath(); ctx.arc(ax,ay,9,0,Math.PI*2);
      ctx.strokeStyle = capture>=1 ? '#f5a623' : 'rgba(233,238,244,0.4)'; ctx.lineWidth=1.6; ctx.stroke();
      ctx.fillStyle = capture>=1 ? '#f5a623' : 'rgba(233,238,244,0.6)';
      ctx.font='10px JetBrains Mono, monospace'; ctx.textAlign='center'; ctx.textBaseline='middle';
      ctx.fillText(capture>=1?'\u2212':'B', ax, ay+0.5);
    } else {
      ctx.beginPath(); ctx.arc(ax,ay,9,0,Math.PI*2);
      ctx.strokeStyle='#f5a623'; ctx.lineWidth=1.6; ctx.stroke();
      ctx.fillStyle='#f5a623'; ctx.font='10px JetBrains Mono, monospace'; ctx.textAlign='center'; ctx.textBaseline='middle';
      ctx.fillText('\u2212', ax, ay+0.5);
    }
    const dx=w*0.72, dy=h*0.55;
    if(cyc<0.5){
      ctx.beginPath(); ctx.arc(dx,dy,9,0,Math.PI*2);
      ctx.strokeStyle='rgba(233,238,244,0.4)'; ctx.lineWidth=1.6; ctx.stroke();
      ctx.fillStyle='rgba(233,238,244,0.6)'; ctx.font='10px JetBrains Mono, monospace'; ctx.textAlign='center'; ctx.textBaseline='middle';
      ctx.fillText('P', dx, dy+0.5);
    } else {
      const rel=clamp01((cyc-0.5)/0.5);
      const ey=lerp(dy-4, dy-30, rel), ex=lerp(dx, w*0.95, rel);
      ctx.beginPath(); ctx.arc(dx,dy,9,0,Math.PI*2);
      ctx.strokeStyle='#4fa8ff'; ctx.lineWidth=1.6; ctx.stroke();
      ctx.fillStyle='#4fa8ff'; ctx.font='10px JetBrains Mono, monospace'; ctx.textAlign='center'; ctx.textBaseline='middle';
      ctx.fillText('+', dx, dy+0.5);
      drawParticle(ctx, ex, ey, 'e', 1, 6);
    }
    ctx.font='8px JetBrains Mono, monospace'; ctx.fillStyle='rgba(233,238,244,0.5)'; ctx.textAlign='center';
    ctx.fillText('acceptor \u2192 N_A\u207B', ax, h-8);
    ctx.fillText('donor \u2192 N_D\u207A', dx, h-8);
  });

  reg('cDiffusion', function(w,h){
    const rnd=seeded(3); const parts=[];
    for(let i=0;i<26;i++) parts.push({x:w*Math.pow(rnd(),1.6), y:16+rnd()*(h-40), vy:(rnd()-0.5)*0.4});
    return {parts};
  }, function(ctx,w,h,t,d){
    ctx.clearRect(0,0,w,h);
    ctx.fillStyle='#0b1119'; ctx.fillRect(0,0,w,h);
    const grad=ctx.createLinearGradient(0,0,w,0);
    grad.addColorStop(0,'rgba(245,166,35,0.12)'); grad.addColorStop(1,'rgba(245,166,35,0.0)');
    ctx.fillStyle=grad; ctx.fillRect(0,0,w,h-24);
    d.parts.forEach(p=>{
      p.x+=0.55; p.y+=p.vy;
      if(p.y<16||p.y>h-30) p.vy*=-1;
      if(p.x>w+8){ p.x=-4; p.y=16+Math.random()*(h-40); }
      drawParticle(ctx,p.x,p.y,'h',1,5.5);
    });
    drawArrow(ctx, 16, h-12, w-16, h-12, 'rgba(245,166,35,0.85)', 2);
    ctx.font='9px JetBrains Mono, monospace'; ctx.fillStyle='#f5a623'; ctx.textAlign='center';
    ctx.fillText('high concentration \u2192 low concentration', w/2, h-2);
  });

  reg('cDrift', function(w,h){
    const rnd=seeded(4); const pos=[], neg=[];
    for(let i=0;i<7;i++) pos.push({x:rnd()*w, y:26+rnd()*(h-70)});
    for(let i=0;i<7;i++) neg.push({x:rnd()*w, y:26+rnd()*(h-70)});
    return {pos,neg};
  }, function(ctx,w,h,t,d){
    ctx.clearRect(0,0,w,h);
    ctx.fillStyle='#0b1119'; ctx.fillRect(0,0,w,h);
    ctx.font='9px JetBrains Mono, monospace'; ctx.fillStyle='rgba(79,168,255,0.8)'; ctx.textAlign='center';
    ctx.fillText('E field (N \u2192 P)', w/2, 12);
    for(let i=0;i<4;i++){ const y=22+i*((h-70)/3); drawArrow(ctx, w-14, y, 14, y, 'rgba(79,168,255,0.55)', 1.4); }
    d.pos.forEach(p=>{ p.x-=0.9; if(p.x<-8) p.x=w+6; drawParticle(ctx,p.x,p.y,'h',1,5.5); });
    d.neg.forEach(p=>{ p.x+=0.9; if(p.x>w+8) p.x=-6; drawParticle(ctx,p.x,p.y,'e',1,5.5); });
    ctx.font='8.5px JetBrains Mono, monospace'; ctx.fillStyle='rgba(233,238,244,0.55)'; ctx.textAlign='center';
    ctx.fillText('+ drifts with E, \u2212 drifts against E', w/2, h-6);
  });

  reg('cDepletion', function(){ return {}; }, function(ctx,w,h,t,d){
    ctx.clearRect(0,0,w,h);
    ctx.fillStyle='#160e08'; ctx.fillRect(0,0,w*0.25,h);
    ctx.fillStyle='#0a121b'; ctx.fillRect(w*0.75,0,w*0.25,h);
    const zx=w*0.25, zw=w*0.5;
    const shimmer=0.75+0.25*Math.sin(t*0.0025);
    const grad=ctx.createLinearGradient(zx,0,zx+zw,0);
    grad.addColorStop(0,'rgba(167,139,250,0.10)'); grad.addColorStop(0.5,'rgba(167,139,250,0.24)'); grad.addColorStop(1,'rgba(167,139,250,0.10)');
    ctx.fillStyle=grad; ctx.fillRect(zx,0,zw,h);
    ctx.strokeStyle='rgba(167,139,250,0.55)'; ctx.setLineDash([3,3]);
    ctx.beginPath(); ctx.moveTo(zx,0); ctx.lineTo(zx,h); ctx.moveTo(zx+zw,0); ctx.lineTo(zx+zw,h); ctx.stroke();
    ctx.setLineDash([]);
    for(let x=zx+10; x<w/2-4; x+=17){ for(let y=30;y<h-22;y+=34){ drawIon(ctx,x,y,'\u2212','#f5a623',shimmer,6); } }
    for(let x=w/2+10; x<zx+zw-4; x+=17){ for(let y=30;y<h-22;y+=34){ drawIon(ctx,x,y,'+','#4fa8ff',shimmer,6); } }
    drawParticle(ctx, w*0.10, h*0.6, 'h', 0.9, 6);
    drawParticle(ctx, w*0.90, h*0.6, 'e', 0.9, 6);
    ctx.font='9px JetBrains Mono, monospace'; ctx.fillStyle='rgba(233,238,244,0.6)'; ctx.textAlign='center';
    ctx.fillText('no free carriers here', w/2, 14);
  });

  reg('cVbi', function(){ return {}; }, function(ctx,w,h,t,d){
    ctx.clearRect(0,0,w,h);
    ctx.fillStyle='#0b1119'; ctx.fillRect(0,0,w,h);
    const pad=18, x0=pad, x1=w-pad, y0=h-24, vbi=h-56;
    ctx.strokeStyle='#1d2733'; ctx.lineWidth=1;
    ctx.beginPath(); ctx.moveTo(x0,y0); ctx.lineTo(x1,y0); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(x0,14); ctx.lineTo(x0,y0); ctx.stroke();
    ctx.beginPath();
    const steps=40;
    for(let i=0;i<=steps;i++){
      const xx=x0+(x1-x0)*(i/steps), yy=y0-vbi*smoothstep(i/steps);
      if(i===0) ctx.moveTo(xx,yy); else ctx.lineTo(xx,yy);
    }
    ctx.strokeStyle='#39ff88'; ctx.lineWidth=2; ctx.stroke();
    ctx.strokeStyle='rgba(57,255,136,0.35)'; ctx.setLineDash([3,3]);
    ctx.beginPath(); ctx.moveTo(x0,y0-vbi); ctx.lineTo(x1,y0-vbi); ctx.stroke(); ctx.setLineDash([]);
    const cyc=(t%2600)/2600;
    const dx=x0+(x1-x0)*cyc, dy=y0-vbi*smoothstep(cyc);
    ctx.beginPath(); ctx.arc(dx,dy,4,0,Math.PI*2); ctx.fillStyle='#39ff88'; ctx.shadowColor='#39ff88'; ctx.shadowBlur=6; ctx.fill(); ctx.shadowBlur=0;
    ctx.font='9px JetBrains Mono, monospace'; ctx.fillStyle='#39ff88'; ctx.textAlign='left';
    ctx.fillText('V_bi \u2248 0.7 V (Si)', x0+4, y0-vbi-6);
    ctx.fillStyle='rgba(233,238,244,0.45)'; ctx.fillText('P', x0, y0+14);
    ctx.textAlign='right'; ctx.fillText('N', x1, y0+14);
  });

  function loopTerms(now){
    registry.forEach(r=> r.drawFn(r.ctx, r.w, r.h, now, r.data));
    requestAnimationFrame(loopTerms);
  }
  requestAnimationFrame(loopTerms);
})();

/* =====================================================================
   SCRIPT B — Step-by-step formation stage + charge/field/potential graphs
===================================================================== */
(function(){
  "use strict";
  function clamp01(x){return Math.max(0,Math.min(1,x));}
  function lerp(a,b,t){return a+(b-a)*t;}
  function smoothstep(t){t=clamp01(t);return t*t*(3-2*t);}
  function seeded(seed){let s=seed;return function(){s=(s*9301+49297)%233280;return s/233280;};}

  function drawParticle(ctx,x,y,type,op,r){
    if(op<=0.02) return;
    r=r||6.5;
    ctx.save(); ctx.globalAlpha=op;
    ctx.beginPath(); ctx.arc(x,y,r,0,Math.PI*2);
    const col = type==='h' ? '#f5a623' : '#4fa8ff';
    ctx.fillStyle=col; ctx.shadowColor=col+'88'; ctx.shadowBlur=6; ctx.fill(); ctx.shadowBlur=0;
    ctx.fillStyle = type==='h' ? '#241505' : '#031320';
    ctx.font='bold 9px JetBrains Mono, monospace'; ctx.textAlign='center'; ctx.textBaseline='middle';
    ctx.fillText(type==='h'?'+':'\u2212', x, y+0.5);
    ctx.restore();
  }
  function drawIon(ctx,x,y,sign,color,op){
    op=op===undefined?1:op;
    ctx.save(); ctx.globalAlpha=op;
    ctx.beginPath(); ctx.arc(x,y,7,0,Math.PI*2);
    ctx.strokeStyle=color; ctx.lineWidth=1.4; ctx.stroke();
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

  const cvs = document.getElementById('stage');
  const ctx = cvs.getContext('2d');
  const W = cvs.width, H = cvs.height, CX = W/2;

  const state = { p:0, target:0, playing:false };
  const rnd = seeded(42);
  const N_H=46, N_E=46, PAD_TOP=34, PAD_BOT=26;
  const holes=[], electrons=[];
  for(let i=0;i<N_H;i++) holes.push({x0:34+rnd()*(CX-34-18), y:PAD_TOP+rnd()*(H-PAD_TOP-PAD_BOT), ph:rnd()*Math.PI*2});
  for(let i=0;i<N_E;i++) electrons.push({x0:CX+18+rnd()*(W-18-(CX+18)-34), y:PAD_TOP+rnd()*(H-PAD_TOP-PAD_BOT), ph:rnd()*Math.PI*2});

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

  function drawStage(p, now){
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

    holes.forEach(h=>{
      const x = mapHoleX(h.x0,d);
      const edge = CX-d.hw;
      let op = d.gap<4 ? clamp01((edge-x)/18) : 1;
      if(x>pRight-6) return;
      drawParticle(ctx, x, h.y+Math.sin(now*0.0016+h.ph)*2.4, 'h', op, 6.5);
    });
    electrons.forEach(e=>{
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

  /* ---- graphs, driven by the same depletion progress ---- */
  const gRho = document.getElementById('gRho').getContext('2d');
  const gField = document.getElementById('gField').getContext('2d');
  const gPot = document.getElementById('gPot').getContext('2d');
  const GW=300, GH=150, GPAD=26;

  function graphAxes(g){
    g.clearRect(0,0,GW,GH);
    g.strokeStyle='#1d2733'; g.lineWidth=1;
    g.beginPath(); g.moveTo(GPAD, GH/2); g.lineTo(GW-10, GH/2); g.stroke();
    g.beginPath(); g.moveTo(GPAD, 14); g.lineTo(GPAD, GH-14); g.stroke();
    g.fillStyle='#57667a'; g.font='9px JetBrains Mono, monospace'; g.textAlign='left';
    g.fillText('x', GW-16, GH/2-6);
  }
  function drawRhoGraph(d){
    graphAxes(gRho);
    const cx=GPAD+(GW-GPAD-10)/2, scaleX=(GW-GPAD-10)/2/MAX_HALF_W, hwpx=d.hw*scaleX, amp=42*d.deplProg;
    if(hwpx>1){
      gRho.fillStyle='rgba(245,166,35,0.8)'; gRho.fillRect(cx-hwpx, GH/2, hwpx, amp);
      gRho.fillStyle='rgba(79,168,255,0.8)'; gRho.fillRect(cx, GH/2-amp, hwpx, amp);
    }
    gRho.fillStyle='#8b97a7'; gRho.font='9px JetBrains Mono, monospace'; gRho.textAlign='center';
    gRho.fillText('\u2212qNa', cx-hwpx/2-6, GH/2+amp+12);
    gRho.fillText('+qNd', cx+hwpx/2+6, GH/2-amp-6);
  }
  function drawFieldGraph(d){
    graphAxes(gField);
    const cx=GPAD+(GW-GPAD-10)/2, scaleX=(GW-GPAD-10)/2/MAX_HALF_W, hwpx=d.hw*scaleX, peak=52*d.fieldProg;
    if(hwpx>1){
      gField.beginPath(); gField.moveTo(cx-hwpx, GH/2); gField.lineTo(cx, GH/2+peak); gField.lineTo(cx+hwpx, GH/2); gField.closePath();
      gField.fillStyle='rgba(79,168,255,0.22)'; gField.fill();
      gField.strokeStyle='#4fa8ff'; gField.lineWidth=1.8;
      gField.beginPath(); gField.moveTo(cx-hwpx, GH/2); gField.lineTo(cx, GH/2+peak); gField.lineTo(cx+hwpx, GH/2); gField.stroke();
    }
    gField.fillStyle='#57667a'; gField.font='9px JetBrains Mono, monospace'; gField.textAlign='center';
    gField.fillText('E max (N\u2192P)', cx, Math.min(GH-6, GH/2+peak+12));
  }
  function drawPotGraph(d){
    graphAxes(gPot);
    const cx=GPAD+(GW-GPAD-10)/2, scaleX=(GW-GPAD-10)/2/MAX_HALF_W, hwpx=d.hw*scaleX, vbi=50*d.deplProg;
    gPot.beginPath();
    const steps=40;
    for(let i=0;i<=steps;i++){
      const x=cx-hwpx+(2*hwpx)*(i/steps), tt=i/steps, y=GH/2+18-vbi*smoothstep(tt);
      if(i===0) gPot.moveTo(x,y); else gPot.lineTo(x,y);
    }
    gPot.strokeStyle='#39ff88'; gPot.lineWidth=2; gPot.stroke();
    gPot.strokeStyle='rgba(57,255,136,0.4)'; gPot.setLineDash([3,3]);
    gPot.beginPath(); gPot.moveTo(GPAD, GH/2+18); gPot.lineTo(cx-hwpx, GH/2+18); gPot.stroke();
    gPot.beginPath(); gPot.moveTo(cx+hwpx, GH/2+18-vbi); gPot.lineTo(GW-10, GH/2+18-vbi); gPot.stroke();
    gPot.setLineDash([]);
    gPot.fillStyle='#57667a'; gPot.font='9px JetBrains Mono, monospace'; gPot.textAlign='left';
    gPot.fillText('V_bi', GW-30, Math.max(12, GH/2+18-vbi-4));
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
  const markers=[0,0.18,0.36,0.54,0.74,1];
  function nearestMarker(p){ let best=markers[0], bd=Infinity; for(const m of markers){ const dd=Math.abs(p-m); if(dd<bd){bd=dd;best=m;} } return best; }

  function updateUI(p,d){
    const st=currentStage(p);
    stageNumEl.textContent=st.num; stageNameEl.textContent=st.name; stageDescEl.textContent=st.desc;
    eqBadge.textContent=st.badge; eqBadge.classList.toggle('on', d.eqProg>0.9);
    idiffLive.textContent='I_diff('+d.deplProg.toFixed(2)+')';
    idriftLive.textContent='I_drift('+d.driftProg.toFixed(2)+')';
    const eq=d.eqProg>0.9;
    eqResult.textContent = eq ? '= 0  \u2705 equilibrium' : '\u2260 0';
    eqFormula.classList.toggle('on', eq);
    stagebtns.forEach(b=>{
      const bp=parseFloat(b.dataset.p);
      b.classList.toggle('active', Math.abs(bp-nearestMarker(p))<0.001);
      b.classList.toggle('passed', p>bp+0.001);
    });
  }

  function frame(now){
    const speed = state.playing ? 0.0035 : 0.006;
    if(state.p<state.target) state.p=Math.min(state.target, state.p+speed*16.7);
    else if(state.p>state.target) state.p=Math.max(state.target, state.p-speed*16.7);
    if(state.playing){
      state.target = Math.min(1, state.target + 0.00018*16.7*0.2);
      state.p = state.target;
      if(state.p>=1){ state.playing=false; setPlayUI(false); }
    }
    const d = computeDerived(state.p);
    drawStage(state.p, now);
    drawRhoGraph(d); drawFieldGraph(d); drawPotGraph(d);
    updateUI(state.p, d);
    requestAnimationFrame(frame);
  }

  const playBtn=document.getElementById('playBtn');
  const playLabel=document.getElementById('playLabel');
  const playIcon=document.getElementById('playIcon');
  const resetBtn=document.getElementById('resetBtn');
  function setPlayUI(playing){
    playLabel.textContent = playing ? 'Pause' : (state.p>=0.999 ? 'Replay' : 'Play formation');
    playIcon.innerHTML = playing ? '<path d="M6 5h4v14H6zM14 5h4v14h-4z"/>' : '<path d="M8 5v14l11-7z"/>';
  }
  playBtn.addEventListener('click', ()=>{
    if(state.p>=0.999 && !state.playing){ state.p=0; state.target=0; }
    state.playing=!state.playing;
    if(state.playing) state.target=1;
    setPlayUI(state.playing);
  });
  resetBtn.addEventListener('click', ()=>{ state.playing=false; state.target=0; setPlayUI(false); });
  stagebtns.forEach(b=>{
    b.addEventListener('click', ()=>{ state.playing=false; state.target=parseFloat(b.dataset.p); setPlayUI(false); });
  });

  setPlayUI(false);
  requestAnimationFrame(frame);
})();

/* =====================================================================
   SCRIPT C — Continuous bias explorer + energy bands + I-V curve
===================================================================== */
(function(){
  "use strict";
  function clamp01(x){return Math.max(0,Math.min(1,x));}
  function lerp(a,b,t){return a+(b-a)*t;}
  function seeded(seed){let s=seed;return function(){s=(s*9301+49297)%233280;return s/233280;};}
  const rnd = seeded(7);

  const V_BI = 0.70;
  let Va = 0.0;

  const cvs = document.getElementById('biasStage');
  const ctx = cvs.getContext('2d');
  const W = cvs.width, H = cvs.height, CX = W/2;

  const N_MAJ = 18;
  const holes=[], electrons=[];
  for(let i=0;i<N_MAJ;i++){
    holes.push({ x: 30+rnd()*(CX-60), y: 26+rnd()*(H-52) });
    electrons.push({ x: CX+30+rnd()*(CX-60), y: 26+rnd()*(H-52) });
  }
  const N_MIN = 4;
  const minElecInP = [], minHoleInN = [];
  for(let i=0;i<N_MIN;i++){
    minElecInP.push({ x: 40+rnd()*(CX-80), y: 26+rnd()*(H-52) });
    minHoleInN.push({ x: CX+40+rnd()*(CX-80), y: 26+rnd()*(H-52) });
  }
  const sparks = [];

  function getMode(){ return Va>0.4 ? 'forward' : (Va<-0.1 ? 'reverse' : 'none'); }
  function getBiasStrength(){
    const mode = getMode();
    if(mode==='forward') return clamp01(Va/1.0);
    if(mode==='reverse') return clamp01(-Va/3.0);
    return 0;
  }
  function getDepletionHalfWidth(){
    const effV = Math.max(0.05, V_BI - Va);
    return Math.min(150, Math.max(12, 60*Math.sqrt(effV/V_BI)));
  }
  function getDiodeCurrent(v){
    const Is=1e-6, Vt=0.026;
    return Is*(Math.exp(v/(1.5*Vt))-1);
  }

  function drawIonBand(hw){
    if(hw<2) return;
    const zx=CX-hw, zw=hw*2;
    const grad=ctx.createLinearGradient(zx,0,zx+zw,0);
    grad.addColorStop(0,'rgba(167,139,250,0.14)'); grad.addColorStop(0.5,'rgba(167,139,250,0.28)'); grad.addColorStop(1,'rgba(167,139,250,0.14)');
    ctx.fillStyle=grad; ctx.fillRect(zx,0,zw,H);
    ctx.strokeStyle='rgba(167,139,250,0.5)'; ctx.setLineDash([4,4]);
    ctx.beginPath(); ctx.moveTo(zx,0); ctx.lineTo(zx,H); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(zx+zw,0); ctx.lineTo(zx+zw,H); ctx.stroke();
    ctx.setLineDash([]);
    const spacing=17;
    ctx.font='10px JetBrains Mono, monospace'; ctx.textAlign='center'; ctx.textBaseline='middle';
    for(let x=CX-8; x>=zx+6; x-=spacing){ for(let y=40;y<H-20;y+=46){ ionMark(x,y,'-','#f5a623'); } }
    for(let x=CX+8; x<=zx+zw-6; x+=spacing){ for(let y=40;y<H-20;y+=46){ ionMark(x,y,'+','#4fa8ff'); } }
  }
  function ionMark(x,y,sign,color){
    ctx.beginPath(); ctx.arc(x,y,6.5,0,Math.PI*2);
    ctx.strokeStyle=color; ctx.lineWidth=1.2; ctx.stroke();
    ctx.fillStyle=color; ctx.fillText(sign,x,y+0.5);
  }
  function drawParticle(x,y,type,op){
    if(op<=0.02) return;
    ctx.save(); ctx.globalAlpha=op;
    ctx.beginPath(); ctx.arc(x,y,6.5,0,Math.PI*2);
    const col = type==='h' ? '#f5a623' : '#4fa8ff';
    ctx.fillStyle=col; ctx.shadowColor=col+'88'; ctx.shadowBlur=6; ctx.fill(); ctx.shadowBlur=0;
    ctx.fillStyle = type==='h' ? '#241505' : '#031320';
    ctx.font='bold 9px JetBrains Mono, monospace'; ctx.textAlign='center'; ctx.textBaseline='middle';
    ctx.fillText(type==='h'?'+':'\u2212', x, y+0.5);
    ctx.restore();
  }
  function drawArrow(x1,y1,x2,y2,color,w){
    ctx.save(); ctx.strokeStyle=color; ctx.fillStyle=color; ctx.lineWidth=w;
    ctx.beginPath(); ctx.moveTo(x1,y1); ctx.lineTo(x2,y2); ctx.stroke();
    const ang=Math.atan2(y2-y1,x2-x1), ah=6;
    ctx.beginPath(); ctx.moveTo(x2,y2);
    ctx.lineTo(x2-ah*Math.cos(ang-0.5), y2-ah*Math.sin(ang-0.5));
    ctx.lineTo(x2-ah*Math.cos(ang+0.5), y2-ah*Math.sin(ang+0.5));
    ctx.closePath(); ctx.fill(); ctx.restore();
  }
  function drawBattery(){
    const mode = getMode();
    if(mode==='none') return;
    const wireY=4, fwd = mode==='forward';
    ctx.save();
    ctx.strokeStyle='rgba(233,238,244,0.35)'; ctx.lineWidth=1.6;
    ctx.beginPath(); ctx.moveTo(CX-140,26); ctx.lineTo(CX-140,wireY); ctx.lineTo(CX-18,wireY); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(CX+140,26); ctx.lineTo(CX+140,wireY); ctx.lineTo(CX+18,wireY); ctx.stroke();
    ctx.strokeStyle='rgba(233,238,244,0.7)'; ctx.lineWidth=3;
    ctx.beginPath(); ctx.moveTo(CX-18,wireY-9); ctx.lineTo(CX-18,wireY+9); ctx.stroke();
    ctx.lineWidth=1.6;
    ctx.beginPath(); ctx.moveTo(CX+18,wireY-5); ctx.lineTo(CX+18,wireY+5); ctx.stroke();
    ctx.fillStyle = fwd ? '#f5a623' : '#a78bfa';
    ctx.font='11px JetBrains Mono, monospace'; ctx.textAlign='center';
    ctx.fillText(fwd?'+':'\u2212', CX-30, wireY+4);
    ctx.fillText(fwd?'\u2212':'+', CX+30, wireY+4);
    ctx.fillStyle='rgba(233,238,244,0.5)'; ctx.fillText('V', CX, wireY-14);
    ctx.beginPath(); ctx.arc(CX-140,26,2.5,0,Math.PI*2); ctx.fill();
    ctx.beginPath(); ctx.arc(CX+140,26,2.5,0,Math.PI*2); ctx.fill();
    ctx.restore();
  }

  function update(dt){
    const mode = getMode(), b = getBiasStrength(), hw = getDepletionHalfWidth();
    if(mode==='forward'){
      const speed = lerp(0.35,2.6,b)*dt;
      holes.forEach(h=>{
        h.x += speed;
        if(h.x > CX + Math.max(18, hw*0.35)){
          sparks.push({x:h.x,y:h.y,life:1,color:'#ffd27a'});
          h.x = 20+rnd()*40; h.y = 26+rnd()*(H-52);
        }
      });
      electrons.forEach(e=>{
        e.x -= speed;
        if(e.x < CX - Math.max(18, hw*0.35)){
          sparks.push({x:e.x,y:e.y,life:1,color:'#9fd6ff'});
          e.x = W-20-rnd()*40; e.y = 26+rnd()*(H-52);
        }
      });
    } else if(mode==='reverse'){
      holes.forEach(h=>{ const target=Math.min(h.x, CX-hw-14); h.x += (target-h.x)*0.02; });
      electrons.forEach(e=>{ const target=Math.max(e.x, CX+hw+14); e.x += (target-e.x)*0.02; });
      const mspeed = lerp(0.12,0.55,b)*dt;
      minElecInP.forEach(m=>{
        m.x += mspeed;
        if(m.x > CX+30){ sparks.push({x:m.x,y:m.y,life:1,color:'#9fd6ff'}); m.x=40+rnd()*(CX-80); m.y=26+rnd()*(H-52); }
      });
      minHoleInN.forEach(m=>{
        m.x -= mspeed;
        if(m.x < CX-30){ sparks.push({x:m.x,y:m.y,life:1,color:'#ffd27a'}); m.x=CX+40+rnd()*(CX-80); m.y=26+rnd()*(H-52); }
      });
    } else {
      if(Math.random()<0.02) sparks.push({x:CX-hw+6,y:36+rnd()*(H-72),life:1,color:'#ffd27a'});
      if(Math.random()<0.02) sparks.push({x:CX+hw-6,y:36+rnd()*(H-72),life:1,color:'#9fd6ff'});
    }
    for(let i=sparks.length-1;i>=0;i--){ sparks[i].life -= dt*0.03; if(sparks[i].life<=0) sparks.splice(i,1); }
  }

  function drawCrystal(now){
    const dt = Math.min(2.2,(now-(drawCrystal._last||now))/16.7);
    drawCrystal._last = now;
    const hw = getDepletionHalfWidth(), mode = getMode();

    ctx.clearRect(0,0,W,H);
    const gradP=ctx.createLinearGradient(0,0,CX,0);
    gradP.addColorStop(0,'#20140a'); gradP.addColorStop(1,'#2a1a0d');
    ctx.fillStyle=gradP; ctx.fillRect(0,0,CX,H);
    const gradN=ctx.createLinearGradient(CX,0,W,0);
    gradN.addColorStop(0,'#0d1c2a'); gradN.addColorStop(1,'#0a1420');
    ctx.fillStyle=gradN; ctx.fillRect(CX,0,W-CX,H);

    ctx.save(); ctx.strokeStyle='rgba(255,255,255,0.028)'; ctx.lineWidth=1;
    for(let gx=0;gx<W;gx+=22){ ctx.beginPath(); ctx.moveTo(gx,0); ctx.lineTo(gx,H); ctx.stroke(); }
    for(let gy=0;gy<H;gy+=22){ ctx.beginPath(); ctx.moveTo(0,gy); ctx.lineTo(W,gy); ctx.stroke(); }
    ctx.restore();

    drawIonBand(hw);
    update(dt||1);

    holes.forEach(h=> drawParticle(h.x,h.y,'h',1));
    electrons.forEach(e=> drawParticle(e.x,e.y,'e',1));
    if(mode==='reverse'){
      minElecInP.forEach(m=> drawParticle(m.x,m.y,'e',0.85));
      minHoleInN.forEach(m=> drawParticle(m.x,m.y,'h',0.85));
    }
    sparks.forEach(s=>{
      ctx.save(); ctx.globalAlpha=clamp01(s.life);
      ctx.beginPath(); ctx.arc(s.x,s.y,10*(1-s.life)+3,0,Math.PI*2);
      ctx.strokeStyle=s.color; ctx.lineWidth=1.6; ctx.stroke();
      ctx.restore();
    });

    ctx.strokeStyle='rgba(255,255,255,0.12)'; ctx.setLineDash([2,3]);
    ctx.beginPath(); ctx.moveTo(CX,0); ctx.lineTo(CX,H); ctx.stroke(); ctx.setLineDash([]);
    drawBattery();

    ctx.font='10.5px JetBrains Mono, monospace'; ctx.textAlign='center';
    if(mode==='forward'){
      drawArrow(CX-90,H-14,CX+90,H-14,'rgba(245,166,35,0.9)',2.6);
      ctx.fillStyle='#f5a623'; ctx.fillText('I_F  (P \u2192 N, large)', CX, H-22);
    } else if(mode==='reverse'){
      drawArrow(CX+70,H-14,CX-70,H-14,'rgba(167,139,250,0.85)',1.6);
      ctx.fillStyle='#a78bfa'; ctx.fillText('I_R  (N \u2192 P, tiny leakage)', CX, H-22);
    } else {
      drawArrow(CX-40,H-14,CX+8,H-14,'rgba(245,166,35,0.55)',1.4);
      drawArrow(CX+40,H-14,CX-8,H-14,'rgba(167,139,250,0.55)',1.4);
      ctx.fillStyle='#8b97a7'; ctx.fillText('I_diff \u2248 I_drift  (balanced)', CX, H-22);
    }
  }

  /* ---- energy band diagram ---- */
  const bandCv = document.getElementById('bandCanvas');
  const ctxBand = bandCv.getContext('2d');
  function drawBands(hw){
    const W2=bandCv.width, H2=bandCv.height;
    ctxBand.clearRect(0,0,W2,H2);
    const bx=W2/2, scaleHw=hw*(W2/W), barrierEv=Math.max(0.08, V_BI-Va), deltaY=barrierEv*60;
    const ecP=40+deltaY, ecN=40, evP=110+deltaY, evN=110;
    drawBandCurve(bx,scaleHw,ecP,ecN,'#38bdf8','Conduction Band (Ec)');
    drawBandCurve(bx,scaleHw,evP,evN,'#f97316','Valence Band (Ev)');
    ctxBand.strokeStyle='#a855f7'; ctxBand.setLineDash([2,2]);
    ctxBand.beginPath(); ctxBand.moveTo(bx+scaleHw+20,ecP); ctxBand.lineTo(bx+scaleHw+20,ecN); ctxBand.stroke();
    ctxBand.setLineDash([]);
    ctxBand.fillStyle='#a855f7'; ctxBand.font='10px JetBrains Mono, monospace'; ctxBand.textAlign='left';
    ctxBand.fillText(`q(V_bi - Va) = ${barrierEv.toFixed(2)} eV`, bx+scaleHw+26, (ecP+ecN)/2);
  }
  function drawBandCurve(cx,hw,yLeft,yRight,color,label){
    ctxBand.strokeStyle=color; ctxBand.lineWidth=2.2;
    ctxBand.beginPath();
    ctxBand.moveTo(20,yLeft); ctxBand.lineTo(cx-hw,yLeft);
    ctxBand.bezierCurveTo(cx-hw*0.4,yLeft, cx+hw*0.4,yRight, cx+hw,yRight);
    ctxBand.lineTo(bandCv.width-20,yRight);
    ctxBand.stroke();
    ctxBand.fillStyle=color; ctxBand.font='10px JetBrains Mono, monospace'; ctxBand.textAlign='left';
    ctxBand.fillText(label, 24, yLeft-8);
  }

  /* ---- I-V curve ---- */
  const ivCv = document.getElementById('ivCanvas');
  const ctxIv = ivCv.getContext('2d');
  function drawIV(){
    const W2=ivCv.width, H2=ivCv.height;
    ctxIv.clearRect(0,0,W2,H2);
    const ox=120, oy=H2-35;
    ctxIv.strokeStyle='#374151'; ctxIv.lineWidth=1;
    ctxIv.beginPath(); ctxIv.moveTo(15,oy); ctxIv.lineTo(W2-15,oy);
    ctxIv.moveTo(ox,15); ctxIv.lineTo(ox,H2-10); ctxIv.stroke();
    ctxIv.fillStyle='#6b7280'; ctxIv.font='9px JetBrains Mono, monospace';
    ctxIv.fillText('V', W2-14, oy-6); ctxIv.fillText('I', ox+8, 18);
    ctxIv.fillText('0', ox-10, oy+12); ctxIv.fillText('0.7V', ox+65, oy+12);
    ctxIv.strokeStyle='#10b981'; ctxIv.lineWidth=2;
    ctxIv.beginPath();
    for(let px=20; px<W2-20; px++){
      const v=(px-ox)/90; let cur=0;
      if(v>0) cur=Math.exp(v/0.13)*0.05;
      const py=oy-cur;
      if(px===20) ctxIv.moveTo(px,Math.max(15,py)); else ctxIv.lineTo(px,Math.max(15,py));
    }
    ctxIv.stroke();
    const currX = ox+Va*90;
    let currI=0; if(Va>0) currI=Math.exp(Va/0.13)*0.05;
    const currY=Math.max(15, oy-currI);
    ctxIv.fillStyle='#f59e0b'; ctxIv.beginPath(); ctxIv.arc(currX,currY,5,0,Math.PI*2); ctxIv.fill();
  }

  /* ---- UI ---- */
  const biasName=document.getElementById('biasName');
  const biasDesc=document.getElementById('biasDesc');
  const biasBadge=document.getElementById('biasBadge');
  const plainCaption=document.getElementById('plainCaption');
  const mWidth=document.getElementById('mWidth');
  const mBarrier=document.getElementById('mBarrier');
  const mIdiff=document.getElementById('mIdiff');
  const mInet=document.getElementById('mInet');
  const slider=document.getElementById('biasSlider');
  const sliderVal=document.getElementById('biasSliderVal');
  const modeBtns=document.querySelectorAll('.btn.mode');

  const COPY = {
    none: { name:'No external voltage — equilibrium',
      desc:"Diffusion current and drift current are still happening every instant — they're just equal and opposite. No net current, depletion width stays fixed.", badge:'I ≈ 0' },
    forward: { name:'Forward bias — barrier lowered',
      desc:"The battery's positive terminal connects to P, negative to N. This pushes majority carriers toward the junction, narrows the depletion region, lowers the barrier, and lets a large current flow P → N.", badge:'I_F ↑ (mA range)' },
    reverse: { name:'Reverse bias — barrier raised',
      desc:"The positive terminal connects to N, negative to P. Majority carriers are pulled away from the junction, widening the depletion region and raising the barrier. Only a tiny drift current of minority carriers leaks through, N → P.", badge:'I_R ≈ 0 (µA leakage)' }
  };

  function updateCaption(){
    if(Va>0.4){
      plainCaption.innerHTML = `At <b>+${Va.toFixed(2)} V (forward bias)</b>, the battery pushes holes and electrons straight toward the junction. The barrier shrinks, they cross and recombine right at the middle — those are the little flashes — and a real, usable current flows.`;
    } else if(Va<-0.1){
      plainCaption.innerHTML = `At <b>${Va.toFixed(2)} V (reverse bias)</b>, the battery pulls holes and electrons away from the junction. The depletion region widens and the barrier grows — almost no current can flow, just a tiny leakage.`;
    } else {
      plainCaption.innerHTML = `At <b>${Va.toFixed(2)} V</b>, nothing is pushing the carriers. Holes and electrons quietly diffuse toward the junction and drift back at the same rate — no net current flows.`;
    }
  }

  function updateUI(){
    const mode = getMode();
    const hw = getDepletionHalfWidth();
    const barrier = Math.max(0, V_BI-Va);
    const netCurrent = getDiodeCurrent(Va);
    const c = COPY[mode];
    biasName.textContent = c.name; biasDesc.textContent = c.desc; biasBadge.textContent = c.badge;
    biasBadge.classList.toggle('on', mode==='forward');
    biasBadge.classList.toggle('rev', mode==='reverse');

    modeBtns.forEach(b=>{
      b.classList.remove('active','rev-active');
      if(b.dataset.mode===mode || (mode==='none' && b.dataset.mode==='none')) b.classList.add(mode==='reverse'?'rev-active':'active');
    });

    sliderVal.textContent = (Va>=0?'+':'')+Va.toFixed(2)+' V';
    mWidth.textContent = (hw*1.8).toFixed(0)+' nm';
    mBarrier.textContent = barrier.toFixed(2)+' eV';
    const diffVal = Va>0 ? (Math.exp(Va/0.12)*0.8).toFixed(1) : (0.8*(1-Math.abs(Va)/3)).toFixed(2);
    mIdiff.textContent = diffVal+' µA';
    if(Va>0.4){ mInet.textContent=(netCurrent*1e3).toFixed(1)+' mA'; mInet.style.color='var(--green)'; }
    else if(Va<-0.1){ mInet.textContent='-1.00 µA (leakage)'; mInet.style.color='var(--red)'; }
    else { mInet.textContent='0.00 mA'; mInet.style.color='var(--text)'; }

    updateCaption();
  }

  function frame(now){
    drawCrystal(now);
    drawBands(getDepletionHalfWidth());
    drawIV();
    updateUI();
    requestAnimationFrame(frame);
  }

  slider.addEventListener('input', ()=>{ Va = parseFloat(slider.value); });
  modeBtns.forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const m = btn.dataset.mode;
      Va = m==='forward' ? 0.7 : (m==='reverse' ? -2.0 : 0.0);
      slider.value = Va;
    });
  });

  requestAnimationFrame(frame);
})();
</script>
</body>
</html>
