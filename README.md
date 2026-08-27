<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>整理券オーダーシステム</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,500;0,600;0,700;1,600&family=Nunito:wght@400;600;700;800&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet">
<style>
  :root{
    --sky-deep:#241E4E;
    --sky-mid:#4B3E82;
    --sky-horizon:#E79A6D;
    --lantern-gold:#FFD98E;
    --lantern-warm:#FFB84D;
    --lantern-glow:#FFE9B8;
    --parchment:#FBF7EE;
    --parchment-panel:#FFFDF8;
    --ink:#2E2A3D;
    --ink-soft:#5B5570;
    --stone:#7C87A6;
    --stone-dark:#4E5875;
    --rose:#C1546B;
    --leaf:#4C8B6E;
    --font-display:'Cormorant Garamond', serif;
    --font-body:'Nunito', sans-serif;
    --font-mono:'JetBrains Mono', monospace;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;height:100%;font-family:var(--font-body);color:var(--ink);}
  body{background:var(--parchment);}
  button{font-family:inherit;cursor:pointer;}
  input{font-family:inherit;}
  .hidden{display:none !important;}

  /* ---------- Fairytale sky scene (customer / display screens) ---------- */
  .scene{
    position:relative;
    min-height:100vh;
    overflow:hidden;
    background:linear-gradient(180deg, var(--sky-deep) 0%, var(--sky-mid) 55%, var(--sky-horizon) 100%);
    display:flex;
    flex-direction:column;
    align-items:center;
  }
  .stars{position:absolute; inset:0; overflow:hidden; pointer-events:none;}
  .star{position:absolute; width:2px; height:2px; background:#fff; border-radius:50%; opacity:.7; animation:twinkle 3.5s ease-in-out infinite;}
  @keyframes twinkle{0%,100%{opacity:.15;} 50%{opacity:.9;}}

  .tower-wrap{position:absolute; bottom:0; left:0; right:0; pointer-events:none;}
  .lantern{
    position:absolute;
    bottom:-40px;
    width:26px; height:34px;
    border-radius:6px 6px 10px 10px;
    background:radial-gradient(circle at 50% 35%, var(--lantern-glow), var(--lantern-gold) 60%, var(--lantern-warm) 100%);
    box-shadow:0 0 14px 4px rgba(255,217,142,.55);
    animation:rise linear infinite;
    opacity:.9;
  }
  .lantern::before{
    content:"";
    position:absolute; top:-6px; left:50%; transform:translateX(-50%);
    width:14px; height:6px; border-radius:50%;
    background:var(--lantern-warm);
  }
  @keyframes rise{
    0%{transform:translateY(0) translateX(0); opacity:0;}
    8%{opacity:.95;}
    50%{transform:translateY(-52vh) translateX(12px);}
    92%{opacity:.85;}
    100%{transform:translateY(-100vh) translateX(-8px); opacity:0;}
  }

  .scene-header{
    position:relative; z-index:2;
    width:100%; max-width:640px;
    padding:22px 20px 6px;
    text-align:center;
  }
  .scene-title{
    font-family:var(--font-display);
    color:var(--lantern-glow);
    font-size:clamp(24px,5vw,34px);
    font-weight:700;
    letter-spacing:.03em;
    text-shadow:0 0 18px rgba(255,217,142,.5);
    margin:0;
  }
  .scene-sub{
    color:#E9E4FF;
    font-size:13px;
    opacity:.8;
    margin-top:4px;
  }

  .admin-link{
    position:absolute; top:14px; right:14px; z-index:5;
    background:rgba(255,255,255,.12);
    border:1px solid rgba(255,255,255,.35);
    color:#F2EEFF;
    font-size:11px;
    padding:6px 10px;
    border-radius:20px;
    backdrop-filter:blur(2px);
  }
  .admin-link:hover{background:rgba(255,255,255,.22);}

  .panel{
    position:relative; z-index:2;
    width:min(92vw, 420px);
    margin:14px auto 40px;
    background:rgba(251,247,238,.96);
    border-radius:18px;
    padding:26px 24px 28px;
    box-shadow:0 18px 40px rgba(10,6,40,.45);
    border:1px solid rgba(255,217,142,.4);
  }
  .panel-label{font-size:13px; color:var(--ink-soft); margin-bottom:8px; font-weight:700;}
  .ticket-input-row{display:flex; gap:8px;}
  .ticket-input{
    flex:1;
    font-family:var(--font-mono);
    font-size:22px;
    text-align:center;
    padding:12px 8px;
    border-radius:10px;
    border:2px solid #E4D9BF;
    background:#fff;
    letter-spacing:.06em;
  }
  .ticket-input:focus{outline:none; border-color:var(--lantern-warm);}
  .btn-primary{
    background:linear-gradient(180deg, var(--lantern-warm), #E68A2E);
    color:#3A2200;
    border:none;
    font-weight:800;
    font-size:15px;
    padding:0 20px;
    border-radius:10px;
    box-shadow:0 4px 0 #B8661A;
  }
  .btn-primary:active{transform:translateY(2px); box-shadow:0 2px 0 #B8661A;}

  .result-box{margin-top:20px; text-align:center;}
  .status-lantern{
    width:96px; height:120px;
    margin:0 auto 14px;
    border-radius:16px 16px 26px 26px;
    display:flex; align-items:center; justify-content:center;
    font-size:38px;
    position:relative;
  }
  .status-lantern::before{content:""; position:absolute; top:-10px; left:50%; transform:translateX(-50%); width:34px; height:10px; border-radius:50%;}
  .status-1 .status-lantern{background:#E7E1D2; box-shadow:none;}
  .status-1 .status-lantern::before{background:#C9C1AC;}
  .status-2 .status-lantern{background:radial-gradient(circle at 50% 35%, #FFE9B8, var(--lantern-warm)); box-shadow:0 0 24px 6px rgba(255,184,77,.55);}
  .status-2 .status-lantern::before{background:var(--lantern-warm);}
  .status-3 .status-lantern{background:radial-gradient(circle at 50% 35%, #FFF6DE, #FFCF5C); box-shadow:0 0 40px 14px rgba(255,207,92,.85); animation:pulseGlow 1.4s ease-in-out infinite;}
  .status-3 .status-lantern::before{background:#FFCF5C;}
  @keyframes pulseGlow{0%,100%{box-shadow:0 0 30px 10px rgba(255,207,92,.7);} 50%{box-shadow:0 0 50px 20px rgba(255,207,92,1);}}
  .status-4 .status-lantern{background:radial-gradient(circle at 50% 35%, #DCE3F0, var(--stone)); box-shadow:0 0 14px 3px rgba(124,135,166,.4);}
  .status-4 .status-lantern::before{background:var(--stone);}

  .status-title{font-family:var(--font-display); font-size:24px; font-weight:700; margin:4px 0 4px;}
  .status-1 .status-title{color:var(--rose);}
  .status-2 .status-title{color:#B8661A;}
  .status-3 .status-title{color:#C98A00;}
  .status-4 .status-title{color:var(--stone-dark);}
  .status-desc{font-size:13px; color:var(--ink-soft); line-height:1.6;}
  .queue-badge{
    display:inline-block; margin-top:10px;
    background:#fff; border:1px solid #E4D9BF; border-radius:20px;
    padding:6px 14px; font-size:13px; color:var(--ink-soft);
  }
  .queue-badge b{color:var(--rose); font-size:16px; font-family:var(--font-mono);}

  /* ---------- Display (storefront) screen ---------- */
  .display-grid{
    position:relative; z-index:2;
    width:min(94vw, 900px);
    display:grid;
    grid-template-columns:repeat(auto-fill, minmax(110px,1fr));
    gap:16px;
    padding:10px 16px 50px;
  }
  .display-card{
    background:radial-gradient(circle at 50% 30%, #FFF6DE, #FFCF5C 70%);
    border-radius:14px 14px 22px 22px;
    box-shadow:0 0 30px 8px rgba(255,207,92,.55);
    padding:18px 6px 22px;
    text-align:center;
    animation:pulseGlow 2.2s ease-in-out infinite;
  }
  .display-card .num{font-family:var(--font-mono); font-weight:700; font-size:clamp(28px,6vw,40px); color:#5C3B00;}
  .display-empty{
    position:relative; z-index:2; color:#E9E4FF; opacity:.75; font-size:14px; margin-top:30px; text-align:center;
  }

  /* ---------- Plain white screens ---------- */
  .plain-wrap{min-height:100vh; background:var(--parchment-panel); display:flex; flex-direction:column; align-items:center; padding:40px 16px;}
  .plain-card{
    width:min(92vw, 380px);
    background:#fff;
    border:1px solid #ECE6D8;
    border-radius:14px;
    padding:28px 24px;
    box-shadow:0 8px 24px rgba(46,42,61,.06);
  }
  .plain-title{font-family:var(--font-display); font-size:22px; font-weight:700; margin:0 0 18px; text-align:center;}
  .field-label{font-size:12px; color:var(--ink-soft); font-weight:700; margin-bottom:6px; display:block;}
  .pw-input{
    width:100%; padding:11px 12px; font-size:17px; border-radius:8px;
    border:1.5px solid #DCD5C4; letter-spacing:.05em;
  }
  .pw-input:focus{outline:none; border-color:var(--lantern-warm);}
  .error-text{color:var(--rose); font-size:12px; margin-top:8px; font-weight:700; min-height:16px;}
  .row-btns{display:flex; gap:10px; margin-top:18px;}
  .btn-block{flex:1; padding:11px 0; border-radius:8px; border:none; font-weight:800; font-size:14px;}
  .btn-gold{background:linear-gradient(180deg, var(--lantern-warm), #E68A2E); color:#3A2200; box-shadow:0 3px 0 #B8661A;}
  .btn-ghost{background:#F1EDE0; color:var(--ink-soft);}
  .btn-ghost:hover{background:#E7E1D0;}

  .choice-btn{
    width:100%; padding:22px 16px; margin-bottom:14px; border-radius:12px; border:1.5px solid #ECE6D8;
    background:#fff; text-align:left; display:flex; flex-direction:column; gap:4px;
  }
  .choice-btn:hover{border-color:var(--lantern-warm); background:#FFFBF0;}
  .choice-btn .t{font-family:var(--font-display); font-weight:700; font-size:19px;}
  .choice-btn .d{font-size:12px; color:var(--ink-soft);}

  .top-bar{width:100%; max-width:1180px; display:flex; justify-content:space-between; align-items:center; margin-bottom:16px;}
  .back-link{font-size:13px; color:var(--ink-soft); background:none; border:none; text-decoration:underline;}
  .staff-wrap{width:100%; max-width:1180px; padding-right:120px;}
  @media (min-width:860px){
    .staff-wrap{padding-right:200px;}
  }
  @media (max-width:640px){
    .staff-save-fixed{top:8px; right:8px; padding:6px;}
    .staff-save-fixed .btn-save-big{font-size:13px; padding:10px 14px;}
    .staff-save-fixed #staffStatus{max-width:120px; font-size:10.5px; text-align:right;}
    .staff-wrap{padding-right:92px;}
  }

  .sheet-bar{display:flex; flex-wrap:wrap; gap:10px; align-items:center; margin-bottom:16px; background:#fff; border:1px solid #ECE6D8; border-radius:10px; padding:12px 14px;}
  .sheet-select{padding:8px 10px; border-radius:6px; border:1.5px solid #DCD5C4; font-size:14px;}
  .small-btn{padding:8px 14px; border-radius:6px; border:1.5px solid #DCD5C4; background:#F8F5EC; font-size:13px; font-weight:700; color:var(--ink);}
  .small-btn:hover{background:#EFE9D8;}
  .small-btn.gold{background:var(--lantern-warm); border-color:#E0973A; color:#3A2200;}
  .staff-save-fixed{
    position:fixed; top:14px; right:14px; z-index:60;
    display:flex; flex-direction:column; align-items:flex-end; gap:4px;
    background:rgba(251,247,238,.92); backdrop-filter:blur(3px);
    padding:8px; border-radius:14px;
    box-shadow:0 6px 18px rgba(46,42,61,.18);
  }
  .btn-save-big{
    background:linear-gradient(180deg, #5FAE7E, #3D8B5D);
    color:#fff; border:none; font-weight:800; font-size:17px;
    padding:14px 26px; border-radius:10px;
    box-shadow:0 4px 0 #2E6B47;
  }
  .btn-save-big:active{transform:translateY(2px); box-shadow:0 2px 0 #2E6B47;}
  .active-tag{font-size:12px; background:#E6F3EA; color:var(--leaf); padding:5px 10px; border-radius:20px; font-weight:700;}

  table.order-table{width:100%; border-collapse:collapse; background:#fff; border:1px solid #ECE6D8; border-radius:10px; overflow:hidden; font-size:13px;}
  table.order-table th{background:#F6F2E7; padding:10px 6px; font-size:12px; color:var(--ink-soft); border-bottom:1.5px solid #ECE6D8; white-space:nowrap;}
  table.order-table td{padding:8px 6px; border-bottom:1px solid #F1EDE0; text-align:center; vertical-align:middle;}
  table.order-table tr:last-child td{border-bottom:none;}
  .num-cell{font-family:var(--font-mono); font-weight:700;}
  .qty-btn{
    width:38px; height:38px; border-radius:50%;
    border:1.5px solid #DCD5C4; background:#F8F5EC;
    font-family:var(--font-mono); font-weight:700; font-size:15px; color:var(--ink);
    transition:background .1s, border-color .1s;
  }
  .qty-btn.has-qty{background:linear-gradient(180deg, var(--lantern-warm), #E68A2E); color:#3A2200; border-color:#E0973A;}
  .qty-btn:active{transform:scale(.9);}
  .total-cell{font-family:var(--font-mono); font-weight:700; color:var(--rose);}
  .del-btn{background:none; border:none; color:#B0455B; font-size:16px;}
  .add-row-btn{margin-top:14px; padding:10px 18px; border-radius:8px; border:none; background:var(--leaf); color:#fff; font-weight:700; font-size:14px;}
  .table-scroll{overflow-x:auto;}
  .empty-note{color:var(--ink-soft); font-size:13px; padding:20px; text-align:center;}

  /* ---------- Custom modal (replaces blocked browser prompt/confirm dialogs) ---------- */
  .modal-overlay{
    position:fixed; inset:0; z-index:100;
    background:rgba(30,24,60,.55);
    display:flex; align-items:center; justify-content:center;
    padding:20px;
  }
  .modal-box{
    width:min(90vw, 360px);
    background:#fff;
    border-radius:14px;
    padding:24px 22px;
    box-shadow:0 20px 50px rgba(10,6,40,.35);
  }
  .modal-message{font-size:15px; line-height:1.6; color:var(--ink); margin-bottom:14px; white-space:pre-line;}

  @media (max-width:640px){
    table.order-table{font-size:11.5px;}
    .qty-btn{width:32px; height:32px; font-size:13px;}
  }
</style>
<!-- Firebase Realtime Database（データの保存・共有に使用） -->
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-database-compat.js"></script>
</head>
<body>

<!-- ============ CUSTOMER SCREEN (default) ============ -->
<div id="view-customer" class="scene">
  <div class="stars" id="starsC"></div>
  <div class="tower-wrap" id="lanternsC"></div>
  <button class="admin-link" onclick="App.go('password')">管理用</button>
  <div class="scene-header">
    <h1 class="scene-title">整理券 呼び出し</h1>
    <div class="scene-sub">整理券番号を入力して、状況をご確認ください</div>
  </div>
  <div class="panel">
    <div class="panel-label">整理券番号</div>
    <div class="ticket-input-row">
      <input id="custTicketInput" class="ticket-input" type="number" min="1" inputmode="numeric" placeholder="例：12">
      <button class="btn-primary" onclick="App.customerSearch()">確認する</button>
    </div>
    <div id="custResult"></div>
  </div>
</div>

<!-- ============ PASSWORD SCREEN ============ -->
<div id="view-password" class="plain-wrap hidden">
  <div class="top-bar" style="max-width:380px;">
    <button class="back-link" onclick="App.go('customer')">← 客側画面に戻る</button>
  </div>
  <div class="plain-card">
    <h2 class="plain-title">管理者ログイン</h2>
    <label class="field-label">パスワード</label>
    <input id="pwInput" class="pw-input" type="password" placeholder="パスワードを入力">
    <div id="pwError" class="error-text"></div>
    <div class="row-btns">
      <button class="btn-block btn-gold" onclick="App.checkPassword()">ログイン</button>
    </div>
  </div>
</div>

<!-- ============ SELECT SCREEN ============ -->
<div id="view-select" class="plain-wrap hidden">
  <div class="top-bar" style="max-width:380px;">
    <button class="back-link" onclick="App.go('customer')">← 客側画面に戻る</button>
  </div>
  <div class="plain-card">
    <h2 class="plain-title">画面を選択</h2>
    <button class="choice-btn" onclick="App.go('staff')">
      <span class="t">店側 注文管理画面</span>
      <span class="d">注文の入力・完成/受渡しの管理を行います</span>
    </button>
    <button class="choice-btn" onclick="App.go('display')">
      <span class="t">店頭表示用画面</span>
      <span class="d">受け取り可能な整理券番号を大きく表示します</span>
    </button>
  </div>
</div>

<!-- ============ STAFF SCREEN ============ -->
<div id="view-staff" class="plain-wrap hidden">
  <div class="staff-save-fixed">
    <button class="btn-save-big" onclick="App.manualUpdate()">変更を保存</button>
    <span id="staffStatus" style="font-size:12px; color:var(--ink-soft);"></span>
  </div>
  <div class="top-bar">
    <button class="back-link" onclick="App.go('select')">← 選択画面に戻る</button>
    <span style="font-family:var(--font-display); font-weight:700; font-size:20px;">店側 注文管理</span>
    <span style="width:150px;"></span>
  </div>
  <div class="staff-wrap">
    <div class="sheet-bar">
      <label class="field-label" style="margin:0;">シート：</label>
      <select id="sheetSelect" class="sheet-select" onchange="App.onSheetChange()"></select>
      <button class="small-btn" onclick="App.createSheet()">＋ シートを追加</button>
      <button class="small-btn gold" id="reflectBtn" onclick="App.selectReflectSheet()">このシートを反映する</button>
      <span id="activeTag" class="active-tag hidden">反映中のシート</span>
    </div>
    <div class="table-scroll">
      <table class="order-table">
        <thead>
          <tr>
            <th>整理券番号</th>
            <th>ミートソース<br>パスタ(150円)</th>
            <th>カルボナーラ<br>パスタ(150円)</th>
            <th>ゼリーポンチ<br>(250円)</th>
            <th>合計金額</th>
            <th>注文日時</th>
            <th>完成</th>
            <th>受け渡し済み</th>
            <th></th>
          </tr>
        </thead>
        <tbody id="orderTbody"></tbody>
      </table>
      <div id="emptyNote" class="empty-note hidden">まだ注文がありません。「新規注文を追加」から入力してください。</div>
    </div>
    <button class="add-row-btn" onclick="App.addOrderRow()">＋ 新規注文を追加</button>
  </div>
</div>

<!-- ============ DISPLAY SCREEN (storefront) ============ -->
<div id="view-display" class="scene hidden">
  <div class="stars" id="starsD"></div>
  <div class="tower-wrap" id="lanternsD"></div>
  <div class="scene-header">
    <button class="back-link" style="color:#E9E4FF; text-decoration:underline; margin-bottom:8px;" onclick="App.go('select')">← 選択画面に戻る</button>
    <h1 class="scene-title">お呼び出し番号</h1>
    <div class="scene-sub">下記の番号の方は受け取り口までお越しください</div>
  </div>
  <div id="displayGrid" class="display-grid"></div>
  <div id="displayEmpty" class="display-empty hidden">現在お呼び出し中の番号はありません</div>
</div>

<!-- ============ CUSTOM MODAL (prompt / confirm replacement) ============ -->
<div id="modalOverlay" class="modal-overlay hidden">
  <div class="modal-box">
    <div id="modalMessage" class="modal-message"></div>
    <input id="modalInput" class="pw-input hidden" type="text" style="margin-bottom:14px;">
    <div class="row-btns">
      <button class="btn-block btn-ghost" id="modalCancelBtn">キャンセル</button>
      <button class="btn-block btn-gold" id="modalOkBtn">OK</button>
    </div>
  </div>
</div>

<script>
// ============================================================
// Firebase 設定（★ここをご自身のFirebaseプロジェクトの値に書き換えてください）
// Firebaseコンソール → プロジェクトの設定 → 全般 → 「マイアプリ」の
// SDK構成にある値をそのままコピーしてください。
// ============================================================
const firebaseConfig = {
  apiKey: "AIzaSyBCZc-KQeQwOTFMCFqv3qewZvRGAqxYPAo",
  authDomain: "toin3f2026.firebaseapp.com",
  databaseURL: "https://toin3f2026-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "toin3f2026",
  storageBucket: "toin3f2026.firebasestorage.app",
  messagingSenderId: "102451971100",
  appId: "1:102451971100:web:fe113142e6e3d8db710bd9",
  measurementId: "G-Q8QXBE41FW"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

const MENU = [
  {key:'meat',  name:'ミートソースパスタ', price:150},
  {key:'carb',  name:'カルボナーラパスタ', price:150},
  {key:'jelly', name:'ゼリーポンチ',     price:250},
];
const ADMIN_PASSWORD = '3f2026';

const App = {
  view:'customer',
  sheets:[],
  activeSheetId:null,
  selectedSheetId:null,
  lastSearchedTicket:null,
  pollTimer:null,

  async init(){
    this.buildAtmosphere('starsC','lanternsC');
    this.buildAtmosphere('starsD','lanternsD');
    await this.loadSheetsFromStorage();
    this.go('customer');
    setInterval(()=>this.backgroundRefresh(), 4000);
  },

  buildAtmosphere(starsId, lanternsId){
    const starsEl = document.getElementById(starsId);
    for(let i=0;i<50;i++){
      const s = document.createElement('div');
      s.className='star';
      s.style.left = Math.random()*100+'%';
      s.style.top = Math.random()*70+'%';
      s.style.animationDelay = (Math.random()*3)+'s';
      starsEl.appendChild(s);
    }
    const lanternEl = document.getElementById(lanternsId);
    for(let i=0;i<10;i++){
      const l = document.createElement('div');
      l.className='lantern';
      l.style.left = (5+Math.random()*90)+'%';
      l.style.animationDuration = (14+Math.random()*10)+'s';
      l.style.animationDelay = (Math.random()*14)+'s';
      lanternEl.appendChild(l);
    }
  },

  async go(view){
    if(this.view==='staff' && view!=='staff' && this.staffDirty){
      const ok = await this.showConfirm('保存されていない変更があります。保存せずに移動しますか？');
      if(!ok) return;
    }
    this.view = view;
    ['customer','password','select','staff','display'].forEach(v=>{
      document.getElementById('view-'+v).classList.toggle('hidden', v!==view);
    });
    if(view==='password'){
      document.getElementById('pwInput').value='';
      document.getElementById('pwError').textContent='';
      setTimeout(()=>document.getElementById('pwInput').focus(),50);
    }
    if(view==='staff'){ this.refreshStaffScreen(); }
    if(view==='display'){ this.refreshDisplayScreen(); }
  },

  checkPassword(){
    const val = document.getElementById('pwInput').value;
    if(val === ADMIN_PASSWORD){
      this.go('select');
    } else {
      document.getElementById('pwError').textContent = 'パスワードが違います';
    }
  },

  // ---------- storage helpers (Firebase Realtime Database) ----------
  // 保存の仕組みだけをFirebaseに差し替えています。呼び出し側のコードは変更していません。
  async storageGet(key){
    try{
      const snap = await db.ref(key).once('value');
      const val = snap.val();
      return (val===null || val===undefined) ? null : val;
    }catch(e){ console.error('firebase get failed', e); return null; }
  },
  async storageSet(key, value){
    try{ await db.ref(key).set(value); }catch(e){ console.error('firebase set failed', e); }
  },

  async loadSheetsFromStorage(){
    let sheets = await this.storageGet('sheets');
    let activeId = await this.storageGet('activeSheetId');
    if(!sheets || sheets.length===0){
      const id = 'sheet_'+Date.now();
      sheets = [{id, name:'シート1'}];
      activeId = id;
      await this.storageSet('sheets', sheets);
      await this.storageSet('activeSheetId', activeId);
      await this.storageSet('orders_'+id, []);
    }
    this.sheets = sheets;
    this.activeSheetId = activeId;
    this.selectedSheetId = activeId;
  },

  async loadOrders(sheetId){
    const o = await this.storageGet('orders_'+sheetId);
    return o || [];
  },
  async saveOrders(sheetId, orders){
    await this.storageSet('orders_'+sheetId, orders);
  },

  async backgroundRefresh(){
    if(this.view==='display'){ this.refreshDisplayScreen(); }
    if(this.view==='staff'){ this.syncStaffFromStorage(); }
    if(this.view==='customer' && this.lastSearchedTicket!==null){
      this.customerSearch(true);
    }
  },

  // Periodically pulls the latest saved data for the staff screen so that a save made
  // on another device shows up here without a manual reload. If there are unsaved local
  // changes (staffDirty), the order table is left alone so in-progress edits are never
  // silently overwritten — the sheet list/active-sheet indicator still stays current.
  async syncStaffFromStorage(){
    const sheets = await this.storageGet('sheets');
    const activeId = await this.storageGet('activeSheetId');
    if(sheets) this.sheets = sheets;
    if(activeId) this.activeSheetId = activeId;
    this.renderSheetBar();

    if(this.staffDirty) return;

    const orders = await this.loadOrders(this.selectedSheetId);
    orders.sort((a,b)=>a.ticket-b.ticket);
    const snap = JSON.stringify(orders);
    if(snap !== this.currentOrdersSnapshot){
      this.currentOrders = orders;
      this.currentOrdersSnapshot = snap;
      this.renderOrderTable();
    }
  },

  // ---------- CUSTOMER ----------
  async customerSearch(silent){
    const input = document.getElementById('custTicketInput');
    const raw = silent ? this.lastSearchedTicket : input.value;
    const resultEl = document.getElementById('custResult');
    if(raw === '' || raw === null || raw === undefined){
      resultEl.innerHTML='';
      return;
    }
    const ticket = parseInt(raw,10);
    if(isNaN(ticket)){
      resultEl.innerHTML = `<div class="result-box status-1">
        <div class="status-lantern">？</div>
        <div class="status-title">不明な番号です</div>
        <div class="status-desc">整理券番号を正しく入力してください。</div>
      </div>`;
      return;
    }
    this.lastSearchedTicket = ticket;
    if(!silent) input.value = ticket;

    const sheetId = this.activeSheetId;
    if(!sheetId){ resultEl.innerHTML=''; return; }
    const orders = await this.loadOrders(sheetId);
    const order = orders.find(o=>o.ticket===ticket);

    if(!order){
      resultEl.innerHTML = `<div class="result-box status-1">
        <div class="status-lantern">？</div>
        <div class="status-title">不明な番号です</div>
        <div class="status-desc">その整理券番号はまだ発行されていません。<br>番号をご確認のうえ、再度お試しください。</div>
      </div>`;
      return;
    }

    const ahead = orders.filter(o=>o.ticket < ticket && !o.completed).length;

    let html='';
    if(!order.completed){
      html = `<div class="result-box status-2">
        <div class="status-lantern">🍳</div>
        <div class="status-title">お作り中です</div>
        <div class="status-desc">只今調理を行っております。今しばらくお待ちください。</div>
        <div class="queue-badge">あなたの前に <b>${ahead}</b> 人 待っています</div>
      </div>`;
    } else if(order.completed && !order.delivered){
      html = `<div class="result-box status-3">
        <div class="status-lantern">✨</div>
        <div class="status-title">受け取り可能です</div>
        <div class="status-desc">商品が出来上がりました。受け取り口までお越しください。</div>
      </div>`;
    } else {
      html = `<div class="result-box status-4">
        <div class="status-lantern">✔</div>
        <div class="status-title">受け渡し済みです</div>
        <div class="status-desc">ご注文の商品は既にお渡しが完了しています。<br>ありがとうございました。</div>
      </div>`;
    }
    resultEl.innerHTML = html;
  },

  // ---------- STAFF ----------
  // currentOrders is an in-memory working copy for the selected sheet. Clicks/checks/
  // add/delete only mutate this copy and update the DOM directly — nothing is written
  // to storage until the staff member presses the 更新 button (manualUpdate).
  currentOrders:[],
  currentOrdersSnapshot:'',
  staffDirty:false,

  async refreshStaffScreen(){
    this.renderSheetBar();
    const orders = await this.loadOrders(this.selectedSheetId);
    orders.sort((a,b)=>a.ticket-b.ticket);
    this.currentOrders = orders;
    this.currentOrdersSnapshot = JSON.stringify(orders);
    this.staffDirty = false;
    this.setStaffStatus('');
    this.renderOrderTable();
  },

  renderSheetBar(){
    const sel = document.getElementById('sheetSelect');
    sel.innerHTML = this.sheets.map(s=>`<option value="${s.id}" ${s.id===this.selectedSheetId?'selected':''}>${this.escapeHtml(s.name)}</option>`).join('');
    const isActive = this.selectedSheetId===this.activeSheetId;
    document.getElementById('activeTag').classList.toggle('hidden', !isActive);
    document.getElementById('reflectBtn').textContent = isActive ? '反映中です' : 'このシートを反映する';
    document.getElementById('reflectBtn').disabled = isActive;
  },

  setStaffStatus(text, isWarning){
    const el = document.getElementById('staffStatus');
    el.textContent = text;
    el.style.color = isWarning ? 'var(--rose)' : 'var(--ink-soft)';
  },

  markDirty(){
    this.staffDirty = true;
    this.setStaffStatus('未保存の変更があります（「変更を保存」で保存）', true);
  },

  rowHtml(o){
    const dt = new Date(o.orderTime);
    const dtStr = isNaN(dt.getTime()) ? '' : dt.toLocaleString('ja-JP',{month:'numeric',day:'numeric',hour:'2-digit',minute:'2-digit'});
    const qtyBtn = (key)=>`<button type="button" class="qty-btn${o[key]>0?' has-qty':''}" data-ticket="${o.ticket}" data-key="${key}" onclick="App.cycleQty(${o.ticket},'${key}',this)">${o[key]}</button>`;
    return `<tr data-ticket="${o.ticket}">
        <td class="num-cell">${o.ticket}</td>
        <td>${qtyBtn('meat')}</td>
        <td>${qtyBtn('carb')}</td>
        <td>${qtyBtn('jelly')}</td>
        <td class="total-cell">¥${o.total}</td>
        <td>${dtStr}</td>
        <td><input type="checkbox" ${o.completed?'checked':''} onchange="App.updateFlag(${o.ticket},'completed',this.checked)"></td>
        <td><input type="checkbox" ${o.delivered?'checked':''} onchange="App.updateFlag(${o.ticket},'delivered',this.checked)"></td>
        <td><button class="del-btn" onclick="App.deleteOrder(${o.ticket})" title="削除">✕</button></td>
      </tr>`;
  },

  renderOrderTable(){
    const tbody = document.getElementById('orderTbody');
    const emptyNote = document.getElementById('emptyNote');
    if(this.currentOrders.length===0){
      tbody.innerHTML='';
      emptyNote.classList.remove('hidden');
      return;
    }
    emptyNote.classList.add('hidden');
    tbody.innerHTML = this.currentOrders.map(o=>this.rowHtml(o)).join('');
  },

  computeTotal(o){
    return MENU.reduce((sum,m)=>sum + (o[m.key]||0)*m.price, 0);
  },

  // Saves currentOrders to storage. Only runs when the 更新 button is pressed.
  async manualUpdate(){
    await this.saveOrders(this.selectedSheetId, this.currentOrders);
    this.currentOrdersSnapshot = JSON.stringify(this.currentOrders);
    this.staffDirty = false;
    this.setStaffStatus('保存しました ✓', false);
  },

  addOrderRow(){
    const nextTicket = this.currentOrders.length ? Math.max(...this.currentOrders.map(o=>o.ticket))+1 : 1;
    const o = {ticket:nextTicket, meat:0, carb:0, jelly:0, total:0, orderTime:new Date().toISOString(), completed:false, delivered:false};
    this.currentOrders.push(o);
    document.getElementById('emptyNote').classList.add('hidden');
    document.getElementById('orderTbody').insertAdjacentHTML('beforeend', this.rowHtml(o));
    this.markDirty();
  },

  // Click cycles a quantity 0 → 1 → ... → 9 → 0. Updates the DOM instantly; the change
  // is only written to storage once 更新 is pressed.
  cycleQty(ticket, key, btnEl){
    const o = this.currentOrders.find(x=>x.ticket===ticket);
    if(!o) return;
    o[key] = (o[key]+1) % 10;
    o.total = this.computeTotal(o);
    btnEl.textContent = o[key];
    btnEl.classList.toggle('has-qty', o[key]>0);
    const row = btnEl.closest('tr');
    if(row) row.querySelector('.total-cell').textContent = '¥'+o.total;
    this.markDirty();
  },

  updateFlag(ticket, key, value){
    const o = this.currentOrders.find(x=>x.ticket===ticket);
    if(!o) return;
    o[key] = value;
    this.markDirty();
  },

  async deleteOrder(ticket){
    const ok = await this.showConfirm(`整理券番号 ${ticket} の注文を削除しますか？\n（「変更を保存」を押すまで保存されません）`);
    if(!ok) return;
    this.currentOrders = this.currentOrders.filter(o=>o.ticket!==ticket);
    const row = document.querySelector(`#orderTbody tr[data-ticket="${ticket}"]`);
    if(row) row.remove();
    if(this.currentOrders.length===0) document.getElementById('emptyNote').classList.remove('hidden');
    this.markDirty();
  },

  // ＋ シートを追加：新しいシートを作り、そのまま編集対象・客側への反映対象にする。
  async createSheet(){
    if(this.staffDirty){
      const ok = await this.showConfirm('保存されていない変更があります。破棄して新しいシートを作成しますか？');
      if(!ok) return;
    }
    const name = await this.showPrompt('新しいシートの名前を入力してください', 'シート'+(this.sheets.length+1));
    if(!name) return;
    const id = 'sheet_'+Date.now();
    this.sheets.push({id, name});
    await this.storageSet('sheets', this.sheets);
    await this.storageSet('orders_'+id, []);
    this.selectedSheetId = id;
    this.activeSheetId = id;
    await this.storageSet('activeSheetId', id);
    await this.refreshStaffScreen();
  },

  // シートのプルダウンを変更した時点で、そのシートの内容を表側の表に読み込んで表示する。
  async onSheetChange(){
    const target = document.getElementById('sheetSelect').value;
    if(target === this.selectedSheetId) return;
    if(this.staffDirty){
      const ok = await this.showConfirm('保存されていない変更があります。破棄してこのシートを表示しますか？');
      if(!ok){
        document.getElementById('sheetSelect').value = this.selectedSheetId;
        return;
      }
    }
    this.selectedSheetId = target;
    await this.refreshStaffScreen();
  },

  // 反映するシートを選択：現在表示中のシートを、客側画面・店頭表示画面に反映するシートとして設定する。
  async selectReflectSheet(){
    if(this.selectedSheetId === this.activeSheetId) return;
    if(this.staffDirty){
      const ok = await this.showConfirm('未保存の変更があります。保存せずに反映しますか？\n（客側には直前に保存した内容が表示されます）');
      if(!ok) return;
    }
    this.activeSheetId = this.selectedSheetId;
    await this.storageSet('activeSheetId', this.activeSheetId);
    this.renderSheetBar();
  },

  // ---------- DISPLAY ----------
  async refreshDisplayScreen(){
    const sheets = await this.storageGet('sheets');
    const activeId = await this.storageGet('activeSheetId');
    if(sheets) this.sheets = sheets;
    if(activeId) this.activeSheetId = activeId;
    const orders = await this.loadOrders(this.activeSheetId);
    const ready = orders.filter(o=>o.completed && !o.delivered).sort((a,b)=>a.ticket-b.ticket);
    const grid = document.getElementById('displayGrid');
    const empty = document.getElementById('displayEmpty');
    if(ready.length===0){
      grid.innerHTML='';
      empty.classList.remove('hidden');
    } else {
      empty.classList.add('hidden');
      grid.innerHTML = ready.map(o=>`<div class="display-card"><div class="num">${o.ticket}</div></div>`).join('');
    }
  },

  escapeHtml(s){
    return String(s).replace(/[&<>"']/g, c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
  },

  // ---------- Custom modal helpers ----------
  // Replace window.prompt()/confirm(): these are blocked (silently return null) inside
  // the sandboxed iframe this page runs in, which is why buttons using them looked
  // like they did nothing. showPrompt/showConfirm render an on-page modal instead.
  modalResolve:null,
  showPrompt(message, defaultValue){
    return new Promise(resolve=>{
      this.modalResolve = resolve;
      document.getElementById('modalMessage').textContent = message;
      const input = document.getElementById('modalInput');
      input.classList.remove('hidden');
      input.value = defaultValue || '';
      document.getElementById('modalOverlay').classList.remove('hidden');
      setTimeout(()=>{ input.focus(); input.select(); },50);
    });
  },
  showConfirm(message){
    return new Promise(resolve=>{
      this.modalResolve = resolve;
      document.getElementById('modalMessage').textContent = message;
      document.getElementById('modalInput').classList.add('hidden');
      document.getElementById('modalOverlay').classList.remove('hidden');
    });
  },
  closeModal(result){
    document.getElementById('modalOverlay').classList.add('hidden');
    const resolve = this.modalResolve;
    this.modalResolve = null;
    if(resolve) resolve(result);
  }
};

document.getElementById('modalOkBtn').addEventListener('click', ()=>{
  const input = document.getElementById('modalInput');
  const isPromptMode = !input.classList.contains('hidden');
  App.closeModal(isPromptMode ? (input.value.trim() || null) : true);
});
document.getElementById('modalCancelBtn').addEventListener('click', ()=>{
  const input = document.getElementById('modalInput');
  const isPromptMode = !input.classList.contains('hidden');
  App.closeModal(isPromptMode ? null : false);
});
document.getElementById('modalInput').addEventListener('keydown', e=>{
  if(e.key==='Enter') document.getElementById('modalOkBtn').click();
});

document.getElementById('custTicketInput').addEventListener('keydown', e=>{
  if(e.key==='Enter') App.customerSearch();
});
document.getElementById('pwInput').addEventListener('keydown', e=>{
  if(e.key==='Enter') App.checkPassword();
});

App.init();
</script>
</body>
</html>
