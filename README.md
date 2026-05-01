<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>PvE Shop and Spree</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@500;700;900&family=JetBrains+Mono:wght@400;500;700&family=Cormorant+Garamond:ital,wght@0,400;0,600;1,400&display=swap" rel="stylesheet">
<style>
  :root {
    --bg-0: #0a0908;
    --bg-1: #14110f;
    --bg-2: #1c1814;
    --ink: #e8dcc4;
    --ink-dim: #8a7d68;
    --gold: #c9a24a;
    --gold-bright: #f0c878;
    --rust: #a14426;
    --moss: #5a7a3a;
    --blood: #6b1a1a;
    --line: #2a221a;
    --grid: rgba(201, 162, 74, 0.06);
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  html, body {
    background: var(--bg-0);
    color: var(--ink);
    font-family: 'Cormorant Garamond', serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  body::before {
    content: "";
    position: fixed; inset: 0;
    background-image:
      linear-gradient(var(--grid) 1px, transparent 1px),
      linear-gradient(90deg, var(--grid) 1px, transparent 1px);
    background-size: 64px 64px;
    pointer-events: none;
    z-index: 0;
    mask-image: radial-gradient(ellipse at center, black 30%, transparent 80%);
  }

  body::after {
    content: "";
    position: fixed; inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='2'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.4'/%3E%3C/svg%3E");
    opacity: 0.04;
    pointer-events: none;
    z-index: 1;
    mix-blend-mode: overlay;
  }

  .wrap {
    position: relative;
    z-index: 2;
    max-width: 1400px;
    margin: 0 auto;
    padding: 32px 24px 80px;
  }

  /* ── FIXED HEADER / WALLET BANNER ────────────────────────────────── */
  #siteHeader {
    position: fixed;
    top: 0; left: 0; right: 0;
    z-index: 8000;
    background: var(--bg-0);
    border-bottom: 1px solid var(--gold);
    border-top: 1px solid var(--gold);
    padding: 0;
  }
  #siteHeader::before, #siteHeader::after {
    content: "✦";
    position: absolute;
    color: var(--gold);
    font-size: 12px;
    bottom: -7px;
    background: var(--bg-0);
    padding: 0 8px;
    z-index: 1;
  }
  #siteHeader::before { left: 24px; }
  #siteHeader::after  { right: 24px; }

  .header-inner {
    max-width: 1400px;
    margin: 0 auto;
    padding: 14px 24px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 16px;
  }

  .brand-mark {
    font-family: 'Cinzel', serif;
    font-weight: 900;
    letter-spacing: 0.18em;
    font-size: clamp(18px, 3vw, 30px);
    line-height: 1;
    color: var(--ink);
    white-space: nowrap;
  }
  .brand-mark span { color: var(--gold); font-style: italic; }
  .brand-sub {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    text-transform: uppercase;
    letter-spacing: 0.3em;
    color: var(--ink-dim);
    margin-top: 4px;
  }

  /* Wallet pill — always visible in header */
  .wallet-pill {
    display: flex;
    align-items: center;
    gap: 0;
    cursor: pointer;
    flex-shrink: 0;
  }
  .wallet-pill-balance {
    font-family: 'Cinzel', serif;
    font-size: 17px;
    font-weight: 700;
    color: var(--gold-bright);
    background: var(--bg-1);
    border: 1px solid var(--gold);
    border-right: none;
    padding: 8px 14px;
    line-height: 1;
    transition: background 0.2s;
  }
  .wallet-pill:hover .wallet-pill-balance { background: var(--bg-2); }
  .wallet-pill-balance::before { content: "◈ "; color: var(--gold); font-size: 12px; }
  .wallet-pill-info {
    background: var(--bg-1);
    border: 1px solid var(--gold);
    border-right: none;
    padding: 8px 12px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    color: var(--ink-dim);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    line-height: 1.5;
    transition: background 0.2s;
  }
  .wallet-pill:hover .wallet-pill-info { background: var(--bg-2); }
  .wallet-pill-info .pill-votes { color: var(--moss); }
  .wallet-pill-info .pill-votes.spent { color: var(--rust); }
  .wallet-pill-open {
    background: var(--gold);
    border: 1px solid var(--gold);
    color: var(--bg-0);
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    font-weight: 700;
    letter-spacing: 0.2em;
    padding: 8px 14px;
    cursor: pointer;
    text-transform: uppercase;
    transition: background 0.2s;
    white-space: nowrap;
    line-height: 1;
    align-self: stretch;
    display: flex;
    align-items: center;
  }
  .wallet-pill:hover .wallet-pill-open,
  .wallet-pill-open:hover { background: var(--gold-bright); }

  /* Push page content below fixed header */
  .wrap { padding-top: 0 !important; }
  /* The hero slideshow sits flush under the fixed header */
  .hero-slideshow { margin-top: 72px; }
  /* Everything after the hero needs no extra top spacing */

  /* OLD header — hide it */
  header { display: none !important; }

  /* ── BIO SETUP MODAL ─────────────────────────────────────────────── */
  #bioOverlay {
    position: fixed; inset: 0; z-index: 9100;
    background: rgba(0,0,0,0.88); backdrop-filter: blur(6px);
    display: none; align-items: center; justify-content: center; padding: 20px;
  }
  #bioOverlay.show { display: flex; }
  .bio-modal {
    background: var(--bg-1); border: 1px solid var(--gold);
    width: 100%; max-width: 460px; padding: 28px 28px 24px;
  }
  .bio-modal-title {
    font-family: 'Cinzel', serif; font-size: 18px; font-weight: 900;
    color: var(--gold-bright); margin-bottom: 6px;
  }
  .bio-modal-sub {
    font-family: 'JetBrains Mono', monospace; font-size: 10px;
    color: var(--ink-dim); letter-spacing: 0.15em; margin-bottom: 18px; line-height: 1.6;
  }
  .bio-input-wrap { position: relative; margin-bottom: 6px; }
  .bio-textarea {
    width: 100%; background: var(--bg-2); border: 1px solid var(--gold);
    color: var(--ink); font-family: 'JetBrains Mono', monospace;
    font-size: 12px; padding: 12px 14px; resize: none; height: 70px;
    letter-spacing: 0.03em; line-height: 1.5; outline: none;
    transition: border-color 0.2s;
  }
  .bio-textarea:focus { border-color: var(--gold-bright); }
  .bio-textarea.over-limit { border-color: var(--rust); }
  .bio-char-count {
    font-family: 'JetBrains Mono', monospace; font-size: 9px;
    text-align: right; margin-bottom: 16px; letter-spacing: 0.1em;
  }
  .bio-char-count.ok   { color: var(--ink-dim); }
  .bio-char-count.near { color: var(--gold); }
  .bio-char-count.over { color: var(--rust); }
  .bio-actions { display: flex; gap: 8px; justify-content: flex-end; }
  #creatorOverlay {
    position: fixed; inset: 0; z-index: 9000;
    background: rgba(0,0,0,0.82); backdrop-filter: blur(4px);
    display: none; align-items: center; justify-content: center; padding: 20px;
  }
  #creatorOverlay.show { display: flex; }
  .creator-modal {
    background: var(--bg-1); border: 1px solid var(--gold);
    width: 100%; max-width: 760px; max-height: 88vh;
    display: flex; flex-direction: column; overflow: hidden;
  }
  .creator-modal-head {
    padding: 18px 22px; border-bottom: 1px solid var(--line);
    background: linear-gradient(90deg, rgba(201,162,74,0.1), transparent);
    display: flex; align-items: center; justify-content: space-between; gap: 12px; flex-shrink: 0;
  }
  .creator-modal-name {
    font-family: 'Cinzel', serif; font-size: 20px; font-weight: 900;
    color: var(--gold-bright);
  }
  .creator-modal-handle {
    font-family: 'JetBrains Mono', monospace; font-size: 11px;
    color: var(--ink-dim); margin-top: 3px;
  }
  .creator-modal-stats {
    display: flex; gap: 20px; font-family: 'JetBrains Mono', monospace;
    font-size: 10px; color: var(--ink-dim); text-align: center;
  }
  .creator-modal-stats b { display: block; font-family: 'Cinzel', serif; font-size: 17px; color: var(--goldb); }
  .creator-modal-body {
    overflow-y: auto; padding: 16px 20px; flex: 1;
    display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 1px;
    background: var(--line);
  }
  .creator-item-card {
    background: var(--bg-2); padding: 14px; cursor: pointer;
    transition: background 0.15s, transform 0.15s;
    display: flex; flex-direction: column; gap: 6px;
  }
  .creator-item-card:hover { background: var(--bg-1); transform: translateY(-2px); }
  .creator-item-art { aspect-ratio: 1; overflow: hidden; margin-bottom: 4px; }
  .creator-item-name {
    font-family: 'Cinzel', serif; font-size: 12px; font-weight: 700; color: var(--ink); line-height: 1.3;
  }
  .creator-item-meta {
    font-family: 'JetBrains Mono', monospace; font-size: 9px; color: var(--ink-dim);
    display: flex; justify-content: space-between; flex-wrap: wrap; gap: 4px;
  }
  .creator-item-price {
    font-family: 'Cinzel', serif; font-size: 14px; font-weight: 700; color: var(--gold);
  }
  .creator-item-vol {
    font-family: 'JetBrains Mono', monospace; font-size: 9px; color: var(--moss);
  }
  .creator-item-score {
    font-family: 'JetBrains Mono', monospace; font-size: 9px; color: var(--gold);
  }
  .oracle-law {
    border: 1px solid var(--gold);
    background: var(--bg-1);
    margin-bottom: 16px;
    overflow: hidden;
  }
  .oracle-law-header {
    background: linear-gradient(90deg, rgba(201,162,74,0.15), rgba(201,162,74,0.05));
    border-bottom: 1px solid var(--gold);
    padding: 12px 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
  }
  .oracle-law-title {
    font-family: 'Cinzel', serif;
    font-size: 15px;
    font-weight: 900;
    color: var(--gold-bright);
    letter-spacing: 0.25em;
    text-align: center;
  }
  .oracle-law-body {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 0;
  }
  .oracle-rule {
    padding: 12px 16px;
    border-right: 1px solid var(--line);
    border-bottom: 1px solid var(--line);
    display: flex;
    gap: 8px;
    align-items: flex-start;
  }
  .oracle-rule:nth-child(3n) { border-right: none; }
  .oracle-rule:nth-last-child(-n+3) { border-bottom: none; }
  .oracle-rule-num {
    font-family: 'Cinzel', serif;
    font-size: 13px;
    font-weight: 700;
    color: var(--gold);
    flex-shrink: 0;
    line-height: 1.4;
    min-width: 20px;
  }
  .oracle-rule-text {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: var(--ink-dim);
    line-height: 1.6;
  }
  .oracle-rule-text b { color: var(--ink); font-weight: 700; }

  @media (max-width: 800px) {
    .oracle-law-body { grid-template-columns: repeat(2, 1fr); }
    .oracle-rule:nth-child(3n)   { border-right: 1px solid var(--line); }
    .oracle-rule:nth-last-child(-n+3) { border-bottom: 1px solid var(--line); }
    .oracle-rule:nth-child(2n)   { border-right: none; }
    .oracle-rule:nth-last-child(-n+2) { border-bottom: none; }
  }
  @media (max-width: 500px) {
    .oracle-law-body { grid-template-columns: 1fr; }
    .oracle-rule { border-right: none !important; border-bottom: 1px solid var(--line) !important; }
    .oracle-rule:last-child { border-bottom: none !important; }
    .oracle-rule-text { font-size: 11px; }
    .oracle-law-title { font-size: 13px; letter-spacing: 0.15em; }
  }

  .ticker {
    background: var(--bg-1);
    border: 1px solid var(--line);
    padding: 12px 0;
    overflow: hidden;
    position: relative;
    margin-bottom: 16px;
  }
    padding: 12px 0;
    overflow: hidden;
    position: relative;
    margin-bottom: 32px;
  }
  .ticker-track {
    display: flex;
    gap: 48px;
    animation: scroll 45s linear infinite;
    white-space: nowrap;
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    width: max-content;
  }
  .ticker-item { color: var(--ink-dim); }
  .ticker-item .name { color: var(--ink); }
  .ticker-item .up { color: var(--moss); }
  .ticker-item .down { color: var(--rust); }
  @keyframes scroll {
    from { transform: translateX(0); }
    to { transform: translateX(-50%); }
  }

  /* ── ORACLE STRIP ── */
  /* ENGAGEMENT ORACLE — 4-cell grid panel */
  .founder-panel {
    display: grid;
    grid-template-columns: 1.3fr 1.2fr 1fr 1.4fr;
    border: 1px solid var(--gold);
    margin-bottom: 16px;
    background: linear-gradient(180deg, rgba(201,162,74,0.07) 0%, transparent 100%), var(--bg-1);
    position: relative;
    min-height: 200px;
  }
  .founder-panel::before {
    content: "ENGAGEMENT ORACLE";
    position: absolute;
    top: -9px; left: 50%;
    transform: translateX(-50%);
    background: var(--bg-0);
    padding: 0 18px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.45em;
    color: var(--gold);
    white-space: nowrap;
  }
  .founder-cell {
    padding: 32px 28px;
    border-right: 1px solid var(--line);
  }
  .founder-cell:last-child { border-right: none; }
  .founder-cell .cell-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.25em;
    text-transform: uppercase;
    color: var(--ink-dim);
    margin-bottom: 14px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .founder-name {
    font-family: 'Cinzel', serif;
    font-size: 26px;
    font-weight: 700;
    color: var(--gold-bright);
    margin-bottom: 6px;
  }
  .founder-handle a {
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    color: var(--ink-dim);
    text-decoration: none;
    border-bottom: 1px dashed var(--ink-dim);
  }
  .founder-handle a:hover { color: var(--gold); border-color: var(--gold); }
  .score-display {
    font-family: 'Cinzel', serif;
    font-size: 52px;
    font-weight: 900;
    color: var(--ink);
    line-height: 1;
  }
  .score-bar {
    height: 6px;
    background: var(--bg-2);
    margin: 12px 0 8px;
    overflow: hidden;
  }
  .score-bar-fill {
    height: 100%;
    background: linear-gradient(90deg, var(--moss), var(--gold-bright), var(--rust));
    transition: width 1.2s cubic-bezier(0.4,0,0.2,1);
  }
  .multiplier-val {
    font-family: 'Cinzel', serif;
    font-size: 48px;
    font-weight: 700;
    color: var(--gold-bright);
    line-height: 1;
  }
  .multiplier-trend {
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    margin-top: 10px;
  }
  .vote-block {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }
  .vote-row { display: flex; gap: 10px; }
  .vote-btn {
    flex: 1;
    padding: 14px 10px;
    background: var(--bg-2);
    border: 1px solid var(--gold);
    color: var(--gold);
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s;
  }
  .vote-btn:hover:not(:disabled) { background: var(--gold); color: var(--bg-0); transform: translateY(-1px); }
  .vote-btn:disabled { opacity: 0.35; cursor: not-allowed; }
  .vote-btn.downvote { border-color: var(--rust); color: var(--rust); }
  .vote-btn.downvote:hover:not(:disabled) { background: var(--rust); color: var(--bg-0); }
  .vote-stats {
    display: flex;
    justify-content: space-between;
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    color: var(--ink-dim);
  }
  .vote-stats .v-up  { color: var(--moss); }
  .vote-stats .v-down{ color: var(--rust); }
  .vote-locked {
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: var(--rust);
    line-height: 1.4;
  }
  .vote-locked.eligible { color: var(--moss); }
  .oracle-votes-chip {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    padding: 2px 8px;
    border: 1px solid var(--line);
    margin-left: 6px;
    letter-spacing: 0.1em;
  }
  .oracle-votes-chip.eligible { border-color: var(--moss); color: var(--moss); }
  .oracle-votes-chip.spent   { border-color: var(--rust);  color: var(--rust);  }
  .oracle-phase-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    color: var(--gold-bright);
    font-weight: 700;
    line-height: 1.4;
  }
  .oracle-phase-detail {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    color: var(--ink-dim);
    margin-top: 6px;
    line-height: 1.5;
  }
  @media (max-width: 900px) {
    .founder-panel { grid-template-columns: 1fr 1fr; }
    .founder-cell:nth-child(2) { border-right: none; }
    .founder-cell:nth-child(1),
    .founder-cell:nth-child(2) { border-bottom: 1px solid var(--line); }
  }
  @media (max-width: 560px) {
    .founder-panel { grid-template-columns: 1fr; }
    .founder-cell { border-right: none; border-bottom: 1px solid var(--line); }
  }

  .onchain-bar {
    background: var(--bg-1);
    border: 1px solid var(--line);
    border-top: none;
    padding: 0;
    margin-bottom: 24px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    overflow: hidden;
  }
  .onchain-bar-inner {
    display: flex;
    align-items: stretch;
    flex-wrap: wrap;
  }
  .onchain-pill {
    display: flex;
    align-items: center;
    gap: 7px;
    padding: 9px 16px;
    border-right: 1px solid var(--line);
    color: var(--ink-dim);
    text-decoration: none;
    transition: background 0.15s;
    white-space: nowrap;
  }
  .onchain-pill:last-child { border-right: none; }
  a.onchain-pill:hover { background: var(--bg-2); color: var(--ink); }
  .onchain-pill .live-dot {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--moss);
    box-shadow: 0 0 6px var(--moss);
    flex-shrink: 0;
    animation: pulse 2s ease-in-out infinite;
  }
  .onchain-pill .chain-badge {
    background: rgba(201,162,74,0.12);
    border: 1px solid var(--gold);
    color: var(--gold);
    padding: 1px 6px;
    font-size: 9px;
    letter-spacing: 0.15em;
    border-radius: 2px;
  }
  .onchain-pill .addr {
    color: var(--gold);
    letter-spacing: 0.03em;
    font-size: 10px;
  }
  .onchain-pill .label { color: var(--ink-dim); font-size: 9px; letter-spacing: 0.15em; text-transform: uppercase; }
  .onchain-pill .ext-icon { font-size: 9px; opacity: 0.5; }
  @keyframes pulse { 50% { opacity: 0.4; } }

  /* PROVENANCE SHEET (inline below card) */
  .provenance-sheet {
    background: var(--bg-0);
    border: 1px solid var(--gold);
    border-top: none;
    padding: 14px 16px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    display: none;
  }
  .provenance-sheet.open { display: block; }
  .prov-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 5px 0;
    border-bottom: 1px dashed var(--line);
    gap: 12px;
  }
  .prov-row:last-child { border-bottom: none; }
  .prov-label { color: var(--ink-dim); flex-shrink: 0; }
  .prov-val { color: var(--ink); text-align: right; word-break: break-all; }
  .prov-val a { color: var(--gold); text-decoration: none; border-bottom: 1px dashed var(--gold); }
  .prov-val a:hover { color: var(--gold-bright); }
  .prov-actions {
    display: flex;
    gap: 8px;
    margin-top: 10px;
    flex-wrap: wrap;
  }
  .prov-btn {
    flex: 1;
    min-width: 120px;
    padding: 8px 12px;
    background: var(--bg-1);
    border: 1px solid var(--line);
    color: var(--ink-dim);
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    cursor: pointer;
    text-decoration: none;
    text-align: center;
    transition: all 0.2s;
    display: block;
  }
  .prov-btn:hover { border-color: var(--gold); color: var(--gold); background: var(--bg-2); }
  .prov-btn.opensea { border-color: #2081e2; color: #2081e2; }
  .prov-btn.opensea:hover { background: rgba(32,129,226,0.1); }
  .prov-btn.pending { opacity: 0.5; cursor: default; border-style: dashed; }

  /* VOTE LEDGER */
  .ledger {
    background: var(--bg-1);
    border: 1px solid var(--line);
    margin-bottom: 24px;
  }
  .ledger-header {
    padding: 14px 18px;
    border-bottom: 1px solid var(--line);
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 8px;
  }
  .ledger-title {
    font-family: 'Cinzel', serif;
    font-size: 14px;
    font-weight: 700;
    color: var(--gold-bright);
    letter-spacing: 0.15em;
  }
  .ledger-sub {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: var(--ink-dim);
    font-style: italic;
  }
  .ledger-body {
    max-height: 260px;
    overflow-y: auto;
  }
  .ledger-row {
    display: grid;
    grid-template-columns: 24px 1.2fr 0.8fr 1fr 1fr 1fr;
    gap: 12px;
    padding: 10px 18px;
    border-bottom: 1px solid var(--line);
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    align-items: center;
    transition: background 0.2s;
  }
  .ledger-row:hover { background: var(--bg-2); }
  .ledger-row:last-child { border-bottom: none; }
  .ledger-row.is-active { border-left: 2px solid var(--moss); }
  .ledger-row.is-grace { border-left: 2px solid var(--gold); }
  .ledger-row.is-burned {
    border-left: 2px solid var(--rust);
    opacity: 0.55;
  }
  .ledger-row.is-burned .voter,
  .ledger-row.is-burned .ledger-balance,
  .ledger-row.is-burned .ledger-status {
    text-decoration: line-through;
    text-decoration-color: var(--rust);
  }
  .ledger-dir {
    font-size: 14px;
    font-weight: 700;
    text-align: center;
  }
  .ledger-dir.up { color: var(--moss); }
  .ledger-dir.down { color: var(--rust); }
  .voter { color: var(--ink); }
  .ledger-balance { color: var(--ink-dim); }
  .ledger-balance.below { color: var(--rust); }
  .ledger-status {
    font-size: 9px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    text-align: center;
    padding: 3px 6px;
    border: 1px solid;
  }
  .status-active { color: var(--moss); border-color: var(--moss); background: rgba(90,122,58,0.08); }
  .status-grace { color: var(--gold); border-color: var(--gold); background: rgba(201,162,74,0.08); }
  .status-burned { color: var(--rust); border-color: var(--rust); background: rgba(161,68,38,0.08); }
  .ledger-action {
    font-size: 9px;
    color: var(--ink-dim);
    text-align: right;
  }
  .ledger-action.warn { color: var(--gold); }
  .ledger-action.bad { color: var(--rust); }
  .ledger-legend {
    display: flex;
    gap: 18px;
    padding: 10px 18px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    color: var(--ink-dim);
    border-top: 1px solid var(--line);
    flex-wrap: wrap;
  }
  .ledger-legend span { display: flex; align-items: center; gap: 6px; }
  .dot-active, .dot-burned {
    width: 8px; height: 8px;
    display: inline-block;
  }
  .dot-active { background: var(--moss); }
  .dot-burned { background: var(--rust); }

  .ledger-empty {
    padding: 32px;
    text-align: center;
    color: var(--ink-dim);
    font-style: italic;
    font-family: 'Cormorant Garamond', serif;
    font-size: 14px;
  }

  @media (max-width: 720px) {
    .ledger-row {
      grid-template-columns: 20px 1fr 1fr;
      gap: 8px;
    }
    .ledger-row .ledger-balance,
    .ledger-row .ledger-action { display: none; }
  }

  .metrics {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 1px;
    background: var(--line);
    border: 1px solid var(--line);
    margin-bottom: 24px;
    font-family: 'JetBrains Mono', monospace;
  }
  .metric { background: var(--bg-1); padding: 14px 18px; text-align: center; }
  .metric .v { font-size: 18px; color: var(--gold-bright); font-weight: 700; }
  .metric .k {
    font-size: 9px;
    letter-spacing: 0.2em;
    color: var(--ink-dim);
    text-transform: uppercase;
    margin-top: 4px;
  }

  .toolbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 16px;
    margin-bottom: 24px;
  }
  .filters {
    display: flex;
    gap: 4px;
    border: 1px solid var(--line);
    background: var(--bg-1);
    flex-wrap: wrap;
  }
  .filter {
    padding: 10px 18px;
    background: #000;
    border: none;
    color: var(--ink-dim);
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s;
  }
  .filter:hover { color: var(--ink); }
  .filter.active { background: var(--gold); color: var(--bg-0); }

  .btn {
    background: var(--bg-0);
    border: 1px solid var(--gold);
    color: var(--gold);
    padding: 12px 24px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.25s;
  }
  .btn:hover { background: var(--gold); color: var(--bg-0); }
  .btn:disabled { opacity: 0.4; cursor: not-allowed; }

  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 20px;
  }
  .item {
    background: var(--bg-1);
    border: 1px solid var(--line);
    position: relative;
    transition: all 0.3s ease;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }
  .item:hover {
    border-color: var(--gold);
    transform: translateY(-3px);
    box-shadow: 0 12px 32px rgba(201,162,74,0.12);
  }
  .item-art {
    aspect-ratio: 1;
    position: relative;
    overflow: hidden;
    background: var(--bg-2);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .item-art svg { width: 70%; height: 70%; }
  .item-rarity {
    position: absolute;
    top: 10px; right: 10px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.2em;
    padding: 4px 8px;
    background: rgba(0,0,0,0.7);
    backdrop-filter: blur(4px);
    border: 1px solid;
  }
  .r-common { color: var(--ink-dim); border-color: var(--ink-dim); }
  .r-uncommon { color: var(--moss); border-color: var(--moss); }
  .r-rare { color: #4a7ab0; border-color: #4a7ab0; }
  .r-epic { color: #9a4abf; border-color: #9a4abf; }
  .r-legendary { color: var(--gold-bright); border-color: var(--gold-bright); }

  .item-creator {
    position: absolute;
    bottom: 10px; left: 10px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: var(--ink);
    background: rgba(0,0,0,0.7);
    backdrop-filter: blur(4px);
    padding: 4px 8px;
    border-left: 2px solid var(--gold);
  }
  .item-creator .at { color: var(--gold); }

  .item-body {
    padding: 16px;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
  }
  .item-name {
    font-family: 'Cinzel', serif;
    font-size: 16px;
    font-weight: 700;
    color: var(--ink);
    letter-spacing: 0.05em;
    margin-bottom: 4px;
  }
  .item-tag {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    color: var(--ink-dim);
    text-transform: uppercase;
    margin-bottom: 12px;
  }
  .item-desc {
    font-size: 14px;
    line-height: 1.4;
    color: var(--ink-dim);
    font-style: italic;
    margin-bottom: 16px;
    flex-grow: 1;
  }
  .item-price {
    border-top: 1px solid var(--line);
    padding-top: 12px;
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
  }
  .price-stack { display: flex; flex-direction: column; }
  .price-base {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: var(--ink-dim);
    text-decoration: line-through;
  }
  .price-final {
    font-family: 'Cinzel', serif;
    font-size: 22px;
    font-weight: 700;
    color: var(--gold-bright);
    letter-spacing: 0.03em;
  }
  .price-final::after {
    content: " $PvE";
    font-size: 11px;
    color: var(--ink-dim);
    font-family: 'JetBrains Mono', monospace;
    letter-spacing: 0.15em;
  }
  .buy-btn {
    background: #000;
    border: 1px solid var(--gold);
    color: var(--gold);
    padding: 8px 14px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s;
  }
  .buy-btn:hover { background: var(--gold); color: var(--bg-0); }
  .buy-btn:disabled { opacity: 0.3; cursor: not-allowed; }
  .buy-btn.sold { border-color: var(--rust); color: var(--rust); }

  .overlay {
    position: fixed;
    inset: 0;
    background: rgba(10,9,8,0.85);
    backdrop-filter: blur(6px);
    display: none;
    align-items: center;
    justify-content: center;
    z-index: 100;
    padding: 24px;
  }
  .overlay.show { display: flex; }
  .modal {
    background: var(--bg-1);
    border: 1px solid var(--gold);
    max-width: 520px;
    width: 100%;
    padding: 32px;
    position: relative;
    max-height: 90vh;
    overflow-y: auto;
  }
  .modal::before {
    content: "✦ ✦ ✦";
    position: absolute;
    top: -10px; left: 50%;
    transform: translateX(-50%);
    background: var(--bg-1);
    padding: 0 12px;
    color: var(--gold);
    font-size: 12px;
    letter-spacing: 0.4em;
  }
  .modal h2 {
    font-family: 'Cinzel', serif;
    font-size: 22px;
    color: var(--gold-bright);
    margin-bottom: 8px;
    text-align: center;
  }
  .modal p {
    color: var(--ink-dim);
    text-align: center;
    margin-bottom: 24px;
    font-style: italic;
  }
  .modal-row {
    display: flex;
    justify-content: space-between;
    padding: 12px 0;
    border-bottom: 1px dashed var(--line);
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
  }
  .modal-row span:first-child { color: var(--ink-dim); }
  .modal-row span:last-child { color: var(--ink); }
  .modal-row.total {
    border-bottom: none;
    border-top: 1px solid var(--gold);
    margin-top: 8px;
    padding-top: 16px;
  }
  .modal-row.total span:last-child {
    color: var(--gold-bright);
    font-size: 18px;
    font-weight: 700;
  }
  .modal-actions {
    display: flex;
    gap: 12px;
    margin-top: 24px;
  }
  .modal-actions .btn { flex: 1; }
  .btn-cancel { border-color: var(--line); color: var(--ink-dim); }
  .btn-cancel:hover { background: var(--bg-2); color: var(--ink); }

  .vote-warning {
    background: rgba(107,26,26,0.15);
    border: 1px solid var(--rust);
    padding: 14px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: var(--ink);
    line-height: 1.6;
    margin-bottom: 18px;
  }
  .vote-warning b { color: var(--rust); letter-spacing: 0.15em; }
  .vote-warning ul {
    margin-top: 8px;
    padding-left: 18px;
  }
  .vote-warning li { margin-top: 4px; }

  .eligibility-check {
    display: grid;
    grid-template-columns: 1fr;
    gap: 8px;
    margin-bottom: 16px;
  }
  .check-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 14px;
    background: var(--bg-2);
    border-left: 2px solid var(--line);
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
  }
  .check-row.pass { border-left-color: var(--moss); }
  .check-row.fail { border-left-color: var(--rust); }
  .check-row .check-label { color: var(--ink-dim); }
  .check-row .check-val { color: var(--ink); }
  .check-row .icon {
    width: 16px;
    text-align: center;
    margin-right: 8px;
  }
  .pass .icon { color: var(--moss); }
  .fail .icon { color: var(--rust); }

  .list-form { display: flex; flex-direction: column; gap: 14px; }
  .list-form label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    color: var(--ink-dim);
    text-transform: uppercase;
    display: block;
    margin-bottom: 6px;
  }
  .list-form input, .list-form select, .list-form textarea {
    width: 100%;
    background: var(--bg-2);
    border: 1px solid var(--line);
    color: var(--ink);
    padding: 10px 12px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    transition: border-color 0.2s;
  }
  .list-form input:focus, .list-form select:focus, .list-form textarea:focus {
    outline: none;
    border-color: var(--gold);
  }
  .list-form textarea {
    font-family: 'Cormorant Garamond', serif;
    font-size: 15px;
    resize: vertical;
    min-height: 60px;
  }
  .price-preview {
    background: var(--bg-2);
    border-left: 2px solid var(--gold);
    padding: 12px 16px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    color: var(--ink-dim);
  }
  .price-preview .calc {
    color: var(--gold-bright);
    font-size: 16px;
    margin-top: 4px;
  }

  .toast {
    position: fixed;
    bottom: 24px;
    left: 50%;
    transform: translateX(-50%) translateY(100px);
    background: var(--bg-1);
    border: 1px solid var(--gold);
    color: var(--ink);
    padding: 14px 24px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    letter-spacing: 0.1em;
    z-index: 200;
    transition: transform 0.4s cubic-bezier(0.4,0,0.2,1);
    max-width: 90%;
    text-align: center;
  }
  .toast.show { transform: translateX(-50%) translateY(0); }
  .toast.error { border-color: var(--rust); color: var(--rust); }
  .toast.success { border-color: var(--moss); color: var(--moss); }

  .help {
    display: inline-block;
    width: 14px;
    height: 14px;
    border: 1px solid var(--ink-dim);
    border-radius: 50%;
    text-align: center;
    line-height: 12px;
    font-size: 10px;
    color: var(--ink-dim);
    cursor: help;
    font-family: 'JetBrains Mono', monospace;
  }

  .dev-panel {
    position: fixed;
    bottom: 16px;
    right: 16px;
    background: var(--bg-1);
    border: 1px solid var(--line);
    padding: 8px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    color: var(--ink-dim);
    z-index: 50;
  }
  .dev-panel button {
    background: #000;
    border: 1px solid var(--ink-dim);
    color: var(--ink-dim);
    padding: 4px 8px;
    margin-left: 4px;
    cursor: pointer;
    font-family: inherit;
    font-size: 9px;
    text-transform: uppercase;
    letter-spacing: 0.1em;
  }
  .dev-panel button:hover { color: var(--gold); border-color: var(--gold); }

  /* WALLET DRAWER */
  .drawer-backdrop {
    position: fixed;
    inset: 0;
    background: rgba(10,9,8,0.7);
    backdrop-filter: blur(4px);
    z-index: 150;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.3s;
  }
  .drawer-backdrop.show {
    opacity: 1;
    pointer-events: auto;
  }
  .drawer {
    position: fixed;
    top: 0; right: 0;
    bottom: 0;
    width: 100%;
    max-width: 480px;
    background: var(--bg-0);
    border-left: 1px solid var(--gold);
    z-index: 160;
    transform: translateX(100%);
    transition: transform 0.35s cubic-bezier(0.4, 0, 0.2, 1);
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }
  .drawer.show { transform: translateX(0); }

  .drawer-header {
    padding: 24px;
    border-bottom: 1px solid var(--gold);
    background:
      linear-gradient(180deg, rgba(201,162,74,0.08) 0%, transparent 100%),
      var(--bg-1);
    position: relative;
  }
  .drawer-close {
    position: absolute;
    top: 18px;
    right: 18px;
    background: #000;
    border: 1px solid var(--ink-dim);
    color: var(--ink-dim);
    width: 28px;
    height: 28px;
    cursor: pointer;
    font-family: 'JetBrains Mono', monospace;
    font-size: 14px;
    transition: all 0.2s;
  }
  .drawer-close:hover {
    border-color: var(--gold);
    color: var(--gold);
  }
  .drawer-title {
    font-family: 'Cinzel', serif;
    font-size: 18px;
    color: var(--gold-bright);
    letter-spacing: 0.2em;
    font-weight: 700;
    margin-bottom: 4px;
  }
  .drawer-addr {
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: var(--ink-dim);
    margin-bottom: 18px;
  }
  .drawer-balance {
    font-family: 'Cinzel', serif;
    font-size: 44px;
    font-weight: 900;
    color: var(--gold-bright);
    line-height: 1;
    margin-bottom: 4px;
  }
  .drawer-balance::before {
    content: "◈ ";
    color: var(--gold);
    font-size: 32px;
  }
  .drawer-balance-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.3em;
    color: var(--ink-dim);
    text-transform: uppercase;
  }

  .drawer-stats {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1px;
    background: var(--line);
    border-bottom: 1px solid var(--line);
  }
  .drawer-stat {
    background: var(--bg-1);
    padding: 14px 12px;
    text-align: center;
    font-family: 'JetBrains Mono', monospace;
  }
  .drawer-stat-v {
    font-size: 16px;
    color: var(--gold-bright);
    font-weight: 700;
  }
  .drawer-stat-k {
    font-size: 9px;
    letter-spacing: 0.2em;
    color: var(--ink-dim);
    text-transform: uppercase;
    margin-top: 4px;
  }

  .drawer-tabs {
    display: flex;
    border-bottom: 1px solid var(--line);
    background: var(--bg-1);
  }
  .drawer-tab {
    flex: 1;
    padding: 14px;
    background: #000;
    border: none;
    color: var(--ink-dim);
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    cursor: pointer;
    border-bottom: 2px solid transparent;
    transition: all 0.2s;
  }
  .drawer-tab:hover { color: var(--ink); }
  .drawer-tab.active {
    color: var(--gold-bright);
    border-bottom-color: var(--gold);
  }
  .drawer-tab .count {
    margin-left: 4px;
    color: var(--gold);
    font-weight: 700;
  }

  .drawer-body {
    flex: 1;
    overflow-y: auto;
    padding: 20px;
  }

  /* INVENTORY */
  .inv-empty {
    text-align: center;
    padding: 60px 24px;
    color: var(--ink-dim);
    font-family: 'Cormorant Garamond', serif;
    font-style: italic;
    font-size: 16px;
    line-height: 1.5;
  }
  .inv-empty .icon {
    font-size: 36px;
    color: var(--gold);
    display: block;
    margin-bottom: 12px;
    opacity: 0.5;
  }

  .inv-item {
    display: grid;
    grid-template-columns: 64px 1fr auto;
    gap: 14px;
    padding: 14px;
    background: var(--bg-1);
    border: 1px solid var(--line);
    margin-bottom: 10px;
    align-items: center;
    transition: all 0.2s;
  }
  .inv-item:hover { border-color: var(--gold); }
  .inv-art {
    width: 64px;
    height: 64px;
    background: var(--bg-2);
    border: 1px solid var(--line);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .inv-art svg { width: 90%; height: 90%; }
  .inv-info { min-width: 0; }
  .inv-name {
    font-family: 'Cinzel', serif;
    font-size: 14px;
    font-weight: 700;
    color: var(--ink);
    margin-bottom: 2px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .inv-meta {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--ink-dim);
    margin-bottom: 4px;
  }
  .inv-paid {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: var(--ink-dim);
  }
  .inv-paid .v { color: var(--gold); }
  .inv-action {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }
  .inv-sell-btn {
    background: #000;
    border: 1px solid var(--moss);
    color: var(--moss);
    padding: 8px 14px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s;
    white-space: nowrap;
  }
  .inv-sell-btn:hover {
    background: var(--moss);
    color: var(--bg-0);
  }
  .inv-sell-btn.listed {
    border-color: var(--gold);
    color: var(--gold);
  }
  .inv-sell-btn.listed:hover {
    background: var(--rust);
    color: var(--bg-0);
    border-color: var(--rust);
  }

  /* TRANSACTION HISTORY */
  .tx-row {
    display: grid;
    grid-template-columns: auto 1fr auto;
    gap: 12px;
    padding: 12px 0;
    border-bottom: 1px dashed var(--line);
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    align-items: center;
  }
  .tx-row:last-child { border-bottom: none; }
  .tx-icon {
    width: 28px;
    height: 28px;
    border: 1px solid;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 14px;
  }
  .tx-icon.in { color: var(--moss); border-color: var(--moss); }
  .tx-icon.out { color: var(--rust); border-color: var(--rust); }
  .tx-icon.list { color: var(--gold); border-color: var(--gold); }
  .tx-info .tx-label {
    color: var(--ink);
    font-size: 12px;
  }
  .tx-info .tx-time {
    color: var(--ink-dim);
    font-size: 9px;
    margin-top: 2px;
    letter-spacing: 0.1em;
  }
  .tx-amt {
    font-weight: 700;
    text-align: right;
  }
  .tx-amt.in { color: var(--moss); }
  .tx-amt.out { color: var(--rust); }
  .tx-amt.neutral { color: var(--ink-dim); }

  /* SELL MODAL */
  .sell-preview {
    background: var(--bg-2);
    border: 1px solid var(--line);
    padding: 14px;
    display: grid;
    grid-template-columns: 64px 1fr;
    gap: 14px;
    align-items: center;
    margin-bottom: 18px;
  }
  .sell-preview .art {
    width: 64px;
    height: 64px;
    background: var(--bg-1);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .sell-preview .art svg { width: 90%; height: 90%; }
  .sell-preview .info {
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: var(--ink-dim);
  }
  .sell-preview .info .nm {
    font-family: 'Cinzel', serif;
    font-size: 15px;
    color: var(--ink);
    margin-bottom: 4px;
  }

  /* REPORT BUTTON & UNDER REVIEW */
  .item-report {
    position: absolute;
    bottom: 10px;
    right: 10px;
    background: rgba(0,0,0,0.75);
    border: 1px solid var(--line);
    color: var(--ink-dim);
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.1em;
    padding: 3px 7px;
    cursor: pointer;
    transition: all 0.2s;
    text-transform: uppercase;
    backdrop-filter: blur(4px);
  }
  .item-report:hover { border-color: var(--rust); color: var(--rust); }
  .item-report.reported { border-color: var(--rust); color: var(--rust); cursor: default; }

  .item.under-review {
    border-color: var(--rust) !important;
    opacity: 0.6;
    pointer-events: none;
  }
  .review-overlay {
    position: absolute;
    inset: 0;
    background: rgba(107,26,26,0.85);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 8px;
    z-index: 10;
    backdrop-filter: blur(2px);
  }
  .review-overlay .review-icon { font-size: 28px; }
  .review-overlay .review-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    letter-spacing: 0.2em;
    color: var(--rust);
    text-transform: uppercase;
    font-weight: 700;
  }
  .review-overlay .review-sub {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    color: var(--ink-dim);
    letter-spacing: 0.1em;
  }

  /* AUDIO UPLOAD */
  .audio-upload-zone {
    border: 1px dashed var(--gold);
    background: var(--bg-2);
    padding: 16px;
    text-align: center;
    cursor: pointer;
    transition: all 0.2s;
    position: relative;
  }
  .audio-upload-zone:hover { border-color: var(--gold-bright); background: var(--bg-1); }
  .audio-upload-zone.has-audio { border-style: solid; border-color: var(--moss); }
  .audio-upload-zone input[type="file"] {
    position: absolute; inset: 0; opacity: 0; cursor: pointer; width: 100%; height: 100%;
  }
  .audio-upload-zone .upload-prompt {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px; letter-spacing: 0.2em; color: var(--ink-dim);
    text-transform: uppercase; pointer-events: none;
  }

  /* MUSIC CARD */
  .item.is-music { border-color: rgba(201,162,74,0.3); }
  .item.is-music:hover { border-color: var(--gold); }
  .music-card-art {
    background: var(--bg-0);
    position: relative;
    overflow: hidden;
    aspect-ratio: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 10px;
  }
  .music-card-art .big-note { font-size: 48px; line-height: 1; color: var(--gold); opacity: 0.6; }
  .waveform-canvas { width: 100%; height: 44px; display: block; }
  .music-controls {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 10px 14px;
    background: var(--bg-2);
    border-top: 1px solid var(--line);
  }
  .play-btn {
    background: var(--gold);
    border: none;
    color: var(--bg-0);
    width: 28px; height: 28px;
    font-size: 12px;
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    flex-shrink: 0;
    transition: background 0.15s;
  }
  .play-btn:hover { background: var(--gold-bright); }
  .play-btn.playing { background: var(--rust); }
  .track-info { flex: 1; min-width: 0; }
  .track-name {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px; color: var(--ink);
    white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
    letter-spacing: 0.05em;
  }
  .track-duration { font-family: 'JetBrains Mono', monospace; font-size: 9px; color: var(--ink-dim); margin-top: 2px; }
  .progress-bar { width: 100%; height: 2px; background: var(--bg-2); margin-top: 4px; cursor: pointer; position: relative; }
  .progress-fill { height: 100%; background: var(--gold); width: 0%; transition: width 0.1s linear; pointer-events: none; }

  .img-upload-zone {
    border: 1px dashed var(--gold);
    background: var(--bg-2);
    padding: 20px;
    text-align: center;
    cursor: pointer;
    transition: all 0.2s;
    position: relative;
  }
  .img-upload-zone:hover { border-color: var(--gold-bright); background: var(--bg-1); }
  .img-upload-zone.has-image { border-style: solid; border-color: var(--moss); padding: 0; }
  .img-upload-zone input[type="file"] {
    position: absolute; inset: 0; opacity: 0; cursor: pointer; width: 100%; height: 100%;
  }
  .img-upload-zone .upload-prompt {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    color: var(--ink-dim);
    text-transform: uppercase;
    pointer-events: none;
  }
  .img-upload-zone .upload-icon {
    font-size: 28px;
    color: var(--gold);
    display: block;
    margin-bottom: 8px;
    pointer-events: none;
  }
  .img-upload-zone img.preview {
    width: 100%;
    aspect-ratio: 1;
    object-fit: cover;
    display: block;
  }
  .img-upload-clear {
    background: rgba(161,68,38,0.8);
    border: none;
    color: var(--ink);
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    padding: 4px 10px;
    cursor: pointer;
    width: 100%;
    transition: background 0.2s;
  }
  .img-upload-clear:hover { background: var(--rust); }

  .ipfs-row {
    display: flex;
    gap: 6px;
    align-items: center;
  }
  .ipfs-row input {
    flex: 1;
    font-size: 11px !important;
  }
  .ipfs-row .ipfs-badge {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.15em;
    color: var(--ink-dim);
    border: 1px solid var(--line);
    padding: 4px 8px;
    white-space: nowrap;
  }
  .ipfs-row .ipfs-badge.linked { color: var(--moss); border-color: var(--moss); }

  /* Override item-art to show uploaded images */
  .item-art img, .inv-art img, .sell-preview .art img {
    width: 100%; height: 100%;
    object-fit: cover;
    display: block;
  }


  /* HERO SLIDESHOW */
  .hero-slideshow {
    position: relative;
    width: 100%;
    height: 420px;
    overflow: hidden;
    border: 1px solid var(--gold);
    border-bottom: none;
    margin-bottom: 0;
  }
  .hero-slide {
    position: absolute;
    inset: 0;
    opacity: 0;
    transition: opacity 1.2s ease;
    display: flex;
    align-items: center;
  }
  .hero-slide.active { opacity: 1; }
  .hero-img {
    position: absolute;
    inset: 0;
    background-size: cover;
    background-position: center top;
    transform: scale(1.06);
    transition: transform 8s ease;
    filter: brightness(0.42);
  }
  .hero-slide.active .hero-img { transform: scale(1.0); }
  .hero-vignette {
    position: absolute;
    inset: 0;
    background:
      linear-gradient(to right, rgba(10,9,8,0.88) 0%, rgba(10,9,8,0.4) 55%, rgba(10,9,8,0.05) 100%),
      linear-gradient(to top, rgba(10,9,8,0.92) 0%, transparent 45%);
  }
  .hero-content {
    position: relative;
    z-index: 2;
    padding: 0 44px;
    max-width: 560px;
  }
  .hero-eyebrow {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.45em;
    text-transform: uppercase;
    color: var(--gold);
    margin-bottom: 14px;
    opacity: 0;
    transform: translateY(14px);
    transition: opacity 0.7s 0.2s ease, transform 0.7s 0.2s ease;
  }
  .hero-slide.active .hero-eyebrow { opacity: 1; transform: translateY(0); }
  .hero-title {
    font-family: 'Cinzel', serif;
    font-size: clamp(26px, 3.8vw, 46px);
    font-weight: 900;
    color: var(--ink);
    line-height: 1.1;
    margin-bottom: 18px;
    opacity: 0;
    transform: translateY(18px);
    transition: opacity 0.7s 0.4s ease, transform 0.7s 0.4s ease;
  }
  .hero-slide.active .hero-title { opacity: 1; transform: translateY(0); }
  .hero-title span { color: var(--gold-bright); }
  .hero-body {
    font-family: 'Cormorant Garamond', serif;
    font-size: 17px;
    font-style: italic;
    color: var(--ink-dim);
    line-height: 1.6;
    margin-bottom: 26px;
    max-width: 400px;
    opacity: 0;
    transform: translateY(12px);
    transition: opacity 0.6s 0.65s ease, transform 0.6s 0.65s ease;
  }
  .hero-slide.active .hero-body { opacity: 1; transform: translateY(0); }
  .hero-cta {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    opacity: 0;
    transform: translateY(10px);
    transition: opacity 0.5s 0.85s ease, transform 0.5s 0.85s ease;
  }
  .hero-slide.active .hero-cta { opacity: 1; transform: translateY(0); }
  .hero-btn {
    padding: 12px 24px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    cursor: pointer;
    border: 1px solid var(--gold);
    background: var(--gold);
    color: var(--bg-0);
    font-weight: 700;
    transition: all 0.2s;
  }
  .hero-btn:hover { background: var(--gold-bright); border-color: var(--gold-bright); }
  .hero-btn.outline { background: #000; color: var(--gold); }
  .hero-btn.outline:hover { background: rgba(201,162,74,0.12); }
  .hero-dots {
    position: absolute;
    bottom: 22px;
    left: 44px;
    display: flex;
    gap: 8px;
    z-index: 3;
  }
  .hero-dot {
    width: 24px; height: 2px;
    background: rgba(232,220,196,0.3);
    cursor: pointer;
    border: none;
    padding: 0;
    transition: all 0.35s;
  }
  .hero-dot.active { background: var(--gold); width: 44px; }
  .hero-stats {
    position: absolute;
    bottom: 0; right: 0;
    display: flex;
    z-index: 3;
  }
  .hero-stat {
    padding: 12px 18px;
    background: rgba(10,9,8,0.82);
    border-top: 1px solid var(--line);
    border-left: 1px solid var(--line);
    text-align: center;
    min-width: 80px;
  }
  .hero-stat-v {
    font-family: 'Cinzel', serif;
    font-size: 20px;
    font-weight: 700;
    color: var(--gold-bright);
    line-height: 1;
  }
  .hero-stat-k {
    font-family: 'JetBrains Mono', monospace;
    font-size: 8px;
    letter-spacing: 0.2em;
    color: var(--ink-dim);
    text-transform: uppercase;
    margin-top: 4px;
  }
  @media (max-width: 600px) {
    .hero-slideshow { height: 320px; }
    .hero-content { padding: 0 20px; max-width: 100%; }
    .hero-stats { display: none; }
    .hero-dots { left: 20px; }
  }/* ── ENTRY SCREEN ─────────────────────────────────────────────────── */
  #entryScreen {
    position: fixed;
    inset: 0;
    z-index: 99999;
    background: #000;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    cursor: none;
  }
  #matrixCanvas {
    position: absolute;
    inset: 0;
    opacity: 0.55;
  }
  .entry-content {
    position: relative;
    z-index: 2;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 32px;
    animation: entryFadeIn 1.8s ease forwards;
  }
  @keyframes entryFadeIn {
    from { opacity: 0; transform: translateY(30px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  .entry-logo {
    font-family: 'Cinzel', serif;
    font-size: clamp(28px, 6vw, 80px);
    font-weight: 900;
    letter-spacing: 0.2em;
    color: #c9a24a;
    text-shadow: 0 0 40px rgba(201,162,74,0.8), 0 0 80px rgba(201,162,74,0.4);
    animation: logoPulse 3s ease-in-out infinite;
    text-align: center;
    line-height: 1.25;
  }
  @keyframes logoPulse {
    0%,100% { text-shadow: 0 0 40px rgba(201,162,74,0.8),0 0 80px rgba(201,162,74,0.4); }
    50%      { text-shadow: 0 0 60px rgba(201,162,74,1),0 0 120px rgba(201,162,74,0.6),0 0 180px rgba(161,68,38,0.4); }
  }
  .entry-sub {
    font-family: 'JetBrains Mono', monospace;
    font-size: clamp(10px, 1.5vw, 13px);
    letter-spacing: 0.5em;
    color: rgba(201,162,74,0.6);
    text-transform: uppercase;
    animation: entryFadeIn 2.5s ease forwards;
  }
  .entry-line {
    width: clamp(200px, 40vw, 400px);
    height: 1px;
    background: linear-gradient(90deg, transparent, #c9a24a, transparent);
    opacity: 0.6;
  }
  .enter-btn {
    position: relative;
    padding: 18px 56px;
    font-family: 'Cinzel', serif;
    font-size: clamp(13px, 2vw, 17px);
    font-weight: 700;
    letter-spacing: 0.4em;
    text-transform: uppercase;
    color: #0a0908;
    background: #c9a24a;
    border: none;
    cursor: pointer;
    overflow: hidden;
    transition: all 0.3s;
    clip-path: polygon(12px 0%, 100% 0%, calc(100% - 12px) 100%, 0% 100%);
    animation: entryFadeIn 3s ease forwards;
  }
  .enter-btn::before {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(90deg, transparent 0%, rgba(255,255,255,0.35) 50%, transparent 100%);
    transform: translateX(-100%);
    transition: transform 0.6s ease;
  }
  .enter-btn:hover { background: #f0c878; transform: scale(1.04); box-shadow: 0 0 40px rgba(201,162,74,0.6); }
  .enter-btn:hover::before { transform: translateX(100%); }
  .enter-btn:active { transform: scale(0.97); }
  .enter-btn-ring {
    position: absolute;
    inset: -8px;
    border: 1px solid rgba(201,162,74,0.4);
    clip-path: polygon(16px 0%, 100% 0%, calc(100% - 16px) 100%, 0% 100%);
    animation: ringPulse 2s ease-in-out infinite;
    pointer-events: none;
  }
  @keyframes ringPulse {
    0%,100% { opacity: 0.3; }
    50%      { opacity: 0.8; }
  }
  .entry-glyph {
    font-family: 'JetBrains Mono', monospace;
    font-size: clamp(11px, 2.5vw, 14px);
    font-weight: 700;
    color: rgba(201,162,74,0.75);
    letter-spacing: 0.12em;
    text-align: center;
    max-width: 360px;
    line-height: 2.2;
    animation: entryFadeIn 3.5s ease forwards;
    text-shadow: 0 0 12px rgba(201,162,74,0.3);
  }
  /* Entry exit animation */
  .entry-exit {
    animation: entryExit 1.2s cubic-bezier(0.4,0,0.2,1) forwards !important;
  }
  @keyframes entryExit {
    0%   { opacity: 1; transform: scale(1); filter: blur(0); }
    60%  { opacity: 1; transform: scale(1.04); filter: blur(0); }
    100% { opacity: 0; transform: scale(1.5); filter: blur(20px); pointer-events: none; }
  }

  /* ── DYNAMIC PAGE EFFECTS ─────────────────────────────────────────── */
  #particleCanvas {
    position: fixed;
    inset: 0;
    pointer-events: none;
    z-index: 0;
    opacity: 0.35;
  }
  .wrap { position: relative; z-index: 1; }

  /* Scroll-reveal: sections start invisible */
  .reveal {
    opacity: 0;
    transform: translateY(28px);
    transition: opacity 0.7s ease, transform 0.7s ease;
  }
  .reveal.visible { opacity: 1; transform: translateY(0); }
  .reveal-left {
    opacity: 0;
    transform: translateX(-32px);
    transition: opacity 0.7s ease, transform 0.7s ease;
  }
  .reveal-left.visible { opacity: 1; transform: translateX(0); }
  .reveal-right {
    opacity: 0;
    transform: translateX(32px);
    transition: opacity 0.7s ease, transform 0.7s ease;
  }
  .reveal-right.visible { opacity: 1; transform: translateX(0); }

  /* Stagger children */
  .reveal-stagger > * {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.5s ease, transform 0.5s ease;
  }
  .reveal-stagger.visible > *:nth-child(1) { opacity:1; transform:none; transition-delay:0s; }
  .reveal-stagger.visible > *:nth-child(2) { opacity:1; transform:none; transition-delay:0.1s; }
  .reveal-stagger.visible > *:nth-child(3) { opacity:1; transform:none; transition-delay:0.2s; }
  .reveal-stagger.visible > *:nth-child(4) { opacity:1; transform:none; transition-delay:0.3s; }
  .reveal-stagger.visible > *:nth-child(5) { opacity:1; transform:none; transition-delay:0.4s; }
  .reveal-stagger.visible > *:nth-child(6) { opacity:1; transform:none; transition-delay:0.5s; }
  .reveal-stagger.visible > *:nth-child(7) { opacity:1; transform:none; transition-delay:0.6s; }
  .reveal-stagger.visible > *:nth-child(8) { opacity:1; transform:none; transition-delay:0.7s; }
  .reveal-stagger.visible > *:nth-child(9) { opacity:1; transform:none; transition-delay:0.8s; }

  /* Gold cursor trail dot */
  .cursor-trail {
    position: fixed;
    width: 6px;
    height: 6px;
    background: radial-gradient(circle, #f0c878 0%, #c9a24a 50%, transparent 100%);
    border-radius: 50%;
    pointer-events: none;
    z-index: 99998;
    transform: translate(-50%, -50%);
    transition: opacity 0.5s;
    mix-blend-mode: screen;
  }

  /* Item card hover lift */
  .item {
    transition: transform 0.25s ease, box-shadow 0.25s ease, border-color 0.25s ease;
  }
  .item:hover {
    transform: translateY(-4px);
    box-shadow: 0 12px 40px rgba(201,162,74,0.15);
  }

  /* Ticker glow on scroll */
  .ticker { transition: opacity 0.3s; }

  /* Header ambient glow */
  header::before {
    content: '';
    position: absolute;
    bottom: -1px; left: 0; right: 0;
    height: 1px;
    background: linear-gradient(90deg, transparent, rgba(201,162,74,0.5), transparent);
    animation: headerGlow 4s ease-in-out infinite;
  }
  @keyframes headerGlow {
    0%,100% { opacity:0.4; }
    50% { opacity:1; }
  }
  header { position: relative; overflow: visible; }


  .sort-btn {
    padding: 5px 14px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    background: var(--bg-2);
    border: 1px solid var(--line);
    color: var(--ink-dim);
    cursor: pointer;
    transition: all 0.2s;
  }
  .sort-btn:hover { border-color: var(--gold); color: var(--gold); }
  .sort-btn.active { background: var(--gold); border-color: var(--gold); color: var(--bg-0); font-weight: 700; }

  /* ── THE EXCHANGE (Secondary Market) ───────────────────────────────── */
  .exchange-section {
    margin-top: 40px;
    margin-bottom: 16px;
  }
  .exchange-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border: 1px solid var(--line);
    border-bottom: none;
    padding: 14px 20px;
    background: var(--bg-1);
    flex-wrap: wrap;
    gap: 10px;
  }
  .exchange-title {
    font-family: 'Cinzel', serif;
    font-size: 18px;
    font-weight: 700;
    color: var(--ink);
    letter-spacing: 0.1em;
  }
  .exchange-subtitle {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    color: var(--ink-dim);
    letter-spacing: 0.2em;
    text-transform: uppercase;
    margin-top: 3px;
  }
  .exchange-badge {
    display: flex;
    align-items: center;
    gap: 8px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: var(--moss);
    letter-spacing: 0.1em;
  }
  .exchange-badge span {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--moss);
    box-shadow: 0 0 6px var(--moss);
    animation: pulse 2s infinite;
  }
  .exchange-grid {
    border: 1px solid var(--line);
    background: var(--bg-1);
  }
  /* Exchange item rows (list view, not grid) */
  .ex-item {
    display: grid;
    grid-template-columns: 52px 1fr auto auto auto;
    align-items: center;
    gap: 14px;
    padding: 14px 20px;
    border-bottom: 1px solid var(--line);
    transition: background 0.15s;
  }
  .ex-item:last-child { border-bottom: none; }
  .ex-item:hover { background: var(--bg-2); }
  .ex-art {
    width: 52px; height: 52px;
    overflow: hidden;
    flex-shrink: 0;
    border: 1px solid var(--line);
  }
  .ex-art canvas, .ex-art img { width: 100%; height: 100%; object-fit: cover; display: block; }
  .ex-name {
    font-family: 'Cinzel', serif;
    font-size: 13px;
    font-weight: 700;
    color: var(--ink);
    margin-bottom: 3px;
  }
  .ex-meta {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    color: var(--ink-dim);
    letter-spacing: 0.1em;
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    align-items: center;
  }
  .ex-meta .frozen { color: var(--gold); }
  .ex-meta .editions { color: var(--ink-dim); }
  .ex-price-col {
    text-align: right;
    min-width: 90px;
  }
  .ex-ask {
    font-family: 'Cinzel', serif;
    font-size: 15px;
    font-weight: 700;
    color: var(--gold-bright);
  }
  .ex-ask-label {
    font-family: 'JetBrains Mono', monospace;
    font-size: 8px;
    color: var(--ink-dim);
    letter-spacing: 0.15em;
    text-transform: uppercase;
    margin-top: 2px;
  }
  .ex-last-col {
    text-align: right;
    min-width: 80px;
  }
  .ex-last-price {
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: var(--ink-dim);
  }
  .ex-change {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    margin-top: 2px;
  }
  .ex-change.up { color: var(--moss); }
  .ex-change.down { color: var(--rust); }
  .ex-change.flat { color: var(--ink-dim); }
  .ex-actions {
    display: flex;
    flex-direction: column;
    gap: 4px;
    min-width: 90px;
  }
  .ex-btn {
    padding: 6px 12px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    cursor: pointer;
    border: 1px solid var(--gold);
    background: #000;
    color: var(--gold);
    transition: all 0.15s;
    white-space: nowrap;
  }
  .ex-btn:hover { background: var(--gold); color: var(--bg-0); }
  .ex-btn.mm { border-color: var(--moss); color: var(--moss); }
  .ex-btn.mm:hover { background: var(--moss); color: var(--bg-0); }
  .ex-btn.relist { border-color: var(--ink-dim); color: var(--ink-dim); }
  .ex-empty {
    padding: 40px 20px;
    text-align: center;
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: var(--ink-dim);
    letter-spacing: 0.1em;
    border: 1px solid var(--line);
  }
  .ex-relist-row {
    display: none;
    grid-column: 1 / -1;
    padding: 12px 16px;
    background: var(--bg-2);
    border-top: 1px dashed var(--line);
    align-items: center;
    gap: 10px;
    flex-wrap: wrap;
  }
  .ex-relist-row.open { display: flex; }
  .ex-relist-input {
    flex: 1;
    min-width: 120px;
    padding: 8px 12px;
    background: var(--bg-0);
    border: 1px solid var(--gold);
    color: var(--ink);
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
  }
  .ex-relist-input:focus { outline: none; border-color: var(--gold-bright); }
  @media (max-width: 600px) {
    .ex-item { grid-template-columns: 40px 1fr auto; gap: 8px; padding: 10px 12px; }
    .ex-last-col { display: none; }
    .ex-actions { flex-direction: row; min-width: auto; }
  }


  /* ── SECONDARY MARKET / THE EXCHANGE ──────────────────────────────── */
  .exchange-section {
    margin-top: 40px;
    margin-bottom: 32px;
  }
  .exchange-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid var(--gold);
    padding-bottom: 12px;
    margin-bottom: 20px;
    gap: 12px;
    flex-wrap: wrap;
  }
  .exchange-title {
    font-family: 'Cinzel', serif;
    font-size: 22px;
    font-weight: 900;
    color: var(--gold-bright);
    letter-spacing: 0.15em;
  }
  .exchange-subtitle {
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: var(--ink-dim);
    letter-spacing: 0.2em;
    text-transform: uppercase;
  }
  .exchange-stats {
    display: flex;
    gap: 20px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    color: var(--ink-dim);
  }
  .exchange-stats span { color: var(--gold); font-weight: 700; }
  .exchange-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
    gap: 1px;
    background: var(--line);
  }
  .ex-card {
    background: var(--bg-1);
    display: flex;
    flex-direction: column;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
  }
  .ex-card:hover { transform: translateY(-3px); box-shadow: 0 8px 32px rgba(201,162,74,0.1); }
  .ex-art {
    aspect-ratio: 1;
    background: var(--bg-2);
    position: relative;
    overflow: hidden;
  }
  .ex-frozen-badge {
    position: absolute;
    top: 10px; right: 10px;
    background: rgba(10,9,8,0.85);
    border: 1px solid var(--gold);
    font-family: 'JetBrains Mono', monospace;
    font-size: 8px;
    letter-spacing: 0.2em;
    color: var(--gold);
    padding: 3px 7px;
    text-transform: uppercase;
  }
  .ex-migrated-badge {
    position: absolute;
    bottom: 10px; left: 10px;
    background: rgba(10,9,8,0.85);
    border: 1px solid var(--ink-dim);
    font-family: 'JetBrains Mono', monospace;
    font-size: 8px;
    letter-spacing: 0.1em;
    color: var(--ink-dim);
    padding: 3px 7px;
  }
  .ex-body {
    padding: 14px 16px;
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 6px;
  }
  .ex-name {
    font-family: 'Cinzel', serif;
    font-size: 14px;
    font-weight: 700;
    color: var(--ink);
    line-height: 1.2;
  }
  .ex-creator {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
    color: var(--ink-dim);
    letter-spacing: 0.1em;
  }
  .ex-desc {
    font-family: 'Cormorant Garamond', serif;
    font-size: 13px;
    font-style: italic;
    color: var(--ink-dim);
    line-height: 1.5;
    flex: 1;
  }
  .ex-price-row {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    gap: 8px;
    margin-top: 6px;
    padding-top: 10px;
    border-top: 1px dashed var(--line);
    flex-wrap: wrap;
  }
  .ex-price-stack { display: flex; flex-direction: column; gap: 2px; }
  .ex-frozen { font-family: 'JetBrains Mono', monospace; font-size: 9px; color: var(--ink-dim); text-decoration: line-through; }
  .ex-ask {
    font-family: 'Cinzel', serif;
    font-size: 18px;
    font-weight: 700;
    color: var(--ink);
  }
  .ex-change {
    font-family: 'JetBrains Mono', monospace;
    font-size: 9px;
  }
  .ex-change.up { color: var(--moss); }
  .ex-change.down { color: var(--rust); }
  .ex-history {
    font-family: 'JetBrains Mono', monospace;
    font-size: 8px;
    color: var(--ink-dim);
    letter-spacing: 0.05em;
  }
  .ex-actions { display: flex; gap: 6px; flex-wrap: wrap; margin-top: 8px; }
  .ex-btn {
    flex: 1;
    min-width: 80px;
    padding: 9px 8px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    background: var(--bg-2);
    border: 1px solid var(--gold);
    color: var(--gold);
    cursor: pointer;
    transition: all 0.2s;
  }
  .ex-btn:hover { background: var(--gold); color: var(--bg-0); }
  .ex-btn.offer { border-color: var(--moss); color: var(--moss); }
  .ex-btn.offer:hover { background: var(--moss); color: var(--bg-0); }
  .ex-btn.mm { border-color: var(--ink-dim); color: var(--ink-dim); font-size: 9px; }
  .ex-btn.mm:hover { background: var(--ink-dim); color: var(--bg-0); }
  .ex-btn.relist { border-color: var(--gold); }
  .ex-empty {
    grid-column: 1 / -1;
    padding: 48px 20px;
    text-align: center;
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    color: var(--ink-dim);
    letter-spacing: 0.15em;
  }
  /* Offer input inline */
  .ex-offer-row {
    display: flex;
    gap: 6px;
    margin-top: 6px;
    display: none;
  }
  .ex-offer-row.open { display: flex; }
  .ex-offer-input {
    flex: 1;
    background: var(--bg-2);
    border: 1px solid var(--gold);
    color: var(--ink);
    font-family: 'JetBrains Mono', monospace;
    font-size: 11px;
    padding: 7px 10px;
  }
  .ex-offer-send {
    padding: 7px 14px;
    background: var(--moss);
    border: none;
    color: #fff;
    font-family: 'JetBrains Mono', monospace;
    font-size: 10px;
    cursor: pointer;
    letter-spacing: 0.1em;
  }
  /* Migration flash */
  @keyframes migrateFlash {
    0%   { background: rgba(201,162,74,0.25); }
    100% { background: var(--bg-1); }
  }
  .ex-card.just-migrated { animation: migrateFlash 2s ease forwards; }

</style>
<script>
function enterBazaar() {
  var screen = document.getElementById('entryScreen');
  if (!screen) return;
  screen.classList.add('entry-exit');
  setTimeout(function() {
    screen.style.display = 'none';
    document.body.style.overflow = '';
    var hdr = document.getElementById('siteHeader');
    if (hdr) hdr.style.display = '';
    if (typeof startParticles === 'function') startParticles();
    if (typeof initScrollReveal === 'function') initScrollReveal();
    if (typeof updateHeroStats === 'function') updateHeroStats();
  }, 1200);
}
// Stub so early clicks on the wallet pill never throw before main script loads
function openWallet() {}
function openListModal() {}
</script>
</head>
<body>


<!-- ── MATRIX ENTRY SCREEN ─────────────────────────────────────────── -->
<div id="entryScreen">
  <canvas id="matrixCanvas"></canvas>
  <div class="entry-content">
    <div class="entry-sub">Welcome to</div>
    <div class="entry-logo">⚔ PvE SHOP<br>&amp; SPREE ⚔</div>
    <div class="entry-line"></div>
    <button class="enter-btn" id="enterBtn" onclick="enterBazaar()">
      <span class="enter-btn-ring"></span>
      ENTER THE SHOP
    </button>
    <div class="entry-glyph">
      ERC-1155 · OPTIMISM · ORACLE-PRICED<br>
      LP MARKET MAKER · COMMUNITY FORGED<br>
      ON-CHAIN FOREVER
    </div>
  </div>
</div>

<!-- Particle canvas (behind page content) -->
<canvas id="particleCanvas"></canvas>

<!-- Fixed wallet banner -->
<div id="siteHeader" style="display:none;">
  <div class="header-inner">
    <div>
      <div class="brand-mark">PvE <span>Shop & Spree</span></div>
      <div class="brand-sub">Player-Forged Goods · Settled in $PvE</div>
    </div>
    <div class="wallet-pill" onclick="openWallet()">
      <div class="wallet-pill-info">
        <div>0x7Af3…c91E</div>
        <div class="pill-votes" id="headerVotes">3 / 3 votes</div>
      </div>
      <div class="wallet-pill-balance" id="headerBalance">10,500</div>
      <div class="wallet-pill-open">WALLET ▸</div>
    </div>
  </div>
</div>

<div class="wrap">


  <!-- HERO SLIDESHOW -->
  <div class="hero-slideshow" id="heroSlideshow">

    <div class="hero-slide active" id="slide_0">
      <div class="hero-img" style="background-image:url('data:image/jpeg;base64,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9O8o1wVmus561tcwnSyK6Z2ZVtWaVtUziaxvth02WtwTZ14USzvjpc456ZzWls9NYiJitPS8v0k5Obu4jPPXJaLUTp6eHYvjrmTW0FYtkaK2hatisTAC1reITEkgmYmWbRaakZ6RatpqZiSZUzePfjnrw9rxPR87nsO3IAAAAAAAAAAAADs9X5/67n1tTow59e7Tzez0eTm4OvytZV54zvamZYRBrbCxoaLG2emdVmNMdM887Z100tjvnM5aazZatzbow67m3n+r5JmmJqK2HPF6kdvFNnTncmbpvXO24ktTNLvrhvZETFT3+d3p0eX73hplz9OE1FbRZG/PtLes1stOepSmlQiYTEkRMASgRJKkpeLyyTNxExjbPXnrXo5Oia04uzzGIHXiAAAAAAAAAAAAAAA93wunOvrsdcfP6uLDOfV46x6GNzxx088tYmFpFqglYmU1ptyybWzxxsTj1a5w6eS1otqKs06+7z+6428nv84iJiaVtUorquEbUuaEpVZVFiUaZlunl6hW2ZPbw9Z9F857XnXHnUvabxr0c1ldMbrtEwldcrFq3qZ2hLIRFqygBKmLAlZtFpqYmJqJTjcZa01Y0pvNZ8HZsz5o6+YAAAAAAAAAAAAAAAD6bLy/b8/t4O27t5PS5vUw6cPJ8z1/Nm+etoWkXharREWWmqxtWXGLxnrJbHpmmtevhx1jOulyXO3p87a525NKFUxEVtRc7i6VmTGb0ZrF61pOWtlefo50jr4+smthh1Uuvv8AV5fLrlfzuZNzTSCkhqreWtb5msUtZCYllExNbVBMRKVWm1ZpTVrwliM03pXOJq2ttc9KTa+N3y9L5rOaD1+IAAAAAAAAAAAAAAADr9HzteH0voeV0Z5dm3lb+rwU8r0fGsljE1sxG04QvVHPaN1E1FJcvYtXRZrLp44adGs+fTt57MG9JYvlU6oraIz1yK2xst75F1nK6VpfqueTTWlmOOua06efZOhGlnNfO65u+6ebOtStNamUXoRthvLFL1qumegWhKpgmLREWmVrabSkwudonOtMrZrCb53TafQ59M+azO3pxjw74eH6XB6/HQdvMAAAAAAAAAAAAAAAB165a8Pp29vwu6T1vOx5fR8yJtGmdbViCi3nKxes2TOL0mr3rbh9JtTS+eqY6eeevl01imbYrXtrrPn49WE015uiNePq5DNJ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gkQgGBBqAOX8iMErQp6/13gjKgyV7/8A888888888888zCr2ZicVHgcNgjKXkbcE067vMQfpCsTNp03RK+o7J0kA82bOAGqc888888888888CMIH+23iKJBp7fhuxgAeYXcIZmbJt6QI6AW10XZj8upRu36syIV3888888888888CCFeC6nfrqLn5TsB+eGkETsxn64CGk3t93EutAzx2JJJdeR85W8V888888888888DnaaQgtPgocL4VB4a6sZ22ubxbUG7Al+9ThvsnWvMnlNm6vySwRFe88888888888W8Gm2vzrdMgH2b/xcALGuySi5eC/akkURVu9oQY1QZbDLcRec5RB888888888888V81M4gbS+kEg3IEBQ9fsGeXd0R4cWbIWqwDq8+jfeo/TjHlXg9hW888888888888czODMPW4BlYUliDVQkb8VtFkpy9HATLMMiTB+L1iI7rxa13SgJ09Y88888888888SNOQYvxSIWnp0DUJOoPomTtgUNXXuqKT9B7OQvNlWDjqw9S46Okcc88888888888BeZBlCyP0tSx05Ykr1M7IZV1/wB4By7Gp0rcLgzEz8MpCI4uYEiyz/PPPPPPPPPPPGC2xBDC375xUTSe/wDj2LJcnxnyvFtJ8pytd05gUSvFtPpgaST+/rzzzzzzzzzzzzydhxuC2FUJ8IGYawTOXUehAV/VpS+8Lvisv0L6+heVJXr5ETRmhzzzzzzzzzzzzzy4nqE57OENrEEniWI3lufyckVs0Cv/AM8XnX/O2JhIyIHR1F9jWY8888888888888qSe29LExWaMi1vN9FI/YJVgRA9tLhkAbDfDTquoCt++UrwLVoC88888888888886cS5nfvw4w1q8KhME2To8qTGcbs1MUUZtDDrHKFRQCMwF391RqYoc88888888884oksV7tv1mzpYZQwmHYuRbq0nbL0C8bsRT6b3re9xHpVAffSoZ/B/8APPPPPPPPPPP2yWi1capFm75d30YEyk/lQwdrpHiohg1ENw+9ft4eOLTmgCcAlZfPPPPPPPPPPPORvXBe+j8arCeh6Hxn6PFdsJDBtVRoP7Kbr2JGZKDj3m5DbRxRO9fOMuvPPPPPPPPFMJTInQ7XZzeEQ479R+489qj3ofNrHIP60MLDy1ma6po58VoSkMvBwv8Azzzzzzzzxguy1/OTYn8w3Zxbjy3ZHpqNh7nukoVaj0jr/wD1ZbDIR+CzhFt8TN+88888888888vSfjcmYdTTx34skjm4WejgBKKEA560YlxeK1aS0gtkVLLSVGfhe88888888888888vPTGeobGAXU49ZbVpn5Y8vNJVhsU+gpvCaACgxNAsdzCF3XG88888888888888886fVqDK3SiR3gAd0ZF0cUdx4t7XaCjDOM9qY2zi2YpMeW7tAN8888888888888888sUjL6fYDzr4uymdASDOxhrGhrTT7NK6qGn70edLlV5vfyzLQ88888888888888888D8VRs5qPawU8X5GZayqWj4es9ZsrxdAeMsuf5gRqlCF9+hp88888888888888888A2LYeivFkAPDq3ipzv2kGz9P4cq5gau5Np8XHavS5n8bA4k88888888888888888ASAYNDFHL0wBs6ty+h6qP5TAWRE76+g0OOV81zWupJyZkRf88888888888888888ABh5ldMNvpWY/pxuaaQBuhjwHqU4j5btYsogfojHA0IujTd88888888888888888ABRTELWTNkRhOSPikgfNaU3Oo5bKjpl9bFCDgPcbVrj2TK/88888888888888884IIBizYU0zCBRmVSuZkwm6jx7iT2qRuyC5f5VBapCHq6S+JV8888888888888888QinBDJf4McviBRBzaCJuVi4yp7Z7Ii2BYbw2CUVLW4CDVW+U8888888888888888uloIZbPxza7KBBI4ITTThDBzGtrkd/tqUBBHxat7SvTpOW++88888888888888888h0EGWFd0CP2BBGrDBBBBBBXgK1tYQU+CBBBZ7JDc8P8A5kfvvPPPPPPPPPPPPPPPPAAd9DgXWMMKCxxywQQQQQQU0DIo6cwQQQQRY5tfvPOwFiffvPPPPPPPPPPPPPPPPAQAETRn2ye2wFigAAAAAAAAAAAAAAAAAAAAAEccccYUcQAPPPPPPPPPPPPPPPPPPPPPPIpFBbbm04B/PPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPGgYz4hqsp2b/vPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPFvMuYf/kcUfMufPPPPPPPPPPPPPPPPPPPPPPPP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');background-position:center top;"></div>
      <div class="hero-vignette"></div>
      <div class="hero-content">
        <div class="hero-eyebrow">⚔ Iron Arena · Season I</div>
        <div class="hero-title">Community-<span>Forged</span><br>Items. On-Chain.</div>
        <div class="hero-body">Every item minted here is an ERC-1155 NFT on Optimism — verifiable, tradeable, and yours forever.</div>
        <div class="hero-cta">
          <button class="hero-btn" onclick="document.getElementById('grid').scrollIntoView({behavior:'smooth'})">Browse Shop</button>
          <button class="hero-btn outline" onclick="openWallet()">My Wallet</button>
        </div>
      </div>
    </div>

    <div class="hero-slide" id="slide_1">
      <div class="hero-img" style="background-image:url('data:image/jpeg;base64,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9O8o1wVmus561tcwnSyK6Z2ZVtWaVtUziaxvth02WtwTZ14USzvjpc456ZzWls9NYiJitPS8v0k5Obu4jPPXJaLUTp6eHYvjrmTW0FYtkaK2hatisTAC1reITEkgmYmWbRaakZ6RatpqZiSZUzePfjnrw9rxPR87nsO3IAAAAAAAAAAAADs9X5/67n1tTow59e7Tzez0eTm4OvytZV54zvamZYRBrbCxoaLG2emdVmNMdM887Z100tjvnM5aazZatzbow67m3n+r5JmmJqK2HPF6kdvFNnTncmbpvXO24ktTNLvrhvZETFT3+d3p0eX73hplz9OE1FbRZG/PtLes1stOepSmlQiYTEkRMASgRJKkpeLyyTNxExjbPXnrXo5Oia04uzzGIHXiAAAAAAAAAAAAAAA93wunOvrsdcfP6uLDOfV46x6GNzxx088tYmFpFqglYmU1ptyybWzxxsTj1a5w6eS1otqKs06+7z+6428nv84iJiaVtUorquEbUuaEpVZVFiUaZlunl6hW2ZPbw9Z9F857XnXHnUvabxr0c1ldMbrtEwldcrFq3qZ2hLIRFqygBKmLAlZtFpqYmJqJTjcZa01Y0pvNZ8HZsz5o6+YAAAAAAAAAAAAAAAD6bLy/b8/t4O27t5PS5vUw6cPJ8z1/Nm+etoWkXharREWWmqxtWXGLxnrJbHpmmtevhx1jOulyXO3p87a525NKFUxEVtRc7i6VmTGb0ZrF61pOWtlefo50jr4+smthh1Uuvv8AV5fLrlfzuZNzTSCkhqreWtb5msUtZCYllExNbVBMRKVWm1ZpTVrwliM03pXOJq2ttc9KTa+N3y9L5rOaD1+IAAAAAAAAAAAAAAADr9HzteH0voeV0Z5dm3lb+rwU8r0fGsljE1sxG04QvVHPaN1E1FJcvYtXRZrLp44adGs+fTt57MG9JYvlU6oraIz1yK2xst75F1nK6VpfqueTTWlmOOua06efZOhGlnNfO65u+6ebOtStNamUXoRthvLFL1qumegWhKpgmLREWmVrabSkwudonOtMrZrCb53TafQ59M+azO3pxjw74eH6XB6/HQdvMAAAAAAAAAAAAAAAB165a8Pp29vwu6T1vOx5fR8yJtGmdbViCi3nKxes2TOL0mr3rbh9JtTS+eqY6eeevl01imbYrXtrrPn49WE015uiNePq5DNJ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gkQgGBBqAOX8iMErQp6/13gjKgyV7/8A888888888888zCr2ZicVHgcNgjKXkbcE067vMQfpCsTNp03RK+o7J0kA82bOAGqc888888888888CMIH+23iKJBp7fhuxgAeYXcIZmbJt6QI6AW10XZj8upRu36syIV3888888888888CCFeC6nfrqLn5TsB+eGkETsxn64CGk3t93EutAzx2JJJdeR85W8V888888888888DnaaQgtPgocL4VB4a6sZ22ubxbUG7Al+9ThvsnWvMnlNm6vySwRFe88888888888W8Gm2vzrdMgH2b/xcALGuySi5eC/akkURVu9oQY1QZbDLcRec5RB888888888888V81M4gbS+kEg3IEBQ9fsGeXd0R4cWbIWqwDq8+jfeo/TjHlXg9hW888888888888czODMPW4BlYUliDVQkb8VtFkpy9HATLMMiTB+L1iI7rxa13SgJ09Y88888888888SNOQYvxSIWnp0DUJOoPomTtgUNXXuqKT9B7OQvNlWDjqw9S46Okcc88888888888BeZBlCyP0tSx05Ykr1M7IZV1/wB4By7Gp0rcLgzEz8MpCI4uYEiyz/PPPPPPPPPPPGC2xBDC375xUTSe/wDj2LJcnxnyvFtJ8pytd05gUSvFtPpgaST+/rzzzzzzzzzzzzydhxuC2FUJ8IGYawTOXUehAV/VpS+8Lvisv0L6+heVJXr5ETRmhzzzzzzzzzzzzzy4nqE57OENrEEniWI3lufyckVs0Cv/AM8XnX/O2JhIyIHR1F9jWY8888888888888qSe29LExWaMi1vN9FI/YJVgRA9tLhkAbDfDTquoCt++UrwLVoC88888888888886cS5nfvw4w1q8KhME2To8qTGcbs1MUUZtDDrHKFRQCMwF391RqYoc88888888884oksV7tv1mzpYZQwmHYuRbq0nbL0C8bsRT6b3re9xHpVAffSoZ/B/8APPPPPPPPPPP2yWi1capFm75d30YEyk/lQwdrpHiohg1ENw+9ft4eOLTmgCcAlZfPPPPPPPPPPPORvXBe+j8arCeh6Hxn6PFdsJDBtVRoP7Kbr2JGZKDj3m5DbRxRO9fOMuvPPPPPPPPFMJTInQ7XZzeEQ479R+489qj3ofNrHIP60MLDy1ma6po58VoSkMvBwv8Azzzzzzzzxguy1/OTYn8w3Zxbjy3ZHpqNh7nukoVaj0jr/wD1ZbDIR+CzhFt8TN+88888888888vSfjcmYdTTx34skjm4WejgBKKEA560YlxeK1aS0gtkVLLSVGfhe88888888888888vPTGeobGAXU49ZbVpn5Y8vNJVhsU+gpvCaACgxNAsdzCF3XG88888888888888886fVqDK3SiR3gAd0ZF0cUdx4t7XaCjDOM9qY2zi2YpMeW7tAN8888888888888888sUjL6fYDzr4uymdASDOxhrGhrTT7NK6qGn70edLlV5vfyzLQ88888888888888888D8VRs5qPawU8X5GZayqWj4es9ZsrxdAeMsuf5gRqlCF9+hp88888888888888888A2LYeivFkAPDq3ipzv2kGz9P4cq5gau5Np8XHavS5n8bA4k88888888888888888ASAYNDFHL0wBs6ty+h6qP5TAWRE76+g0OOV81zWupJyZkRf88888888888888888ABh5ldMNvpWY/pxuaaQBuhjwHqU4j5btYsogfojHA0IujTd88888888888888888ABRTELWTNkRhOSPikgfNaU3Oo5bKjpl9bFCDgPcbVrj2TK/88888888888888884IIBizYU0zCBRmVSuZkwm6jx7iT2qRuyC5f5VBapCHq6S+JV8888888888888888QinBDJf4McviBRBzaCJuVi4yp7Z7Ii2BYbw2CUVLW4CDVW+U8888888888888888uloIZbPxza7KBBI4ITTThDBzGtrkd/tqUBBHxat7SvTpOW++88888888888888888h0EGWFd0CP2BBGrDBBBBBBXgK1tYQU+CBBBZ7JDc8P8A5kfvvPPPPPPPPPPPPPPPPAAd9DgXWMMKCxxywQQQQQQU0DIo6cwQQQQRY5tfvPOwFiffvPPPPPPPPPPPPPPPPAQAETRn2ye2wFigAAAAAAAAAAAAAAAAAAAAAEccccYUcQAPPPPPPPPPPPPPPPPPPPPPPIpFBbbm04B/PPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPGgYz4hqsp2b/vPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPFvMuYf/kcUfMufPPPPPPPPPPPPPPPPPPPPPPPP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');background-position:40% top;filter:brightness(0.32) hue-rotate(18deg);"></div>
      <div class="hero-vignette"></div>
      <div class="hero-content">
        <div class="hero-eyebrow">⟡ Engagement Oracle</div>
        <div class="hero-title">The Score<br>Drives the <span>Market</span></div>
        <div class="hero-body">Founder engagement determines every item's price. The more the community votes, the closer the ecosystem reaches a perfect 1000.</div>
        <div class="hero-cta">
          <button class="hero-btn" onclick="document.querySelector('.founder-panel').scrollIntoView({behavior:'smooth'})">View Oracle</button>
        </div>
      </div>
    </div>

    <div class="hero-slide" id="slide_2">
      <div class="hero-img" style="background-image:url('data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcUFhYaHSUfGhsjHBYWICwgIyYnKSopGR8tMC0oMCUoKSj/2wBDAQcHBwoIChMKChMoGhYaKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCj/wgARCAUABAADASIAAhEBAxEB/8QAGwABAAMBAQEBAAAAAAAAAAAAAAECAwQFBgf/xAAZAQEBAQEBAQAAAAAAAAAAAAAAAQIDBAX/2gAMAwEAAhADEAAAAvlAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAFqgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA3Kerza2e34Hqejy7fEvW8nfMLAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAN+rj+imvMj0+TfPk2zyl+g+Z9byOPfEdeIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAD2vF7sb9zj7OCzkztfeNfJ7eLGg1AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAG2No+r8j2vC598uvk6+vHysyQKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAATA+u8P2/D4943y06cvJGsgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAJjpl+j8T3PD59bXp62s/LPR87XMKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAANfdl8P2Pe4ji15csdPU8Xv4Sfe8L2l9TGdbj5jxP0fm1j4B7PjUAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAOuMPc9Kcdbxy69OWvneh5m+WGd6Td8b1xu3o+b0L6nT52l5+hvwaWeh4no3zv4Cn2/yU1yjWQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB3RP0+WOOl8K268LdOHTrFfM9HzVyyvlN2ml5WlZzr0GWmsW1xrZ0386mde3Pkexx9HyfB9z8rrPAOnIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB9F879FnXRmrvmvja56tsd9Z4+Pq4s7zpeitM9FsrGNb6c82b8+YiRNPd+e9le3jm3l9vyNOzj9PjCwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB9B8/wDRS3rGe+fRERZ17c97nh5Ormz0rS9CCJZmo2tleyKWpLNsNpbdeKzu34+vy+zwfP7uHt5w3gAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA7tefXzHppfMemPMemPMemPMemPMekPNekPNekPNekPNekPNekPNekPNekPNekPNekPN+k870rMVJ7efSKVOrs83vuebg7OCaVFQCYEzQt6wiLxos3zvLp6Pl+nw9Hgcfs43PmPTWeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY9MeY7+DfMNZAAAAAAAAAAAAAAAA9HbDfy+wM6AAEVMCgAAECUCUCUCUCUSAT38XX05c0WenxURVenbhm524deeWVS2VgvNJJVksXLdmV0yzvlNad3m6cvTpnMefsAAAAAAAAAFAESAAAEDPzfS83t5w68QAAAAAAAAAAAAAAAPQ3w38vsDOgAqAoABAAABQAAAQDbSum+WVeXj9Xj9S3ndJ389oueLPqrNczozKTeSjSbMp1uZXuKkSzV0zWW/LvjrUeb0hAAAAUAIJQJRJAJRIgAAUADPzfS83t5g68QAAAAAAAAAAAAAAAPQ3w38vsDOgETFAoCABQoAAAAAAI6tsunpx8HDV6PLW28ZuFei1nJPVUxnQVmkWaVpYVulzrsMWsGfTSlepbXTl24B5/QFABAipgAAUAAAACUCYAADPzfR87t5g68QAAAAAAAAAAAAAAAPR2x28vsDOhFAoCABQoQSgAAASgSiQDr6eaOvm4MfZ8Xt594jbHSK9fV05eRr18+NTbmxmuuefplrpw6100zpE5d2++flunGbyy0zs92+W3Lr544+gAQTAoAAAAAAAAAAAAGXnej53bzB14gAAAAAAAAAAAAAAAehvhv5fYE1AUBEwAoUgAUAAAAAAE77Xa5ceueHp8eL0KHJfKMb6GVlZaQW157y1znKz0deKU7ObKhtjS9Yd2/Tc27dp49/mZhy7goAABAmAATAAAATAlAATAlAz870fO7+YOnEAAAAAAAAAAAAAAAD0N8N/L7ETE0CiAFCkACkAAAABMCUSAevz9eF5eN03z9Xjmt4ud689E2w19OvG06uDn11ml86rCLMp9jLfPzNNKG8c02ae143fnfv5a58+ny44eoAQSgAAoAUAAAAEAAABWfneh5/byh04gAAAAAAAAAAAAAAAehvhv5fZAmwEBrPRw40g6QQoAAA0M3qTz15Tq5dwLAAPe4+7ztcebHbP0+Ots7iJrUWiU7vO7uHOtLJ59c9+fp3z28np5t81llWUmur0uD0o9KInG/lx5/YgAAUKAIEoEoAEoEoEokEEgEEwGfn+h5/byh04gAAAAAAAAAAAAAAAehtjt5fYE2IF6dEs8yLQqADdcHdz5YWr6FnPHbw4vpV5NMXlvx278dJ9qc9PCb4aJi573ler5W/PjS70eTLvw6K5efbEWrcvy9XPm5WtM1h149lzyQnRZKIttK9bxO2a9u+euNfKDz+wFCiJJi+csxatCCUCUCUCUCQAAAACDPg7+Dt5Q6cAAAAAAAAAAAAAAAAPQ2x28vsCbQC0QsC1FpKLVXT0K8HK6zxz05bZUbzvXK8W255lrhvl24/RdXz3u3HL5fdHH0+f039fO6cXVlrl5l7R6vFrotrPn564TVr8+8rG+edWmqWvdwdusY1vGolJNkS9EZ7TXsdPn9fLp8wtXl6yJIe/wJ56TUT1VxcczcAELIoJAAAE7YhAmAAz4O7h7eUOnAAAAAAAAAAAAAAAAD0NsdvL7RE0C91OrzeW56OKOk68M4qda+9c8XjfbZdPN8XPVyas65d2FIlOWvb5WsY4/R+B1mXseTr25+lj0edz6W6OTnz0+jypilcb59eHYnn1nHi3nPSnTn0XPFdnz6WnEu98NtZraL6xWUlq2iWNcZl9Tq87p83s87D3/P59ePm38/tw+o15ehPI6fD6cdPVtxdXHr50J75iZTXVj025a856/PZwDrAExJPqWjOejztdpPHTGtAILc+Hu4evkDpwAAAAAAAAAAAAAAAA9DbHby+1BNBb1chLfPowWO7s9npxy65t28bPTK5875f6/wA9r5rvwnj1tXr4sZmIaehzwTHPqy9OPR4ptm8V8bnT1Zcus7aV0Tp5duY5NcNpvToy0uOW1I5dl8tEm+fRqZyjeEhMEtYnomp9HzeTG/S4qc2OmVqdXfz+vGfHjfDMq6Onj38/qhntJ2+b1uffLGHRN8poAST6jKY24aZd/H0+l4/bz604vZ8/n6OYaogz4u3i6+QOnAAAAAAAAAAAAAAAAD0NsdvN7YGdImbY3zymujv4feY325bdvP2zwb9vLvjphc8+FszDzPQya5ujiny9r0vAyW3Na2jvz5erkvNYTcduU3uPO7Msl665aBFzsx6fLRWXPrNqzEdXJ16znPTybzMVqW1ywO7l5uqXTSKWW5OjCMu/i9WnH1ecVl3c+nN035uPTVn6mZ41+3jvXW+uvH0eU3w7UWqPUtSco4Zj0eJ1R6O+fFh6nHLTq5NPP6+jz6+nz9Hix73n2+bxdvF28gdOAAAAAAAAAAAAAAAAHoa47eb2hNwvtm55dfWvTtXLXHfl459Pk7ejg36ce/Pj5jpc2phlfKVjql5o6KZ1S62pMSs58OzGbovVLb8nTZOelk48uvCp244l6uSVm1sNMbvObNvStdT1s+HPWOzmoWJdA2CCErhthNdHfhe55ee1prrr1eb5ul5qq/b5/o5nT4vb5rr124s707ccNtS3rYbY66ZV5PT8/PbL0tZ20mLly9XLFMd5mnlerz+T6dOny117Hzu/J08tR184AAAAAAAAAAAAAAAHftjt5vaiYm0C9Ptct5mfPrl6fHdnfpy6Nea7LJVdNOexfNBKBKJKzOZotCVz25lnPSi8/Tx9B0TSxphfRPPjoyrJtUpNhStoliWllb93KmE7JqNq3lqhcqzmsRTpl6+XblsjbNm9nF34ebpztWltcuYZ2dpRZrE+lx92OnT5laitdt8+v0uHtuLVmLly7YrHVy9cef3U5+Pp4cvZ8bj78+Ps4+/kDfnAAAAAAAAAAAAAAAA79sdfN7kTE22x9I6uCeLt5NGN+vn0tSavbMkqXIVlZQSUSTNJJTcmtZRydfGt62rNce+FrOu+di1qybYdMpwV7cKxi8FK2qW6efWy+uV4mds5rKdYOZaCmWvNK7ebpIpaLIraJW3NblrWlak1tn0kqrJ1WJ7+ZZbilLOtJOvr4Oi57nPqzfk6eYdfL1k8HfkZ80d3l+h89x+p5fTAdPOAAAA6MvT59PJHTmAAAAAAAAAB3647eb3QRN21xy6cs13byNKaWaWztcyqLTQaVQTNRapFqzYvMLKWxtLrydXNVq2rLxa49GsumiXbTPVLTlU0ZyZ5dNK5o0wlva2lmXTw6536OeVDvpltc5Ya4GGVku+s1sRaCK3pERYtU5kViVbyReug5pwNNOfVdLRskW59Ts149k6ue9bLdfJ13M1mDlx6OHHX0Pmfo8fN6/nh6fIAAAB6Md3i+f0Zj0ecAAAAAAAAADu1y18/vQ0m6Z9HN38MzDfKbUkugSqE0g10ylLXrYVmC1q2LTMJxRW869lOzm1y562pNcXqeb6Os9OelZeTTTEugWmJSImKyz2RaJio5+7WWmrFM9ObpmqYbc9mOuPVLeIvYWsY1vWIhRWUSroElMJbKcFrWU0mNiU2Rpms6NcLm981zv083RcopXO8eLTmz36PU8H0vP24vL+v+Y3y5x24gALV7M30fC7eLHQOvIAAAAAAAAADu0y18/vjq5dlphL0eBE0smc4l1tz3TZCycdqLdjeNtOfWy0pSbQNKMzi6OXqnT2PP9Dztcs8ujml5urn3t69uP2c9eKvrcOuPDGuRMxKAVRJN4uacvTgta1wz169tM9csuXp5jLs5uiWNc9rL20ozz03xaxwWzqOlNkb46pzLYKm0lK6pcb1ldSWYkW+mGqdCk6ztPHaa0pXum+d6CXyK93Bz6+zTm9Ph3+Mj2fG9XiDWQHp+Z2c98kHTAAAAAAAAAAAHbplpw+h6vlfQ83TyeJes9ONk2M2tTKm1Slq2WSUzpvEtLZF7NM73E1SSDLLrwa9HhpCb8fVx2T0Z9dmXr+b1Y6+tTHquPL4vo/KuPLvbNq6lkpNarvOHrJXPu8heTq4Paz0jn38rXPo5NMpdr1k06cO25nLbGynndfJnbeQ0p1JnzdPFVbkswmBBGW1F0tlsI0gzmtTopmrS+HWvV18XdeUxMZ3xcvRy8fVbv8AM35+j1/kPr/HvDxB6fKBevdwZ0GsgAAAAAAAAAAdmmWnD6OvteBdPU8n1dfR87w9b850Vi1UrpSM4RLKINGcmuka2EkRISmnRn3J5VddVw8zt5jp6c9bNN+baOPtwpj1fRcdsunj5ub0MThz9HkmuPqj2it3Hc8/J6W2evgez5nQs+VvEql4uNSxv38m+sRzTjLlvtyylqC2BZABKECQSuOk0XoilVi0XuYkSOrHU36ebS47KxE1w8XXxcfVHZw9DfsxO3n6/JYe/wCB6fG2x9RKedtjYG8gAAAAAAAAAAdemenD6ItOmnT2/O78X0XH5PV38m1dbnM6IXnpvUwjbOWtqUOnTi3OhF7KSE3Qb445R28vNzDs5fQrTSlktplqnNx9fPj1+pt5Xbvh2aYTedeeJmtevHW4z82u2evZS3m3HBnOmPRrvrjrjx2rY2tEG1OPqst6E7s8fJ7PAvHjassShZQJAmETMSL0vVst6S0WNTFq3EAnXGydGmOlm+mEZ1lxdfJx9UbZb6z6Pf43pY1f5f6zzca+b9PzNu/nxG8AAAAAAAAAAAAdemenD6T0/N+kyjz/AE63z+Lj7PH6/D5eXZhVNc5l6NeKserr4mh6vLjC2raZW/PB0155NaxBXkvkRpPST05aWTMEnXG4thvN1ja9kaa3lyw6ue8+ni6+dfI9rj7JtxdlLz8i/fzTppnetzx0255evKvfZn05wnsd3D0XNPC6vKmogmiBKBKBOkakZ3oTati6EMr526xFkiLQkJgvvy3O3DTKXPDTPj62uVzq2wv183rV5evy+zy8/Qcenyjv4Pf88NQAAAAAAAAAADr0z04fS7/a4+3njS3TXt5eXy/b8Lrx8/NSr11WZRrjLNqWL530s4nTlLe3Jc6Ixzl0zCNLdFldVrEiJAixy9XFtOnZ18fo65707eaOTm6ORNdOfakRCKznLXm6MmqSqtefp6UhpzpakQWjHNbECEAQCpixfStEi1ZWyETNbUrYV15+sre97nlppnKBeaVz0UtXl6ZtWY6r5advLfu8/THTs8v1/I83p7fm+vp6cvJHp8wAAAAAAAAAAHXvh63D6Hqa8+k4etEX9Hk5vnve+erhaQ1Eysxz6UuUyS85l0reEwy6arjS8xF29Zb4apps9a58enTzgkrNbEY7l29Lzu659bm3wZ87i7OGa20x0JqqKTUjO8LllryNej1V6rjgx7OKK5352otnYmEAgmCEwGuegohbzEiES3mtrJtXQ5u/j61vWc7iud6y1CqWz59pQ59ptWTovlp1897Usd3J0eTw78Nurz+3C9DWAoAAAAAAAAADr3w04fS+g5/N69eb3+n5bs7eTt8Tp47MU5zek1lJhBCJJz25JrsZWsilsItXTNddaXuaTMHvej43p3nyeZ7fltckoUVLTEJr6Pk9x65a48rz/Q8+a10y0FLUKwKrNTmw3wmvou/z/Q1zz8X6fxTxebfOdK2vAVFoEAEy2RABNqyVtS6zal0vplrZj0c281esmMy64rUlik15emRNTNby30zt046Wpa57PI9XyefTHLS++fKN4AAAAAAAAAAA69M+3h9H0fK+jynHxNKYerxaR12OOOqDnttc52+MIpVb828y8mu+S1iNTONKmlom5UF26uS6dHV59JvbDs5tYpAlqWqZdHOmvpd/n/RvPl5FV3vndFZqREwqlqmPJ2cTX0Hb4nr3Hp+N6Xkp5nN18k3ZApntnNXtlokiwM6IZ6TNZsmaykXzvZGmd0vfGajp5esmZvcZWmsuWU546oOfZMFnbHe5tS1LNLM07vK7/O5704e/z+nIOnMAAAAAAAAAADr9zw/q/P79dc9b5+Xw/f8AJ68vH3Z9OfZbzps68cUt712Wk3kwx7MZee1ozpaki02smkUNr1slrUtZphqXDrxmamsLm01m5pXYtenCEhEmt87JNZqIrQvSma1y0ounr+J3M+zy+fauGtsM76GWtyiQAAiWdwMdImJlTAi1Vl5rM1KJsjp5t7m9bEpGlWsazGNgsiJ3w3uaXysukV0zrXyvU8yOnz+vk68E2prAUAAAAAAAAAB2/WfI93H3e31+blfP3eZfk3zxrrXpjKuiKXsG2VyyKSzFc1rbO2dzamlzNK0srn05LbfDRNJzqbzz62a6Y3SnJ6MLxb15zrYa2WRCFRtbCDfPHBejPCJrXa1rnHLp50z7eHrImgzw6MZbaZ6UCAACZYJWIslqUlmaXWyyahKI3y0s1mJuYraF5ovXHSBLIlnbHXWapiW01tjdsLWZ4urLp1jhyOvEKAAAAAAAAAA69M/R4fRynv8AD6ePpk6cb1rYrKSFplo12OZtxGnNMzdGlYTVTWOmzna5EzEoy1leS+9S9+e6dGvPprG1JpZnTpvHG0waVikXra1Z06Yl5Lts9OzDXLfCuO+C4dfLsbZ9PNZTPWkuWlJlvMLJAAAmBNZzlRay5XmDWaWubTWS16Vl7NePfWds71Tkz3zx0yjSmOsTFs7XrNl63qk3pc5u3zfb5bnwvT8awPR5wAAAAAAAAAAOv6nxPofP7dfM9WnTzfHvY8jry6e7xb2exl5uZ3Z8Wk115xaM6bpcZtOd0rrVaSGnped7G+XFxejwLQSyiSUSkZa52xvw3O62G1xaaLNOPXEm1NlqtRJgMPQ4uuapS1biMtaLzXpMvqcfXy6xnS1Zqi8KmJRMSAATAURpLIsRIxtbGa6Jxsms06Sm1Is6aVqlawlitq53W0TjstWbNqXpcTels74PW8aGUHTmAAAAAAAAAAAB9D6fy/u+b299eXLpwr5vXzdOWa86zi0S56xcmZEES5Wp0Y6ZxeFyptlnfRvxz149mFtmeGOjnaTEiYJOWuZz2b1Xek3PRlNbmlJrNT1cvTZnnOZoiqZdfH0S6RNbm2d6LzRekvbXKbmK65LAAExIAAIK3peVMLJRIy1pLnexbb46pas1TWswUrMSoTNVtE47RJc753rczE453xDpxAAAAAAAAAAAAA6/Z8b3vJ9Lirbk9Pg0spvneFzJZLWZiWzMXVpLO+F870Z2mq5me8TE74W6+O2ue/NeKqkQkUytmX1rayUEurFlazBbbK6ZRFi2elKx6cOnOtcuzn1zyTC4U0pLaaa2VrNSUCQJgSABEwU0pMthYmBMCpXiLVkuiS1UEVmITCbSY6JidZm0TLPH2eclRvmAAAAAAAAAAAA6On6vOvl+z0eNrzsOzHXPHopFmt+W1ds86L5Yxnetcby3i186znVNUK56UTE9Uzfp14+N2V3z5rdl9Y4XVQwrrUwp0VWEkiJiiJSsWhLWqrK1RrMdlmFe3hmtYwtc2rJMKdVTl3y1WM9aJSYkkAEgAmJgiJFb5WW6somJG2O0uasmk1kmJrERMLIzu0TGdrROszaJzrPg6ebXMNZAAAAAAAAAAAdPP9jnWnBPPz7xTXLpxxz2pvGUaUQqLVWXO1ompz0tm5bRWa3Y2zrp49ssdLZ3o9N/R8u98/fp5zXL17eFG+fv2+ftc/QZ+NpZ1cXT0Hlu/mXCtoVEwkRapKISt4tWvVw2Z3pbauWbchvXlpL05ZqtvhtE1vWzIhbhAExIILRMAgrntlK0poJgk6xmqYF7UtFqXyJImrDPS0SFokmY1zvzO/utx34nOeryhYAAAAAAAAAB7vqzy8fRFtL9ePn8fp+XrOuayZ0tUrKFkQmCyhLa2czWk56SxlvljtVrln0WmauWm2N9csqXjWM41rZRosrrzjttwaXPTilKR0UrKs0LSIToVuzTXPmpU11uYNs5Zi6zPXG661suc8t8iZiQABMSTEwQBS9SL0tLM1ul61utYi0LUsWpFlE50Jz1km5Wi0u1K8WdXyydOAagAAAAAAAAADu4fZzfd87bDl36u7zfT9Pk5PK9zyjz73quVNKS0SlBQJQlmLWM5vMsU1jHSlZjHpm+e952ptG+PM6KWZpoaac2lzlNdZrGN8rKaUizs187ZnqxvFmMzZUY4ppS91oDSLZ3M5xE1erS5wm+VnUiSuemZKYAAAJBAETBW1bSgiYEzWZZRC3klTEzSYtjqlNylMrzu7z9YDfMAAAAAAAAAAB7fidWN/Sce0Z3T1vH9Tt5+ry/X5bn52nocjWFd8ZaKXmpBEwJEq9JNdMNi8Sx0ry9PNz9Lpw3vK9cNN87IjWbqLm8X6bPNz7uOatbFmzTorXPGlbmt60L1i9ka3iVEZls4pc7Z6a6zh1TotMrZpnlrkb9HJ2mePXz2ZTEywVE0ushJmJKgRNYi2eioElEwmCpposzEyyMdF62mklTrn089+by6Z9/MFgB1+ly6+E+i+fSo68wAAAAAAAPS9r5j6vn15OilNY9q/B2dOXneb73jJy475TfPW2c1qxGs4yuzK0XmJlnXPSr2rbG8c9cufa9NMtYz359N43RNyhFm3Vw9FxTl1ymq1tWUmxnTWtZtqJmtWxFpszm9EdGPQTS9jG1dW656Z3FM9KJXu4Os6efqwucItDVaaRFbAAtWxUQpepnpneWQiYLMTUteJlStNQMdZvE0mLSz1cvRy6eNTqp38uBprNfa338ftmvF5Ob0cJ7fEGoAAAAAAAA+p+W9fG/Y5+zmzvCM+Xrz7eTn1Ssb1Tmr0Zrkkqboo0FLk1tbGxtONpYx1z5+jW8WvOvL2ZbxXTHa5VtWy2udrnLO9JqK3qRWsS6dHL02dOHPa56aYLL0jpTkptlLfStrGmU2dHXweoeRnviZU0oZ7Y2X1KV11jjrek1EWiIAAtWxUQrIytCauEEglZmJltKc7rKc9LCyZi2dN+L0eeuP2OPycX0eHkj0efv5cmshvIAAAAAAAAADXJH23Lg5ejn59+rrw5ezsvc+Xh6PFqcdN651zV3xW6JBBdW81aZ1zrlm+Zrjrnz9O8o1wVmus561tcwnSyK6Z2ZVtWaVtUziaxvth02WtwTZ14USzvjpc456ZzWls9NYiJitPS8v0k5Obu4jPPXJaLUTp6eHYvjrmTW0FYtkaK2hatisTAC1reITEkgmYmWbRaakZ6RatpqZiSZUzePfjnrw9rxPR87nsO3IAAAAAAAAAAAADs9X5/67n1tTow59e7Tzez0eTm4OvytZV54zvamZYRBrbCxoaLG2emdVmNMdM887Z100tjvnM5aazZatzbow67m3n+r5JmmJqK2HPF6kdvFNnTncmbpvXO24ktTNLvrhvZETFT3+d3p0eX73hplz9OE1FbRZG/PtLes1stOepSmlQiYTEkRMASgRJKkpeLyyTNxExjbPXnrXo5Oia04uzzGIHXiAAAAAAAAAAAAAAA93wunOvrsdcfP6uLDOfV46x6GNzxx088tYmFpFqglYmU1ptyybWzxxsTj1a5w6eS1otqKs06+7z+6428nv84iJiaVtUorquEbUuaEpVZVFiUaZlunl6hW2ZPbw9Z9F857XnXHnUvabxr0c1ldMbrtEwldcrFq3qZ2hLIRFqygBKmLAlZtFpqYmJqJTjcZa01Y0pvNZ8HZsz5o6+YAAAAAAAAAAAAAAAD6bLy/b8/t4O27t5PS5vUw6cPJ8z1/Nm+etoWkXharREWWmqxtWXGLxnrJbHpmmtevhx1jOulyXO3p87a525NKFUxEVtRc7i6VmTGb0ZrF61pOWtlefo50jr4+smthh1Uuvv8AV5fLrlfzuZNzTSCkhqreWtb5msUtZCYllExNbVBMRKVWm1ZpTVrwliM03pXOJq2ttc9KTa+N3y9L5rOaD1+IAAAAAAAAAAAAAAADr9HzteH0voeV0Z5dm3lb+rwU8r0fGsljE1sxG04QvVHPaN1E1FJcvYtXRZrLp44adGs+fTt57MG9JYvlU6oraIz1yK2xst75F1nK6VpfqueTTWlmOOua06efZOhGlnNfO65u+6ebOtStNamUXoRthvLFL1qumegWhKpgmLREWmVrabSkwudonOtMrZrCb53TafQ59M+azO3pxjw74eH6XB6/HQdvMAAAAAAAAAAAAAAAB165a8Pp29vwu6T1vOx5fR8yJtGmdbViCi3nKxes2TOL0mr3rbh9JtTS+eqY6eeevl01imbYrXtrrPn49WE015uiNePq5DNJ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gkQgGBBqAOX8iMErQp6/13gjKgyV7/8A888888888888zCr2ZicVHgcNgjKXkbcE067vMQfpCsTNp03RK+o7J0kA82bOAGqc888888888888CMIH+23iKJBp7fhuxgAeYXcIZmbJt6QI6AW10XZj8upRu36syIV3888888888888CCFeC6nfrqLn5TsB+eGkETsxn64CGk3t93EutAzx2JJJdeR85W8V888888888888DnaaQgtPgocL4VB4a6sZ22ubxbUG7Al+9ThvsnWvMnlNm6vySwRFe88888888888W8Gm2vzrdMgH2b/xcALGuySi5eC/akkURVu9oQY1QZbDLcRec5RB888888888888V81M4gbS+kEg3IEBQ9fsGeXd0R4cWbIWqwDq8+jfeo/TjHlXg9hW888888888888czODMPW4BlYUliDVQkb8VtFkpy9HATLMMiTB+L1iI7rxa13SgJ09Y88888888888SNOQYvxSIWnp0DUJOoPomTtgUNXXuqKT9B7OQvNlWDjqw9S46Okcc88888888888BeZBlCyP0tSx05Ykr1M7IZV1/wB4By7Gp0rcLgzEz8MpCI4uYEiyz/PPPPPPPPPPPGC2xBDC375xUTSe/wDj2LJcnxnyvFtJ8pytd05gUSvFtPpgaST+/rzzzzzzzzzzzzydhxuC2FUJ8IGYawTOXUehAV/VpS+8Lvisv0L6+heVJXr5ETRmhzzzzzzzzzzzzzy4nqE57OENrEEniWI3lufyckVs0Cv/AM8XnX/O2JhIyIHR1F9jWY8888888888888qSe29LExWaMi1vN9FI/YJVgRA9tLhkAbDfDTquoCt++UrwLVoC88888888888886cS5nfvw4w1q8KhME2To8qTGcbs1MUUZtDDrHKFRQCMwF391RqYoc88888888884oksV7tv1mzpYZQwmHYuRbq0nbL0C8bsRT6b3re9xHpVAffSoZ/B/8APPPPPPPPPPP2yWi1capFm75d30YEyk/lQwdrpHiohg1ENw+9ft4eOLTmgCcAlZfPPPPPPPPPPPORvXBe+j8arCeh6Hxn6PFdsJDBtVRoP7Kbr2JGZKDj3m5DbRxRO9fOMuvPPPPPPPPFMJTInQ7XZzeEQ479R+489qj3ofNrHIP60MLDy1ma6po58VoSkMvBwv8Azzzzzzzzxguy1/OTYn8w3Zxbjy3ZHpqNh7nukoVaj0jr/wD1ZbDIR+CzhFt8TN+88888888888vSfjcmYdTTx34skjm4WejgBKKEA560YlxeK1aS0gtkVLLSVGfhe88888888888888vPTGeobGAXU49ZbVpn5Y8vNJVhsU+gpvCaACgxNAsdzCF3XG88888888888888886fVqDK3SiR3gAd0ZF0cUdx4t7XaCjDOM9qY2zi2YpMeW7tAN8888888888888888sUjL6fYDzr4uymdASDOxhrGhrTT7NK6qGn70edLlV5vfyzLQ88888888888888888D8VRs5qPawU8X5GZayqWj4es9ZsrxdAeMsuf5gRqlCF9+hp88888888888888888A2LYeivFkAPDq3ipzv2kGz9P4cq5gau5Np8XHavS5n8bA4k88888888888888888ASAYNDFHL0wBs6ty+h6qP5TAWRE76+g0OOV81zWupJyZkRf88888888888888888ABh5ldMNvpWY/pxuaaQBuhjwHqU4j5btYsogfojHA0IujTd88888888888888888ABRTELWTNkRhOSPikgfNaU3Oo5bKjpl9bFCDgPcbVrj2TK/88888888888888884IIBizYU0zCBRmVSuZkwm6jx7iT2qRuyC5f5VBapCHq6S+JV8888888888888888QinBDJf4McviBRBzaCJuVi4yp7Z7Ii2BYbw2CUVLW4CDVW+U8888888888888888uloIZbPxza7KBBI4ITTThDBzGtrkd/tqUBBHxat7SvTpOW++88888888888888888h0EGWFd0CP2BBGrDBBBBBBXgK1tYQU+CBBBZ7JDc8P8A5kfvvPPPPPPPPPPPPPPPPAAd9DgXWMMKCxxywQQQQQQU0DIo6cwQQQQRY5tfvPOwFiffvPPPPPPPPPPPPPPPPAQAETRn2ye2wFigAAAAAAAAAAAAAAAAAAAAAEccccYUcQAPPPPPPPPPPPPPPPPPPPPPPIpFBbbm04B/PPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPGgYz4hqsp2b/vPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPPFvMuYf/kcUfMufPPPPPPPPPPPPPPPPPPPPPPPP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');background-position:62% center;filter:brightness(0.32) sepia(0.25);"></div>
      <div class="hero-vignette" style="background:linear-gradient(to right,rgba(10,9,8,0.92) 0%,rgba(10,9,8,0.42) 60%,transparent 100%),linear-gradient(to top,rgba(10,9,8,0.95) 0%,transparent 50%);"></div>
      <div class="hero-content">
        <div class="hero-eyebrow">⟡ LP Market Maker · Always Open</div>
        <div class="hero-title">Instant <span>Liquidity</span><br>at 80% Oracle</div>
        <div class="hero-body">The PvE treasury bids on every item at 80% of the Oracle price. Sell in one tap — no waiting for a buyer.</div>
        <div class="hero-cta">
          <button class="hero-btn" onclick="openListModal()">List an Item</button>
          <button class="hero-btn outline" onclick="openWallet()">My Inventory</button>
        </div>
      </div>
    </div>

    <div class="hero-dots" id="heroDots">
      <button class="hero-dot active" onclick="goToSlide(0)"></button>
      <button class="hero-dot" onclick="goToSlide(1)"></button>
      <button class="hero-dot" onclick="goToSlide(2)"></button>
    </div>

    <div class="hero-stats">
      <div class="hero-stat"><div class="hero-stat-v" id="heroListings">—</div><div class="hero-stat-k">Listings</div></div>
      <div class="hero-stat"><div class="hero-stat-v" id="heroScore">—</div><div class="hero-stat-k">Score</div></div>
      <div class="hero-stat"><div class="hero-stat-v" id="heroMult">—</div><div class="hero-stat-k">Multiplier</div></div>
    </div>
  </div>

  <header>
    <div class="brand">
      <div>
        <div class="brand-mark">PvE <span>Shop & Spree</span></div>
        <div class="brand-sub">Player-Forged Goods · Settled in $PvE</div>
      </div>
      <div class="wallet" onclick="openWallet()" title="Click to open wallet">
        <div class="wallet-addr">0x7Af3...c91E</div>
        <div class="balance" id="balance">10,500</div>
        <div class="wallet-row">
          <span>$PvE Balance</span>
          <span class="wallet-cta">▸ OPEN WALLET</span>
        </div>
        <div class="votes-left" id="votesLeft">VOTES REMAINING: 3 / 3</div>
      </div>
    </div>
  </header>

  <!-- Oracle Law — full ruleset -->
  <div class="oracle-law reveal">
    <div class="oracle-law-header">
      <div class="oracle-law-title">⚔ &nbsp; ORACLE LAW &nbsp; ⚔</div>
    </div>
    <div class="oracle-law-body">

      <div class="oracle-rule">
        <span class="oracle-rule-num">I.</span>
        <span class="oracle-rule-text">Hold <b>≥10,002 $PvE</b> to vote · <b>3 votes per wallet per week</b> · resets Monday UTC</span>
      </div>

      <div class="oracle-rule">
        <span class="oracle-rule-num">II.</span>
        <span class="oracle-rule-text">Each vote moves score <b>+1.5–3 pts</b> (random flat) · <b>one vote per founder per week</b> · no double-voting</span>
      </div>
      <div class="oracle-rule">
        <span class="oracle-rule-num">III.</span>
        <span class="oracle-rule-text">Max <b>3 active listings</b> · <b>10 $PvE deposit</b> per listing · hard cap <b>69,420 $PvE</b></span>
      </div>
      <div class="oracle-rule">
        <span class="oracle-rule-num">IV.</span>
        <span class="oracle-rule-text"><b>Min 5 editions</b> per item · <b>prices are Oracle-set</b> — no manual pricing allowed</span>
      </div>
      <div class="oracle-rule">
        <span class="oracle-rule-num">V.</span>
        <span class="oracle-rule-text">Sales are <b>first come, first served</b> — earliest buyer receives Edition #1</span>
      </div>
      <div class="oracle-rule">
        <span class="oracle-rule-num">VI.</span>
        <span class="oracle-rule-text"><b>No refunds</b> on community-flagged items · reports reviewed by community</span>
      </div>
      <div class="oracle-rule">
        <span class="oracle-rule-num">VII.</span>
        <span class="oracle-rule-text">Instant sell to <b>LP Market Maker at 80%</b> of Oracle price always available</span>
      </div>
    </div>
  </div>

  <!-- Engagement Oracle — 4-cell grid panel -->
  <div class="founder-panel reveal-stagger">

    <!-- Cell 1: Founder identity -->
    <div class="founder-cell">
      <div class="cell-label">Founder</div>
      <div class="founder-name">FibTechnician</div>
      <div class="founder-handle">
        <a href="https://x.com/PvEcosystem" target="_blank" rel="noopener">@PvEcosystem ↗</a>
      </div>
      <div style="margin-top:14px;font-family:'JetBrains Mono',monospace;font-size:10px;color:var(--ink-dim);line-height:1.7;">
        Iron Arena · Trade Together<br>Reap What You Sow · Shop & Spree
      </div>
    </div>

    <!-- Cell 2: Engagement score -->
    <div class="founder-cell">
      <div class="cell-label">Engagement Score</div>
      <div class="score-display" id="scoreDisplay">100.00</div>
      <div class="score-bar"><div class="score-bar-fill" id="scoreBar" style="width:80%"></div></div>
      <div id="scoreDistLabel" style="font-family:'JetBrains Mono',monospace;font-size:10px;color:var(--gold);margin-top:2px;"></div>
      <div style="margin-top:6px;font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--ink-dim);">
        Community-weighted · max 1,000
      </div>
    </div>

    <!-- Cell 3: Price multiplier -->
    <div class="founder-cell">
      <div class="cell-label">Price Multiplier</div>
      <div class="multiplier-val" id="multiplier">×2.10</div>
      <div class="multiplier-trend" id="multiplierTrend" style="color:var(--ink-dim);">— stable</div>
      <div style="margin-top:10px;font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--ink-dim);">
        Applied to all Oracle prices
      </div>
    </div>

    <!-- Cell 4: Vote + phase -->
    <div class="founder-cell">
      <div class="cell-label">
        Cast Your Vote
        <span id="votesLeft" class="oracle-votes-chip">∞ votes</span>
      </div>
      <div class="vote-block">
        <div class="vote-row">
          <button class="vote-btn" id="upBtn" onclick="openVoteModal('up')">▲ Upvote</button>
          <button class="vote-btn downvote" id="downBtn" onclick="openVoteModal('down')">▼ Downvote</button>
        </div>
        <div class="vote-stats">
          <span><span class="v-up">▲ <span id="upCount">412</span></span> upvotes</span>
          <span><span class="v-down">▼ <span id="downCount">88</span></span> downvotes</span>
        </div>
        <div class="vote-locked" id="voteLockMsg">Hold ≥10,002 $PvE · 3 votes/week · resets Monday UTC</div>
      </div>
      <div style="margin-top:12px;padding-top:10px;border-top:1px solid var(--line);">
        <div id="phaseLabel" class="oracle-phase-label"></div>
        <div id="phaseDetail" class="oracle-phase-detail"></div>
      </div>
    </div>

  </div>

  <!-- On-chain status bar -->
  <div class="onchain-bar reveal">
    <div class="onchain-bar-inner">
      <div class="onchain-pill">
        <span class="live-dot"></span>
        <span class="chain-badge">OPTIMISM</span>
        <span class="label">Live on-chain</span>
      </div>
      <a class="onchain-pill" id="contractLink" href="#" target="_blank" rel="noopener">
        <span class="label">Contract</span>
        <span class="addr" id="contractAddrDisplay">awaiting deploy</span>
        <span class="ext-icon">↗</span>
      </a>
      <a class="onchain-pill" id="tokenLink" href="#" target="_blank" rel="noopener">
        <span class="label">$PvE Token</span>
        <span class="addr" id="tokenAddrDisplay">awaiting deploy</span>
        <span class="ext-icon">↗</span>
      </a>
      <a class="onchain-pill" href="https://opensea.io/collection/pve-ecosystem" target="_blank" rel="noopener">
        <span style="font-size:13px;">⬡</span>
        <span class="label">View collection on</span>
        <span style="color:#2081e2;font-weight:700;letter-spacing:0.05em;">OpenSea</span>
        <span class="ext-icon">↗</span>
      </a>
      <div class="onchain-pill" style="margin-left:auto;border-right:none;">
        <span class="label">NFTs auto-indexed on OpenSea after first mint · 0.1% fee · 80% instant liquidity</span>
      </div>
    </div>
  </div>

  <!-- Live vote ledger — shows every historical vote and its current state -->
  <div class="ledger">
    <div class="ledger-header">
      <span class="ledger-title">⚖ Live Vote Ledger</span>
      <span class="ledger-sub">votes stay weighted only while voter holds ≥10,002 $PvE</span>
    </div>
    <div class="ledger-body" id="ledgerBody"></div>
    <div class="ledger-legend">
      <span><span class="dot-active"></span> Active · counts toward score</span>
      <span><span class="dot-burned"></span> Burned · permanently excluded</span>
    </div>
  </div>

  <div class="metrics">
    <div class="metric"><div class="v" id="mTotal">—</div><div class="k">Listings</div></div>
    <div class="metric"><div class="v" id="mVolume">—</div><div class="k">24h Volume</div></div>
    <div class="metric"><div class="v" id="mFloor">—</div><div class="k">Floor Price</div></div>
    <div class="metric"><div class="v" id="mCreators">—</div><div class="k">Creators</div></div>
    <div class="metric"><div class="v" id="mMmBalance">—</div><div class="k">LP MM Budget</div></div>
    <div class="metric"><div class="v" id="mMmListings">—</div><div class="k">MM Listings</div></div>
  </div>

  <div class="toolbar reveal">
    <div class="filters" id="filters">

    </div>
    <button class="btn" id="listBtn" onclick="openListModal()">+ List an Item</button>
  </div>


  <!-- Sort controls -->
  <div id="sortBar" style="display:flex;gap:8px;align-items:center;padding:0 0 12px;flex-wrap:wrap;">
    <span style="font-family:'JetBrains Mono',monospace;font-size:9px;letter-spacing:0.25em;color:var(--ink-dim);text-transform:uppercase;margin-right:4px;">Sort by</span>
    <button class="sort-btn active" data-sort="score" onclick="setSort('score')">⚡ Best Score</button>
    <button class="sort-btn" data-sort="volume" onclick="setSort('volume')">◈ Volume</button>
    <button class="sort-btn" data-sort="combined" onclick="setSort('combined')">▲ Score × Volume</button>
  </div>
  <div class="grid reveal-stagger" id="grid"></div>

  <!-- ── THE EXCHANGE — Secondary Market ───────────────────────────── -->
  <div class="exchange-section reveal" id="exchangeSection">
    <div class="exchange-header">
      <div>
        <div class="exchange-title">⬡ The Exchange</div>
        <div class="exchange-subtitle">Secondary Market · Free price discovery · Price frozen at week close</div>
      </div>
      <div class="exchange-stats">
        <div>Listings <span id="exCount">0</span></div>
        <div>Volume <span id="exVolume">0</span></div>
      </div>
    </div>
    <div class="exchange-grid" id="exchangeGrid"></div>
  </div>


  <!-- ── THE EXCHANGE — Secondary Market ──────────────────────────── -->
  <div class="exchange-section reveal" id="exchangeSection">
    <div class="exchange-header">
      <div>
        <div class="exchange-title">◈ The Exchange</div>
        <div class="exchange-subtitle">Secondary Market · Free price discovery · Oracle price frozen at migration</div>
      </div>
      <div class="exchange-badge">
        <span></span>
        <span id="exchangeCount">0 listings</span>
      </div>
    </div>
    <div id="exchangeGrid"></div>
  </div>
</div>

<!-- VOTE MODAL -->
<div class="overlay" id="voteOverlay">
  <div class="modal">
    <h2 id="voteTitle">Cast Founder Vote</h2>
    <p>This vote is permanent and cannot be reversed.</p>

    <div class="vote-warning">
      <b>⚠ ORACLE LAW</b>
      <ul>
        <li>Your wallet must hold at least <b style="color:var(--gold-bright)">10,002 $PvE for 48h</b> before voting.</li>
        <li><b style="color:var(--gold-bright)">Weeks 1–3 (bootstrap):</b> 3 votes/wallet/week — no no reset 00:00 UTC.</li>
        <li><b style="color:var(--gold-bright)">Week 4+ (decay):</b> 3 lifetime votes total — older votes count less over time.</li>
        <li>Votes fall off if balance drops below threshold. <b style="color:var(--gold-bright)">7-day grace</b>, then they burn.</li>
      </ul>
    </div>

    <div class="eligibility-check" id="eligibilityCheck"></div>

    <div class="modal-row"><span>Founder</span><span>FibTechnician (@PvEcosystem)</span></div>
    <div class="modal-row"><span>Vote direction</span><span id="voteDir">—</span></div>
    <div class="modal-row"><span>Score change</span><span id="voteImpact">—</span></div>
    <div class="modal-row total"><span>Votes</span><span id="votesAfter">Unlimited</span></div>
    <div class="modal-row" style="border-bottom:none;padding-top:8px;">
      <span style="color:var(--ink-dim);">Distance to perfect score</span>
      <span id="distToMax" style="color:var(--gold);">—</span>
    </div>

    <div class="modal-actions">
      <button class="btn btn-cancel" onclick="closeOverlay('voteOverlay')">Cancel</button>
      <button class="btn" id="confirmVote" onclick="submitVote()">Confirm & Sign</button>
    </div>
  </div>
</div>

<!-- BUY MODAL -->
<div class="overlay" id="buyOverlay">
  <div class="modal">
    <h2 id="buyTitle">Confirm Purchase</h2>
    <p id="buyDesc"></p>
    <div class="modal-row"><span>Base price</span><span id="buyBase">—</span></div>
    <div class="modal-row"><span>Engagement multiplier</span><span id="buyMult">—</span></div>
    <div class="modal-row"><span>Marketplace fee (0.1%)</span><span id="buyFee">—</span></div>
    <div class="modal-row total"><span>You pay</span><span id="buyTotal">—</span></div>
    <div class="modal-actions">
      <button class="btn btn-cancel" onclick="closeOverlay('buyOverlay')">Cancel</button>
      <button class="btn" id="confirmBuy">Confirm</button>
    </div>
  </div>
</div>

<!-- LIST MODAL -->
<div class="overlay" id="listOverlay">
  <div class="modal">
    <h2>Forge a New Listing</h2>
    <p>Community-crafted goods. Settled in $PvE on Optimism.</p>
    <div style="font-family:'JetBrains Mono',monospace;font-size:10px;text-align:center;margin-bottom:16px;padding:6px;border:1px solid var(--line);color:var(--gold);">
      LISTING SLOTS: <span id="listModalCap">0 of 3 used</span>
    </div>
    <div class="list-form">
      <div>
        <label>Item Image <span style="color:var(--ink-dim);font-size:9px;letter-spacing:0.1em;">(OPTIONAL · JPG/PNG/GIF/WEBP · MAX 10MB)</span></label>
        <div class="img-upload-zone" id="newImgZone">
          <input type="file" id="newImgFile" accept="image/jpeg,image/png,image/gif,image/webp" onchange="handleImageUpload('new')">
          <span class="upload-icon">⬆</span>
          <div class="upload-prompt">Click or drag to upload image</div>
        </div>
        <button class="img-upload-clear" id="newImgClear" style="display:none" onclick="clearImage('new')">✕ Remove image</button>
        <div style="margin-top:6px;">
          <div class="ipfs-row">
            <input type="text" id="newImgCid" placeholder="ipfs://Qm... or ar://... (optional)" style="background:var(--bg-2);border:1px solid var(--line);color:var(--ink);padding:8px 10px;">
            <span class="ipfs-badge" id="newCidBadge">IPFS/AR</span>
          </div>
        </div>
      </div>
      <div>
        <label>Item Name</label>
        <input type="text" id="newName" placeholder="Whisperblade of the Marsh" maxlength="40">
      </div>
      <div>
        
      </div>
      <div>
        <label>Description</label>
        <textarea id="newDesc" placeholder="A blade rumored to whisper..." maxlength="120"></textarea>
      </div>
      <div>
        <label>Editions <span style="color:var(--rust);font-size:9px;letter-spacing:0.1em;">MIN ${MIN_EDITIONS}</span></label>
        <input type="number" id="newEditions" placeholder="5" min="${MIN_EDITIONS}" max="10000" value="${MIN_EDITIONS}">
      </div>
      <div>
        <label>Base Price <span style="color:var(--ink-dim);font-size:9px;letter-spacing:0.1em;">11 – 69,420 $PvE</span></label>
        <input type="number" id="newBasePrice" placeholder="100" min="11" max="69420" value="100" oninput="updateListPreview()">
      </div>
      <div class="price-preview">
        <div style="font-size:10px;color:var(--ink-dim);margin-bottom:6px;">Oracle multiplier applied at sale · final price = your base × multiplier</div>
        <div style="display:flex;justify-content:space-between;align-items:center;gap:12px;flex-wrap:wrap;">
          <div>Current price (×<span id="curMult">1.00</span>):
            <div class="calc" id="newCalc">— $PvE</div>
          </div>
        </div>
        <div style="font-size:10px;margin-top:8px;color:var(--gold);">⬡ ${LISTING_DEPOSIT} $PvE deposit · NFT minted on confirm · max 69,420 $PvE</div>
      </div>
      <div class="modal-actions">
        <button class="btn btn-cancel" onclick="closeOverlay('listOverlay')">Cancel</button>
        <button class="btn" onclick="submitListing()">Forge Listing</button>
      </div>
    </div>
  </div>
</div>

<div class="toast" id="toast"></div>

<!-- WALLET DRAWER -->
<div class="drawer-backdrop" id="drawerBackdrop" onclick="closeWallet()"></div>
<aside class="drawer" id="walletDrawer">
  <div class="drawer-header">
    <button class="drawer-close" onclick="closeWallet()">×</button>
    <div class="drawer-title">⚜ MY WALLET</div>
    <div class="drawer-addr">0x7Af3...c91E · Optimism</div>
    <div class="drawer-balance" id="drawerBalance">10,500</div>
    <div class="drawer-balance-label">$PvE Available</div>
  </div>

  <div class="drawer-stats">
    <div class="drawer-stat">
      <div class="drawer-stat-v" id="drawerInvCount">0</div>
      <div class="drawer-stat-k">Items Owned</div>
    </div>
    <div class="drawer-stat">
      <div class="drawer-stat-v" id="drawerListedCount">0</div>
      <div class="drawer-stat-k">Listed</div>
    </div>
    <div class="drawer-stat">
      <div class="drawer-stat-v" id="drawerEarnings">0</div>
      <div class="drawer-stat-k">Earned</div>
    </div>
    <div class="drawer-stat">
      <div class="drawer-stat-v" id="drawerLocked">0</div>
      <div class="drawer-stat-k">Locked Dep.</div>
    </div>
  </div>

  <!-- Bio strip -->
  <div id="drawerBioStrip" style="padding:10px 16px;border-bottom:1px solid var(--line);display:flex;align-items:center;justify-content:space-between;gap:10px;background:var(--bg-2);">
    <div id="drawerBioText" style="font-family:'JetBrains Mono',monospace;font-size:10px;font-style:italic;color:var(--ink-dim);flex:1;"></div>
    <button onclick="openEditBio()" style="background:none;border:1px solid var(--line);color:var(--ink-dim);font-family:'JetBrains Mono',monospace;font-size:9px;letter-spacing:0.15em;padding:4px 10px;cursor:pointer;white-space:nowrap;transition:all 0.15s;"
      onmouseover="this.style.borderColor='var(--gold)';this.style.color='var(--gold)'"
      onmouseout="this.style.borderColor='var(--line)';this.style.color='var(--ink-dim)'">
      ✏ Edit Bio
    </button>
  </div>

  <div class="drawer-tabs">
    <button class="drawer-tab active" data-tab="inventory" onclick="switchDrawerTab('inventory')">
      Inventory<span class="count" id="tabInvCount">(0)</span>
    </button>
    <button class="drawer-tab" data-tab="listed" onclick="switchDrawerTab('listed')">
      Listed<span class="count" id="tabListedCount">(0)</span>
    </button>
    <button class="drawer-tab" data-tab="collection" onclick="switchDrawerTab('collection')">
      Collection
    </button>
    <button class="drawer-tab" data-tab="pnl" onclick="switchDrawerTab('pnl')">
      P&amp;L
    </button>
    <button class="drawer-tab" data-tab="history" onclick="switchDrawerTab('history')">
      History
    </button>
  </div>

  <div class="drawer-body" id="drawerBody"></div>
</aside>

<!-- SELL MODAL -->
<div class="overlay" id="sellOverlay">
  <div class="modal">
    <h2>List for Sale</h2>
    <p>Price is set by the Oracle. You receive 98% at sale time.</p>

    <div class="sell-preview" id="sellPreview"></div>

    <div class="list-form">
      <div>
        <label>Update Image <span style="color:var(--ink-dim);font-size:9px;letter-spacing:0.1em;">(OPTIONAL)</span></label>
        <div class="img-upload-zone" id="sellImgZone">
          <input type="file" id="sellImgFile" accept="image/jpeg,image/png,image/gif,image/webp" onchange="handleImageUpload('sell')">
          <span class="upload-icon">⬆</span>
          <div class="upload-prompt">Click or drag to upload image</div>
        </div>
        <button class="img-upload-clear" id="sellImgClear" style="display:none" onclick="clearImage('sell')">✕ Remove image</button>
      </div>
      <div>
        <label>Update Audio <span style="color:var(--ink-dim);font-size:9px;letter-spacing:0.1em;">(OPTIONAL)</span></label>
        <div class="audio-upload-zone" id="sellAudioZone">
          <input type="file" id="sellAudioFile" accept="audio/mpeg,audio/wav,audio/ogg,audio/mp3" onchange="handleAudioUpload('sell')">
          <span class="upload-icon" style="font-size:22px;">♪</span>
          <div class="upload-prompt">Click or drag to upload audio</div>
        </div>
        <button class="img-upload-clear" id="sellAudioClear" style="display:none" onclick="clearAudio('sell')">✕ Remove audio</button>
        <div id="sellAudioPreview" style="display:none;margin-top:8px;"></div>
      </div>
      <div class="price-preview">
        <div style="font-size:10px;color:var(--ink-dim);margin-bottom:4px;">Oracle price at current multiplier (×<span id="sellMult">2.10</span>):</div>
        <div class="calc" id="sellCalc">— $PvE</div>
        <div style="font-size:10px;margin-top:6px;color:var(--ink-dim);">
          0.1% fee · <span style="color:var(--gold);">${LISTING_DEPOSIT} $PvE deposit</span> returned · You receive: <span id="sellYouGet" style="color:var(--moss);">—</span>
        </div>
        <div style="font-size:9px;margin-top:6px;color:var(--ink-dim);">Sales are first come, first served · no refunds on reported items</div>
      </div>
      <div class="modal-actions">
        <button class="btn btn-cancel" onclick="closeOverlay('sellOverlay')">Cancel</button>
        <button class="btn" onclick="confirmSell()">List for Sale</button>
      </div>
    </div>
  </div>
</div>

<div class="dev-panel">
  WALLET SIM:
  <button onclick="setBalance(10500)">10,500</button>
  <button onclick="setBalance(5000)">5,000</button>
  <button onclick="useWeeklyVotes(0)">votes=0</button>
  <button onclick="useWeeklyVotes(3)">votes=3</button>
  <button onclick="simDecayPhase()">→ decay phase</button>
  <button onclick="simBootstrapPhase()">→ bootstrap</button>
  <button onclick="dropVoter('drifter.eth')">drop drifter</button>
  <button onclick="restoreVoter('0x..3fA1')">restore 0x..3fA1</button>
</div>

<script>
// enterBazaar defined in <head> — available globally before any onclick fires
// ============================================================
// PvE Shop and Spree — community marketplace prototype
// VOTING MODEL (continuous eligibility):
//   - Voter must hold >=10,002 $PvE in wallet to cast a vote
//   - Each wallet has 3 lifetime founder votes
//   - Votes only count toward the score while voter still holds >=10,002
//   - Drop below threshold -> 7-day grace period
//   - Don't re-up within 7 days -> vote burns permanently (cannot be revived)
// ============================================================

const ELIGIBILITY_MIN = 10002;          // Minimum $PvE hold to cast & maintain
// Votes: unlimited — any wallet holding ≥10,002 $PvE may vote at any time
// No holding period — balance ≥10,002 $PvE is the only requirement to vote
const FOUNDER_LISTING_CAP = 3;          // Max simultaneous active listings per founder
const LISTING_DEPOSIT = 10;             // $PvE locked per active listing, returned on sale/unlist
const MIN_EDITIONS = 5;                 // Minimum editions per item listing
// Rarity floor base prices — Oracle multiplies these by current multiplier + rarity fee
// Users cannot manually set prices; the oracle sets them from rarity tier
// Rarity removed — basePrice is the sole pricing input

function oraclePrice(rarity) {
  // Rarity removed — use a standard base of 100 for unrealized P&L estimates
  const mult = scoreToMultiplier(computeScore());
  return Math.min(100 * mult, MAX_FINAL_PRICE);
}

function oracleBasePrice() {
  return 100;
}
const MAX_FINAL_PRICE = 69420;          // Hard cap: no item may sell for more than 69,420 $PvE

// ── ON-CHAIN CONFIG ──────────────────────────────────────────────────────────
// PRODUCTION: replace with deployed contract addresses after launch
const CHAIN_NAME        = 'Optimism';
const CHAIN_EXPLORER    = 'https://optimistic.etherscan.io';
const MARKETPLACE_ADDR  = '0x0000…deploy';   // ERC-1155 marketplace contract
const PVE_TOKEN_ADDR    = '0x0000…pvERC20';  // $PvE ERC-20 token
const OPENSEA_BASE      = 'https://opensea.io/assets/optimism';

function explorerTx(h)    { return `${CHAIN_EXPLORER}/tx/${h}`; }
function explorerToken(id){ return `${CHAIN_EXPLORER}/token/${MARKETPLACE_ADDR}?a=${id}`; }
function openSeaItem(id)  { return `${OPENSEA_BASE}/${MARKETPLACE_ADDR}/${id}`; }
function ipfsUrl(cid) {
  if (!cid) return null;
  if (cid.startsWith('ar://')) return `https://arweave.net/${cid.slice(5)}`;
  return `https://ipfs.io/ipfs/${cid.replace('ipfs://','')}`;
}
const GRACE_PERIOD_MS = 7 * 24 * 60 * 60 * 1000;  // 7 days
const REPORT_THRESHOLD = 3;             // Reports before listing is auto-hidden for review
const LP_TOTAL = 300_000_000;           // 30% of 1B token supply = LP pool
const LP_MM_RESERVE = 100_000_000;       // LP Market Maker pool — 100M $PvE
const MM_BID_PCT   = 0.80;              // MM bids at 80% of list price (wPvE instant liquidity)
const MM_RELIST_PCT = 1.00;             // MM relists acquired items at 100% of original price
const MARKETPLACE_FEE = 0.001;           // 0.1% marketplace fee on all sales

// Decay weights for vote age (decay phase only)
// A vote cast today is worth 1×; one cast 6+ months ago is worth 0.25×
const DECAY_SCHEDULE = [
  { maxAgeDays: 30,  weight: 1.00 },
  { maxAgeDays: 90,  weight: 0.75 },
  { maxAgeDays: 180, weight: 0.50 },
  { maxAgeDays: Infinity, weight: 0.25 },
];

function voteWeight(castAt) {
  const ageDays = (Date.now() - castAt) / (24 * 60 * 60 * 1000);
  for (const band of DECAY_SCHEDULE) {
    if (ageDays <= band.maxAgeDays) return band.weight;
  }
  return 0.25;
}

// PRODUCTION: set this to the actual UTC timestamp of your shop launch
// For the prototype we simulate launch as 2 weeks ago so bootstrap phase is active
const LAUNCH_TS_DEFAULT = Date.now() - 14 * 24 * 60 * 60 * 1000; // 2 weeks ago = week 2 of bootstrap

function getLaunchTs() {
  return window._launchTsOverride || LAUNCH_TS_DEFAULT;
}

function weeksSinceLaunch() {
  return (Date.now() - getLaunchTs()) / (7 * 24 * 60 * 60 * 1000);
}

function isBootstrapPhase() {
  return weeksSinceLaunch() < BOOTSTRAP_WEEKS;
}

// How many ms until the bootstrap phase ends
function msUntilDecayPhase() {
  const endTs = getLaunchTs() + BOOTSTRAP_WEEKS * 7 * 24 * 60 * 60 * 1000;
  return Math.max(0, endTs - Date.now());
}

// Which bootstrap week we're currently in (1, 2, or 3)
function currentBootstrapWeek() {
  return Math.min(BOOTSTRAP_WEEKS, Math.floor(weeksSinceLaunch()) + 1);
}

// Returns the ISO week number for a given Date (Monday-anchored)
function isoWeek(date) {
  const d = new Date(Date.UTC(date.getFullYear(), date.getMonth(), date.getDate()));
  d.setUTCDate(d.getUTCDate() + 4 - (d.getUTCDay() || 7));
  const yearStart = new Date(Date.UTC(d.getUTCFullYear(), 0, 1));
  return { week: Math.ceil((((d - yearStart) / 86400000) + 1) / 7), year: d.getUTCFullYear() };
}

function currentWeekKey(date) {
  const { week, year } = isoWeek(date || new Date());
  return `${year}-W${week}`;
}

// How many seconds until next Monday 00:00 UTC
function msUntilWeekReset() {
  const now = new Date();
  const dayOfWeek = now.getUTCDay(); // 0=Sun, 1=Mon …
  const daysUntilMonday = dayOfWeek === 0 ? 1 : (8 - dayOfWeek) % 7 || 7;
  const nextMonday = new Date(Date.UTC(now.getUTCFullYear(), now.getUTCMonth(), now.getUTCDate() + daysUntilMonday));
  return nextMonday - now;
}

function fmtCountdown(ms) {
  const totalSec = Math.floor(ms / 1000);
  const d = Math.floor(totalSec / 86400);
  const h = Math.floor((totalSec % 86400) / 3600);
  const m = Math.floor((totalSec % 3600) / 60);
  if (d > 0) return `${d}d ${h}h`;
  if (h > 0) return `${h}h ${m}m`;
  return `${m}m`;
}

// Pre-seeded vote ledger demonstrating the three states
// In production, this comes from the VotingRegistry contract
const seedLedger = [
    { id: 'v1', voter: 'drifter.eth',       balance: 14200, direction: 'up',   castAt: Date.now() - 12 * 24*60*60*1000, droppedAt: null },
  { id: 'v2', voter: 'thane_g',           balance: 11500, direction: 'up',   castAt: Date.now() - 10 * 24*60*60*1000, droppedAt: null },
  { id: 'v3', voter: 'kingless',          balance: 28000, direction: 'up',   castAt: Date.now() - 9  * 24*60*60*1000, droppedAt: null },
  { id: 'v4', voter: 'ashenwarden',       balance: 10800, direction: 'up',   castAt: Date.now() - 8  * 24*60*60*1000, droppedAt: null },
  { id: 'v5', voter: 'shade_19',          balance: 12100, direction: 'down', castAt: Date.now() - 5  * 24*60*60*1000, droppedAt: null },
  { id: 'v6', voter: 'smallhands',        balance: 45000, direction: 'up',   castAt: Date.now() - 2  * 24*60*60*1000, droppedAt: null },
];

const state = {
  balance: 10500,
  heldSince: 0, // hold period removed — balance check only
  lockedDeposit: 0,  // $PvE currently locked in listing deposits
  founderScore: 100, // loaded from localStorage below
  prevScore: 100,
  filter: 'all',
  items: [],
  // Per-wallet voting state for the connected user
  // PRODUCTION: read from on-chain VotingRegistry for msg.sender
  weeklyVotes: {},    // { "2025-W12": 2, "2025-W13": 1, ... }
  ledger: [...seedLedger],   // every vote ever cast for this founder
  myWallet: 'you.eth',
  myBio: '',        // 80-char max biography — required before first listing
  reports: {},       // { itemId: { count: N, myReport: bool } }
  mmBalance: 0,      // initialised after seedItems (below)
  mmInventory: [],   // items held by MM waiting to relist
  // ---- WALLET ----
  inventory: [],     // items the user has bought and not yet listed
  myListings: [],    // items the user has listed for sale (live in market)
  totalEarned: 0,    // cumulative $PvE earned from sales
  txHistory: [],     // [{ type: 'buy'|'sell'|'list'|'unlist', itemName, amount, ts }]
  drawerTab: 'inventory',
  sortOrder: 'score',
  exchange: [],        // items migrated from Oracle Market after week ends [{...item, frozenPrice, askPrice, priceHistory:[]}]
  lastWeekKey: null,   // tracks current week — triggers migration on change
  secondaryItems: [],  // items migrated from Oracle Market after week reset
  // P&L ledger — one entry per realized sale
  pnlLedger: [],     // [{ itemName, rarity, costBasis, salePrice, pnl, ts }]
  totalCostBasis: 0, // running total of what was spent on currently held items
  totalRealized: 0,  // cumulative realized P&L from closed positions
};

function logTx(type, itemName, amount) {
  state.txHistory.unshift({ type, itemName, amount, ts: Date.now() });
}

function reportItem(itemId) {
  if (!state.reports[itemId]) state.reports[itemId] = { count: 0, myReport: false };
  const r = state.reports[itemId];
  if (r.myReport) return; // already reported by this wallet
  r.myReport = true;
  r.count += 1;
  // Simulate other community reports arriving within seconds
  // PRODUCTION: this would be an on-chain or off-chain aggregation call
  const extraReports = Math.floor(Math.random() * 3); // 0-2 extra community reports
  r.count += extraReports;
  if (r.count >= REPORT_THRESHOLD) {
    toast(`⚠ Item flagged by ${r.count} reports — auto-hidden for review`, 'error');
  } else {
    toast(`Report submitted · ${r.count} / ${REPORT_THRESHOLD} needed to auto-hide`, '');
  }
  render();
}

// ---- VOTE STATE MACHINE ----
// Returns 'active' | 'grace' | 'burned' for a given vote
function voteStatus(vote) {
  if (vote.balance >= ELIGIBILITY_MIN) return 'active';
  if (!vote.droppedAt) return 'active';
  const elapsed = Date.now() - vote.droppedAt;
  if (elapsed >= GRACE_PERIOD_MS) return 'burned';
  return 'grace';
}

function graceTimeLeft(vote) {
  if (!vote.droppedAt) return null;
  return Math.max(0, GRACE_PERIOD_MS - (Date.now() - vote.droppedAt));
}

function fmtTimeLeft(ms) {
  if (ms == null) return '';
  const days = Math.floor(ms / (24*60*60*1000));
  const hours = Math.floor((ms % (24*60*60*1000)) / (60*60*1000));
  if (days > 0) return `${days}d ${hours}h left`;
  return `${hours}h left`;
}

// ============================================================
// SCORE MODEL — Asymptotic / Mass-Adoption
// ============================================================
// Each vote moves the score by a flat random amount between 1.5 and 3 points.
// Direction: +1 (upvote) adds to score, -1 (downvote) subtracts.
// Clamped to [SCORE_BASE, SCORE_CEILING].
// ============================================================

const SCORE_MIN_PTS = 1.5;    // minimum points per vote
const SCORE_MAX_PTS = 3.0;    // maximum points per vote
const WEEKLY_VOTE_CAP = 3;    // max votes per wallet per week (resets Monday UTC)
const SCORE_BASE    = 100;     // floor / starting point — score 100 = 1× multiplier
const SCORE_CEILING = 1000;    // only reachable via genuine mass adoption — score 1000 = 10×

// Storage key — changes each week so bootstrap resets are reflected
function scoreStorageKey() {
  const { week, year } = isoWeek(new Date());
  return `pve_founder_score_${year}_W${week}`;
}

// Load score from localStorage for the current week.
// Falls back to SCORE_BASE if no entry exists (new week / first load).
function loadPersistedScore() {
  try {
    const raw = localStorage.getItem(scoreStorageKey());
    if (raw !== null) {
      const val = parseFloat(raw);
      if (!isNaN(val)) return Math.max(SCORE_BASE, Math.min(SCORE_CEILING, val));
    }
  } catch (e) { /* localStorage unavailable */ }
  return SCORE_BASE;
}

function persistScore(score) {
  try { localStorage.setItem(scoreStorageKey(), String(score)); } catch(e) {}
}

// How much a single vote moves the score: flat random 1.5–3 pts.
function scoreImpact(currentScore, direction) {
  const pts = SCORE_MIN_PTS + Math.random() * (SCORE_MAX_PTS - SCORE_MIN_PTS);
  return direction > 0 ? +pts.toFixed(2) : -pts.toFixed(2);
}

// Apply a vote to state.founderScore and persist.
// Returns the score delta so UI can display it.
function applyVoteToScore(direction) {
  const before = state.founderScore;
  const delta  = scoreImpact(before, direction);
  state.prevScore   = before;
  state.founderScore = Math.max(SCORE_BASE, Math.min(SCORE_CEILING,
    parseFloat((before + delta).toFixed(2))));
  persistScore(state.founderScore);
  return delta;
}

// computeScore now just returns the live stored value.
// (Legacy ledger-iteration logic removed — score is now event-driven.)
function computeScore() {
  return state.founderScore;
}

function countActiveVotes() {
  let up = 0, down = 0, burned = 0, grace = 0;
  for (const v of state.ledger) {
    const s = voteStatus(v);
    if (s === 'burned') { burned++; continue; }
    if (s === 'grace') grace++;
    if (v.direction === 'up') up++; else down++;
  }
  return { up, down, burned, grace };
}

// ---- ENGAGEMENT SCORE → PRICE MULTIPLIER ----
// Linear: score 0 → 0.5×, score 1000 → 2.5×
function scoreToMultiplier(score) {
  // score 100 = 1×, score 1000 = 10× (linear scale)
  return +(score / 100).toFixed(2);
}

function itemArt(seed, rarity) {
  // Allow string seeds (item names) — hash them to a stable integer
  if (typeof seed === 'string') {
    let h = 0;
    for (let i = 0; i < seed.length; i++) h = ((h << 5) - h + seed.charCodeAt(i)) | 0;
    seed = Math.abs(h);
  }
  const palettes = {
    common:    ['#8a7d68', '#5a4d38'],
    uncommon:  ['#5a7a3a', '#3a5a1a'],
    rare:      ['#4a7ab0', '#2a4a80'],
    epic:      ['#9a4abf', '#6a2a8f'],
    legendary: ['#f0c878', '#a14426'],
    standard:  ['#c9a24a', '#7a5a1a'],
  };
  // Fallback: derive a palette from the seed itself
  const colors = palettes[rarity] || palettes['standard'];
  const shapes = [
    `<polygon points="50,5 60,40 95,50 60,60 50,95 40,60 5,50 40,40" fill="${colors[0]}" stroke="${colors[1]}" stroke-width="2"/>`,
    `<path d="M50 10 L80 30 L80 70 L50 90 L20 70 L20 30 Z" fill="${colors[0]}" stroke="${colors[1]}" stroke-width="2"/><circle cx="50" cy="50" r="12" fill="${colors[1]}"/>`,
    `<path d="M50 5 L55 45 L95 50 L55 55 L50 95 L45 55 L5 50 L45 45 Z" fill="${colors[1]}"/><circle cx="50" cy="50" r="8" fill="${colors[0]}"/>`,
    `<rect x="20" y="20" width="60" height="60" fill="${colors[0]}" stroke="${colors[1]}" stroke-width="3" transform="rotate(45 50 50)"/><circle cx="50" cy="50" r="10" fill="${colors[1]}"/>`,
    `<path d="M50 10 Q70 30 70 50 Q70 70 50 90 Q30 70 30 50 Q30 30 50 10 Z" fill="${colors[0]}" stroke="${colors[1]}" stroke-width="2"/><circle cx="50" cy="40" r="4" fill="${colors[1]}"/><circle cx="50" cy="60" r="4" fill="${colors[1]}"/>`,
    `<polygon points="50,10 90,90 10,90" fill="${colors[0]}" stroke="${colors[1]}" stroke-width="2"/><polygon points="50,30 75,80 25,80" fill="${colors[1]}"/>`
  ];
  return `<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
    <defs><radialGradient id="g${seed}"><stop offset="0%" stop-color="${colors[0]}" stop-opacity="0.3"/><stop offset="100%" stop-color="transparent"/></radialGradient></defs>
    <circle cx="50" cy="50" r="48" fill="url(#g${seed})"/>
    ${shapes[seed % shapes.length]}
  </svg>`;
}

const seedItems = [
  // ── Existing core items ─────────────────────────────────────────────
  { name: "Iron Arena Champion Pin",       desc: "Awarded to wave 100 survivors. Glints when held aloft in town squares.",                       basePrice: 1200, creator: "fibtechnician",      editions: 5,  tokenId: 'T-1001', editionsSold: 2 },
  { name: "Whisperblade of the Marsh",     desc: "Forged by a guild that has long since dissolved. Hums faintly near water.",                    basePrice: 280,  creator: "drifter.eth",        editions: 10, tokenId: 'T-1002', editionsSold: 4 },
  { name: "Codex Fragment XII",            desc: "A page torn from the founding ledger. References a treasury never found.",                     basePrice: 90,   creator: "scribe.dao",         editions: 12, tokenId: 'T-1003', editionsSold: 7 },
  // ── Community listings ──────────────────────────────────────────────
  { name: "Ashen Pauldrons",               desc: "Salvaged from the burned chapter. Scorch marks considered cosmetic.",                          basePrice: 420,  creator: "ashenwarden",        editions: 8,  tokenId: 'T-1004', editionsSold: 1 },
  { name: "Obsidian Hilt",                 desc: "No blade. Just the hilt. The rest of the story is yours to forge.",                           basePrice: 155,  creator: "thane_g",            editions: 15, tokenId: 'T-1005', editionsSold: 5 },
  { name: "Gilded Signet Ring",            desc: "Marked with a crest no one recognizes. Worth collecting regardless.",                          basePrice: 340,  creator: "kingless",           editions: 7,  tokenId: 'T-1006', editionsSold: 3 },
  { name: "Vial of Blight Moss",           desc: "Emits a faint green haze. Handle without sneezing.",                                          basePrice: 45,   creator: "alchemist0",         editions: 30, tokenId: 'T-1007', editionsSold: 12 },
  { name: "Expedition Journal — Vol. 3",   desc: "Handwritten notes from a cartographer who never came back. Vol. 1 and 2 are missing.",         basePrice: 175,  creator: "the_cartographer",   editions: 9,  tokenId: 'T-1008', editionsSold: 2 },
  { name: "Shadow Wraps",                  desc: "Light cloth bindings favored by scouts. Disappear in dim light.",                              basePrice: 210,  creator: "shade_19",           editions: 18, tokenId: 'T-1009', editionsSold: 6 },
  { name: "Drifter's Compass",             desc: "Points north. Unless it doesn't. Still useful in most biomes.",                               basePrice: 130,  creator: "drifter.eth",        editions: 20, tokenId: 'T-1010', editionsSold: 8 },
  { name: "Guild Seal — Dissolved",        desc: "Once opened doors across six provinces. Now opens conversations.",                             basePrice: 680,  creator: "smallhands",         editions: 6,  tokenId: 'T-1011', editionsSold: 1 },
  { name: "Thornwood Staff Fragment",      desc: "The lower third of something that was once powerful. Splinters on impact.",                    basePrice: 95,   creator: "kingless",           editions: 14, tokenId: 'T-1012', editionsSold: 4 },
];


state.items = seedItems.map((item, i) => ({
  id: 'item_' + i,
  ...item,
  sold: false,
  editionsSold: item.editionsSold || 0,
}));

// LP Market Maker — 100M $PvE trading budget
state.mmBalance  = LP_MM_RESERVE;   // 100M $PvE available to bid
state.mmInventory = [];              // items MM has bought and is holding for relist

const $ = (id) => document.getElementById(id);
const fmt = (n) => Math.round(n).toLocaleString();
const rarityFee = () => 0;  // rarity removed — no fee multiplier
const calcFinalPrice = (item, mult) => Math.min(item.basePrice * mult * (1 + rarityFee()), MAX_FINAL_PRICE);

// ---- ELIGIBILITY ----
function votesThisWeek() {
  return state.weeklyVotes[currentWeekKey()] || 0;
}
function votesAvailable() {
  return Math.max(0, WEEKLY_VOTE_CAP - votesThisWeek());
}
function lifetimeVotesCast() {
  return Object.values(state.weeklyVotes).reduce((a, b) => a + b, 0);
}
// Check if user has already voted on this founder this week
function alreadyVotedThisWeek() {
  const wk = currentWeekKey();
  return state.ledger.some(v =>
    v.voter === state.myWallet &&
    currentWeekKey(new Date(v.castAt)) === wk
  );
}
function isEligibleToVote() {
  return state.balance >= ELIGIBILITY_MIN &&
         votesAvailable() > 0 &&
         !alreadyVotedThisWeek();
}

function updateEligibilityUI() {
  const balOk      = state.balance >= ELIGIBILITY_MIN;
  const votesLeft  = votesAvailable();
  const alreadyVoted = alreadyVotedThisWeek();
  const eligible   = balOk && votesLeft > 0 && !alreadyVoted;
  const chip       = $('votesLeft');
  if (chip) {
    chip.textContent = eligible
      ? `${votesLeft}/${WEEKLY_VOTE_CAP} this week`
      : alreadyVoted ? 'voted this week' : votesLeft === 0 ? '0 left' : 'insufficient $PvE';
    chip.className = 'oracle-votes-chip ' + (eligible ? 'eligible' : 'spent');
  }
  $('upBtn').disabled   = !eligible;
  $('downBtn').disabled = !eligible;
  const lock = $('voteLockMsg');
  if (lock) {
    lock.className = 'vote-locked' + (eligible ? ' eligible' : '');
    if (!balOk)        lock.textContent = `✗ Hold ≥${ELIGIBILITY_MIN.toLocaleString()} $PvE to vote`;
    else if (alreadyVoted) lock.textContent = `✗ Already voted this week · resets ${fmtCountdown(msUntilWeekReset())}`;
    else if (!votesLeft)   lock.textContent = `✗ 0 of ${WEEKLY_VOTE_CAP} votes left · resets ${fmtCountdown(msUntilWeekReset())}`;
    else               lock.textContent = `✓ Eligible · ${votesLeft}/${WEEKLY_VOTE_CAP} votes left this week`;
  }
  if ($('phaseLabel'))  $('phaseLabel').textContent  = 'Open Voting';
  if ($('phaseDetail')) $('phaseDetail').textContent = `${votesLeft}/${WEEKLY_VOTE_CAP} votes remaining · resets Monday UTC`;
}

function openVoteModal(direction) {
  pendingVote = direction;
  const available    = votesAvailable();
  const balOk        = state.balance >= ELIGIBILITY_MIN;
  const alreadyVoted = alreadyVotedThisWeek();
  const eligible     = balOk && available > 0 && !alreadyVoted;

  $('voteTitle').textContent = direction === 'up' ? 'Cast Upvote' : 'Cast Downvote';
  $('voteDir').textContent   = direction === 'up' ? '▲ Upvote (+score)' : '▼ Downvote (−score)';

  // Show expected impact range for this vote direction
  const curScore  = state.founderScore;
  const sign      = direction === 'up' ? '+' : '−';
  $('voteImpact').textContent = `${sign}${SCORE_MIN_PTS.toFixed(1)}–${SCORE_MAX_PTS.toFixed(1)} pts (random flat)`;
  $('votesAfter').textContent = available > 0 && !alreadyVoted
    ? `${available - 1}/${WEEKLY_VOTE_CAP} left this week · resets ${fmtCountdown(msUntilWeekReset())}`
    : alreadyVoted ? 'Already voted this week' : '0 remaining';

  if ($('distToMax')) {
    const dist = (SCORE_CEILING - curScore).toFixed(2);
    $('distToMax').textContent = `${dist} pts — needs broad community votes`;
  }

  const check = $('eligibilityCheck');
  check.innerHTML = `
    <div class="check-row ${balOk ? 'pass' : 'fail'}">
      <span><span class="icon">${balOk ? '✓' : '✗'}</span><span class="check-label">Wallet holds ≥10,002 $PvE</span></span>
      <span class="check-val">${state.balance.toLocaleString()} $PvE</span>
    </div>
    <div class="check-row ${available > 0 ? 'pass' : 'fail'}">
      <span><span class="icon">${available > 0 ? '✓' : '✗'}</span><span class="check-label">Weekly votes remaining</span></span>
      <span class="check-val">${available}/${WEEKLY_VOTE_CAP} · resets ${fmtCountdown(msUntilWeekReset())}</span>
    </div>
    <div class="check-row ${!alreadyVoted ? 'pass' : 'fail'}">
      <span><span class="icon">${!alreadyVoted ? '✓' : '✗'}</span><span class="check-label">Not yet voted this week</span></span>
      <span class="check-val">${alreadyVoted ? 'Already cast this week' : 'Clear to vote'}</span>
    </div>
  `;

  $('confirmVote').disabled = !eligible;
  $('voteOverlay').classList.add('show');
}

function submitVote() {
  if (!isEligibleToVote() || !pendingVote) {
    const reason = !state.balance >= ELIGIBILITY_MIN ? 'Insufficient $PvE'
                 : alreadyVotedThisWeek() ? 'Already voted this week'
                 : 'No votes remaining this week';
    toast(reason, 'error');
    return;
  }

  // ---- ON-CHAIN INTEGRATION POINT ----
  // PRODUCTION: replace applyVoteToScore with:
  //   const tx = await votingRegistry.castVote(founderId, pendingVote === 'up');
  //   await tx.wait();
  // -----------------------------------

  // Flat random 1.5–3 pt score move
  const delta = applyVoteToScore(pendingVote === 'up' ? 1 : -1);
  const scoreAfter = state.founderScore;
  const distToMax  = (SCORE_CEILING - scoreAfter).toFixed(2);

  state.ledger.unshift({
    id: 'v_me_' + Date.now(),
    voter: state.myWallet,
    balance: state.balance,
    direction: pendingVote,
    castAt: Date.now(),
    droppedAt: null
  });

  // Track weekly vote count and per-founder dedup
  const wk = currentWeekKey();
  state.weeklyVotes[wk] = (state.weeklyVotes[wk] || 0) + 1;

  closeOverlay('voteOverlay');
  const sign     = delta >= 0 ? '+' : '';
  const deltaStr = `${sign}${Math.abs(delta).toFixed(2)}`;
  const left     = votesAvailable();
  toast(`Score ${deltaStr} → ${scoreAfter.toFixed(2)} · ${distToMax} pts to perfect · ${left}/${WEEKLY_VOTE_CAP} left`, 'success');
  pendingVote = null;
  render();
}

// ---- LEDGER RENDER ----
function renderLedger() {
  const body = $('ledgerBody');
  if (!state.ledger.length) {
    body.innerHTML = `<div class="ledger-empty">No votes cast yet. Be the first to weigh in.</div>`;
    return;
  }

  // Sort: active first, grace next, burned last; within each group, most recent first
  const sorted = [...state.ledger].sort((a, b) => {
    const order = { active: 0, grace: 1, burned: 2 };
    const sa = order[voteStatus(a)], sb = order[voteStatus(b)];
    if (sa !== sb) return sa - sb;
    return b.castAt - a.castAt;
  });

  body.innerHTML = sorted.map(v => {
    const status = voteStatus(v);
    const isMine = v.voter === state.myWallet;
    const balOk = v.balance >= ELIGIBILITY_MIN;
    const balCls = balOk ? '' : 'below';

    let actionText = '';
    let actionCls = '';
    if (status === 'active') {
      actionText = balOk ? 'in good standing' : '—';
    } else if (status === 'grace') {
      actionText = fmtTimeLeft(graceTimeLeft(v)) + ' to recover';
      actionCls = 'warn';
    } else {
      const overdue = Date.now() - v.droppedAt - GRACE_PERIOD_MS;
      const days = Math.floor(overdue / (24*60*60*1000));
      actionText = `burned ${days}d ago`;
      actionCls = 'bad';
    }

    return `
      <div class="ledger-row is-${status}">
        <div class="ledger-dir ${v.direction}">${v.direction === 'up' ? '▲' : '▼'}</div>
        <div class="voter">${isMine ? '★ ' : ''}${v.voter}</div>
        <div class="ledger-balance ${balCls}">${v.balance.toLocaleString()} $PvE</div>
        <div class="ledger-status status-${status}">${status}</div>
        <div class="ledger-balance">${daysAgo(v.castAt)} ago</div>
        <div class="ledger-action ${actionCls}">${actionText}</div>
      </div>
    `;
  }).join('');
}

function daysAgo(ts) {
  const d = Math.floor((Date.now() - ts) / (24*60*60*1000));
  if (d === 0) return 'today';
  if (d === 1) return '1d';
  return `${d}d`;
}

function setSort(order) {
  state.sortOrder = order;
  document.querySelectorAll('.sort-btn').forEach(b => {
    b.classList.toggle('active', b.dataset.sort === order);
  });
  render();
}

function sortItems(items) {
  const mult = scoreToMultiplier(computeScore());
  return items.slice().sort((a, b) => {
    const volA = a.editionsSold ? a.editionsSold * calcFinalPrice(a, mult) : 0;
    const volB = b.editionsSold ? b.editionsSold * calcFinalPrice(b, mult) : 0;
    // Score factor: items with higher rarity floors score better
    const scoreA = calcFinalPrice(a, mult);
    const scoreB = calcFinalPrice(b, mult);
    if (state.sortOrder === 'score')    return scoreB - scoreA;
    if (state.sortOrder === 'volume')   return volB - volA;
    if (state.sortOrder === 'combined') return (scoreB * 0.6 + volB * 0.4) - (scoreA * 0.6 + volA * 0.4);
    return 0;
  });
}

function render() {
  const founderScore = computeScore();
  const mult = scoreToMultiplier(founderScore);
  $('multiplier').textContent = '×' + mult.toFixed(2);
  $('curMult').textContent = mult.toFixed(2);

  // Update on-chain bar with contract addresses
  const isDeployed = !MARKETPLACE_ADDR.includes('deploy');
  if ($('contractAddrDisplay')) {
    $('contractAddrDisplay').textContent = isDeployed
      ? MARKETPLACE_ADDR.slice(0,6) + '…' + MARKETPLACE_ADDR.slice(-4)
      : 'awaiting deploy';
  }
  if ($('contractLink')) {
    $('contractLink').href = isDeployed
      ? `${CHAIN_EXPLORER}/address/${MARKETPLACE_ADDR}` : '#';
  }
  if ($('tokenAddrDisplay')) {
    $('tokenAddrDisplay').textContent = isDeployed
      ? PVE_TOKEN_ADDR.slice(0,6) + '…' + PVE_TOKEN_ADDR.slice(-4)
      : 'awaiting deploy';
  }
  if ($('tokenLink')) {
    $('tokenLink').href = isDeployed
      ? `${CHAIN_EXPLORER}/address/${PVE_TOKEN_ADDR}` : '#';
  }

  const prevMult = scoreToMultiplier(state.prevScore);
  const delta = mult - prevMult;
  const trendEl = $('multiplierTrend');
  if (delta > 0) {
    trendEl.className = 'multiplier-trend trend-up';
    trendEl.textContent = `▲ +${delta.toFixed(2)} from last vote`;
  } else if (delta < 0) {
    trendEl.className = 'multiplier-trend trend-down';
    trendEl.textContent = `▼ ${delta.toFixed(2)} from last vote`;
  } else {
    trendEl.className = 'multiplier-trend';
    trendEl.style.color = 'var(--ink-dim)';
    trendEl.textContent = '— stable';
  }

  $('scoreDisplay').textContent = founderScore.toFixed(2);
  $('scoreBar').style.width = (founderScore / 10) + '%';
  // Distance to perfect — shown as sub-label if element exists
  const distEl = document.getElementById('scoreDistLabel');
  if (distEl) {
    const dist = (1000 - founderScore).toFixed(2);
    distEl.textContent = `${dist} pts to perfect · needs mass adoption`;
  }
  $('balance').textContent = fmt(state.balance);
  // Sync fixed header wallet pill
  if ($('headerBalance')) $('headerBalance').textContent = fmt(state.balance);
  if ($('headerVotes')) {
    const vl = votesAvailable();
    const hv = $('headerVotes');
    const alreadyVoted = alreadyVotedThisWeek();
    hv.textContent = alreadyVoted ? 'voted this week' : `${vl} / ${WEEKLY_VOTE_CAP} votes`;
    hv.className = 'pill-votes' + (!alreadyVoted && vl > 0 ? '' : ' spent');
  }

  const counts = countActiveVotes();
  $('upCount').textContent = counts.up;
  $('downCount').textContent = counts.down;

  updateEligibilityUI();

  // Phase — compact oracle strip
  // No bootstrap/decay phase — open voting always
  const phaseLabel  = $('phaseLabel');
  const phaseDetail = $('phaseDetail');
  if (phaseLabel && phaseDetail) {
    phaseLabel.textContent  = 'OPEN VOTING';
    phaseDetail.textContent = 'Hold ≥10,002 $PvE · 3 votes/week · resets Monday UTC';
  }

  // Listing cap indicator
  const myActive = state.items.filter(i => state.myListings.includes(i.id) && !i.sold).length;
  const atCap = myActive >= FOUNDER_LISTING_CAP;
  const listBtn = $('listBtn');
  if (listBtn) {
    listBtn.disabled = atCap;
    listBtn.textContent = atCap
      ? `✗ Cap reached (${myActive}/${FOUNDER_LISTING_CAP})`
      : `+ List an Item (${myActive}/${FOUNDER_LISTING_CAP})`;
  }

  const rawVisible = state.filter === 'all'
    ? state.items
    : state.items.filter(i => i.category === state.filter);
  const visible = sortItems(rawVisible);

  const allFinal = state.items.filter(i => !i.sold).map(i => calcFinalPrice(i, mult));
  $('mTotal').textContent = state.items.filter(i => !i.sold).length;
  $('mFloor').textContent = allFinal.length ? fmt(Math.min(...allFinal)) : '—';
  $('mVolume').textContent = fmt(state.items.filter(i => i.sold).reduce((s, i) => s + calcFinalPrice(i, mult), 0)) || '0';
  $('mCreators').textContent = new Set(state.items.map(i => i.creator)).size;
  if ($('mMmBalance')) $('mMmBalance').textContent = fmt(state.mmBalance);
  if ($('mMmListings')) $('mMmListings').textContent = state.items.filter(i => i.isMMlisting && !i.sold).length;

  const grid = $('grid');
  // Build name→invId map so sell buttons on cards know which inventory slot to sell
  const ownedInvIds = {};
  for (const inv of state.inventory) {
    if (!ownedInvIds[inv.name]) ownedInvIds[inv.name] = [];
    ownedInvIds[inv.name].push(inv.invId);
  }
  grid.innerHTML = visible.map((item) => {
    const final = calcFinalPrice(item, mult);
    const rpt = state.reports[item.id] || { count: 0, myReport: false };
    const underReview = rpt.count >= REPORT_THRESHOLD;
    const isMine = state.myListings.includes(item.id);
    const isMusic = item.category === 'music';

    // Volume = editions sold × final price
    const volume = (item.editionsSold || 0) * final;
    // Score contribution = current Oracle price (reflects engagement)
    const scoreVal = computeScore();

    return `
      <div class="item ${underReview ? 'under-review' : ''} ${isMusic ? 'is-music' : ''}">
        ${underReview ? `
          <div class="review-overlay">
            <span class="review-icon">⚠</span>
            <div class="review-label">Under Review</div>
            <div class="review-sub">${rpt.count} community reports</div>
          </div>` : ''}
        ${isMusic ? renderMusicCard(item) : `
        <div class="item-art">
          ${renderArt(item)}
          ${item.ipfsCid ? `<div style="position:absolute;top:10px;left:10px;font-family:'JetBrains Mono',monospace;font-size:9px;letter-spacing:0.1em;padding:3px 6px;background:rgba(0,0,0,0.75);border:1px solid var(--moss);color:var(--moss);">⬡ IPFS</div>` : ''}
          ${item.tokenId ? `<div style="position:absolute;bottom:10px;left:10px;font-family:'JetBrains Mono',monospace;font-size:9px;letter-spacing:0.1em;padding:3px 6px;background:rgba(0,0,0,0.75);border:1px solid var(--gold);color:var(--gold);">⬡ ${item.tokenId}</div>` : ''}
          ${!isMine && !item.sold ? `<button class="item-report ${rpt.myReport ? 'reported' : ''}" onclick="event.stopPropagation(); reportItem('${item.id}')">${rpt.myReport ? `⚑ ${rpt.count}` : '⚐ Report'}</button>` : ''}
        </div>`}
        <div class="item-body">
          <div class="item-name">${item.name}</div>
          <!-- Clickable creator chip -->
          <button onclick="event.stopPropagation(); openCreatorCollection('${item.creator}')" style="
            display:inline-flex;align-items:center;gap:4px;
            background:transparent;border:1px solid var(--line);
            font-family:'JetBrains Mono',monospace;font-size:9px;
            color:var(--ink-dim);letter-spacing:0.1em;
            padding:3px 8px;cursor:pointer;margin-bottom:6px;
            transition:all 0.15s;
          " onmouseover="this.style.borderColor='var(--gold)';this.style.color='var(--gold)'"
             onmouseout="this.style.borderColor='var(--line)';this.style.color='var(--ink-dim)'">
            <span style="color:var(--gold);">@</span>${item.creator}
            <span style="opacity:0.5;">↗</span>
          </button>
          <!-- Score + Volume stats row -->
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:1px;background:var(--line);margin-bottom:8px;">
            <div style="background:var(--bg-2);padding:5px 8px;text-align:center;">
              <div style="font-family:'JetBrains Mono',monospace;font-size:8px;color:var(--ink-dim);letter-spacing:0.15em;text-transform:uppercase;">Score</div>
              <div style="font-family:'Cinzel',serif;font-size:13px;font-weight:700;color:var(--gold);">${scoreVal.toFixed(0)}</div>
            </div>
            <div style="background:var(--bg-2);padding:5px 8px;text-align:center;">
              <div style="font-family:'JetBrains Mono',monospace;font-size:8px;color:var(--ink-dim);letter-spacing:0.15em;text-transform:uppercase;">Volume</div>
              <div style="font-family:'Cinzel',serif;font-size:13px;font-weight:700;color:var(--moss);">${volume > 0 ? fmt(volume) : '—'}</div>
            </div>
          </div>
          <div class="item-tag">${(item.editions || 1) - (item.editionsSold || 0)} / ${item.editions || 1} left${item.isMMlisting ? ' · <span style="color:var(--moss);">⬡ MM</span>' : ''}</div>
          <div class="item-desc">${item.desc}</div>
          <div class="item-price">
            <div class="price-stack">
              <span class="price-base">Oracle · </span>
              <span class="price-final">${fmt(final)}${final >= MAX_FINAL_PRICE ? '<span style="font-family:\'JetBrains Mono\',monospace;font-size:9px;color:var(--gold);margin-left:4px;vertical-align:middle;">MAX</span>' : ''}</span>
            </div>
            <div style="display:flex;flex-direction:column;gap:4px;align-items:flex-end;">
              <button class="buy-btn ${item.sold ? 'sold' : ''}" onclick="openBuyModal('${item.id}')" ${item.sold ? 'disabled' : ''}>
                ${item.sold ? 'Sold' : 'Buy'}
              </button>
              ${ownedInvIds[item.name]?.length ? `
                <button class="buy-btn" style="border-color:var(--moss);color:var(--moss);font-size:9px;"
                  onclick="openSellModal('${ownedInvIds[item.name][0]}')">
                  ▲ Sell
                </button>
                <button class="buy-btn" style="border-color:var(--ink-dim);color:var(--ink-dim);font-size:9px;"
                  onclick="instantSell('${ownedInvIds[item.name][0]}', ${Math.round(final * MM_BID_PCT)})"
                  title="Instant sell to LP Market Maker at 80% of Oracle price">
                  ⬡ MM ${fmt(Math.round(final * MM_BID_PCT))}
                </button>` : ''}
            </div>
          </div>
        </div>
      </div>
    `;
  }).join('');

  if (!visible.length) {
    grid.innerHTML = `<div style="grid-column:1/-1;text-align:center;padding:60px 20px;color:var(--ink-dim);font-style:italic;">No goods of this kind in the shop today.</div>`;
  }

  renderTicker();
  renderLedger();
  renderExchange();
  // Draw waveforms after DOM updates (needs canvas in DOM first)
  setTimeout(drawDeferredWaveforms, 50);
}

function renderTicker() {
  const mult = scoreToMultiplier(computeScore());
  const items = state.items.slice(0, 8);
  const oneSet = items.map(item => {
    const final = calcFinalPrice(item, mult);
    const change = (Math.random() * 20 - 10).toFixed(1);
    const cls = parseFloat(change) >= 0 ? 'up' : 'down';
    const arrow = parseFloat(change) >= 0 ? '▲' : '▼';
    return `<span class="ticker-item"><span class="name">${item.name}</span> · ${fmt(final)} $PvE <span class="${cls}">${arrow} ${Math.abs(change)}%</span></span>`;
  }).join('');
  $('ticker').innerHTML = oneSet + oneSet;
}

let pendingBuy = null;

function openBuyModal(itemId) {
  const item = state.items.find(i => i.id === itemId);
  if (!item || item.sold) return;
  pendingBuy = item;

  const mult = scoreToMultiplier(computeScore());
  const adjustedBase = item.basePrice * mult * (1 + rarityFee());
  const rpt = state.reports[item.id] || { count: 0 };
  const editionsSold = item.editionsSold || 0;
  const totalEd = item.editions || 1;
  const remaining = totalEd - editionsSold;

  $('buyTitle').textContent = item.name;
  $('buyDesc').textContent = item.desc;
  $('buyBase').textContent = fmt(item.basePrice * (1 + rarityFee())) + ' $PvE';
  $('buyMult').textContent = '× ' + mult.toFixed(2);
  $('buyFee').textContent = fmt(adjustedBase * MARKETPLACE_FEE) + ' $PvE';
  $('buyTotal').textContent = fmt(adjustedBase * (1 + MARKETPLACE_FEE)) + ' $PvE';

  // Edition counter row
  let editionHtml = `<div class="modal-row">
    <span>Edition (first come, first served)</span>
    <span style="color:var(--gold-bright);font-weight:700;">#${editionsSold + 1} of ${totalEd}</span>
  </div>
  <div class="modal-row"><span>Editions remaining after this</span><span>${remaining - 1} of ${totalEd}</span></div>`;

  // MM listing badge
  let mmHtml = item.isMMlisting
    ? `<div style="font-family:'JetBrains Mono',monospace;font-size:10px;color:var(--moss);padding:8px 0;border-bottom:1px dashed var(--line);">⬡ LP MARKET MAKER LISTING · instant liquidity from PvE treasury</div>`
    : '';

  // Report warning — no refund
  let warnHtml = '';
  if (rpt.count > 0) {
    warnHtml = `
      <div style="background:rgba(161,68,38,0.2);border:1px solid var(--rust);padding:12px;margin:12px 0;font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--ink);line-height:1.6;">
        <b style="color:var(--rust);">⚠ ${rpt.count} COMMUNITY REPORT${rpt.count > 1 ? 'S' : ''}</b><br>
        This item has been flagged by community members. If it is found in violation after purchase,
        <b style="color:var(--rust);">no refund will be issued</b>. You proceed at your own risk.
      </div>`;
  }

  // On-chain provenance block — shown before purchase
  const tokenPending = !item.tokenId;
  const chainHtml = `
    <div style="background:var(--bg-2);border:1px solid var(--line);padding:12px;margin:10px 0;font-family:'JetBrains Mono',monospace;font-size:10px;line-height:1.7;">
      <div style="color:var(--gold);font-weight:700;letter-spacing:0.15em;margin-bottom:6px;">⬡ ON-CHAIN NFT</div>
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:4px 12px;color:var(--ink-dim);">
        <span>Network</span><span style="color:var(--ink);">${CHAIN_NAME}</span>
        <span>Standard</span><span style="color:var(--ink);">ERC-1155</span>
        <span>Token ID</span><span style="color:var(--gold);">${item.tokenId || 'Assigned at mint'}</span>
        <span>IPFS</span><span style="color:var(--ink);">${item.ipfsCid ? '<a href="' + ipfsUrl(item.ipfsCid) + '" target="_blank" style="color:var(--moss);">Pinned ↗</a>' : 'Stored at mint'}</span>
      </div>
      <div style="margin-top:8px;display:flex;gap:6px;flex-wrap:wrap;">
        <a href="${openSeaItem(item.tokenId || 'pending')}" target="_blank" rel="noopener"
           style="flex:1;min-width:100px;padding:5px 8px;border:1px solid #2081e2;color:#2081e2;text-align:center;font-size:9px;letter-spacing:0.1em;text-decoration:none;text-transform:uppercase;">
          ⬡ OpenSea ↗
        </a>
        <a href="${CHAIN_EXPLORER}/token/${MARKETPLACE_ADDR}" target="_blank" rel="noopener"
           style="flex:1;min-width:100px;padding:5px 8px;border:1px solid var(--line);color:var(--ink-dim);text-align:center;font-size:9px;letter-spacing:0.1em;text-decoration:none;text-transform:uppercase;">
          Explorer ↗
        </a>
      </div>
    </div>`;

  // Inject dynamic rows into modal
  $('buyOverlay').querySelectorAll('.dynamic-buy-row').forEach(el => el.remove());
  const totalRow = $('buyTotal').closest('.modal-row');
  totalRow.insertAdjacentHTML('beforebegin', `<div class="dynamic-buy-row">${mmHtml}${editionHtml}</div>`);
  totalRow.insertAdjacentHTML('afterend', `<div class="dynamic-buy-row">${chainHtml}${warnHtml}</div>`);

  $('buyOverlay').classList.add('show');
}

function closeCreatorModal() {
  $('creatorOverlay').classList.remove('show');
}

function openEditBio() {
  if ($('bioInput')) {
    $('bioInput').value = state.myBio || '';
    updateBioCount();
  }
  $('bioOverlay').classList.add('show');
}
  const mult    = scoreToMultiplier(computeScore());
  const score   = computeScore();
  // All items by this creator (active listings + exchange)
  const items   = state.items.filter(i => i.creator === creator && !i.sold);
  const exItems = state.exchange.filter(i => i.creator === creator && !i.exSold);
  const allItems = [...items, ...exItems];

  // Stats
  const totalVol   = allItems.reduce((s, i) => s + (i.editionsSold||0) * calcFinalPrice(i, mult), 0);
  const totalEds   = allItems.reduce((s, i) => s + (i.editions||1), 0);
  const soldEds    = allItems.reduce((s, i) => s + (i.editionsSold||0), 0);
  const floorPrice = items.length ? Math.min(...items.map(i => calcFinalPrice(i, mult))) : 0;

  $('creatorModalName').textContent = creator;
  // Show bio if it's the current user, otherwise show a placeholder
  const bioText = (creator === state.myWallet && state.myBio)
    ? state.myBio
    : '—';
  $('creatorModalHandle').textContent = `@${creator} · ${allItems.length} listing${allItems.length !== 1 ? 's' : ''} · ${items.length} active`;
  // Insert bio below handle
  const bioEl = document.getElementById('creatorModalBio');
  if (bioEl) bioEl.textContent = bioText;
  $('creatorModalStats').innerHTML = [
    ['Volume',    totalVol > 0 ? fmt(totalVol) + ' $PvE' : '—'],
    ['Sold',      `${soldEds} / ${totalEds} eds`],
    ['Floor',     floorPrice > 0 ? fmt(floorPrice) + ' $PvE' : '—'],
    ['Score',     score.toFixed(0)],
  ].map(([k,v]) => `<div><b>${v}</b>${k}</div>`).join('');

  if (!allItems.length) {
    $('creatorModalBody').innerHTML = `<div style="grid-column:1/-1;padding:48px;text-align:center;font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--ink-dim);">No active listings from @${creator}</div>`;
  } else {
    $('creatorModalBody').innerHTML = allItems.map(item => {
      const fp    = item.askPrice !== undefined ? item.askPrice : calcFinalPrice(item, mult);
      const vol   = (item.editionsSold||0) * fp;
      const left  = (item.editions||1) - (item.editionsSold||0);
      const isEx  = item.askPrice !== undefined;
      return `
        <div class="creator-item-card" onclick="${isEx ? `exBuy('${item.exId}')` : `openBuyModal('${item.id}'); closeCreatorModal()`}">
          <div class="creator-item-art">${renderArt(item, 'sm')}</div>
          <div class="creator-item-name">${item.name}</div>
          <div class="creator-item-meta">
            <span>${left}/${item.editions||1} left</span>
            <span>${isEx ? '⬡ Exchange' : '◈ Active'}</span>
          </div>
          <div class="creator-item-price">${fmt(fp)} <span style="font-size:9px;color:var(--ink-dim);">$PvE</span></div>
          <div style="display:flex;justify-content:space-between;">
            <div class="creator-item-score">Score ${computeScore().toFixed(0)}</div>
            <div class="creator-item-vol">${vol > 0 ? 'Vol ' + fmt(vol) : 'No sales'}</div>
          </div>
        </div>`;
    }).join('');
  }

  $('creatorOverlay').classList.add('show');
}

