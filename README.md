<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>計算機</title>
<style>
  *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #111827;
    font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
    user-select: none;
  }

  .wrap {
    display: flex;
    flex-direction: column;
    gap: 12px;
    width: 300px;
  }

  .calc {
    background: #1f2937;
    border-radius: 20px;
    padding: 20px;
    box-shadow: 0 25px 60px rgba(0,0,0,0.7), inset 0 1px 0 rgba(255,255,255,0.06);
  }

  .screen {
    background: #0d1117;
    border-radius: 12px;
    padding: 16px 18px 12px;
    margin-bottom: 16px;
    min-height: 90px;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    align-items: flex-end;
    overflow: hidden;
  }

  .expr {
    font-size: 12px;
    color: #6ee7b7;
    min-height: 16px;
    max-width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    margin-bottom: 4px;
    opacity: 0.75;
    letter-spacing: 0.04em;
  }

  .num {
    font-size: 42px;
    font-weight: 200;
    color: #f0fdf4;
    letter-spacing: -0.02em;
    max-width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    transition: font-size 0.1s;
  }
  .num.md { font-size: 30px; }
  .num.sm { font-size: 22px; }

  .toast {
    background: #7f1d1d;
    color: #fca5a5;
    font-size: 12px;
    text-align: center;
    padding: 7px 10px;
    border-radius: 8px;
    margin-bottom: 12px;
    display: none;
  }
  .toast.show { display: block; }

  .grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 8px;
  }

  .k {
    height: 58px;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 18px;
    font-weight: 400;
    cursor: pointer;
    transition: transform 0.07s, filter 0.07s;
    -webkit-tap-highlight-color: transparent;
  }

  .k:active { transform: scale(0.91); filter: brightness(0.8); }

  .k-num  { background: #374151; color: #f9fafb; }
  .k-num:hover  { background: #4b5563; }

  .k-op   { background: #065f46; color: #6ee7b7; font-size: 20px; }
  .k-op:hover   { background: #047857; }

  .k-fn   { background: #1e3a4a; color: #93c5fd; font-size: 15px; }
  .k-fn:hover   { background: #1e4a6a; }

  .k-eq   { background: #6ee7b7; color: #064e3b; font-size: 22px; font-weight: 600; }
  .k-eq:hover   { background: #a7f3d0; }

  .k-span2 { grid-column: span 2; justify-content: flex-start; padding-left: 20px; }

  .history {
    background: #1f2937;
    border-radius: 16px;
    padding: 14px 16px;
    box-shadow: 0 8px 24px rgba(0,0,0,0.4), inset 0 1px 0 rgba(255,255,255,0.04);
  }

  .hist-head {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
  }

  .hist-title {
    font-size: 10px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: #6ee7b7;
    opacity: 0.6;
  }

  .hist-clear {
    font-size: 11px;
    color: #fca5a5;
    cursor: pointer;
    opacity: 0.65;
    transition: opacity 0.15s;
    letter-spacing: 0.02em;
  }
  .hist-clear:hover { opacity: 1; }

  .hist-list {
    max-height: 130px;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .hist-list::-webkit-scrollbar { width: 3px; }
  .hist-list::-webkit-scrollbar-thumb { background: #374151; border-radius: 2px; }

  .hist-row {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    padding: 5px 8px;
    border-radius: 8px;
    cursor: pointer;
    transition: background 0.1s;
  }
  .hist-row:hover { background: #374151; }

  .hist-expr { font-size: 11px; color: #6ee7b7; opacity: 0.6; max-width: 160px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
  .hist-val  { font-size: 14px; color: #f0fdf4; font-weight: 300; }

  .hist-empty { font-size: 12px; color: #6ee7b7; opacity: 0.3; text-align: center; padding: 12px 0; }
</style>
</head>
<body>

<div class="wrap">

  <div class="calc">
    <div class="screen">
      <div class="expr" id="expr"></div>
      <div class="num"  id="num">0</div>
    </div>
    <div class="toast" id="toast"></div>
    <div class="grid">
      <div class="k k-fn"            onclick="clearAll()">AC</div>
      <div class="k k-fn"            onclick="toggleSign()">+/−</div>
      <div class="k k-fn"            onclick="percent()">%</div>
      <div class="k k-op"            onclick="inputOp('÷')">÷</div>

      <div class="k k-num"           onclick="inputNum('7')">7</div>
      <div class="k k-num"           onclick="inputNum('8')">8</div>
      <div class="k k-num"           onclick="inputNum('9')">9</div>
      <div class="k k-op"            onclick="inputOp('×')">×</div>

      <div class="k k-num"           onclick="inputNum('4')">4</div>
      <div class="k k-num"           onclick="inputNum('5')">5</div>
      <div class="k k-num"           onclick="inputNum('6')">6</div>
      <div class="k k-op"            onclick="inputOp('−')">−</div>

      <div class="k k-num"           onclick="inputNum('1')">1</div>
      <div class="k k-num"           onclick="inputNum('2')">2</div>
      <div class="k k-num"           onclick="inputNum('3')">3</div>
      <div class="k k-op"            onclick="inputOp('+')">+</div>

      <div class="k k-num k-span2"   onclick="inputNum('0')">0</div>
      <div class="k k-num"           onclick="inputDot()">.</div>
      <div class="k k-eq"            onclick="calculate()">=</div>
    </div>
  </div>

  <div class="history">
    <div class="hist-head">
      <span class="hist-title">計算記錄</span>
      <span class="hist-clear" onclick="clearHistory()">清除全部</span>
    </div>
    <div class="hist-list" id="hist-list">
      <div class="hist-empty">尚無記錄</div>
    </div>
  </div>

</div>

<script>
  let cur = '0', expr = '', op = null, prev = null, justCalc = false;
  let hist = [];

  const elNum   = document.getElementById('num');
  const elExpr  = document.getElementById('expr');
  const elToast = document.getElementById('toast');
  const elHist  = document.getElementById('hist-list');

  function render() {
    elNum.textContent  = cur;
    elExpr.textContent = expr;
    const l = cur.replace('-','').length;
    elNum.className = 'num' + (l > 12 ? ' sm' : l > 9 ? ' md' : '');
  }

  function toast(msg) {
    elToast.textContent = msg;
    elToast.classList.add('show');
    clearTimeout(toast._t);
    toast._t = setTimeout(() => elToast.classList.remove('show'), 2000);
  }

  function fmt(n) {
    if (!isFinite(n)) return '錯誤';
    return parseFloat(n.toPrecision(12)).toString();
  }

  function inputNum(d) {
    if (justCalc) { cur = d; expr = ''; justCalc = false; }
    else if (cur === '0') cur = d;
    else if (cur.replace('-','').length >= 14) return;
    else cur += d;
    render();
  }

  function inputDot() {
    if (justCalc) { cur = '0.'; expr = ''; justCalc = false; render(); return; }
    if (!cur.includes('.')) cur += '.';
    render();
  }

  function inputOp(o) {
    if (prev !== null && op && !justCalc) compute();
    prev = parseFloat(cur);
    op = o;
    expr = fmt(prev) + ' ' + o;
    justCalc = false;
    cur = '0';   // ← 清空，等待輸入第二個數字
    render();
  }

  function compute() {
    const a = prev, b = parseFloat(cur);
    if (op === '÷' && b === 0) { toast('不能除以零'); return false; }
    let r;
    if (op === '+') r = a + b;
    else if (op === '−') r = a - b;
    else if (op === '×') r = a * b;
    else if (op === '÷') r = a / b;
    cur = fmt(r); prev = r;
    return true;
  }

  function calculate() {
    if (prev === null || op === null) return;
    const b = parseFloat(cur);
    if (op === '÷' && b === 0) { toast('不能除以零'); return; }
    const fullExpr = expr + ' ' + fmt(b);
    const ok = compute();
    if (!ok) return;
    addHist(fullExpr, cur);
    expr = fullExpr + ' =';
    op = null; prev = null; justCalc = true;
    render();
  }

  function clearAll() {
    cur = '0'; expr = ''; op = null; prev = null; justCalc = false;
    elToast.classList.remove('show');
    render();
  }

  function toggleSign() {
    if (cur === '0') return;
    cur = cur.startsWith('-') ? cur.slice(1) : '-' + cur;
    render();
  }

  function percent() {
    cur = fmt(parseFloat(cur) / 100);
    render();
  }

  function addHist(e, v) {
    hist.unshift({ e, v });
    if (hist.length > 20) hist.pop();
    renderHist();
  }

  function renderHist() {
    if (!hist.length) { elHist.innerHTML = '<div class="hist-empty">尚無記錄</div>'; return; }
    elHist.innerHTML = hist.map((h, i) =>
      `<div class="hist-row" onclick="useHist(${i})">
        <span class="hist-expr">${h.e}</span>
        <span class="hist-val">${h.v}</span>
      </div>`
    ).join('');
  }

  function useHist(i) {
    cur = hist[i].v; expr = ''; op = null; prev = null; justCalc = true;
    render();
  }

  function clearHistory() { hist = []; renderHist(); }

  document.addEventListener('keydown', e => {
    if (e.key >= '0' && e.key <= '9') inputNum(e.key);
    else if (e.key === '.') inputDot();
    else if (e.key === '+') inputOp('+');
    else if (e.key === '-') inputOp('−');
    else if (e.key === '*') inputOp('×');
    else if (e.key === '/') { e.preventDefault(); inputOp('÷'); }
    else if (e.key === 'Enter' || e.key === '=') calculate();
    else if (e.key === 'Escape') clearAll();
    else if (e.key === 'Backspace') {
      if (justCalc) return;
      cur = cur.length > 1 ? cur.slice(0,-1) : '0';
      render();
    }
  });

  render();
</script>
</body>
</html>
