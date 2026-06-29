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
    flex-d