$('confirmBuy').addEventListener('click', () => {
  if (!pendingBuy) return;
  const mult = scoreToMultiplier(computeScore());
  const totalCost = calcFinalPrice(pendingBuy, mult) * (1 + MARKETPLACE_FEE);

  if (state.balance < totalCost) {
    toast('Insufficient $PvE balance', 'error');
    return;
  }

  state.balance -= totalCost;

  // Track editions — only fully "sold" when all editions are gone
  pendingBuy.editionsSold = (pendingBuy.editionsSold || 0) + 1;
  const totalEditions = pendingBuy.editions || 1;
  if (pendingBuy.editionsSold >= totalEditions) {
    pendingBuy.sold = true;  // last edition — remove from market
  }

  // ---- LP MARKET MAKER BID ----
  // MM places a standing bid at 80% of current final price for instant liquidity
  const mmBidPrice = totalCost * MM_BID_PCT;
  if (state.mmBalance >= mmBidPrice) {
    state.mmInventory.push({
      itemId: pendingBuy.id,
      name: pendingBuy.name,
      category: pendingBuy.category,
      rarity: pendingBuy.rarity,
      desc: pendingBuy.desc,
      imageUrl: pendingBuy.imageUrl || null,
      originalListPrice: calcFinalPrice(pendingBuy, mult),  // relist at 100% of this
      boughtAt: mmBidPrice,
      acquiredAt: Date.now()
    });
    // MM immediately relists at 100% of the original list price
    const mmRelisted = {
      id: 'item_mm_' + Date.now(),
      name: pendingBuy.name,
      category: pendingBuy.category,
      rarity: pendingBuy.rarity,
      desc: pendingBuy.desc,
      basePrice: pendingBuy.basePrice * MM_RELIST_PCT,
      creator: '⬡ LP Market Maker',
      sold: false,
      editions: 1,
      editionsSold: 0,
      imageUrl: pendingBuy.imageUrl || null,
      ipfsCid: pendingBuy.ipfsCid || null,
      isMMlisting: true
    };
    state.items.push(mmRelisted);
    state.mmBalance -= mmBidPrice;
  }

  // Add to user's inventory
  state.inventory.push({
    invId: 'inv_' + Date.now() + '_' + Math.random().toString(36).slice(2, 6),
    name: pendingBuy.name,
    category: pendingBuy.category,
    rarity: pendingBuy.rarity,
    desc: pendingBuy.desc,
    paidAmount: totalCost,
    editionNumber: pendingBuy.editionsSold,
    totalEditions,
    originalCreator: pendingBuy.creator,
    artSeed: pendingBuy.name,
    imageUrl: pendingBuy.imageUrl || null,
    ipfsCid: pendingBuy.ipfsCid || null,
    audioUrl: pendingBuy.audioUrl || null,
    audioFileName: pendingBuy.audioFileName || null,
    audioDuration: pendingBuy.audioDuration || null,
    tokenId: pendingBuy.tokenId || null,
    reportedItem: (state.reports[pendingBuy.id]?.count || 0) > 0,
    acquiredAt: Date.now()
  });
  logTx('buy', pendingBuy.name, totalCost);
  state.totalCostBasis += totalCost;

  closeOverlay('buyOverlay');
  toast(`Acquired · Edition ${pendingBuy.editionsSold}/${totalEditions} · ${pendingBuy.name}`, 'success');
  pendingBuy = null;
  render();
});

