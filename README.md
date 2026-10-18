<!doctype html>
<html lang="ko">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="theme-color" content="#070b14">
  <title>Lucidas — 천문 관측 기록</title>
  <style>
    :root {
      color-scheme: dark;
      --bg: #070b14;
      --panel: rgba(17, 27, 48, .82);
      --panel-strong: #101a2e;
      --line: rgba(166, 190, 235, .14);
      --text: #f4f7ff;
      --muted: #8e9bb0;
      --blue: #a9c5ff;
      --blue-strong: #79a5ff;
      --cyan: #70e1d0;
      --gold: #f4c779;
      --danger: #ff8f9c;
      --shadow: 0 24px 70px rgba(0, 0, 0, .28);
    }

    * { box-sizing: border-box; }
    html { scroll-behavior: smooth; }
    body {
      margin: 0;
      min-width: 320px;
      color: var(--text);
      background:
        radial-gradient(circle at 15% 12%, rgba(69, 101, 168, .17), transparent 27%),
        radial-gradient(circle at 86% 25%, rgba(37, 73, 143, .12), transparent 28%),
        radial-gradient(circle at 60% 90%, rgba(40, 100, 107, .08), transparent 25%),
        var(--bg);
      font-family: Inter, Pretendard, "Noto Sans KR", system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      overflow-x: hidden;
    }

    body::before {
      content: "";
      position: fixed;
      inset: 0;
      z-index: -1;
      pointer-events: none;
      background-image:
        radial-gradient(circle, rgba(255,255,255,.55) 0 1px, transparent 1.2px),
        radial-gradient(circle, rgba(153,188,255,.45) 0 1px, transparent 1.2px);
      background-position: 0 0, 21px 31px;
      background-size: 89px 89px, 137px 137px;
      opacity: .18;
    }

    button, input, textarea { font: inherit; }
    button { color: inherit; }
    button:focus-visible, input:focus-visible, textarea:focus-visible {
      outline: 2px solid var(--blue-strong);
      outline-offset: 2px;
    }

    .topbar {
      position: sticky;
      top: 0;
      z-index: 50;
      height: 68px;
      display: grid;
      grid-template-columns: 1fr auto 1fr;
      align-items: center;
      padding: 0 clamp(18px, 5vw, 72px);
      border-bottom: 1px solid var(--line);
      background: rgba(7, 11, 20, .79);
      backdrop-filter: blur(18px);
    }

    .brand {
      display: inline-flex;
      align-items: center;
      gap: 10px;
      justify-self: start;
      border: 0;
      padding: 0;
      background: none;
      font-family: Georgia, "Times New Roman", serif;
      font-size: 22px;
      cursor: pointer;
    }

    .brand-mark {
      width: 28px;
      height: 28px;
      display: grid;
      place-items: center;
      border: 1px solid rgba(169, 197, 255, .32);
      border-radius: 50%;
      color: var(--blue);
      box-shadow: inset 0 0 18px rgba(121, 165, 255, .12);
    }

    .desktop-nav { display: flex; gap: 4px; }
    .desktop-nav button, .mobile-menu button {
      border: 0;
      border-radius: 8px;
      padding: 9px 13px;
      color: #94a0b4;
      background: transparent;
      font-size: 12px;
      font-weight: 720;
      cursor: pointer;
    }
    .desktop-nav button:hover, .desktop-nav button.active {
      color: #e8efff;
      background: rgba(125, 168, 255, .09);
    }

    .header-actions {
      justify-self: end;
      display: flex;
      align-items: center;
      gap: 9px;
    }

    .api-pill {
      display: flex;
      align-items: center;
      gap: 7px;
      padding: 7px 10px;
      border: 1px solid var(--line);
      border-radius: 999px;
      color: #9aa7bb;
      background: rgba(255,255,255,.025);
      font-size: 10px;
      font-weight: 800;
    }
    .api-pill i, .source-line i {
      width: 7px;
      height: 7px;
      display: inline-block;
      border-radius: 50%;
      background: #6e7787;
      box-shadow: 0 0 0 3px rgba(110,119,135,.08);
    }
    .api-pill.ok { color: #bdf3e9; border-color: rgba(112,225,208,.2); }
    .api-pill.ok i, .source-line i.online { background: var(--cyan); box-shadow: 0 0 10px rgba(112,225,208,.8); }
    .api-pill.loading i { background: var(--gold); animation: pulse 1s infinite; }
    .api-pill.error { color: #ffb1ba; }
    .api-pill.error i { background: var(--danger); }

    .icon-button {
      width: 40px;
      height: 40px;
      display: grid;
      place-items: center;
      border: 1px solid var(--line);
      border-radius: 10px;
      color: #b9c5d8;
      background: rgba(255,255,255,.035);
      cursor: pointer;
    }
    .menu-button { display: none; }
    .mobile-menu {
      position: absolute;
      top: 60px;
      right: 14px;
      width: 210px;
      display: none;
      gap: 3px;
      padding: 8px;
      border: 1px solid var(--line);
      border-radius: 12px;
      background: #0d1527;
      box-shadow: var(--shadow);
    }
    .mobile-menu.open { display: grid; }
    .mobile-menu button { text-align: left; }

    .view[hidden] { display: none !important; }
    .hero {
      position: relative;
      min-height: 690px;
      display: grid;
      grid-template-columns: minmax(0, 1.2fr) minmax(280px, .8fr);
      align-items: center;
      gap: 40px;
      padding: 72px clamp(26px, 8vw, 130px);
      overflow: hidden;
    }
    .hero::after {
      content: "";
      position: absolute;
      inset: 0;
      z-index: -1;
      background: linear-gradient(90deg, rgba(7,11,20,.15), rgba(7,11,20,.52));
    }
    .eyebrow {
      display: flex;
      align-items: center;
      gap: 8px;
      margin: 0 0 18px;
      color: #93b3f2;
      font-size: 10px;
      font-weight: 850;
      letter-spacing: .18em;
    }
    .hero h1, .page-intro h1 {
      margin: 0;
      font-family: Georgia, "Times New Roman", serif;
      font-size: clamp(54px, 7vw, 94px);
      line-height: 1.03;
      font-weight: 500;
      letter-spacing: -.045em;
    }
    .hero h1 em { color: var(--blue); font-style: italic; text-shadow: 0 0 40px rgba(125,168,255,.22); }
    .hero-copy {
      max-width: 570px;
      margin: 27px 0 34px;
      color: #a6afc0;
      font-size: 16px;
      line-height: 1.75;
    }
    .hero-buttons { display: flex; flex-wrap: wrap; gap: 11px; }
    .primary-button, .ghost-button, .small-button, .light-button {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      border-radius: 9px;
      padding: 12px 17px;
      font-size: 12px;
      font-weight: 800;
      cursor: pointer;
      transition: transform .2s, background .2s, border-color .2s;
    }
    .primary-button {
      border: 1px solid #89aef7;
      color: #07101f;
      background: var(--blue);
      box-shadow: 0 10px 30px rgba(82,127,220,.2);
    }
    .primary-button:hover { transform: translateY(-2px); background: #c3d6ff; }
    .primary-button:disabled { opacity: .55; cursor: wait; transform: none; }
    .ghost-button, .small-button {
      border: 1px solid var(--line);
      color: #dce3ef;
      background: rgba(255,255,255,.04);
    }
    .ghost-button:hover, .small-button:hover { border-color: rgba(125,168,255,.5); background: rgba(125,168,255,.08); }
    .small-button { padding: 9px 12px; }
    .wide { width: 100%; }

    .hero-orbit {
      position: relative;
      width: min(37vw, 465px);
      aspect-ratio: 1;
      display: grid;
      place-items: center;
      justify-self: center;
    }
    .planet {
      width: 180px;
      aspect-ratio: 1;
      border-radius: 50%;
      background: radial-gradient(circle at 35% 30%, #9cb5dc 0 3%, #40577f 24%, #16213b 58%, #050810 82%);
      box-shadow: -35px 12px 90px rgba(106,147,220,.22), inset -28px -10px 43px #02040a;
    }
    .planet::after {
      content: "";
      position: absolute;
      width: 260px;
      height: 75px;
      border: 3px solid rgba(180,200,235,.32);
      border-radius: 50%;
      transform: rotate(-18deg);
      box-shadow: 0 0 20px rgba(150,180,230,.12);
    }
    .orbit-ring {
      position: absolute;
      inset: 9%;
      border: 1px solid rgba(151,180,232,.18);
      border-radius: 50%;
      transform: rotate(14deg);
    }
    .orbit-ring::before, .orbit-ring::after {
      content: "";
      position: absolute;
      width: 7px;
      height: 7px;
      border-radius: 50%;
      background: #dce9ff;
      box-shadow: 0 0 18px #8db2ff;
    }
    .orbit-ring::before { top: 11%; left: 18%; }
    .orbit-ring::after { right: 15%; bottom: 16%; }

    .section-block, .page-section {
      width: min(1160px, calc(100% - 46px));
      margin: 0 auto;
      padding: 94px 0;
    }
    .page-section { min-height: calc(100vh - 68px); padding-top: 72px; }
    .section-heading { margin-bottom: 36px; }
    .section-heading h2, .cta-band h2 {
      margin: 0 0 9px;
      font-family: Georgia, "Times New Roman", serif;
      font-size: clamp(34px, 4vw, 51px);
      font-weight: 500;
      letter-spacing: -.025em;
    }
    .section-heading p:last-child, .page-intro > p:last-child { margin-bottom: 0; color: var(--muted); }
    .split-heading { display: flex; align-items: end; justify-content: space-between; gap: 22px; }
    .section-actions { display: flex; gap: 9px; }

    .sky-grid { display: grid; grid-template-columns: 1.1fr .9fr; gap: 15px; }
    .glass-card {
      border: 1px solid var(--line);
      background: linear-gradient(145deg, rgba(20,31,56,.8), rgba(9,15,29,.9));
      box-shadow: 0 18px 50px rgba(0,0,0,.13);
    }
    .score-card {
      min-height: 225px;
      display: flex;
      align-items: center;
      gap: 26px;
      padding: 31px;
      border-radius: 18px;
    }
    .score-orb {
      flex: 0 0 132px;
      height: 132px;
      display: grid;
      place-content: center;
      text-align: center;
      border: 1px solid rgba(125,168,255,.35);
      border-radius: 50%;
      background: radial-gradient(circle, rgba(125,168,255,.17), transparent 68%);
    }
    .score-orb strong { display: block; font-family: Georgia, serif; font-size: 50px; line-height: .9; }
    .score-orb span { margin-top: 6px; color: var(--muted); font-size: 10px; }
    .score-orb.excellent { border-color: rgba(112,225,208,.5); background: radial-gradient(circle, rgba(112,225,208,.16), transparent 68%); }
    .score-orb.poor { border-color: rgba(244,199,121,.45); background: radial-gradient(circle, rgba(244,199,121,.14), transparent 68%); }
    .quality-badge {
      display: inline-block;
      padding: 5px 9px;
      border: 1px solid rgba(125,168,255,.3);
      border-radius: 99px;
      color: var(--blue);
      font-size: 9px;
      font-weight: 850;
      letter-spacing: .08em;
    }
    .quality-badge.excellent { color: var(--cyan); border-color: rgba(112,225,208,.34); }
    .quality-badge.poor { color: var(--gold); border-color: rgba(244,199,121,.35); }
    .score-card h3 { margin: 13px 0 8px; font-size: 21px; }
    .score-card p { margin: 0; color: var(--muted); font-size: 12px; line-height: 1.65; }
    .metric-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 11px; }
    .metric {
      min-height: 107px;
      display: grid;
      align-content: center;
      gap: 6px;
      padding: 18px;
      border-radius: 15px;
    }
    .metric span { color: var(--muted); font-size: 11px; }
    .metric strong { font-family: Georgia, serif; font-size: 24px; font-weight: 500; }
    .metric small { color: #7599df; font-size: 14px; }
    .forecast-card { margin-top: 15px; padding: 23px; border-radius: 18px; }
    .forecast-title { display: flex; justify-content: space-between; margin-bottom: 19px; }
    .forecast-title small { color: var(--muted); }
    .forecast-strip { display: grid; grid-template-columns: repeat(8, 1fr); }
    .forecast-hour {
      min-height: 111px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 7px;
      border-left: 1px solid var(--line);
    }
    .forecast-hour:first-child { border-left: 0; }
    .forecast-hour span, .forecast-hour small { color: var(--muted); font-size: 10px; }
    .forecast-hour b { color: #9ab9f5; font-size: 19px; font-weight: 500; }
    .forecast-hour strong { font-size: 14px; }
    .source-line {
      margin: 17px 0 0;
      padding-top: 16px;
      border-top: 1px solid var(--line);
      display: flex;
      justify-content: flex-end;
      align-items: center;
      gap: 7px;
      color: #69758a;
      font-size: 9px;
    }
    .api-error {
      grid-column: 1 / -1;
      min-height: 140px;
      padding: 27px;
      border: 1px solid rgba(255,143,156,.25);
      border-radius: 17px;
      color: #ffb6be;
      background: rgba(255,143,156,.055);
    }
    .api-error p { color: var(--muted); }

    .event-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 13px; }
    .event-card {
      position: relative;
      min-height: 220px;
      padding: 28px;
      border-radius: 16px;
      overflow: hidden;
    }
    .event-card::after {
      content: "";
      position: absolute;
      width: 145px;
      height: 145px;
      right: -50px;
      bottom: -62px;
      border: 1px solid rgba(125,168,255,.13);
      border-radius: 50%;
    }
    .event-card time { color: #8eb1fa; font-size: 11px; font-weight: 850; letter-spacing: .08em; }
    .event-card h3 { margin: 44px 0 8px; font-size: 19px; }
    .event-card p { margin: 0; color: var(--muted); font-size: 12px; }

    .cta-band {
      width: min(1260px, calc(100% - 30px));
      min-height: 250px;
      margin: 36px auto 90px;
      padding: 48px clamp(26px, 6vw, 72px);
      border: 1px solid rgba(125,168,255,.18);
      border-radius: 22px;
      display: grid;
      grid-template-columns: 1.7fr .45fr .45fr auto;
      align-items: center;
      gap: 28px;
      background: radial-gradient(circle at 80% 50%, rgba(74,111,182,.23), transparent 40%), linear-gradient(120deg,#111d37,#080e1c);
    }
    .cta-band h2 { margin: 0; }
    .cta-stat { padding-left: 23px; border-left: 1px solid var(--line); }
    .cta-stat strong { display: block; font-family: Georgia, serif; font-size: 39px; }
    .cta-stat span { color: var(--muted); font-size: 10px; }
    .light-button { border: 0; color: #0a1020; background: #fff; white-space: nowrap; }

    .page-intro { margin-bottom: 42px; }
    .page-intro h1 { font-size: clamp(52px, 7vw, 82px); }
    .page-intro p:last-child { max-width: 670px; line-height: 1.7; }
    .journal-layout { display: grid; grid-template-columns: minmax(330px, .8fr) minmax(470px, 1.2fr); gap: 17px; align-items: start; }
    .journal-form, .journal-list-wrap, .post-form, .post-list {
      padding: 27px;
      border-radius: 18px;
    }
    .form-title, .list-toolbar { display: flex; align-items: start; justify-content: space-between; gap: 12px; margin-bottom: 24px; }
    .form-title span, .list-toolbar span { color: #728098; font-size: 9px; font-weight: 850; letter-spacing: .15em; }
    .form-title h2, .list-toolbar h2, .post-form h2 { margin: 6px 0 0; font-family: Georgia, serif; font-size: 26px; font-weight: 500; }
    .step-badge { padding: 5px 8px; border: 1px solid rgba(125,168,255,.26); border-radius: 99px; color: #91b4fa; font-size: 9px; font-weight: 850; }
    .form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
    .form-grid label, .post-form label { display: flex; flex-direction: column; gap: 8px; }
    .form-grid label.full { grid-column: 1 / -1; }
    .form-grid label > span, .post-form label > span { color: #b7c0d0; font-size: 10px; font-weight: 750; }
    .form-grid input, .form-grid textarea, .post-form input, .post-form textarea {
      width: 100%;
      border: 1px solid var(--line);
      border-radius: 9px;
      padding: 12px 13px;
      color: #fff;
      background: rgba(255,255,255,.035);
      resize: vertical;
    }
    .form-grid input::placeholder, .form-grid textarea::placeholder,
    .post-form input::placeholder, .post-form textarea::placeholder { color: #596478; }
    .form-grid input[type="range"] { padding: 8px 0; border: 0; background: transparent; accent-color: var(--blue-strong); }
    .photo-drop {
      min-height: 104px;
      margin: 17px 0;
      border: 1px dashed rgba(125,168,255,.35);
      border-radius: 12px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 5px;
      color: #8eabdf;
      background: rgba(125,168,255,.035);
      cursor: pointer;
      overflow: hidden;
      text-align: center;
    }
    .photo-drop strong { color: #c9d5e9; font-size: 12px; }
    .photo-drop span { color: #68758a; font-size: 9px; }
    .photo-drop img { width: 100%; height: 190px; object-fit: cover; }
    .form-message { min-height: 17px; margin: 10px 0 0; color: #ffadb7; font-size: 11px; }
    .list-toolbar h2 b {
      min-width: 26px;
      height: 26px;
      display: inline-grid;
      place-items: center;
      padding: 0 7px;
      border-radius: 99px;
      color: var(--blue);
      background: rgba(125,168,255,.1);
      font: 700 10px sans-serif;
    }
    .toolbar-actions { display: flex; gap: 7px; }
    .search-box {
      height: 41px;
      display: flex;
      align-items: center;
      gap: 9px;
      border: 1px solid var(--line);
      border-radius: 9px;
      padding: 0 12px;
      color: #64738b;
      background: rgba(255,255,255,.025);
    }
    .search-box input { flex: 1; min-width: 0; border: 0; outline: 0; color: white; background: transparent; }
    .journal-list { max-height: 720px; margin-top: 15px; padding-right: 4px; display: flex; flex-direction: column; gap: 10px; overflow-y: auto; }
    .log-card {
      position: relative;
      min-height: 137px;
      display: grid;
      grid-template-columns: 112px 1fr;
      border: 1px solid var(--line);
      border-radius: 13px;
      overflow: hidden;
      background: rgba(255,255,255,.026);
    }
    .log-card > img, .log-placeholder { width: 112px; height: 100%; min-height: 137px; object-fit: cover; }
    .log-placeholder { display: grid; place-items: center; color: #667da9; font-size: 28px; background: radial-gradient(circle,#202f50,#0a1120); }
    .log-content { min-width: 0; padding: 16px 42px 14px 17px; }
    .log-meta { display: flex; flex-wrap: wrap; gap: 9px; color: #78869d; font-size: 9px; }
    .log-content h3 { margin: 8px 0 5px; font-size: 17px; }
    .equipment { color: #91a7cb; font-size: 10px; }
    .log-notes { margin: 7px 0; color: #a4adbd; font-size: 11px; line-height: 1.55; display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden; }
    .rating-row { color: #7182a0; font-size: 9px; }
    .delete-button {
      position: absolute;
      top: 9px;
      right: 9px;
      width: 30px;
      height: 30px;
      display: grid;
      place-items: center;
      border: 0;
      border-radius: 50%;
      color: #707d91;
      background: transparent;
      cursor: pointer;
    }
    .delete-button:hover { color: var(--danger); background: rgba(255,143,156,.08); }
    .empty-state {
      min-height: 255px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 9px;
      color: #5f6f89;
      text-align: center;
    }
    .empty-state strong { color: #aeb8c9; }
    .empty-state p { margin: 0; font-size: 11px; }

    .messier-intro { display: flex; justify-content: space-between; align-items: center; gap: 20px; }
    .progress-orb {
      flex: 0 0 auto;
      width: 106px;
      height: 106px;
      display: grid;
      place-content: center;
      text-align: center;
      border: 1px solid rgba(125,168,255,.32);
      border-radius: 50%;
      background: radial-gradient(circle,rgba(125,168,255,.14),transparent 70%);
    }
    .progress-orb strong { font-family: Georgia, serif; font-size: 37px; line-height: 1; }
    .progress-orb span { color: var(--muted); font-size: 9px; }
    .progress-track { height: 4px; margin-top: 20px; border-radius: 99px; overflow: hidden; background: rgba(255,255,255,.07); }
    .progress-track i { display: block; height: 100%; border-radius: inherit; background: linear-gradient(90deg,#729eff,#76e1cf); }
    .messier-toolbar { display: flex; align-items: center; justify-content: space-between; gap: 20px; margin: 23px 0; }
    .messier-toolbar .search-box { width: min(440px, 100%); }
    .messier-toolbar > span { color: var(--muted); font-size: 10px; }
    .messier-grid { display: grid; grid-template-columns: repeat(5, 1fr); gap: 9px; }
    .messier-card { position: relative; min-height: 158px; padding: 16px; border-radius: 13px; }
    .messier-code { color: #85aaf4; font: 21px Georgia, serif; }
    .messier-mark { position: absolute; top: 14px; right: 14px; color: #43516a; }
    .messier-card h3 { margin: 29px 0 4px; font-size: 12px; }
    .messier-card p { margin: 0; color: #667287; font-size: 9px; }
    .messier-card button { position: absolute; left: 16px; bottom: 13px; padding: 0; border: 0; color: #7895c9; background: none; font-size: 9px; cursor: pointer; }
    .messier-card.done { border-color: rgba(112,225,208,.3); background: linear-gradient(145deg,rgba(28,76,74,.38),rgba(9,25,31,.75)); }
    .messier-card.done .messier-mark, .messier-card.done .messier-code { color: var(--cyan); }

    .gallery-grid { display: grid; grid-template-columns: repeat(3, 1fr); grid-auto-rows: 295px; gap: 12px; }
    .gallery-item { position: relative; margin: 0; border-radius: 16px; overflow: hidden; }
    .gallery-item:nth-child(5n+1) { grid-column: span 2; }
    .gallery-item img { width: 100%; height: 100%; object-fit: cover; transition: transform .45s; }
    .gallery-item:hover img { transform: scale(1.035); }
    .gallery-item figcaption { position: absolute; inset: auto 0 0; padding: 58px 20px 18px; background: linear-gradient(transparent,rgba(4,7,14,.92)); }
    .gallery-item figcaption span, .gallery-item figcaption small { display: block; color: #9aa7bb; font-size: 9px; }
    .gallery-item figcaption strong { display: block; margin: 4px 0; font-size: 18px; }
    .gallery-empty {
      min-height: 460px;
      display: grid;
      place-items: center;
      border: 1px solid var(--line);
      border-radius: 20px;
      text-align: center;
      background: radial-gradient(circle at center,rgba(41,65,112,.2),transparent 48%);
    }
    .gallery-empty div { max-width: 410px; padding: 34px; border: 1px solid var(--line); border-radius: 16px; background: rgba(8,14,27,.87); }
    .gallery-empty h2 { margin: 10px 0 8px; }
    .gallery-empty p { color: var(--muted); font-size: 12px; line-height: 1.6; }

    .community-layout { display: grid; grid-template-columns: 350px 1fr; gap: 17px; align-items: start; }
    .post-form h2 { margin: 0 0 22px; }
    .post-form label { margin-bottom: 15px; }
    .local-note { color: #69758b; font-size: 9px; line-height: 1.6; }
    .post-list { min-height: 390px; display: flex; flex-direction: column; gap: 10px; }
    .post-card { display: grid; grid-template-columns: 38px 1fr; gap: 12px; padding: 17px; border: 1px solid var(--line); border-radius: 12px; background: rgba(255,255,255,.026); }
    .post-avatar { width: 37px; height: 37px; display: grid; place-items: center; border-radius: 50%; color: #94b6f8; background: rgba(125,168,255,.1); }
    .post-meta { display: flex; align-items: baseline; gap: 9px; }
    .post-meta strong { font-size: 12px; }
    .post-meta time { color: #68748a; font-size: 9px; }
    .post-card p { margin: 8px 0 0; color: #adb6c5; font-size: 11px; line-height: 1.65; white-space: pre-wrap; }

    footer {
      min-height: 125px;
      display: grid;
      grid-template-columns: 1fr auto 1fr;
      align-items: center;
      padding: 0 clamp(24px, 7vw, 95px);
      border-top: 1px solid var(--line);
      color: #667186;
      font-size: 9px;
    }
    footer .brand { color: #a8b3c6; font-size: 17px; }
    footer p { font: 13px Georgia, serif; }
    footer > span { justify-self: end; }
    .toast {
      position: fixed;
      left: 50%;
      bottom: 26px;
      z-index: 80;
      transform: translateX(-50%);
      display: flex;
      align-items: center;
      gap: 8px;
      padding: 11px 16px;
      border: 1px solid rgba(112,225,208,.25);
      border-radius: 99px;
      color: #c9f7ed;
      background: rgba(10,30,31,.95);
      box-shadow: 0 15px 45px rgba(0,0,0,.42);
      font-size: 11px;
      animation: toast-in .25s ease-out;
    }

    @keyframes pulse { 50% { opacity: .35; } }
    @keyframes toast-in { from { opacity: 0; transform: translate(-50%, 10px); } }

    @media (max-width: 980px) {
      .topbar { grid-template-columns: 1fr auto; }
      .desktop-nav { display: none; }
      .menu-button { display: grid; }
      .hero { min-height: 620px; grid-template-columns: 1fr .55fr; }
      .hero-orbit { width: min(38vw,350px); }
      .planet { width: 135px; }
      .planet::after { width: 198px; height: 57px; }
      .sky-grid { grid-template-columns: 1fr; }
      .cta-band { grid-template-columns: 1fr 1fr 1fr; }
      .cta-band > div:first-child { grid-column: 1 / -1; }
      .journal-layout { grid-template-columns: 1fr; }
      .journal-list { max-height: none; }
      .messier-grid { grid-template-columns: repeat(4,1fr); }
    }

    @media (max-width: 680px) {
      .topbar { height: 62px; padding: 0 15px; }
      .api-pill { display: none; }
      .hero { min-height: 605px; display: block; padding: 78px 23px 88px; }
      .hero h1 { font-size: 50px; }
      .hero-copy { font-size: 14px; }
      .hero-orbit { position: absolute; right: -68px; bottom: -5px; width: 230px; opacity: .68; }
      .planet { width: 93px; }
      .planet::after { width: 138px; height: 39px; }
      .section-block, .page-section { width: min(100% - 28px,1160px); padding: 67px 0; }
      .split-heading { flex-direction: column; align-items: start; }
      .score-card { flex-direction: column; align-items: start; }
      .score-orb { width: 108px; height: 108px; flex-basis: 108px; }
      .forecast-card { padding: 18px 0; overflow: hidden; }
      .forecast-title, .source-line { margin-left: 18px; margin-right: 18px; }
      .forecast-strip { display: flex; overflow-x: auto; }
      .forecast-hour { min-width: 82px; }
      .event-grid { grid-template-columns: 1fr; }
      .cta-band { grid-template-columns: 1fr 1fr; margin-bottom: 55px; }
      .light-button { grid-column: 1 / -1; }
      .page-intro h1 { font-size: 53px; }
      .journal-form, .journal-list-wrap, .post-form, .post-list { padding: 20px; }
      .form-grid { grid-template-columns: 1fr; }
      .form-grid label.full { grid-column: auto; }
      .log-card { grid-template-columns: 83px 1fr; }
      .log-card > img, .log-placeholder { width: 83px; }
      .rating-row { display: none; }
      .messier-intro { align-items: end; }
      .progress-orb { width: 83px; height: 83px; }
      .progress-orb strong { font-size: 29px; }
      .messier-toolbar { flex-direction: column; align-items: stretch; }
      .messier-grid { grid-template-columns: repeat(2,1fr); }
      .gallery-grid { grid-template-columns: 1fr; grid-auto-rows: 270px; }
      .gallery-item:nth-child(5n+1) { grid-column: auto; }
      .community-layout { grid-template-columns: 1fr; }
      footer { grid-template-columns: 1fr; gap: 11px; padding: 32px 23px; }
      footer p, footer > span { justify-self: start; margin: 0; }
    }
  </style>
</head>
<body>
  <header class="topbar">
    <button class="brand" data-go="home" aria-label="Lucidas 홈">
      <span class="brand-mark">✦</span><span>Lucidas</span>
    </button>
    <nav class="desktop-nav" aria-label="주요 메뉴">
      <button class="active" data-go="home">오늘의 하늘</button>
      <button data-go="journal">관측일지</button>
      <button data-go="messier">메시에</button>
      <button data-go="gallery">갤러리</button>
      <button data-go="community">게시판</button>
    </nav>
    <div class="header-actions">
      <span id="api-pill" class="api-pill loading"><i></i><span>API 연결 중</span></span>
      <button id="menu-button" class="icon-button menu-button" aria-label="메뉴 열기">☰</button>
    </div>
    <nav id="mobile-menu" class="mobile-menu" aria-label="모바일 메뉴">
      <button data-go="home">☾ 오늘의 하늘</button>
      <button data-go="journal">▤ 관측일지</button>
      <button data-go="messier">⌁ 메시에</button>
      <button data-go="gallery">▧ 갤러리</button>
      <button data-go="community">○ 게시판</button>
    </nav>
  </header>

  <main>
    <section class="view" data-view="home">
      <div class="hero">
        <div>
          <p class="eyebrow">✦ ASTRONOMY COMMUNITY</p>
          <h1>오늘 밤, 별을<br><em>만나러 갈까요?</em></h1>
          <p class="hero-copy">관측 조건을 확인하고, 밤하늘에서 만난 순간을 한 장의 기록으로 남겨보세요.</p>
          <div class="hero-buttons">
            <button class="primary-button" data-go="journal">▤ 관측일지 쓰기</button>
            <button class="ghost-button" id="show-sky">☾ 관측 조건 보기</button>
          </div>
        </div>
        <div class="hero-orbit" aria-hidden="true"><div class="planet"></div><div class="orbit-ring"></div></div>
      </div>

      <section id="sky-panel" class="section-block">
        <div class="section-heading split-heading">
          <div>
            <p class="eyebrow">☾ LIVE SKY</p>
            <h2>오늘의 관측 조건</h2>
            <p><span id="location-label">부산</span>의 실시간 날씨와 앞으로 12시간을 분석합니다.</p>
          </div>
          <div class="section-actions">
            <button id="use-location" class="small-button">⌖ 현재 위치</button>
            <button id="refresh-sky" class="icon-button" aria-label="날씨 새로고침">↻</button>
          </div>
        </div>
        <div id="sky-content">
          <div class="sky-grid">
            <article class="score-card glass-card">
              <div class="score-orb"><strong>—</strong><span>/ 100</span></div>
              <div><span class="quality-badge">연결 중</span><h3>하늘을 분석하고 있어요</h3><p>구름·강수·바람·가시거리를 종합해 관측 조건을 계산합니다.</p></div>
            </article>
            <div class="metric-grid">
              <article class="metric glass-card"><small>☁</small><span>구름량</span><strong>—</strong></article>
              <article class="metric glass-card"><small>≋</small><span>풍속</span><strong>—</strong></article>
              <article class="metric glass-card"><small>◉</small><span>가시거리</span><strong>—</strong></article>
              <article class="metric glass-card"><small>☾</small><span>달</span><strong>—</strong></article>
            </div>
          </div>
        </div>
      </section>

      <section class="section-block">
        <div class="section-heading">
          <p class="eyebrow">◇ SKY CALENDAR</p>
          <h2>다가오는 밤하늘</h2>
          <p>관측 준비를 위한 2026년 주요 천문 일정입니다.</p>
        </div>
        <div class="event-grid">
          <article class="event-card glass-card"><time>10. 21–22</time><h3>오리온자리 유성우</h3><p>새벽 시간대 동쪽 하늘</p></article>
          <article class="event-card glass-card"><time>11. 17–18</time><h3>사자자리 유성우</h3><p>자정 이후 복사점 상승</p></article>
          <article class="event-card glass-card"><time>12. 13–14</time><h3>쌍둥이자리 유성우</h3><p>연중 가장 풍성한 유성우</p></article>
        </div>
      </section>

      <section class="cta-band">
        <div><p class="eyebrow">⌁ YOUR NIGHT, YOUR STORY</p><h2>관측한 밤이 쌓일수록<br>나만의 우주가 완성됩니다.</h2></div>
        <div class="cta-stat"><strong id="home-entry-count">0</strong><span>관측 기록</span></div>
        <div class="cta-stat"><strong id="home-messier-count">0</strong><span>메시에 완료</span></div>
        <button class="light-button" data-go="journal">첫 기록 남기기 →</button>
      </section>
    </section>

    <section class="view page-section" data-view="journal" hidden>
      <div class="page-intro">
        <p class="eyebrow">▤ OBSERVATION LOG</p>
        <h1>관측일지</h1>
        <p>그날의 하늘, 장비, 느낌을 기록하세요.</p>
      </div>
      <div class="journal-layout">
        <form id="journal-form" class="journal-form glass-card">
          <div class="form-title"><div><span>NEW RECORD</span><h2>새 관측 기록</h2></div><b class="step-badge">LOCAL SAVE</b></div>
          <div class="form-grid">
            <label><span>관측 일시</span><input id="observed-at" name="observedAt" type="datetime-local" required></label>
            <label><span>장소</span><input id="journal-location" name="location" placeholder="예: 부산 금정산" required></label>
            <label class="full"><span>관측 대상</span><input id="target-input" name="target" placeholder="예: M13 헤라클레스 구상성단" required></label>
            <label class="full"><span>장비</span><input name="equipment" placeholder="예: Dwarf Mini"></label>
            <label><span>시상 <output id="seeing-output">3</output>/5</span><input id="seeing-input" name="seeing" type="range" min="1" max="5" value="3"></label>
            <label><span>투명도 <output id="transparency-output">3</output>/5</span><input id="transparency-input" name="transparency" type="range" min="1" max="5" value="3"></label>
            <label class="full"><span>관측 메모</span><textarea name="notes" rows="5" placeholder="보인 구조, 촬영 설정, 그날의 느낌을 기록해 보세요."></textarea></label>
          </div>
          <label class="photo-drop" for="photo-input" id="photo-drop">
            <strong>＋ 관측 사진 추가</strong>
            <span>JPG · PNG · WEBP, 사진은 이 브라우저에 저장됩니다.</span>
          </label>
          <input id="photo-input" type="file" accept="image/jpeg,image/png,image/webp" hidden>
          <button id="save-entry" class="primary-button wide" type="submit">관측일지 저장</button>
          <p id="form-message" class="form-message"></p>
        </form>

        <section class="journal-list-wrap glass-card">
          <div class="list-toolbar">
            <div><span>MY ARCHIVE</span><h2>나의 기록 <b id="entry-count">0</b></h2></div>
            <div class="toolbar-actions">
              <button id="export-journal" class="icon-button" aria-label="관측일지 JSON 내보내기">⇩</button>
            </div>
          </div>
          <label class="search-box">⌕ <input id="journal-search" type="search" placeholder="대상·장소·장비 검색"></label>
          <div id="journal-list" class="journal-list"></div>
        </section>
      </div>
    </section>

    <section class="view page-section" data-view="messier" hidden>
      <div class="page-intro messier-intro">
        <div>
          <p class="eyebrow">⌁ MESSIER CATALOG</p>
          <h1>메시에</h1>
          <p>관측일지의 대상에 M 번호를 적으면 자동으로 완료 표시됩니다.</p>
        </div>
        <div class="progress-orb"><strong id="messier-count">0</strong><span>/ 110 완료</span></div>
      </div>
      <div class="progress-track"><i id="messier-progress" style="width:0%"></i></div>
      <div class="messier-toolbar">
        <label class="search-box">⌕ <input id="messier-search" type="search" placeholder="M13 또는 천체 이름 검색"></label>
        <span>카드를 눌러 관측일지에 바로 추가할 수 있어요.</span>
      </div>
      <div id="messier-grid" class="messier-grid"></div>
    </section>

    <section class="view page-section" data-view="gallery" hidden>
      <div class="page-intro">
        <p class="eyebrow">▧ NIGHT ARCHIVE</p>
        <h1>갤러리</h1>
        <p>관측일지에 사진을 더하면 이곳에 자동으로 모입니다.</p>
      </div>
      <div id="gallery-content"></div>
    </section>

    <section class="view page-section" data-view="community" hidden>
      <div class="page-intro">
        <p class="eyebrow">○ COMMUNITY</p>
        <h1>게시판</h1>
        <p>오늘의 하늘과 관측 경험을 짧게 나눠보세요.</p>
      </div>
      <div class="community-layout">
        <form id="post-form" class="post-form glass-card">
          <h2>새 글 남기기</h2>
          <label><span>닉네임</span><input name="nickname" maxlength="24" required placeholder="별을 좋아하는 이름"></label>
          <label><span>메시지</span><textarea name="message" rows="6" maxlength="500" required placeholder="오늘 무엇을 관측했나요?"></textarea></label>
          <button class="primary-button wide" type="submit">게시글 등록</button>
          <p class="local-note">단일 HTML 버전의 게시글은 현재 브라우저에만 저장됩니다.</p>
        </form>
        <div id="post-list" class="post-list glass-card"></div>
      </div>
    </section>
  </main>

  <footer>
    <button class="brand" data-go="home"><span class="brand-mark">✦</span><span>Lucidas</span></button>
    <p>Tonight, we look up.</p>
    <span>Open-Meteo weather data · Local-first observation log</span>
  </footer>

  <div id="toast" class="toast" hidden>✓ 저장했습니다.</div>

  <script>
    (function () {
      "use strict";

      var STORAGE_ENTRIES = "lucidas-standalone-entries-v1";
      var STORAGE_POSTS = "lucidas-standalone-posts-v1";
      var currentLocation = { latitude: 35.1796, longitude: 129.0756, label: "부산" };
      var entries = readJson(STORAGE_ENTRIES, []);
      var posts = readJson(STORAGE_POSTS, []);
      var selectedPhoto = null;
      var photoPreviewUrl = "";
      var dbPromise = openPhotoDb();

      var specialNames = {
        1:"게 성운",2:"물병자리 구상성단",3:"사냥개자리 구상성단",4:"전갈자리 구상성단",5:"뱀자리 구상성단",6:"나비 성단",7:"프톨레마이오스 성단",8:"석호 성운",
        11:"야생오리 성단",13:"헤라클레스 대성단",16:"독수리 성운",17:"오메가 성운",20:"삼렬 성운",22:"궁수자리 대성단",24:"궁수자리 별구름",27:"아령 성운",
        31:"안드로메다 은하",32:"안드로메다 위성은하",33:"삼각형자리 은하",42:"오리온 대성운",43:"드 마이란 성운",44:"벌집 성단",45:"플레이아데스 성단",
        51:"소용돌이 은하",57:"고리 성운",63:"해바라기 은하",64:"검은 눈 은하",74:"유령 은하",76:"작은 아령 성운",78:"반사 성운",81:"보데 은하",
        82:"시가 은하",87:"처녀자리 A",92:"헤라클레스 구상성단",97:"부엉이 성운",101:"바람개비 은하",104:"솜브레로 은하",108:"서프보드 은하",
        109:"진공청소기 은하",110:"안드로메다 위성은하"
      };

      function byId(id) { return document.getElementById(id); }
      function escapeHtml(value) {
        return String(value == null ? "" : value)
          .replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;")
          .replace(/"/g, "&quot;").replace(/'/g, "&#039;");
      }
      function readJson(key, fallback) {
        try {
          var value = JSON.parse(localStorage.getItem(key));
          return Array.isArray(value) ? value : fallback;
        } catch (error) { return fallback; }
      }
      function saveEntries() { localStorage.setItem(STORAGE_ENTRIES, JSON.stringify(entries)); }
      function savePosts() { localStorage.setItem(STORAGE_POSTS, JSON.stringify(posts)); }
      function uid() {
        if (window.crypto && crypto.randomUUID) return crypto.randomUUID();
        return Date.now().toString(36) + Math.random().toString(36).slice(2);
      }
      function localInputValue(date) {
        var d = date || new Date();
        function pad(value) { return String(value).padStart(2, "0"); }
        return d.getFullYear() + "-" + pad(d.getMonth() + 1) + "-" + pad(d.getDate()) + "T" + pad(d.getHours()) + ":" + pad(d.getMinutes());
      }
      function formatDate(value) {
        var date = new Date(value);
        if (Number.isNaN(date.getTime())) return value;
        return new Intl.DateTimeFormat("ko-KR", { month:"long", day:"numeric", hour:"2-digit", minute:"2-digit", hour12:false }).format(date);
      }
      function showToast(message) {
        var toast = byId("toast");
        toast.textContent = "✓ " + message;
        toast.hidden = false;
        clearTimeout(showToast.timer);
        showToast.timer = setTimeout(function () { toast.hidden = true; }, 2500);
      }

      function openPhotoDb() {
        return new Promise(function (resolve) {
          if (!window.indexedDB) return resolve(null);
          var request = indexedDB.open("lucidas-standalone-photos", 1);
          request.onupgradeneeded = function () {
            if (!request.result.objectStoreNames.contains("photos")) request.result.createObjectStore("photos");
          };
          request.onsuccess = function () { resolve(request.result); };
          request.onerror = function () { resolve(null); };
        });
      }
      async function putPhoto(id, dataUrl) {
        var db = await dbPromise;
        if (!db) throw new Error("사진 저장소를 사용할 수 없습니다.");
        return new Promise(function (resolve, reject) {
          var tx = db.transaction("photos", "readwrite");
          tx.objectStore("photos").put(dataUrl, id);
          tx.oncomplete = resolve;
          tx.onerror = function () { reject(tx.error); };
        });
      }
      async function getPhoto(id) {
        var db = await dbPromise;
        if (!db) return "";
        return new Promise(function (resolve) {
          var request = db.transaction("photos", "readonly").objectStore("photos").get(id);
          request.onsuccess = function () { resolve(request.result || ""); };
          request.onerror = function () { resolve(""); };
        });
      }
      async function deletePhoto(id) {
        var db = await dbPromise;
        if (!db) return;
        return new Promise(function (resolve) {
          var tx = db.transaction("photos", "readwrite");
          tx.objectStore("photos").delete(id);
          tx.oncomplete = resolve;
          tx.onerror = resolve;
        });
      }
      function fileToCompressedDataUrl(file) {
        return new Promise(function (resolve, reject) {
          var reader = new FileReader();
          reader.onerror = function () { reject(new Error("사진을 읽지 못했습니다.")); };
          reader.onload = function () {
            var image = new Image();
            image.onerror = function () { reject(new Error("지원하지 않는 이미지입니다.")); };
            image.onload = function () {
              var max = 1400;
              var scale = Math.min(1, max / Math.max(image.width, image.height));
              var canvas = document.createElement("canvas");
              canvas.width = Math.max(1, Math.round(image.width * scale));
              canvas.height = Math.max(1, Math.round(image.height * scale));
              canvas.getContext("2d").drawImage(image, 0, 0, canvas.width, canvas.height);
              resolve(canvas.toDataURL("image/jpeg", .82));
            };
            image.src = reader.result;
          };
          reader.readAsDataURL(file);
        });
      }

      function navigate(view) {
        document.querySelectorAll(".view").forEach(function (section) {
          section.hidden = section.getAttribute("data-view") !== view;
        });
        document.querySelectorAll(".desktop-nav button").forEach(function (button) {
          button.classList.toggle("active", button.getAttribute("data-go") === view);
        });
        byId("mobile-menu").classList.remove("open");
        if (view === "gallery") renderGallery();
        window.scrollTo({ top: 0, behavior: "smooth" });
      }

      function moonInfo(date) {
        var d = date || new Date();
        var synodic = 29.53058867;
        var known = Date.UTC(2000, 0, 6, 18, 14);
        var phase = ((((d.getTime() - known) / 86400000) % synodic) + synodic) % synodic / synodic;
        var illumination = Math.round((1 - Math.cos(2 * Math.PI * phase)) * 50);
        var name = phase < .03 || phase > .97 ? "삭" : phase < .22 ? "초승달" : phase < .28 ? "상현달" : phase < .47 ? "차오르는 달" : phase < .53 ? "보름달" : phase < .72 ? "기우는 달" : phase < .78 ? "하현달" : "그믐달";
        return { name:name, illumination:illumination };
      }
      function calculateScore(data, hourIndex) {
        var cloud = Number(data.current.cloud_cover || 0);
        var rain = Number(data.hourly.precipitation_probability[hourIndex] || 0);
        var wind = Number(data.current.wind_speed_10m || 0);
        var visibility = Number(data.hourly.visibility[hourIndex] || 10000) / 1000;
        var score = 100 - cloud * .68 - rain * .18 - Math.max(0, wind - 12) * 1.25;
        if (visibility < 10) score -= (10 - visibility) * 2;
        return Math.max(0, Math.min(100, Math.round(score)));
      }
      function skyState(score) {
        if (score >= 75) return { label:"관측 추천", cls:"excellent", headline:"망원경을 챙겨도 좋아요" };
        if (score >= 50) return { label:"관측 가능", cls:"fair", headline:"구름 사이 관측을 노려보세요" };
        return { label:"관측 어려움", cls:"poor", headline:"다음 맑은 밤을 기다려요" };
      }
      function updateApiPill(status, text) {
        var pill = byId("api-pill");
        pill.className = "api-pill " + status;
        pill.querySelector("span").textContent = text;
      }
      async function loadSky(location) {
        var loc = location || currentLocation;
        updateApiPill("loading", "API 연결 중");
        byId("location-label").textContent = loc.label;
        try {
          var params = new URLSearchParams({
            latitude:String(loc.latitude),
            longitude:String(loc.longitude),
            current:"temperature_2m,relative_humidity_2m,precipitation,weather_code,cloud_cover,wind_speed_10m",
            hourly:"temperature_2m,cloud_cover,visibility,precipitation_probability,wind_speed_10m",
            daily:"sunrise,sunset",
            timezone:"auto",
            forecast_days:"2"
          });
          var controller = new AbortController();
          var timer = setTimeout(function () { controller.abort(); }, 12000);
          var response = await fetch("https://api.open-meteo.com/v1/forecast?" + params.toString(), { signal:controller.signal, cache:"no-store" });
          clearTimeout(timer);
          if (!response.ok) throw new Error("HTTP " + response.status);
          var data = await response.json();
          renderSky(data);
          updateApiPill("ok", "API 연결됨");
        } catch (error) {
          updateApiPill("error", "API 재연결 필요");
          byId("sky-content").innerHTML =
            '<div class="api-error"><strong>관측 조건을 불러오지 못했어요.</strong>' +
            '<p>인터넷 연결을 확인한 뒤 새로고침해 주세요. 파일을 직접 연 경우에도 Open-Meteo API 연결은 지원됩니다.</p>' +
            '<button class="small-button" id="retry-sky">다시 연결</button></div>';
          byId("retry-sky").addEventListener("click", function () { loadSky(); });
        }
      }
      function renderSky(data) {
        var currentHour = data.current.time.slice(0, 13) + ":00";
        var index = data.hourly.time.findIndex(function (time) { return time >= currentHour; });
        if (index < 0) index = 0;
        var score = calculateScore(data, index);
        var state = skyState(score);
        var moon = moonInfo();
        var visibility = Math.round(Number(data.hourly.visibility[index] || 0) / 1000);
        var hours = data.hourly.time.slice(index, index + 8).map(function (time, offset) {
          var i = index + offset;
          var hour = time.slice(11, 13);
          return '<div class="forecast-hour">' +
            '<span>' + (offset === 0 ? "지금" : hour + "시") + '</span>' +
            '<b>☾</b><strong>' + Math.round(data.hourly.cloud_cover[i]) + '%</strong>' +
            '<small>강수 ' + Math.round(data.hourly.precipitation_probability[i]) + '%</small></div>';
        }).join("");
        var sunset = data.daily && data.daily.sunset && data.daily.sunset[0] ? data.daily.sunset[0].slice(-5) : "—";
        byId("sky-content").innerHTML =
          '<div class="sky-grid">' +
            '<article class="score-card glass-card">' +
              '<div class="score-orb ' + state.cls + '"><strong>' + score + '</strong><span>/ 100</span></div>' +
              '<div><span class="quality-badge ' + state.cls + '">' + state.label + '</span><h3>' + state.headline + '</h3>' +
              '<p>구름·강수·바람·가시거리를 종합한 참고 점수입니다.</p></div>' +
            '</article>' +
            '<div class="metric-grid">' +
              '<article class="metric glass-card"><small>☁</small><span>구름량</span><strong>' + Math.round(data.current.cloud_cover) + '%</strong></article>' +
              '<article class="metric glass-card"><small>≋</small><span>풍속</span><strong>' + Number(data.current.wind_speed_10m).toFixed(1) + ' km/h</strong></article>' +
              '<article class="metric glass-card"><small>◉</small><span>가시거리</span><strong>' + visibility + ' km</strong></article>' +
              '<article class="metric glass-card"><small>☾</small><span>' + moon.name + '</span><strong>' + moon.illumination + '%</strong></article>' +
            '</div>' +
          '</div>' +
          '<article class="forecast-card glass-card"><div class="forecast-title"><strong>시간별 하늘</strong><small>일몰 ' + sunset + '</small></div>' +
          '<div class="forecast-strip">' + hours + '</div>' +
          '<p class="source-line"><i class="online"></i> Open-Meteo 실시간 예보 · 정상 수신</p></article>';
      }

      function completedMessier() {
        var set = new Set();
        entries.forEach(function (entry) {
          var match = String(entry.target).toUpperCase().match(/\bM\s?(\d{1,3})\b/);
          if (match) {
            var number = Number(match[1]);
            if (number >= 1 && number <= 110) set.add(number);
          }
        });
        return set;
      }
      function updateCounts() {
        var completed = completedMessier();
        byId("entry-count").textContent = String(entries.length);
        byId("home-entry-count").textContent = String(entries.length);
        byId("home-messier-count").textContent = String(completed.size);
        byId("messier-count").textContent = String(completed.size);
        byId("messier-progress").style.width = (completed.size / 110 * 100) + "%";
      }

      async function renderEntries() {
        var query = byId("journal-search").value.trim().toLowerCase();
        var filtered = entries.filter(function (entry) {
          if (!query) return true;
          return [entry.target, entry.location, entry.equipment, entry.notes].some(function (value) {
            return String(value || "").toLowerCase().includes(query);
          });
        });
        var list = byId("journal-list");
        if (!filtered.length) {
          list.innerHTML = '<div class="empty-state"><span style="font-size:34px">☾</span><strong>' +
            (entries.length ? "검색 결과가 없습니다." : "아직 관측 기록이 없습니다.") +
            '</strong><p>밤하늘에서 만난 첫 대상을 기록해 보세요.</p></div>';
          updateCounts();
          return;
        }
        var cards = await Promise.all(filtered.map(async function (entry) {
          var photo = entry.hasPhoto ? await getPhoto(entry.id) : "";
          var visual = photo ? '<img src="' + photo + '" alt="">' : '<div class="log-placeholder">✦</div>';
          return '<article class="log-card">' + visual +
            '<div class="log-content"><div class="log-meta"><span>◷ ' + escapeHtml(formatDate(entry.observedAt)) + '</span><span>⌖ ' + escapeHtml(entry.location) + '</span></div>' +
            '<h3>' + escapeHtml(entry.target) + '</h3><div class="equipment">⌁ ' + escapeHtml(entry.equipment || "장비 미입력") + '</div>' +
            '<p class="log-notes">' + escapeHtml(entry.notes || "메모가 없습니다.") + '</p>' +
            '<div class="rating-row">시상 ' + entry.seeing + '/5 · 투명도 ' + entry.transparency + '/5</div></div>' +
            '<button class="delete-button" data-delete-entry="' + entry.id + '" aria-label="기록 삭제">×</button></article>';
        }));
        list.innerHTML = cards.join("");
        updateCounts();
      }

      function renderMessier() {
        var completed = completedMessier();
        var query = byId("messier-search").value.trim().toLowerCase();
        var html = "";
        for (var number = 1; number <= 110; number += 1) {
          var code = "M" + number;
          var name = specialNames[number] || "메시에 천체";
          if (query && code.toLowerCase().indexOf(query) < 0 && name.toLowerCase().indexOf(query) < 0) continue;
          var done = completed.has(number);
          html += '<article class="messier-card glass-card ' + (done ? "done" : "") + '">' +
            '<span class="messier-code">' + code + '</span><span class="messier-mark">' + (done ? "✓" : "○") + '</span>' +
            '<h3>' + escapeHtml(name) + '</h3><p>' + (done ? "관측 완료" : "아직 기록 없음") + '</p>' +
            '<button data-messier="' + code + ' ' + escapeHtml(name) + '">' + (done ? "기록 더하기" : "관측일지 작성") + ' →</button></article>';
        }
        byId("messier-grid").innerHTML = html || '<div class="empty-state"><strong>검색 결과가 없습니다.</strong></div>';
        updateCounts();
      }

      async function renderGallery() {
        var photoEntries = entries.filter(function (entry) { return entry.hasPhoto; });
        var container = byId("gallery-content");
        if (!photoEntries.length) {
          container.innerHTML = '<div class="gallery-empty"><div><span style="font-size:34px">▧</span><h2>첫 관측 사진을 기다리고 있어요.</h2>' +
            '<p>관측일지에 사진을 추가하면 대상과 촬영 정보가 이곳에 자동으로 정리됩니다.</p>' +
            '<button class="primary-button" data-go="journal">사진 기록 남기기</button></div></div>';
          return;
        }
        var items = await Promise.all(photoEntries.map(async function (entry) {
          var photo = await getPhoto(entry.id);
          if (!photo) return "";
          return '<figure class="gallery-item"><img src="' + photo + '" alt="' + escapeHtml(entry.target) + '">' +
            '<figcaption><span>' + escapeHtml(formatDate(entry.observedAt)) + '</span><strong>' + escapeHtml(entry.target) + '</strong>' +
            '<small>' + escapeHtml(entry.equipment || entry.location) + '</small></figcaption></figure>';
        }));
        container.innerHTML = '<div class="gallery-grid">' + items.join("") + '</div>';
      }

      function renderPosts() {
        var list = byId("post-list");
        if (!posts.length) {
          list.innerHTML = '<div class="empty-state"><span style="font-size:32px">○</span><strong>첫 이야기를 남겨보세요.</strong><p>관측 후기나 오늘의 하늘을 가볍게 나눌 수 있어요.</p></div>';
          return;
        }
        list.innerHTML = posts.map(function (post) {
          return '<article class="post-card"><div class="post-avatar">✦</div><div><div class="post-meta"><strong>' +
            escapeHtml(post.nickname) + '</strong><time>' + escapeHtml(formatDate(post.createdAt)) + '</time></div><p>' +
            escapeHtml(post.message) + '</p></div></article>';
        }).join("");
      }

      document.querySelectorAll("[data-go]").forEach(function (button) {
        button.addEventListener("click", function () { navigate(button.getAttribute("data-go")); });
      });
      byId("menu-button").addEventListener("click", function () { byId("mobile-menu").classList.toggle("open"); });
      byId("show-sky").addEventListener("click", function () { byId("sky-panel").scrollIntoView({ behavior:"smooth" }); });
      byId("refresh-sky").addEventListener("click", function () { loadSky(); });
      byId("use-location").addEventListener("click", function () {
        if (!navigator.geolocation) return showToast("이 브라우저에서는 위치 기능을 사용할 수 없습니다.");
        navigator.geolocation.getCurrentPosition(function (position) {
          currentLocation = { latitude:position.coords.latitude, longitude:position.coords.longitude, label:"현재 위치" };
          byId("journal-location").value = "현재 위치";
          loadSky(currentLocation);
        }, function () {
          showToast("위치 권한이 없어서 부산 기준을 유지합니다.");
        }, { enableHighAccuracy:false, timeout:10000 });
      });

      byId("seeing-input").addEventListener("input", function (event) { byId("seeing-output").value = event.target.value; });
      byId("transparency-input").addEventListener("input", function (event) { byId("transparency-output").value = event.target.value; });
      byId("photo-input").addEventListener("change", async function (event) {
        var file = event.target.files && event.target.files[0];
        if (!file) return;
        var drop = byId("photo-drop");
        drop.innerHTML = "<strong>사진을 준비하는 중…</strong>";
        try {
          selectedPhoto = await fileToCompressedDataUrl(file);
          photoPreviewUrl = selectedPhoto;
          drop.innerHTML = '<img src="' + photoPreviewUrl + '" alt="선택한 관측 사진 미리보기">';
        } catch (error) {
          selectedPhoto = null;
          drop.innerHTML = "<strong>＋ 관측 사진 추가</strong><span>사진을 읽지 못했습니다. 다른 파일을 선택해 주세요.</span>";
        }
      });

      byId("journal-form").addEventListener("submit", async function (event) {
        event.preventDefault();
        var form = event.currentTarget;
        var data = new FormData(form);
        var target = String(data.get("target") || "").trim();
        var location = String(data.get("location") || "").trim();
        if (!target || !location) return;
        var button = byId("save-entry");
        button.disabled = true;
        button.textContent = "저장 중…";
        byId("form-message").textContent = "";
        var entry = {
          id:uid(),
          observedAt:String(data.get("observedAt") || new Date().toISOString()),
          location:location,
          target:target,
          equipment:String(data.get("equipment") || "").trim(),
          seeing:Number(data.get("seeing") || 3),
          transparency:Number(data.get("transparency") || 3),
          notes:String(data.get("notes") || "").trim(),
          hasPhoto:Boolean(selectedPhoto),
          createdAt:new Date().toISOString()
        };
        try {
          if (selectedPhoto) await putPhoto(entry.id, selectedPhoto);
          entries.unshift(entry);
          saveEntries();
          form.reset();
          byId("observed-at").value = localInputValue();
          byId("journal-location").value = currentLocation.label;
          byId("seeing-output").value = "3";
          byId("transparency-output").value = "3";
          selectedPhoto = null;
          photoPreviewUrl = "";
          byId("photo-input").value = "";
          byId("photo-drop").innerHTML = "<strong>＋ 관측 사진 추가</strong><span>JPG · PNG · WEBP, 사진은 이 브라우저에 저장됩니다.</span>";
          await renderEntries();
          renderMessier();
          showToast("관측일지를 저장했습니다.");
        } catch (error) {
          byId("form-message").textContent = error.message || "관측일지를 저장하지 못했습니다.";
        } finally {
          button.disabled = false;
          button.textContent = "관측일지 저장";
        }
      });

      byId("journal-list").addEventListener("click", async function (event) {
        var button = event.target.closest("[data-delete-entry]");
        if (!button) return;
        var id = button.getAttribute("data-delete-entry");
        entries = entries.filter(function (entry) { return entry.id !== id; });
        saveEntries();
        await deletePhoto(id);
        await renderEntries();
        renderMessier();
        showToast("관측일지를 삭제했습니다.");
      });
      byId("journal-search").addEventListener("input", renderEntries);
      byId("export-journal").addEventListener("click", function () {
        var blob = new Blob([JSON.stringify(entries, null, 2)], { type:"application/json;charset=utf-8" });
        var url = URL.createObjectURL(blob);
        var anchor = document.createElement("a");
        anchor.href = url;
        anchor.download = "lucidas-observation-log-" + new Date().toISOString().slice(0, 10) + ".json";
        anchor.click();
        URL.revokeObjectURL(url);
        showToast("관측일지를 내보냈습니다.");
      });

      byId("messier-search").addEventListener("input", renderMessier);
      byId("messier-grid").addEventListener("click", function (event) {
        var button = event.target.closest("[data-messier]");
        if (!button) return;
        byId("target-input").value = button.getAttribute("data-messier");
        navigate("journal");
        byId("target-input").focus();
      });
      byId("gallery-content").addEventListener("click", function (event) {
        var button = event.target.closest("[data-go]");
        if (button) navigate(button.getAttribute("data-go"));
      });

      byId("post-form").addEventListener("submit", function (event) {
        event.preventDefault();
        var form = event.currentTarget;
        var data = new FormData(form);
        var nickname = String(data.get("nickname") || "").trim();
        var message = String(data.get("message") || "").trim();
        if (!nickname || !message) return;
        posts.unshift({ id:uid(), nickname:nickname, message:message, createdAt:new Date().toISOString() });
        savePosts();
        form.reset();
        renderPosts();
        showToast("게시글을 등록했습니다.");
      });

      byId("observed-at").value = localInputValue();
      byId("journal-location").value = currentLocation.label;
      updateCounts();
      renderEntries();
      renderMessier();
      renderPosts();
      loadSky();
    })();
  </script>
</body>
</html>