// ==================== IMAGE HANDLING ====================
// Staging area for images being attached to current modal
const imageStaging = { new: null, sell: null }; // base64 data URLs

function handleImageUpload(ctx) {
  const file = $(`${ctx}ImgFile`).files[0];
  if (!file) return;
  if (file.size > 10 * 1024 * 1024) {
    toast('Image too large — max 10MB', 'error');
    $(`${ctx}ImgFile`).value = '';
    return;
  }
  const reader = new FileReader();
  reader.onload = (e) => {
    imageStaging[ctx] = e.target.result;
    const zone = $(`${ctx}ImgZone`);
    zone.classList.add('has-image');
    // Replace zone contents with preview (keep hidden file input)
    const fileInput = zone.querySelector('input[type="file"]');
    zone.innerHTML = '';
    zone.appendChild(fileInput);
    const img = document.createElement('img');
    img.className = 'preview';
    img.src = e.target.result;
    zone.appendChild(img);
    $(`${ctx}ImgClear`).style.display = 'block';
    if (ctx === 'sell') updateSellPreview();
  };
  reader.readAsDataURL(file);
}

function clearImage(ctx) {
  imageStaging[ctx] = null;
  const zone = $(`${ctx}ImgZone`);
  zone.classList.remove('has-image');
  zone.innerHTML = `
    <input type="file" id="${ctx}ImgFile" accept="image/jpeg,image/png,image/gif,image/webp" onchange="handleImageUpload('${ctx}')">
    <span class="upload-icon">⬆</span>
    <div class="upload-prompt">Click or drag to upload image</div>
  `;
  $(`${ctx}ImgClear`).style.display = 'none';
}

// ==================== AUDIO HANDLING ====================
const audioStaging = { new: null, sell: null };  // { dataUrl, fileName, duration }

function handleAudioUpload(ctx) {
  const file = $(`${ctx}AudioFile`).files[0];
  if (!file) return;
  const MAX_AUDIO_MB = 10;
  if (file.size > MAX_AUDIO_MB * 1024 * 1024) {
    toast(`Audio too large — max ${MAX_AUDIO_MB}MB`, 'error');
    $(`${ctx}AudioFile`).value = '';
    return;
  }
  const reader = new FileReader();
  reader.onload = (e) => {
    const dataUrl = e.target.result;
    // Get duration via a throwaway Audio element
    const tmp = new Audio(dataUrl);
    tmp.addEventListener('loadedmetadata', () => {
      audioStaging[ctx] = { dataUrl, fileName: file.name, duration: tmp.duration };
      const zone = $(`${ctx}AudioZone`);
      zone.classList.add('has-audio');
      const fileInput = zone.querySelector('input[type="file"]');
      zone.innerHTML = '';
      zone.appendChild(fileInput);
      zone.innerHTML += `<div style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--moss);padding:8px 0;pointer-events:none;">♪ ${file.name} · ${fmtDuration(tmp.duration)}</div>`;
      $(`${ctx}AudioClear`).style.display = 'block';
      // Draw waveform preview
      const preview = $(`${ctx}AudioPreview`);
      preview.style.display = 'block';
      preview.innerHTML = `<canvas id="${ctx}WavePreview" class="waveform-canvas" style="background:var(--bg-2);border:1px solid var(--line);"></canvas>`;
      drawWaveformFromUrl(dataUrl, `${ctx}WavePreview`, '#c9a24a');
    });
  };
  reader.readAsDataURL(file);
}

function clearAudio(ctx) {
  audioStaging[ctx] = null;
  const zone = $(`${ctx}AudioZone`);
  zone.classList.remove('has-audio');
  zone.innerHTML = `
    <input type="file" id="${ctx}AudioFile" accept="audio/mpeg,audio/wav,audio/ogg,audio/mp3" onchange="handleAudioUpload('${ctx}')">
    <span class="upload-icon" style="font-size:22px;">♪</span>
    <div class="upload-prompt">Click or drag to upload audio</div>
  `;
  $(`${ctx}AudioClear`).style.display = 'none';
  $(`${ctx}AudioPreview`).style.display = 'none';
}

function fmtDuration(secs) {
  if (!isFinite(secs)) return '?:??';
  const m = Math.floor(secs / 60);
  const s = Math.floor(secs % 60);
  return `${m}:${s.toString().padStart(2,'0')}`;
}

// Draw waveform bars from an audio data URL using Web Audio API
function drawWaveformFromUrl(dataUrl, canvasId, color) {
  const canvas = document.getElementById(canvasId);
  if (!canvas) return;
  const ctx2d = canvas.getContext('2d');
  canvas.width = canvas.offsetWidth || 260;
  canvas.height = 44;

  fetch(dataUrl)
    .then(r => r.arrayBuffer())
    .then(buf => new AudioContext().decodeAudioData(buf))
    .then(audioBuf => {
      const data = audioBuf.getChannelData(0);
      const W = canvas.width, H = canvas.height;
      const bars = Math.floor(W / 3);
      const step = Math.floor(data.length / bars);
      ctx2d.clearRect(0, 0, W, H);
      ctx2d.fillStyle = color;
      for (let i = 0; i < bars; i++) {
        let max = 0;
        for (let j = 0; j < step; j++) max = Math.max(max, Math.abs(data[i * step + j] || 0));
        const barH = Math.max(2, max * H * 0.9);
        ctx2d.fillRect(i * 3, (H - barH) / 2, 2, barH);
      }
    })
    .catch(() => {
      // Fallback: random-ish bars if decode fails (Safari quirk)
      const W = canvas.width, H = canvas.height;
      ctx2d.fillStyle = color;
      for (let i = 0; i < Math.floor(W/3); i++) {
        const h = 4 + Math.random() * (H - 8);
        ctx2d.fillRect(i * 3, (H - h) / 2, 2, h);
      }
    });
}

// Active audio players: { itemId: HTMLAudioElement }
const activePlayers = {};

function togglePlay(itemId, dataUrl) {
  // Stop all other players first
  for (const [id, audio] of Object.entries(activePlayers)) {
    if (id !== itemId) {
      audio.pause();
      audio.currentTime = 0;
      const btn = document.querySelector(`[data-play="${id}"]`);
      if (btn) { btn.textContent = '▶'; btn.classList.remove('playing'); }
      const prog = document.getElementById(`prog_${id}`);
      if (prog) prog.style.width = '0%';
    }
  }

  let audio = activePlayers[itemId];
  const btn = document.querySelector(`[data-play="${itemId}"]`);
  const prog = document.getElementById(`prog_${itemId}`);

  if (!audio) {
    audio = new Audio(dataUrl);
    activePlayers[itemId] = audio;
    audio.addEventListener('timeupdate', () => {
      const pct = (audio.currentTime / audio.duration) * 100;
      if (prog) prog.style.width = pct + '%';
    });
    audio.addEventListener('ended', () => {
      if (btn) { btn.textContent = '▶'; btn.classList.remove('playing'); }
      if (prog) prog.style.width = '0%';
    });
  }

  if (audio.paused) {
    audio.play();
    if (btn) { btn.textContent = '■'; btn.classList.add('playing'); }
  } else {
    audio.pause();
    audio.currentTime = 0;
    if (btn) { btn.textContent = '▶'; btn.classList.remove('playing'); }
    if (prog) prog.style.width = '0%';
  }
}

// Show/hide audio section based on category selection
// Category change listener removed — audio upload removed from list modal

// Music card art renderer — called from grid render when item.category === 'music'
function renderMusicCard(item) {
  if (!item.audioUrl) {
    return `<div class="music-card-art">
      <div class="big-note">♪</div>
      <div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--ink-dim);letter-spacing:0.15em;">NO PREVIEW</div>
    </div>`;
  }
  const dur = item.audioDuration ? fmtDuration(item.audioDuration) : '?:??';
  return `
    <div class="music-card-art">
      <div class="big-note">♪</div>
      <canvas id="wave_${item.id}" class="waveform-canvas" style="background:transparent;padding:0 12px;"></canvas>
    </div>
    <div class="music-controls">
      <button class="play-btn" data-play="${item.id}" onclick="event.stopPropagation();togglePlay('${item.id}','${item.audioUrl}')">▶</button>
      <div class="track-info">
        <div class="track-name">${item.audioFileName || item.name}</div>
        <div class="track-duration">${dur}</div>
        <div class="progress-bar" onclick="seekTrack(event,'${item.id}')"><div class="progress-fill" id="prog_${item.id}"></div></div>
      </div>
    </div>`;
}

function seekTrack(e, itemId) {
  const audio = activePlayers[itemId];
  if (!audio) return;
  const rect = e.currentTarget.getBoundingClientRect();
  const pct = (e.clientX - rect.left) / rect.width;
  audio.currentTime = pct * audio.duration;
}

// Deferred waveform draw — called after grid renders (canvas must be in DOM)
function drawDeferredWaveforms() {
  const mult = scoreToMultiplier(computeScore());
  const visible = state.filter === 'all'
    ? state.items : state.items.filter(i => i.category === state.filter);
  for (const item of visible) {
    if (item.category === 'music' && item.audioUrl) {
      const canvasId = `wave_${item.id}`;
      const canvas = document.getElementById(canvasId);
      if (canvas) drawWaveformFromUrl(item.audioUrl, canvasId, '#c9a24a');
    }
  }
}

// Render item art: if item has an imageUrl, show it; otherwise fall back to SVG
function renderArt(item, sizeClass) {
  if (item.imageUrl) {
    return `<img src="${item.imageUrl}" alt="${item.name}" style="width:100%;height:100%;object-fit:cover;display:block;">`;
  }
  return itemArt(item.name, item.rarity);
}

// Watch IPFS CID field and update badge
document.addEventListener('input', (e) => {
  if (e.target.id === 'newImgCid') {
    const val = e.target.value.trim();
    const badge = $('newCidBadge');
    if (val.startsWith('ipfs://') || val.startsWith('ar://') || val.startsWith('Qm')) {
      badge.className = 'ipfs-badge linked';
      badge.textContent = '✓ LINKED';
    } else if (val) {
      badge.className = 'ipfs-badge';
      badge.textContent = '? CHECK';
    } else {
      badge.className = 'ipfs-badge';
      badge.textContent = 'IPFS/AR';
    }
  }
});

// ── BIO FUNCTIONS ────────────────────────────────────────────────────
function updateBioCount() {
  const input = $('bioInput');
  if (!input) return;
  const len = input.value.length;
  const count = $('bioCount');
  const btn   = $('bioSaveBtn');
  if (count) {
    count.textContent = `${len} / 80`;
    count.className   = len >= 80 ? 'bio-char-count over' : len >= 65 ? 'bio-char-count near' : 'bio-char-count ok';
  }
  if (input) input.classList.toggle('over-limit', len >= 80);
  if (btn)   btn.disabled = len === 0;
}

function saveBio() {
  const val = ($('bioInput').value || '').trim();
  if (!val) { toast('Please write a biography before continuing', 'error'); return; }
  if (val.length > 80) { toast('Biography must be 80 characters or fewer', 'error'); return; }
  state.myBio = val;
  try { localStorage.setItem('pve_bio_' + state.myWallet, val); } catch(e) {}
  closeOverlay('bioOverlay');
  toast('Biography saved ✓', 'success');
  setTimeout(_openListModalDirect, 350);
}

function openListModal() {
  // Load persisted bio
  if (!state.myBio) {
    try { const s = localStorage.getItem('pve_bio_' + state.myWallet); if (s) state.myBio = s; } catch(e) {}
  }
  // Gate: bio required before first listing
  if (!state.myBio || !state.myBio.trim()) {
    if ($('bioInput')) { $('bioInput').value = ''; updateBioCount(); }
    $('bioOverlay').classList.add('show');
    return;
  }
  _openListModalDirect();
}

function _openListModalDirect() {
  const myActive = state.items.filter(i => state.myListings.includes(i.id) && !i.sold).length;
  if (myActive >= FOUNDER_LISTING_CAP) {
    toast(`Listing cap reached — max ${FOUNDER_LISTING_CAP} active listings.`, 'error');
    return;
  }
  $('newName').value = '';
  $('newDesc').value = '';
  if ($('newBasePrice')) $('newBasePrice').value = '100';
  if ($('newCategory')) $('newCategory').value = 'item';
  if ($('newImgCid')) $('newImgCid').value = '';
  if ($('newEditions')) $('newEditions').value = MIN_EDITIONS;
  if ($('newCidBadge')) { $('newCidBadge').className = 'ipfs-badge'; $('newCidBadge').textContent = 'IPFS/AR'; }
  clearImage('new');
  if ($('listModalCap')) $('listModalCap').textContent = `${myActive} of ${FOUNDER_LISTING_CAP} slots used`;
  updateListPreview();
  $('listOverlay').classList.add('show');
}

function updateListPreview() {
  const base  = Math.min(69420, Math.max(11, parseFloat($('newBasePrice')?.value) || 100));
  const mult  = scoreToMultiplier(computeScore());
  const final = Math.min(base * mult, MAX_FINAL_PRICE);
  const el    = $('newCalc');
  if (el) {
    el.textContent = fmt(final) + ' $PvE' + (base * mult > MAX_FINAL_PRICE ? ' ⚠ capped' : '');
    el.style.color = base * mult > MAX_FINAL_PRICE ? 'var(--rust)' : 'var(--gold-bright)';
  }
  if ($('curMult')) $('curMult').textContent = mult.toFixed(2);
}

function submitListing() {
  const name     = $('newName').value.trim();
  const desc     = $('newDesc').value.trim();
  const editions = parseInt($('newEditions')?.value || MIN_EDITIONS);
  const category = ($('newCategory') && $('newCategory').value) || 'item';
  const basePrice = parseFloat($('newBasePrice')?.value);

  if (!name || !desc) {
    toast('Fill all fields with valid values', 'error');
    return;
  }
  if (isNaN(basePrice) || basePrice < 11 || basePrice > MAX_FINAL_PRICE) {
    toast(`Price must be between 11 and ${MAX_FINAL_PRICE.toLocaleString()} $PvE`, 'error');
    return;
  }
  if (isNaN(editions) || editions < MIN_EDITIONS) {
    toast(`Minimum ${MIN_EDITIONS} editions required per listing`, 'error');
    return;
  }
  const myActiveListing = state.items.filter(i => state.myListings.includes(i.id) && !i.sold).length;
  if (myActiveListing >= FOUNDER_LISTING_CAP) {
    toast(`Listing cap reached — max ${FOUNDER_LISTING_CAP} active listings. Unlist or sell one first.`, 'error');
    return;
  }
  if (state.balance < LISTING_DEPOSIT) {
    toast(`Insufficient balance — ${LISTING_DEPOSIT} $PvE deposit required to list.`, 'error');
    return;
  }

  state.balance      -= LISTING_DEPOSIT;
  state.lockedDeposit += LISTING_DEPOSIT;

  const imageUrl = imageStaging.new || null;
  const ipfsCid  = ($('newImgCid') && $('newImgCid').value.trim()) || null;

  const mockTokenId = 'T-' + Math.floor(Math.random() * 9000 + 1000);
  const mockTxHash  = '0x' + [...Array(8)].map(() => Math.floor(Math.random()*16).toString(16)).join('') + '...';

  const newItem = {
    id: 'item_' + Date.now(),
    name, desc, category,
    rarity: 'standard',
    basePrice,
    editions,
    editionsSold: 0,
    creator: state.myWallet,
    sold: false,
    listedByMe: true,
    imageUrl,
    ipfsCid,
    audioUrl:      audioStaging.new?.dataUrl   || null,
    audioFileName: audioStaging.new?.fileName  || null,
    audioDuration: audioStaging.new?.duration  || null,
    tokenId:       mockTokenId,
    txHash:        mockTxHash,
    depositLocked: LISTING_DEPOSIT
  };

  imageStaging.new = null;
  audioStaging.new = null;
  state.items.unshift(newItem);
  state.myListings.push(newItem.id);
  logTx('list', name, basePrice);
  closeOverlay('listOverlay');
  toast(`Listed · ${editions} editions · ${fmt(basePrice)} base · NFT ${mockTokenId} · ${LISTING_DEPOSIT} $PvE deposit locked`, 'success');
  render();
}

function closeOverlay(id) {
  $(id).classList.remove('show');
}
document.querySelectorAll('.overlay').forEach(o => {
  o.addEventListener('click', (e) => {
    if (e.target === o) o.classList.remove('show');
  });
});

$('filters').addEventListener('click', (e) => {
  const btn = e.target.closest('.filter');
  if (!btn) return;
  document.querySelectorAll('.filter').forEach(f => f.classList.remove('active'));
  btn.classList.add('active');
  state.filter = 'all';  // categories removed
  render();
});

let toastTimeout;
function toast(msg, type = '') {
  const t = $('toast');
  t.textContent = msg;
  t.className = 'toast show ' + type;
  clearTimeout(toastTimeout);
  toastTimeout = setTimeout(() => t.classList.remove('show'), 3500);
}

function setBalance(n) { state.balance = n; render(); toast(`Balance set: ${n.toLocaleString()} $PvE`); }
function useWeeklyVotes(n) {
  state.weeklyVotes[currentWeekKey()] = n;
  render();
  toast(`Weekly votes used set to: ${n}`);
}
function simDecayPhase() {
  // Simulate being 4 weeks past launch
  window._launchTsOverride = Date.now() - 28 * 24 * 60 * 60 * 1000;
  render();
  toast('Simulating decay phase (4 weeks since launch)', 'success');
}
function simBootstrapPhase() {
  window._launchTsOverride = Date.now() - 14 * 24 * 60 * 60 * 1000;
  render();
  toast('Simulating bootstrap phase week 2', 'success');
}

// ==================== WALLET DRAWER ====================
function openCollectionItem(invId) {
  const item = state.inventory.find(i => i.invId === invId);
  if (!item) return;
  const isReported = item.reportedItem;
  const art = item.imageUrl
    ? `<img src="${item.imageUrl}" style="width:100%;max-height:260px;object-fit:contain;display:block;">`
    : `<div style="width:100%;aspect-ratio:1;max-height:260px;display:flex;align-items:center;justify-content:center;background:var(--bg-2);">${itemArt(item.artSeed || item.name, item.rarity)}</div>`;

  const reportWarning = isReported
    ? `<div style="background:rgba(161,68,38,0.2);border:1px solid var(--rust);padding:10px;margin:10px 0;font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--rust);line-height:1.5;">
        ⚑ This item was flagged at purchase. No refund is available.
       </div>`
    : '';

  const body = $('drawerBody');
  body.innerHTML = `
    <div style="padding-bottom:80px;">
      <button onclick="switchDrawerTab('collection')" style="background:transparent;border:none;color:var(--gold);font-family:'JetBrains Mono',monospace;font-size:11px;cursor:pointer;letter-spacing:0.1em;margin-bottom:14px;">← BACK TO COLLECTION</button>
      ${art}
      ${reportWarning}
      <div style="padding:16px 0;">
        <div style="font-family:'Cinzel',serif;font-size:20px;font-weight:700;color:var(--gold-bright);margin-bottom:4px;">${item.name}</div>
        <div style="font-family:'JetBrains Mono',monospace;font-size:10px;letter-spacing:0.2em;color:var(--ink-dim);text-transform:uppercase;margin-bottom:12px;" class="r-">${item.category}</div>
        <div style="font-family:'Cormorant Garamond',serif;font-style:italic;color:var(--ink-dim);font-size:14px;line-height:1.5;margin-bottom:16px;">${item.desc}</div>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:1px;background:var(--line);margin-bottom:16px;">
          ${[
            ['Edition', `${item.editionNumber || 1} of ${item.totalEditions || 1}`],
            ['Paid', `${fmt(item.paidAmount)} $PvE`],
            ['Creator', item.originalCreator || '—'],
            ['Acquired', timeAgo(item.acquiredAt)],
            ['Token ID', item.tokenId || 'N/A'],
            ['IPFS', item.ipfsCid ? '✓ Pinned' : 'Not pinned'],
          ].map(([k, v]) => `
            <div style="background:var(--bg-2);padding:10px 12px;">
              <div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--ink-dim);letter-spacing:0.15em;text-transform:uppercase;">${k}</div>
              <div style="font-family:'JetBrains Mono',monospace;font-size:12px;color:var(--ink);margin-top:4px;overflow:hidden;text-overflow:ellipsis;">${v}</div>
            </div>`).join('')}
        </div>
        <button class="inv-sell-btn" style="width:100%;padding:12px;" onclick="openSellModal('${item.invId}')">List for Sale</button>
        <button class="inv-sell-btn" style="width:100%;padding:12px;margin-top:8px;border-color:var(--moss);color:var(--moss);" onclick="instantSell('${item.invId}', ${Math.round(item.paidAmount * MM_BID_PCT)})">
          ⬡ Instant Sell · ~${fmt(Math.round(item.paidAmount * MM_BID_PCT))} $PvE (80% MM bid)
        </button>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:6px;margin-top:10px;">
          <a class="inv-sell-btn" style="text-align:center;text-decoration:none;border-color:#2081e2;color:#2081e2;padding:10px;"
             href="${openSeaItem(item.tokenId || 'pending')}" target="_blank" rel="noopener">
            ⬡ OpenSea ↗
          </a>
          <a class="inv-sell-btn" style="text-align:center;text-decoration:none;padding:10px;"
             href="${CHAIN_EXPLORER}/token/${MARKETPLACE_ADDR}${item.tokenId ? '?a='+item.tokenId : ''}" target="_blank" rel="noopener">
            Explorer ↗
          </a>
        </div>
        <div style="font-family:'JetBrains Mono',monospace;font-size:8px;color:var(--ink-dim);margin-top:8px;line-height:1.5;padding:8px;background:var(--bg-2);border:1px solid var(--line);">
          ⬡ ERC-1155 on ${CHAIN_NAME} · Ownership verified on-chain · Auto-indexed on OpenSea after first mint
        </div>
      </div>
    </div>
  `;
}

function openWallet() {
  $('walletDrawer').classList.add('show');
  $('drawerBackdrop').classList.add('show');
  renderDrawer();
}
function closeWallet() {
  $('walletDrawer').classList.remove('show');
  $('drawerBackdrop').classList.remove('show');
}
function switchDrawerTab(tab) {
  state.drawerTab = tab;
  document.querySelectorAll('.drawer-tab').forEach(t => {
    t.classList.toggle('active', t.dataset.tab === tab);
  });
  renderDrawer();
}

function renderDrawer() {
  $('drawerBalance').textContent = fmt(state.balance);
  $('drawerInvCount').textContent = state.inventory.length;
  // Listings = my items still alive in the market
  const liveListings = state.items.filter(i => state.myListings.includes(i.id) && !i.sold);
  $('drawerListedCount').textContent = liveListings.length;
  $('drawerEarnings').textContent = fmt(state.totalEarned);
  if ($('drawerLocked')) $('drawerLocked').textContent = fmt(state.lockedDeposit);
  $('tabInvCount').textContent = `(${state.inventory.length})`;
  // Bio strip
  const bioEl = $('drawerBioText');
  if (bioEl) {
    if (state.myBio) {
      bioEl.textContent = '"' + state.myBio + '"';
      bioEl.style.color = 'var(--ink)';
    } else {
      bioEl.textContent = 'No biography yet — required before listing';
      bioEl.style.color = 'var(--rust)';
    }
  }
  $('tabListedCount').textContent = `(${liveListings.length})`;

  const body = $('drawerBody');

  if (state.drawerTab === 'inventory') {
    if (!state.inventory.length) {
      body.innerHTML = `<div class="inv-empty">
        <span class="icon">⚔</span>
        Your inventory is empty.<br>
        Buy items from the shop — they'll appear here, ready to use, hold, or sell.
      </div>`;
      return;
    }
    const mult = scoreToMultiplier(computeScore());
    body.innerHTML = state.inventory.map(item => {
      // Find the original market item to price against
      const marketItem = state.items.find(i => i.name === item.name && !i.isMMlisting);
      const baseForMM = marketItem ? marketItem.basePrice : (item.paidAmount / (mult * (1 + (rarityFee() || 0)) * (1 + MARKETPLACE_FEE)));
      const currentFinalPrice = baseForMM * mult * (1 + (rarityFee() || 0));
      const instantSellPrice = Math.round(currentFinalPrice * MM_BID_PCT);
      return `
        <div class="inv-item">
          <div class="inv-art">${renderArt(item)}</div>
          <div class="inv-info">
            <div class="inv-name">${item.name}</div>
            <div class="inv-meta">${item.category}</div>
            <div class="inv-paid">Paid <span class="v">${fmt(item.paidAmount)}</span> $PvE</div>
            <div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--moss);margin-top:3px;">
              ⬡ MM bid: <b>${fmt(instantSellPrice)} $PvE</b>
            </div>
          </div>
          <div class="inv-action">
            <button class="inv-sell-btn" onclick="openSellModal('${item.invId}')">List</button>
            <button class="inv-sell-btn" style="border-color:var(--moss);color:var(--moss);margin-top:4px;" onclick="instantSell('${item.invId}', ${instantSellPrice})">
              Sell Now<br><span style="font-size:8px;letter-spacing:0;">${fmt(instantSellPrice)} $PvE</span>
            </button>
          </div>
        </div>
      `;
    }).join('');
  }
  else if (state.drawerTab === 'listed') {
    if (!liveListings.length) {
      body.innerHTML = `<div class="inv-empty">
        <span class="icon">⚖</span>
        No active listings.<br>
        Sell from your inventory or forge a new listing from the main page.
      </div>`;
      return;
    }
    const mult = scoreToMultiplier(computeScore());
    body.innerHTML = liveListings.map(item => {
      const final = calcFinalPrice(item, mult);
      return `
        <div class="inv-item">
          <div class="inv-art">${renderArt(item)}</div>
          <div class="inv-info">
            <div class="inv-name">${item.name}</div>
            <div class="inv-meta">${item.category}</div>
            <div class="inv-paid">Listed @ <span class="v">${fmt(final)}</span> $PvE</div>
            ${item.tokenId ? `<div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--moss);margin-top:2px;">⬡ NFT ${item.tokenId}</div>` : ''}
            <div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--gold);margin-top:2px;">⬡ ${item.depositLocked || LISTING_DEPOSIT} $PvE locked</div>
          </div>
          <div class="inv-action">
            <button class="inv-sell-btn listed" onclick="unlistItem('${item.id}')">Unlist</button>
          </div>
        </div>
      `;
    }).join('');
  }
  else if (state.drawerTab === 'collection') {
    const allOwned = [...state.inventory];
    // Also include items currently listed by me (they're part of collection)
    const listedItems = state.items.filter(i => state.myListings.includes(i.id) && !i.sold);

    if (!allOwned.length && !listedItems.length) {
      body.innerHTML = `<div class="inv-empty">
        <span class="icon">⚔</span>
        Your collection is empty.<br>Buy items from the shop to start building.
      </div>`;
      return;
    }

    const rarityOrder = { legendary: 0, epic: 1, rare: 2, uncommon: 3, common: 4 };
    const sorted = [...allOwned].sort((a, b) => (rarityOrder[a.rarity] || 5) - (rarityOrder[b.rarity] || 5));

    // Rarity totals
    const rarityCount = {};
    for (const item of sorted) rarityCount[item.rarity] = (rarityCount[item.rarity] || 0) + 1;
    const rarityBadges = Object.entries(rarityCount)
      .map(([r, n]) => `<span style="font-family:'JetBrains Mono',monospace;font-size:9px;letter-spacing:0.1em;padding:2px 7px;border:1px solid;margin-right:4px;" class="r-${r}">${n} ${r}</span>`)
      .join('');

    const totalValue = sorted.reduce((s, i) => s + (i.paidAmount || 0), 0);

    body.innerHTML = `
      <div style="padding:14px 0 8px;border-bottom:1px solid var(--line);margin-bottom:12px;">
        <div style="font-family:'JetBrains Mono',monospace;font-size:10px;color:var(--ink-dim);margin-bottom:8px;letter-spacing:0.1em;">
          ${sorted.length} ITEM${sorted.length !== 1 ? 'S' : ''} · TOTAL PAID ${fmt(totalValue)} $PvE
        </div>
        <div>${rarityBadges}</div>
      </div>
      <div style="display:grid;grid-template-columns:repeat(2,1fr);gap:10px;">
        ${sorted.map(item => {
          const isReported = item.reportedItem;
          return `
            <div style="background:var(--bg-2);border:1px solid ${isReported ? 'var(--rust)' : 'var(--line)'};padding:0;position:relative;cursor:pointer;" onclick="openCollectionItem('${item.invId}')">
              <div style="aspect-ratio:1;overflow:hidden;display:flex;align-items:center;justify-content:center;background:var(--bg-0);">
                ${item.imageUrl
                  ? `<img src="${item.imageUrl}" style="width:100%;height:100%;object-fit:cover;">`
                  : `<div style="width:100%;height:100%;display:flex;align-items:center;justify-content:center;">${itemArt(item.artSeed || item.name, item.rarity)}</div>`}
                ${isReported ? `<div style="position:absolute;top:4px;right:4px;background:var(--rust);font-family:'JetBrains Mono',monospace;font-size:8px;padding:2px 5px;color:white;">⚑</div>` : ''}
                ${item.tokenId ? `<div style="position:absolute;bottom:4px;left:4px;background:rgba(0,0,0,0.75);border:1px solid var(--gold);font-family:'JetBrains Mono',monospace;font-size:8px;color:var(--gold);padding:2px 5px;">${item.tokenId}</div>` : ''}
              </div>
              <div style="padding:8px;">
                <div style="font-family:'Cinzel',serif;font-size:12px;font-weight:700;color:var(--ink);overflow:hidden;text-overflow:ellipsis;white-space:nowrap;">${item.name}</div>
                <div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--ink-dim);margin-top:2px;" class="r-"></div>
                <div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--gold);margin-top:4px;">Ed. ${item.editionNumber || 1}/${item.totalEditions || 1}</div>
              </div>
            </div>`;
        }).join('')}
      </div>
      ${listedItems.length ? `
        <div style="margin-top:16px;padding-top:14px;border-top:1px solid var(--line);font-family:'JetBrains Mono',monospace;font-size:10px;color:var(--ink-dim);letter-spacing:0.1em;">
          ${listedItems.length} ITEM${listedItems.length !== 1 ? 'S' : ''} CURRENTLY LISTED FOR SALE
        </div>` : ''}
    `;
  }
  else if (state.drawerTab === 'pnl') {
    const mult = scoreToMultiplier(computeScore());
    const unrealized = state.inventory.reduce((sum, item) => {
      const currentVal = oraclePrice(item.rarity);
      return sum + (currentVal - item.paidAmount);
    }, 0);
    const totalPnl = state.totalRealized + unrealized;
    const pnlColor = (v) => v >= 0 ? 'var(--moss)' : 'var(--rust)';
    const pnlSign  = (v) => v >= 0 ? '+' : '';

    // Win rate from closed positions
    const winners = state.pnlLedger.filter(e => e.pnl >= 0).length;
    const winRate = state.pnlLedger.length
      ? Math.round((winners / state.pnlLedger.length) * 100)
      : null;

    // Best and worst closed trade
    const bestTrade  = state.pnlLedger.length ? state.pnlLedger.reduce((a,b) => a.pnl > b.pnl ? a : b) : null;
    const worstTrade = state.pnlLedger.length ? state.pnlLedger.reduce((a,b) => a.pnl < b.pnl ? a : b) : null;

    body.innerHTML = `
      <!-- Summary cards -->
      <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:1px;background:var(--line);margin-bottom:12px;">
        ${[['Total P&L', totalPnl],['Realized', state.totalRealized],['Unrealized', unrealized]].map(([k,v]) => `
          <div style="background:var(--bg-2);padding:10px;text-align:center;">
            <div style="font-family:'JetBrains Mono',monospace;font-size:8px;letter-spacing:0.2em;color:var(--ink-dim);text-transform:uppercase;margin-bottom:3px;">${k}</div>
            <div style="font-family:'Cinzel',serif;font-size:16px;font-weight:700;color:${pnlColor(v)};">${pnlSign(v)}${fmt(v)}</div>
            <div style="font-family:'JetBrains Mono',monospace;font-size:8px;color:var(--ink-dim);">$PvE</div>
          </div>`).join('')}
      </div>

      <!-- Stats row -->
      <div style="display:grid;grid-template-columns:repeat(4,1fr);gap:1px;background:var(--line);margin-bottom:14px;">
        ${[
          ['Cost Basis', fmt(state.totalCostBasis) + ' $PvE', 'var(--ink)'],
          ['Win Rate', winRate !== null ? winRate + '%' : '—', winRate >= 50 ? 'var(--moss)' : 'var(--rust)'],
          ['Best Trade', bestTrade ? pnlSign(bestTrade.pnl)+fmt(bestTrade.pnl) : '—', 'var(--moss)'],
          ['Worst Trade', worstTrade ? pnlSign(worstTrade.pnl)+fmt(worstTrade.pnl) : '—', 'var(--rust)'],
        ].map(([k,v,c]) => `
          <div style="background:var(--bg-2);padding:8px;text-align:center;">
            <div style="font-family:'JetBrains Mono',monospace;font-size:8px;letter-spacing:0.15em;color:var(--ink-dim);text-transform:uppercase;margin-bottom:2px;">${k}</div>
            <div style="font-family:'JetBrains Mono',monospace;font-size:11px;font-weight:700;color:${c};">${v}</div>
          </div>`).join('')}
      </div>

      <!-- Open Positions -->
      ${state.inventory.length ? `
        <div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--gold);letter-spacing:0.2em;text-transform:uppercase;margin-bottom:6px;">
          Open Positions (${state.inventory.length})
        </div>
        ${state.inventory.map(item => {
          const currentVal = oraclePrice(item.rarity);
          const upnl = currentVal - item.paidAmount;
          const pct  = item.paidAmount > 0 ? ((upnl / item.paidAmount)*100).toFixed(1) : '0.0';
          return `<div style="display:grid;grid-template-columns:1fr auto;gap:8px;align-items:center;padding:8px 0;border-bottom:1px dashed var(--line);">
            <div>
              <div style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--ink);">${item.name}</div>
              <div style="font-family:'JetBrains Mono',monospace;font-size:8px;color:var(--ink-dim);margin-top:1px;">
                Ed.${item.editionNumber||1}/${item.totalEditions||1} · basis ${fmt(item.paidAmount)} → now ${fmt(currentVal)}
              </div>
            </div>
            <div style="text-align:right;">
              <div style="font-family:'JetBrains Mono',monospace;font-size:12px;font-weight:700;color:${pnlColor(upnl)};">${pnlSign(upnl)}${fmt(upnl)}</div>
              <div style="font-family:'JetBrains Mono',monospace;font-size:8px;color:${pnlColor(upnl)};">${pnlSign(upnl)}${pct}%</div>
            </div>
          </div>`;
        }).join('')}
      ` : `<div style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--ink-dim);padding:16px 0;text-align:center;">No open positions.</div>`}

      <!-- Closed Positions -->
      ${state.pnlLedger.length ? `
        <div style="font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--gold);letter-spacing:0.2em;text-transform:uppercase;margin:14px 0 6px;">
          Closed Positions (${state.pnlLedger.length} · ${winRate}% win rate)
        </div>
        ${state.pnlLedger.slice(0,20).map(entry => {
          const pct = entry.costBasis > 0 ? ((entry.pnl / entry.costBasis)*100).toFixed(1) : '0.0';
          return `<div style="display:grid;grid-template-columns:1fr auto;gap:8px;align-items:center;padding:7px 0;border-bottom:1px dashed var(--line);">
            <div>
              <div style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--ink);">${entry.itemName}</div>
              <div style="font-family:'JetBrains Mono',monospace;font-size:8px;color:var(--ink-dim);margin-top:1px;">
                ${entry.edition ? `Ed.${entry.edition} · ` : ''}via ${entry.via} · ${timeAgo(entry.ts)}
              </div>
            </div>
            <div style="text-align:right;">
              <div style="font-family:'JetBrains Mono',monospace;font-size:12px;font-weight:700;color:${pnlColor(entry.pnl)};white-space:nowrap;">${pnlSign(entry.pnl)}${fmt(entry.pnl)}</div>
              <div style="font-family:'JetBrains Mono',monospace;font-size:8px;color:var(--ink-dim);white-space:nowrap;">${fmt(entry.costBasis)} → ${fmt(entry.salePrice)} (${pnlSign(entry.pnl)}${pct}%)</div>
            </div>
          </div>`;
        }).join('')}
      ` : `<div class="inv-empty"><span class="icon">⚖</span>No closed positions yet.</div>`}
    `;
  }
  else if (state.drawerTab === 'history') {
    if (!state.txHistory.length) {
      body.innerHTML = `<div class="inv-empty">
        <span class="icon">⌛</span>
        No transactions yet.
      </div>`;
      return;
    }
    body.innerHTML = state.txHistory.slice(0, 50).map(tx => {
      const cfg = {
        buy:   { icon: '↓', cls: 'in',  label: 'Bought', amtCls: 'out',     amtPrefix: '−' },
        sell:  { icon: '↑', cls: 'out', label: 'Sold',   amtCls: 'in',      amtPrefix: '+' },
        list:  { icon: '⊕', cls: 'list', label: 'Listed', amtCls: 'neutral', amtPrefix: '@' },
        unlist:{ icon: '⊖', cls: 'list', label: 'Unlisted', amtCls: 'neutral', amtPrefix: '' }
      }[tx.type];
      return `
        <div class="tx-row">
          <div class="tx-icon ${cfg.cls}">${cfg.icon}</div>
          <div class="tx-info">
            <div class="tx-label">${cfg.label} · ${tx.itemName}</div>
            <div class="tx-time">${timeAgo(tx.ts)}</div>
          </div>
          <div class="tx-amt ${cfg.amtCls}">${cfg.amtPrefix}${fmt(tx.amount)} $PvE</div>
        </div>
      `;
    }).join('');
  }
}

function timeAgo(ts) {
  const s = Math.floor((Date.now() - ts) / 1000);
  if (s < 60) return 'just now';
  if (s < 3600) return `${Math.floor(s/60)}m ago`;
  if (s < 86400) return `${Math.floor(s/3600)}h ago`;
  return `${Math.floor(s/86400)}d ago`;
}

// ==================== SELL FLOW ====================
let pendingSell = null;

// ---- INSTANT SELL (LP Market Maker bid at 80%) ----
function instantSell(invId, price) {
  const item = state.inventory.find(i => i.invId === invId);
  if (!item) return;

  if (state.mmBalance < price) {
    toast('MM budget exhausted — use List to set your own price', 'error');
    return;
  }

  // MM pays seller
  state.balance += price;
  state.mmBalance -= price;
  state.totalEarned += price;

  // MM takes ownership — will relist at 100%
  const mult = scoreToMultiplier(computeScore());
  const marketItem = state.items.find(i => i.name === item.name && !i.isMMlisting);
  const relistBase = marketItem ? marketItem.basePrice : Math.round(price / (mult * (1 + rarityFee()) * MM_BID_PCT));

  state.items.push({
    id: 'item_mm_' + Date.now(),
    name: item.name,
    category: item.category,
    rarity: item.rarity,
    desc: item.desc,
    basePrice: relistBase,
    creator: '⬡ LP Market Maker',
    sold: false,
    editions: 1,
    editionsSold: 0,
    imageUrl: item.imageUrl || null,
    ipfsCid: item.ipfsCid || null,
    isMMlisting: true
  });

  // Remove from inventory
  state.inventory = state.inventory.filter(i => i.invId !== invId);
  logTx('sell', item.name, price);
  const realizedPnl = price - item.paidAmount;
  state.pnlLedger.unshift({
    itemName: item.name, rarity: item.rarity,
    costBasis: item.paidAmount, salePrice: price,
    pnl: realizedPnl, ts: Date.now(), via: 'instant-sell',
    edition: item.editionNumber || null
  });
  state.totalRealized += realizedPnl;
  state.totalCostBasis = Math.max(0, state.totalCostBasis - item.paidAmount);

  toast(`⬡ Sold to MM · +${fmt(price)} $PvE instant`, 'success');
  render();
  renderDrawer();
}

function openSellModal(invId) {
  const item = state.inventory.find(i => i.invId === invId);
  if (!item) return;
  pendingSell = item;

  // Reset sell image staging, pre-fill if item already has image
  clearImage('sell');
  if (item.imageUrl) {
    imageStaging.sell = item.imageUrl;
    const zone = $('sellImgZone');
    zone.classList.add('has-image');
    const fileInput = zone.querySelector('input[type="file"]');
    zone.innerHTML = '';
    zone.appendChild(fileInput);
    const img = document.createElement('img');
    img.className = 'preview';
    img.src = item.imageUrl;
    zone.appendChild(img);
    $('sellImgClear').style.display = 'block';
  }

  $('sellPreview').innerHTML = `
    <div class="art">${item.imageUrl ? `<img src="${item.imageUrl}" alt="${item.name}" style="width:100%;height:100%;object-fit:cover;">` : itemArt(item.artSeed || item.name, item.rarity)}</div>
    <div class="info">
      <div class="nm">${item.name}</div>
      ${item.category}<br>
      Originally bought for ${fmt(item.paidAmount)} $PvE
    </div>
  `;

  updateSellPreview();
  closeWallet();
  $('sellOverlay').classList.add('show');
}

function updateSellPreview() {
  if (!pendingSell) return;
  const mult = scoreToMultiplier(computeScore());
  const base = oracleBasePrice(pendingSell.rarity);
  const final = Math.min(base * mult * (1 + rarityFee()), MAX_FINAL_PRICE);
  if ($('sellMult')) $('sellMult').textContent = mult.toFixed(2);
  const calcEl = $('sellCalc');
  if (calcEl) { calcEl.textContent = fmt(final) + ' $PvE'; calcEl.style.color = 'var(--gold-bright)'; }
  if ($('sellYouGet')) $('sellYouGet').textContent = fmt(final * (1 - MARKETPLACE_FEE) + LISTING_DEPOSIT) + ' $PvE (incl. deposit)';
}



function confirmSell() {
  if (!pendingSell) return;
  const price = oracleBasePrice(pendingSell.rarity);   // Oracle sets price
  // ---- FOUNDER LISTING CAP ----
  const myActiveListing = state.items.filter(i => state.myListings.includes(i.id) && !i.sold).length;
  if (myActiveListing >= FOUNDER_LISTING_CAP) {
    toast(`Listing cap reached — max ${FOUNDER_LISTING_CAP} active listings. Unlist or sell one first.`, 'error');
    closeOverlay('sellOverlay');
    openWallet();
    switchDrawerTab('listed');
    return;
  }

  // ---- LISTING DEPOSIT ----
  if (state.balance < LISTING_DEPOSIT) {
    toast(`Insufficient balance — ${LISTING_DEPOSIT} $PvE deposit required to list.`, 'error');
    return;
  }
  state.balance -= LISTING_DEPOSIT;
  state.lockedDeposit += LISTING_DEPOSIT;

  // ---- NFT MINT (stub) — item already exists as NFT if bought; re-listing re-registers ownership ----
  const mockTokenId = pendingSell.tokenId || ('T-' + Math.floor(Math.random() * 9000 + 1000));
  const mockTxHash  = '0x' + [...Array(8)].map(() => Math.floor(Math.random()*16).toString(16)).join('') + '...';

  // Move from inventory to market
  const newItem = {
    id: 'item_' + Date.now(),
    name: pendingSell.name,
    desc: pendingSell.desc,
    category: pendingSell.category,
    rarity: pendingSell.rarity,
    basePrice: price,
    creator: state.myWallet,
    sold: false,
    listedByMe: true,
    imageUrl: imageStaging.sell || pendingSell.imageUrl || null,
    ipfsCid: pendingSell.ipfsCid || null,
    audioUrl: audioStaging.sell?.dataUrl || pendingSell.audioUrl || null,
    audioFileName: audioStaging.sell?.fileName || pendingSell.audioFileName || null,
    audioDuration: audioStaging.sell?.duration || pendingSell.audioDuration || null,
    tokenId: mockTokenId,
    txHash: mockTxHash,
    depositLocked: LISTING_DEPOSIT
  };
  imageStaging.sell = null;
  audioStaging.sell = null;
  state.items.unshift(newItem);
  state.myListings.push(newItem.id);
  state.inventory = state.inventory.filter(i => i.invId !== pendingSell.invId);
  logTx('list', pendingSell.name, price);

  closeOverlay('sellOverlay');
  toast(`Listed · NFT ${mockTokenId} · ${LISTING_DEPOSIT} $PvE deposit locked`, 'success');
  pendingSell = null;
  openWallet();  // jump back to wallet so user sees the new listing
  switchDrawerTab('listed');
  render();
}

function unlistItem(itemId) {
  const item = state.items.find(i => i.id === itemId);
  if (!item) return;
  // ---- RETURN DEPOSIT ----
  const dep = item.depositLocked || 0;
  if (dep > 0) {
    state.balance += dep;
    state.lockedDeposit = Math.max(0, state.lockedDeposit - dep);
  }
  // Return it to inventory
  state.inventory.push({
    invId: 'inv_' + Date.now() + '_' + Math.random().toString(36).slice(2, 6),
    name: item.name,
    category: item.category,
    rarity: item.rarity,
    desc: item.desc,
    paidAmount: item.basePrice,
    originalCreator: item.creator,
    artSeed: item.name,
    imageUrl: item.imageUrl || null,
    ipfsCid: item.ipfsCid || null,
    audioUrl: item.audioUrl || null,
    audioFileName: item.audioFileName || null,
    audioDuration: item.audioDuration || null,
    tokenId: item.tokenId || null,
    acquiredAt: Date.now()
  });
  // Remove from market
  state.items = state.items.filter(i => i.id !== itemId);
  state.myListings = state.myListings.filter(id => id !== itemId);
  logTx('unlist', item.name, dep);
  toast(`Unlisted · ${dep} $PvE deposit returned`, dep > 0 ? 'success' : '');
  render();
  renderDrawer();
}

// Simulate other buyers occasionally purchasing your live listings
// In production, the marketplace contract would emit ItemSold events your frontend subscribes to.
setInterval(() => {
  const liveListings = state.items.filter(i => state.myListings.includes(i.id) && !i.sold);
  if (!liveListings.length) return;
  // 25% chance per tick that someone buys one
  if (Math.random() < 0.25) {
    const pick = liveListings[Math.floor(Math.random() * liveListings.length)];
    const mult = scoreToMultiplier(computeScore());
    const salePrice = calcFinalPrice(pick, mult);
    const dep = pick.depositLocked || 0;
    const sellerProceeds = salePrice * (1 - MARKETPLACE_FEE) + dep;  // 0.1% fee, deposit returned
    pick.sold = true;
    state.balance += sellerProceeds;
    state.lockedDeposit = Math.max(0, state.lockedDeposit - dep);
    state.totalEarned += salePrice * (1 - MARKETPLACE_FEE);
    logTx('sell', pick.name, sellerProceeds);
    // For simulator sales, cost basis is the listing deposit (we don't have a buy-side record)
    state.pnlLedger.unshift({
      itemName: pick.name, rarity: pick.rarity || 'common',
      costBasis: pick.depositLocked || LISTING_DEPOSIT,
      salePrice: sellerProceeds,
      pnl: sellerProceeds - (pick.depositLocked || LISTING_DEPOSIT),
      ts: Date.now(), via: 'market-sale',
      edition: null
    });
    state.totalRealized += sellerProceeds - (pick.depositLocked || LISTING_DEPOSIT);
    toast(`◈ Sold: ${pick.name} (+${fmt(sellerProceeds)} $PvE)`, 'success');
    render();
    if ($('walletDrawer').classList.contains('show')) renderDrawer();
  }
}, 18000);

// Close drawer with Escape
document.addEventListener('keydown', (e) => {
  if (e.key === 'Escape') {
    closeWallet();
    document.querySelectorAll('.overlay.show').forEach(o => o.classList.remove('show'));
  }
});
function dropVoter(name) {
  const v = state.ledger.find(x => x.voter === name);
  if (!v) return toast(`No vote from ${name}`, 'error');
  v.balance = 4000;
  v.droppedAt = Date.now();
  state.prevScore = computeScore() + 6;  // show downward trend on drop
  render();
  toast(`${name} dropped below threshold — entering grace`, 'error');
}
function restoreVoter(name) {
  const v = state.ledger.find(x => x.voter === name);
  if (!v) return toast(`No vote from ${name}`, 'error');
  if (voteStatus(v) === 'burned') return toast(`${name}'s vote is already burned`, 'error');
  v.balance = 12000;
  v.droppedAt = null;
  state.prevScore = computeScore() - 6;
  render();
  toast(`${name} re-upped — vote restored to active`, 'success');
}


// HERO SLIDESHOW
var _heroSlide = 0;
var _heroTimer = null;
function goToSlide(n) {
  var slides = document.querySelectorAll('.hero-slide');
  var dots   = document.querySelectorAll('.hero-dot');
  slides[_heroSlide].classList.remove('active');
  dots[_heroSlide].classList.remove('active');
  _heroSlide = ((n % 3) + 3) % 3;
  slides[_heroSlide].classList.add('active');
  dots[_heroSlide].classList.add('active');
  updateHeroStats();
}
function updateHeroStats() {
  try {
    var sc = computeScore ? computeScore() : 100;
    var m  = scoreToMultiplier ? scoreToMultiplier(sc) : 2.16;
    var li = state && state.items ? state.items.filter(function(i){return !i.sold;}).length : 0;
    if (document.getElementById('heroListings')) document.getElementById('heroListings').textContent = li;
    if (document.getElementById('heroScore'))    document.getElementById('heroScore').textContent    = sc.toFixed(1);
    if (document.getElementById('heroMult'))     document.getElementById('heroMult').textContent     = '×' + m.toFixed(2);
  } catch(e) {}
}
function _heroNext() { goToSlide(_heroSlide + 1); }
function startHeroAuto() { _heroTimer = setInterval(_heroNext, 6000); }
var _hs = document.getElementById('heroSlideshow');
if (_hs) {
  _hs.addEventListener('mouseenter', function(){ clearInterval(_heroTimer); });
  _hs.addEventListener('mouseleave', startHeroAuto);
}
startHeroAuto();


// ══════════════════════════════════════════════════════════════════════
// MATRIX ENTRY SCREEN
// ══════════════════════════════════════════════════════════════════════
(function() {
  var canvas = document.getElementById('matrixCanvas');
  var ctx    = canvas.getContext('2d');
  var W, H, cols, drops;

  // Characters: katakana + latin + symbols
  var chars = 'アイウエオカキクケコサシスセソタチツテトナニヌネノハヒフヘホマミムメモヤユヨラリルレロワヲン0123456789ABCDEF⚔◈⬡▲△◆◇⚙';

  function resize() {
    W = canvas.width  = window.innerWidth;
    H = canvas.height = window.innerHeight;
    cols  = Math.floor(W / 18);
    drops = Array(cols).fill(1);
  }
  resize();
  window.addEventListener('resize', resize);

  var hue = 0;
  function drawMatrix() {
    // Subtle fade trail
    ctx.fillStyle = 'rgba(0,0,0,0.055)';
    ctx.fillRect(0, 0, W, H);
    hue = (hue + 0.3) % 360;

    for (var i = 0; i < drops.length; i++) {
      var ch = chars[Math.floor(Math.random() * chars.length)];
      var x  = i * 18;
      var y  = drops[i] * 18;

      // Leading character — bright gold/white
      if (Math.random() > 0.95) {
        ctx.fillStyle = '#ffffff';
        ctx.shadowBlur = 8;
        ctx.shadowColor = '#f0c878';
      } else {
        // Gradient from gold to rust to green
        var t = (i / cols);
        if (t < 0.33)      ctx.fillStyle = 'rgba(201,162,74,0.85)';
        else if (t < 0.66) ctx.fillStyle = 'rgba(161,68,38,0.75)';
        else               ctx.fillStyle = 'rgba(90,122,58,0.7)';
        ctx.shadowBlur = 0;
      }

      ctx.font = 'bold 14px "JetBrains Mono", monospace';
      ctx.fillText(ch, x, y);
      ctx.shadowBlur = 0;

      if (y > H && Math.random() > 0.975) drops[i] = 0;
      drops[i]++;
    }
  }

  var matrixTimer = setInterval(drawMatrix, 40);

  // enterBazaar is defined at top level — just hook keyboard shortcut here


  // Prevent scroll while entry screen is shown
  document.body.style.overflow = 'hidden';

  // Allow ENTER key to proceed
  document.addEventListener('keydown', function(e) {
    if ((e.key === 'Enter' || e.key === ' ') && document.getElementById('entryScreen').style.display !== 'none') {
      window.enterBazaar();
    }
  });
})();

// ══════════════════════════════════════════════════════════════════════
// BACKGROUND PARTICLE SYSTEM
// ══════════════════════════════════════════════════════════════════════
function startParticles() {
  var canvas = document.getElementById('particleCanvas');
  var ctx    = canvas.getContext('2d');
  var particles = [];

  function resize() {
    canvas.width  = window.innerWidth;
    canvas.height = document.body.scrollHeight;
  }
  resize();
  window.addEventListener('resize', resize);

  // Seed particles
  var count = Math.min(80, Math.floor(window.innerWidth / 20));
  for (var i = 0; i < count; i++) {
    particles.push({
      x:  Math.random() * canvas.width,
      y:  Math.random() * canvas.height,
      vx: (Math.random() - 0.5) * 0.3,
      vy: (Math.random() - 0.5) * 0.3,
      r:  Math.random() * 1.5 + 0.5,
      o:  Math.random() * 0.4 + 0.1,
      // color: gold or rust
      c:  Math.random() > 0.6 ? '201,162,74' : Math.random() > 0.5 ? '161,68,38' : '90,122,58'
    });
  }

  function drawParticles() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    var W = canvas.width, H = canvas.height;

    particles.forEach(function(p) {
      // Drift
      p.x += p.vx; p.y += p.vy;
      if (p.x < 0) p.x = W; if (p.x > W) p.x = 0;
      if (p.y < 0) p.y = H; if (p.y > H) p.y = 0;

      // Draw
      ctx.beginPath();
      ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
      ctx.fillStyle = 'rgba(' + p.c + ',' + p.o + ')';
      ctx.fill();
    });

    // Draw connection lines between nearby particles
    for (var i = 0; i < particles.length; i++) {
      for (var j = i + 1; j < particles.length; j++) {
        var dx = particles[i].x - particles[j].x;
        var dy = particles[i].y - particles[j].y;
        var d  = Math.sqrt(dx*dx + dy*dy);
        if (d < 120) {
          ctx.beginPath();
          ctx.moveTo(particles[i].x, particles[i].y);
          ctx.lineTo(particles[j].x, particles[j].y);
          ctx.strokeStyle = 'rgba(201,162,74,' + (0.06 * (1 - d/120)) + ')';
          ctx.lineWidth = 0.5;
          ctx.stroke();
        }
      }
    }

    requestAnimationFrame(drawParticles);
  }
  drawParticles();
}

// ══════════════════════════════════════════════════════════════════════
// SCROLL REVEAL
// ══════════════════════════════════════════════════════════════════════
function initScrollReveal() {
  var revealEls = document.querySelectorAll('.reveal, .reveal-left, .reveal-right, .reveal-stagger');

  var obs = new IntersectionObserver(function(entries) {
    entries.forEach(function(e) {
      if (e.isIntersecting) {
        e.target.classList.add('visible');
        obs.unobserve(e.target);
      }
    });
  }, { threshold: 0.08 });

  revealEls.forEach(function(el) { obs.observe(el); });
}

// ══════════════════════════════════════════════════════════════════════
// CURSOR TRAIL
// ══════════════════════════════════════════════════════════════════════
(function() {
  var trails = [];
  var COUNT  = 8;
  for (var i = 0; i < COUNT; i++) {
    var dot = document.createElement('div');
    dot.className = 'cursor-trail';
    dot.style.opacity = (1 - i / COUNT) * 0.7;
    dot.style.width   = (6 - i * 0.5) + 'px';
    dot.style.height  = (6 - i * 0.5) + 'px';
    dot.style.transitionDelay = (i * 25) + 'ms';
    document.body.appendChild(dot);
    trails.push({ el: dot, x: -100, y: -100 });
  }

  var mouseX = -100, mouseY = -100;
  document.addEventListener('mousemove', function(e) {
    mouseX = e.clientX; mouseY = e.clientY;
  });

  function animTrail() {
    var lx = mouseX, ly = mouseY;
    trails.forEach(function(t, i) {
      t.x += (lx - t.x) * (0.25 - i * 0.02);
      t.y += (ly - t.y) * (0.25 - i * 0.02);
      lx = t.x; ly = t.y;
      t.el.style.left = t.x + 'px';
      t.el.style.top  = t.y + 'px';
    });
    requestAnimationFrame(animTrail);
  }
  animTrail();

  // Hide on mobile
  if ('ontouchstart' in window) {
    trails.forEach(function(t) { t.el.style.display = 'none'; });
  }
})();

// ══════════════════════════════════════════════════════════════════════
// PARALLAX ON SCROLL
// ══════════════════════════════════════════════════════════════════════
window.addEventListener('scroll', function() {
  var sy = window.scrollY;

  // Hero slides shift slightly on scroll (depth effect)
  var heroImgs = document.querySelectorAll('.hero-img');
  heroImgs.forEach(function(img) {
    img.style.transform = 'scale(1.0) translateY(' + (sy * 0.08) + 'px)';
  });

  // Particle canvas stays fixed — already done via fixed positioning
}, { passive: true });

// ══════════════════════════════════════════════════════════════════════
// ITEM CARD TILT ON HOVER
// ══════════════════════════════════════════════════════════════════════
document.addEventListener('mouseover', function(e) {
  var card = e.target.closest('.item');
  if (!card) return;
  card.addEventListener('mousemove', tiltCard);
  card.addEventListener('mouseleave', untiltCard);
});

function tiltCard(e) {
  var r   = this.getBoundingClientRect();
  var cx  = r.left + r.width  / 2;
  var cy  = r.top  + r.height / 2;
  var dx  = (e.clientX - cx) / (r.width  / 2);
  var dy  = (e.clientY - cy) / (r.height / 2);
  this.style.transform = 'translateY(-4px) perspective(600px) rotateX(' + (-dy * 4) + 'deg) rotateY(' + (dx * 4) + 'deg)';
}
function untiltCard() {
  this.style.transform = '';
  this.removeEventListener('mousemove', tiltCard);
  this.removeEventListener('mouseleave', untiltCard);
}

// ══════════════════════════════════════════════════════════════════════
// GRID SECTION DYNAMIC LABELS
// Animate number counters when oracle panel comes into view
// ══════════════════════════════════════════════════════════════════════
function animateCounter(el, from, to, duration, suffix) {
  var start = null;
  function step(ts) {
    if (!start) start = ts;
    var prog  = Math.min((ts - start) / duration, 1);
    var eased = 1 - Math.pow(1 - prog, 3);
    el.textContent = Math.round(from + (to - from) * eased) + (suffix || '');
    if (prog < 1) requestAnimationFrame(step);
  }
  requestAnimationFrame(step);
}


// ── EXCHANGE RENDER ──────────────────────────────────────────────────
function renderExchange() {
  const grid    = document.getElementById('exchangeGrid');
  const countEl = document.getElementById('exCount');
  const volEl   = document.getElementById('exVolume');
  if (!grid) return;

  const items = state.exchange.filter(i => !i.exSold);
  if (countEl) countEl.textContent = items.length;
  if (volEl) {
    const vol = state.exchange
      .filter(i => i.exSold)
      .reduce((s, i) => s + (i.lastSalePrice || 0), 0);
    volEl.textContent = fmt(vol);
  }

  if (!items.length) {
    grid.innerHTML = '<div class="ex-empty">No secondary listings yet · items migrate here at the end of each Oracle week</div>';
    return;
  }

  grid.innerHTML = items.map(item => {
    const pctChange = item.frozenPrice > 0
      ? (((item.askPrice - item.frozenPrice) / item.frozenPrice) * 100).toFixed(1)
      : '0.0';
    const isUp   = item.askPrice >= item.frozenPrice;
    const mmBid  = Math.round(item.askPrice * MM_BID_PCT);
    const isMine = state.myListings.includes(item.id);
    const hasInv = state.inventory.some(inv => inv.name === item.name);
    const lastSale = item.priceHistory.length > 1
      ? item.priceHistory[item.priceHistory.length - 1]
      : null;

    return `<div class="ex-card${item.justMigrated ? ' just-migrated' : ''}" id="excard_${item.exId}">
      <div class="ex-art">
        ${renderArt(item, 'ex')}
        <div class="ex-frozen-badge">⬡ Exchange</div>
        <div class="ex-migrated-badge">Wk ${item.weekMigrated || '—'}</div>
      </div>
      <div class="ex-body">
        <div class="ex-name">${item.name}</div>
        <div class="ex-creator">@${item.creator} · ${(item.editions||1) - (item.editionsSold||0)} left</div>
        <div class="ex-desc">${item.desc}</div>
        <div class="ex-price-row">
          <div class="ex-price-stack">
            <div class="ex-frozen">Frozen ${fmt(item.frozenPrice)}</div>
            <div class="ex-ask">${fmt(item.askPrice)} <span style="font-family:'JetBrains Mono',monospace;font-size:10px;color:var(--ink-dim);">$PvE</span></div>
            <div class="ex-change ${isUp ? 'up' : 'down'}">${isUp ? '▲' : '▼'} ${Math.abs(pctChange)}% from freeze</div>
            ${lastSale ? `<div class="ex-history">Last sale ${fmt(lastSale.price)}</div>` : ''}
          </div>
          <div style="text-align:right;font-family:'JetBrains Mono',monospace;font-size:9px;color:var(--ink-dim);">
            ${item.priceHistory.length} tx${item.priceHistory.length !== 1 ? 's' : ''}
          </div>
        </div>
        <div class="ex-actions">
          ${isMine
            ? `<button class="ex-btn relist" onclick="exRelist('${item.exId}')">Relist</button>`
            : `<button class="ex-btn" onclick="exBuy('${item.exId}')">Buy ${fmt(item.askPrice)}</button>`
          }
          ${!isMine
            ? `<button class="ex-btn offer" onclick="exToggleOffer('${item.exId}')">Offer</button>`
            : ''
          }
          ${hasInv
            ? `<button class="ex-btn mm" onclick="exInstantSell('${item.exId}', ${mmBid})" title="Sell to LP MM at 80%">⬡ MM ${fmt(mmBid)}</button>`
            : ''
          }
        </div>
        <div class="ex-offer-row" id="offerrow_${item.exId}">
          <input class="ex-offer-input" type="number" id="offerinput_${item.exId}" placeholder="Your offer in $PvE" min="1">
          <button class="ex-offer-send" onclick="exSendOffer('${item.exId}')">Send</button>
        </div>
      </div>
    </div>`;
  }).join('');
}

// ── EXCHANGE ACTIONS ─────────────────────────────────────────────────
function exBuy(exId) {
  const item = state.exchange.find(i => i.exId === exId);
  if (!item) return;
  const cost = item.askPrice * 1.001; // 0.1% fee
  if (state.balance < cost) { toast('Insufficient balance', 'error'); return; }
  state.balance -= cost;
  state.totalCostBasis += cost;
  state.inventory.push({
    ...item,
    invId:         'inv_ex_' + Date.now(),
    paidAmount:    cost,
    source:        'exchange',
    editionNumber: (item.editionsSold || 0) + 1,
    totalEditions: item.editions || 1,
  });
  item.editionsSold = (item.editionsSold || 0) + 1;
  item.lastSalePrice = item.askPrice;
  item.priceHistory.push({ price: item.askPrice, ts: Date.now(), label: 'Market buy' });
  if (item.editionsSold >= (item.editions || 1)) item.exSold = true;

  // MM bids at 80%
  const mmBid = Math.round(item.askPrice * MM_BID_PCT);
  if (state.mmBalance >= mmBid) {
    state.mmBalance -= mmBid;
    state.mmInventory.push({ ...item, mmAcquiredAt: Date.now() });
  }

  state.totalCostBasis += cost;
  logTx('buy', item.name, cost);
  toast(`Bought ${item.name} · Ed.${item.editionNumber} · ${fmt(cost)} $PvE`, 'success');
  renderExchange();
  if ($('walletDrawer')?.classList.contains('show')) renderDrawer();
}

function exRelist(exId) {
  const item = state.exchange.find(i => i.exId === exId);
  if (!item) return;
  const input = prompt(`Relist "${item.name}" at new price ($PvE)?\nCurrent ask: ${item.askPrice}`, item.askPrice);
  if (!input) return;
  const newPrice = parseFloat(input);
  if (isNaN(newPrice) || newPrice <= 0) { toast('Invalid price', 'error'); return; }
  item.priceHistory.push({ price: item.askPrice, ts: Date.now(), label: `Relisted → ${newPrice}` });
  item.askPrice = newPrice;
  toast(`Relisted at ${fmt(newPrice)} $PvE`, 'success');
  renderExchange();
}

function exToggleOffer(exId) {
  const row = document.getElementById('offerrow_' + exId);
  if (row) row.classList.toggle('open');
}

function exSendOffer(exId) {
  const item   = state.exchange.find(i => i.exId === exId);
  const input  = document.getElementById('offerinput_' + exId);
  if (!item || !input) return;
  const offer  = parseFloat(input.value);
  if (isNaN(offer) || offer <= 0) { toast('Enter a valid offer amount', 'error'); return; }
  if (state.balance < offer) { toast('Insufficient balance for this offer', 'error'); return; }

  item.offers.push({ amount: offer, from: state.myWallet, ts: Date.now() });

  // Auto-accept if offer >= ask (simplified: in production the seller approves)
  if (offer >= item.askPrice) {
    exBuy(exId);
    toast(`Offer of ${fmt(offer)} accepted — at or above ask`, 'success');
  } else {
    toast(`Offer of ${fmt(offer)} $PvE sent to seller · ${Math.round((offer/item.askPrice)*100)}% of ask`, '');
    input.value = '';
    exToggleOffer(exId);
  }
  renderExchange();
}

function exInstantSell(exId, payout) {
  const item = state.exchange.find(i => i.exId === exId);
  if (!item) return;
  if (state.mmBalance < payout) { toast('LP MM has insufficient funds', 'error'); return; }
  state.mmBalance -= payout;
  state.balance += payout;
  const invIdx = state.inventory.findIndex(i => i.name === item.name);
  if (invIdx !== -1) {
    const inv = state.inventory.splice(invIdx, 1)[0];
    const pnl = payout - inv.paidAmount;
    state.totalRealized += pnl;
    state.pnlLedger.unshift({ itemName: item.name, costBasis: inv.paidAmount, salePrice: payout, pnl, ts: Date.now(), via: 'exchange-mm' });
  }
  item.priceHistory.push({ price: payout, ts: Date.now(), label: 'MM instant sell' });
  toast(`⬡ MM instant sell · +${fmt(payout)} $PvE`, 'success');
  renderExchange();
  if ($('walletDrawer')?.classList.contains('show')) renderDrawer();
}

// Load persisted score for current week (survives page reloads within same week)
// score + lastWeekKey initialized in the weekly reset block below

// ── WEEKLY RESET (checked every 60s and on load) ────────────────────────
function checkWeeklyReset() {
  const wk = currentWeekKey();
  if (!state.lastWeekKey || state.lastWeekKey === wk) {
    state.lastWeekKey = wk;
    return;   // same week or first load — nothing to migrate
  }
  // New week detected — freeze prices and migrate to Exchange
  const finalMult = scoreToMultiplier(state.founderScore);
  const migrated  = [];
  state.items = state.items.filter(item => {
    if (item.sold) return true;
    const frozenPrice = Math.round(calcFinalPrice(item, finalMult));
    state.exchange.unshift({
      ...item,
      exId:         'ex_' + item.id,
      frozenPrice,
      askPrice:     frozenPrice,
      offers:       [],
      priceHistory: [{ price: frozenPrice, ts: Date.now(), label: 'Migrated from Oracle' }],
      migratedAt:   Date.now(),
      weekMigrated: state.lastWeekKey,
      justMigrated: true,
    });
    migrated.push(item.name);
    return false;
  });
  state.founderScore = SCORE_BASE;
  state.prevScore    = SCORE_BASE;
  persistScore(SCORE_BASE);
  state.weeklyVotes  = {};
  state.lastWeekKey  = wk;
  if (migrated.length) {
    toast('⬡ Week reset · ' + migrated.length + ' item' + (migrated.length > 1 ? 's' : '') + ' migrated to The Exchange', 'success');
    setTimeout(() => { state.exchange.forEach(e => { e.justMigrated = false; }); renderExchange(); }, 2500);
  }
}

// Load persisted score and set week key BEFORE first render
state.founderScore = loadPersistedScore();
state.prevScore    = state.founderScore;
state.lastWeekKey  = currentWeekKey();

render();

setInterval(() => { updateEligibilityUI(); checkWeeklyReset(); }, 60000);
checkWeeklyReset();


</script>

<!-- Bio Setup Modal -->
<div id="bioOverlay">
  <div class="bio-modal">
    <div class="bio-modal-title">⚔ Creator Biography</div>
    <div class="bio-modal-sub">
      Required before listing · 80 characters max<br>
      This appears on your creator profile for buyers to see.
    </div>
    <div class="bio-input-wrap">
      <textarea class="bio-textarea" id="bioInput"
        placeholder="Lore writer and item forger from the Iron Arena circuit."
        maxlength="80"
        oninput="updateBioCount()"></textarea>
    </div>
    <div class="bio-char-count ok" id="bioCount">0 / 80</div>
    <div class="bio-actions">
      <button class="btn btn-cancel" onclick="closeOverlay('bioOverlay')">Cancel</button>
      <button class="btn" id="bioSaveBtn" onclick="saveBio()" disabled>Save & Continue</button>
    </div>
  </div>
</div>

<!-- Creator Collection Modal -->
<div id="creatorOverlay" onclick="if(event.target===this)closeCreatorModal()">
  <div class="creator-modal">
    <div class="creator-modal-head">
      <div>
        <div class="creator-modal-name" id="creatorModalName">—</div>
        <div class="creator-modal-handle" id="creatorModalHandle">—</div>
        <div id="creatorModalBio" style="font-family:'JetBrains Mono',monospace;font-size:10px;color:var(--ink);margin-top:5px;font-style:italic;max-width:360px;line-height:1.5;"></div>
      </div>
      <div class="creator-modal-stats" id="creatorModalStats"></div>
      <button onclick="closeCreatorModal()" style="background:none;border:1px solid var(--line);color:var(--ink-dim);font-size:18px;cursor:pointer;padding:4px 10px;line-height:1;">×</button>
    </div>
    <div class="creator-modal-body" id="creatorModalBody"></div>
  </div>
</div>

</body>
</html>
