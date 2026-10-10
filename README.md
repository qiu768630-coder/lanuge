<!DOCTYPE html>
<html lang="zh-CN" data-theme="light">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="theme-color" content="#2f6bff">
<title>我的时间表</title>
<style>
:root{
  --bg:#eef2f8;--panel:#fff;--line:#dfe6f0;--text:#16202c;--muted:#7d8b9c;
  --accent:#2f6bff;--accent-ink:#fff;--soft:#e9f0ff;
  --gutter-w:52px;--hour-h:52px;
  --safe-t:env(safe-area-inset-top,0px);--safe-b:env(safe-area-inset-bottom,0px);
}
html[data-theme="dark"]{
  --bg:#0d1117;--panel:#151c25;--line:#232d3a;--text:#e6edf6;--muted:#8593a5;
  --accent:#5b8cff;--soft:#1a2740;
}
*{box-sizing:border-box}
html,body{height:100%}
body{margin:0;background:var(--bg);color:var(--text);
  font:15px/1.45 system-ui,-apple-system,"PingFang SC","Microsoft YaHei",sans-serif;
  -webkit-tap-highlight-color:transparent;overflow:hidden;
  display:flex;flex-direction:column;height:100vh;height:100dvh}
button{font:inherit;color:inherit;cursor:pointer}

/* 顶栏 */
.topbar{display:flex;align-items:center;gap:5px;flex-wrap:wrap;
  padding:calc(6px + var(--safe-t)) 8px 6px;background:var(--panel);
  border-bottom:1px solid var(--line);position:relative;z-index:20}
.nav{width:30px;height:30px;border-radius:50%;border:1px solid var(--line);
  background:var(--panel);font-size:17px;display:grid;place-items:center}
.wtitle{display:flex;flex-direction:column;align-items:center;min-width:96px}
.wtitle b{font-size:14px}.wtitle span{font-size:10px;color:var(--muted)}
.chip{border:1px solid var(--line);background:var(--panel);border-radius:9px;
  padding:5px 9px;font-size:12.5px}
.plus{border:none;background:var(--accent);color:var(--accent-ink);
  border-radius:9px;padding:5px 12px;font-size:17px;font-weight:600}
.grow{flex:1}
.chip.on{background:var(--accent);color:var(--accent-ink);border-color:var(--accent)}

/* 周视图 */
.viewport{flex:1;overflow:auto;-webkit-overflow-scrolling:touch;overscroll-behavior:contain}
.board{display:grid;grid-template-columns:var(--gutter-w) repeat(7,minmax(84px,1fr));
  width:100%;min-width:calc(var(--gutter-w) + 7 * 84px)}
.cell{position:relative}
.corner{position:sticky;top:0;left:0;z-index:6;background:var(--panel);
  border-right:1px solid var(--line);border-bottom:1px solid var(--line)}
.dayhead{position:sticky;top:0;z-index:5;background:var(--panel);
  border-bottom:1px solid var(--line);border-right:1px solid var(--line);
  display:flex;flex-direction:column;align-items:center;justify-content:center;padding:5px 2px}
.dayhead .dn{font-size:11px;color:var(--muted)}
.dayhead .dd{font-size:12.5px;font-weight:600}
.dayhead.today{background:var(--accent)}
.dayhead.today .dn,.dayhead.today .dd{color:var(--accent-ink)}
.gutter{position:sticky;left:0;z-index:4;background:var(--panel);border-right:1px solid var(--line)}
.gutter-inner{position:relative}
.hourlabel{position:absolute;right:6px;transform:translateY(-50%);font-size:10px;color:var(--muted)}
.daycol{position:relative;z-index:1;border-right:1px solid var(--line)}
.hline{position:absolute;left:0;right:0;height:1px;background:var(--line);opacity:.65}
.event{position:absolute;border-radius:8px;overflow:hidden;padding:3px 5px;
  background:var(--c);color:#fff;box-shadow:0 1px 3px rgba(0,0,0,.2);
  display:flex;flex-direction:column;cursor:pointer;border-left:3px solid rgba(0,0,0,.18)}
.ev-t{font-size:11px;font-weight:600;line-height:1.2;word-break:break-all}
.ev-h{font-size:9px;opacity:.85}
.nowline{position:absolute;left:0;right:0;height:2px;background:#ff3b30;z-index:3;pointer-events:none}
.nowline::before{content:'';position:absolute;left:-4px;top:-4px;width:9px;height:9px;
  border-radius:50%;background:#ff3b30}

/* 日视图 */
.daywrap{flex:1;overflow:auto;-webkit-overflow-scrolling:touch;padding-bottom:calc(20px + var(--safe-b))}
.dtabs{display:flex;gap:4px;padding:8px 10px;position:sticky;top:0;z-index:5;
  background:var(--bg);border-bottom:1px solid var(--line)}
.dtab{flex:1;border:1px solid var(--line);background:var(--panel);border-radius:9px;
  padding:6px 0;font-size:12px;color:var(--text)}
.dtab.on{background:var(--accent);color:var(--accent-ink);border-color:var(--accent);font-weight:600}
.dsum{margin:10px 12px 0;padding:9px 12px;border-radius:11px;background:var(--panel);
  border:1px solid var(--line);font-size:12px;color:var(--muted);line-height:1.7}
.dsum b{color:var(--text)}
.dlist{padding:10px 12px 0}
.ditem{display:flex;gap:10px;align-items:stretch;padding:9px 0;
  border-bottom:1px dashed var(--line);cursor:pointer}
.ditem:active{background:var(--soft)}
.dtime{width:78px;flex:none;font-size:11px;color:var(--muted);line-height:1.5;padding-top:2px}
.dbar{width:4px;border-radius:4px;background:var(--c);flex:none}
.dbody{flex:1;min-width:0}
.dtitle{font-size:14.5px;font-weight:600;line-height:1.35}
.dnote{font-size:11.5px;color:var(--muted);margin-top:2px;line-height:1.5}
.dtag{display:inline-block;font-size:10px;padding:1px 6px;border-radius:6px;
  background:var(--soft);color:var(--muted);margin-top:4px}

/* 弹层 */
.overlay{position:fixed;inset:0;z-index:100;background:rgba(10,14,20,.45);
  display:flex;align-items:flex-end;justify-content:center;opacity:0;
  transition:opacity .18s;padding-top:var(--safe-t)}
.overlay.show{opacity:1}
@media(min-width:560px){.overlay{align-items:center}}
.sheet{width:100%;max-width:480px;max-height:88vh;overflow:auto;background:var(--panel);
  border-radius:18px 18px 0 0;padding:18px 16px calc(18px + var(--safe-b));
  transform:translateY(14px);transition:transform .18s}
@media(min-width:560px){.sheet{border-radius:18px;padding-bottom:18px}}
.overlay.show .sheet{transform:translateY(0)}
.sheet h3{margin:0 0 14px;font-size:17px}
.f{margin-bottom:12px}.f.two{display:flex;gap:10px}.f.two>*{flex:1;min-width:0}
.f label{display:block;font-size:12px;color:var(--muted);margin-bottom:5px}
.f input,.f select{width:100%;padding:10px 11px;border-radius:10px;font:inherit;
  border:1px solid var(--line);background:var(--bg);color:var(--text)}
.sws{display:flex;gap:8px;flex-wrap:wrap}
.sw{width:28px;height:28px;border-radius:50%;border:2px solid transparent;background:var(--c);padding:0}
.sw.on{border-color:var(--text);transform:scale(1.08)}
.acts{display:flex;gap:9px;margin-top:16px}
.btn{flex:1;border-radius:11px;padding:11px 12px;font-size:14px;
  border:1px solid var(--line);background:var(--panel);color:var(--text)}
.btn.primary{background:var(--accent);color:var(--accent-ink);border-color:var(--accent);font-weight:600}
.btn.danger{color:#e5484d;border-color:#e5484d55}
.btn.block{width:100%;margin-top:10px;flex:none}
.tip{font-size:12px;color:var(--muted);margin:10px 0 0;line-height:1.7}
.switch{display:flex;align-items:center;gap:9px;font-size:14px;margin:14px 0}
.legend{display:grid;grid-template-columns:1fr 1fr;gap:6px 10px;font-size:12px;color:var(--muted)}
.legend i{display:inline-block;width:10px;height:10px;border-radius:3px;margin-right:6px}
</style>
</head>
<body>

<header class="topbar">
  <button class="nav" id="prev">‹</button>
  <div class="wtitle"><b id="t1">第 1 周</b><span id="t2">—</span></div>
  <button class="nav" id="next">›</button>
  <button class="chip" id="btnToday">今天</button>
  <div class="grow"></div>
  <button class="chip" id="btnView">周视图</button>
  <button class="plus" id="btnAdd">＋</button>
  <button class="chip" id="btnMenu">⋯</button>
</header>

<main class="viewport" id="viewport"><div class="board" id="board"></div></main>
<div class="daywrap" id="daywrap" hidden></div>

<div class="overlay" id="overlay" hidden><div class="sheet" id="sheet"></div></div>
<input type="file" id="fileInput" accept="application/json,.json" hidden>

<script>
/* ========== 模块配色 ========== */
const COLOR = {
  '核心学习':'#2f6bff','运动健康':'#12a150','健康养生':'#0891b2','阅读思考':'#8b5cf6',
  '输出表达':'#e8850c','复盘总结':'#d1345b','系统调节':'#6366f1','探索创新':'#f59e0b',
  '休息缓冲':'#64748b','生活':'#94a3b8'
};
const MODULES = Object.keys(COLOR);
const HOUR_H = 52, KEY = 'myschedule.v2';
const DAYS = ['周一','周二','周三','周四','周五','周六','周日'];

/* ========== 你的作息数据 [星期, 开始, 结束, 标题, 模块, 备注] ========== */
const RAW = [
[1,'07:00','07:20','拍八虚','健康养生'],
[1,'07:20','08:30','早餐 · 晨间准备','生活'],
[1,'08:30','11:00','学习课程一','核心学习','含英语 20–30 分钟'],
[1,'11:00','11:30','散心 · 机动','休息缓冲'],
[1,'11:30','13:00','午饭 · 新闻体态 · 消食散步','生活'],
[1,'13:00','13:30','午睡','健康养生'],
[1,'13:30','14:00','看书本一（硬书）','阅读思考'],
[1,'14:00','16:00','学习课程二','核心学习','含英语 20–30 分钟'],
[1,'16:00','17:00','休息散心 / 空余','休息缓冲'],
[1,'17:00','18:30','做运动','运动健康','力量45 + 有氧25 + 拉伸15'],
[1,'18:30','19:30','晚饭 · 看新闻','生活'],
[1,'19:30','21:00','系统调节','系统调节','清收件箱 / 更新清单 / 处理杂事'],
[1,'21:00','21:30','看书本二（软书）','阅读思考'],
[1,'21:30','22:00','写互联网思考','输出表达','100–300 字'],
[1,'22:00','22:30','日复盘 · 读书思考记录','复盘总结','做成什么 / 卡在哪 / 明天第一件事'],
[1,'22:30','23:00','洗漱放松','生活'],

[2,'07:00','07:20','拍八虚','健康养生'],
[2,'07:20','08:30','早餐 · 晨间准备','生活'],
[2,'08:30','10:30','学习课程一','核心学习','含英语 20–30 分钟'],
[2,'10:30','11:30','休息散心 / 空余','休息缓冲'],
[2,'11:30','13:00','午饭 · 新闻体态 · 消食散步','生活'],
[2,'13:00','13:30','午睡','健康养生'],
[2,'13:30','14:00','看书本一（硬书）','阅读思考'],
[2,'14:00','16:30','学习课程二','核心学习','含英语 20–30 分钟'],
[2,'16:30','18:00','做运动','运动健康'],
[2,'18:00','18:30','散心 · 机动','休息缓冲'],
[2,'18:30','19:30','晚饭 · 看新闻','生活','原表未写，按其他天补齐'],
[2,'19:30','21:00','系统调节','系统调节'],
[2,'21:00','21:30','看书本二（软书）','阅读思考'],
[2,'21:30','22:00','写互联网思考','输出表达'],
[2,'22:00','22:30','日复盘 · 读书思考记录','复盘总结'],
[2,'22:30','23:00','洗漱放松','生活'],

[3,'07:00','07:20','拍八虚','健康养生'],
[3,'07:20','08:30','早餐 · 晨间准备','生活'],
[3,'08:30','11:30','学习课程一（3h）','核心学习','中间休 5–10 分钟 · 含英语'],
[3,'11:30','13:00','午饭 · 新闻体态 · 消食散步','生活'],
[3,'13:00','13:30','午睡','健康养生'],
[3,'13:30','14:00','看书本一（硬书）','阅读思考'],
[3,'14:00','15:30','学习课程二','核心学习','含英语 20–30 分钟'],
[3,'15:30','17:00','★ 新尝试（弹性）','探索创新','事前 5 分钟写下我想验证什么'],
[3,'17:00','18:30','做运动','运动健康'],
[3,'18:30','19:30','晚饭 · 看新闻','生活'],
[3,'19:30','21:00','系统调节','系统调节'],
[3,'21:00','21:30','看书本二（软书）','阅读思考'],
[3,'21:30','22:00','写互联网思考','输出表达'],
[3,'22:00','22:30','尝试评估 + 日复盘','复盘总结','继续 / 加大 / 砍掉'],
[3,'22:30','23:00','洗漱放松','生活'],

[4,'07:00','07:20','拍八虚','健康养生'],
[4,'07:20','08:30','早餐 · 晨间准备','生活'],
[4,'08:30','10:00','做运动','运动健康'],
[4,'10:00','11:30','休息散心 + 弹性格子','休息缓冲'],
[4,'11:30','13:00','午饭 · 新闻体态 · 消食散步','生活'],
[4,'13:00','13:30','午睡','健康养生'],
[4,'13:30','14:00','看书本一（硬书）','阅读思考'],
[4,'14:00','16:30','学习课程一','核心学习','含英语 20–30 分钟'],
[4,'16:30','18:30','学习课程二','核心学习','中间休 10 分钟'],
[4,'18:30','19:30','晚饭 · 看新闻','生活','原表未写，按其他天补齐'],
[4,'19:30','21:00','系统调节','系统调节'],
[4,'21:00','21:30','看书本二（软书）','阅读思考'],
[4,'21:30','22:00','写互联网思考','输出表达'],
[4,'22:00','22:30','日复盘 · 读书思考记录','复盘总结'],
[4,'22:30','23:00','洗漱放松','生活'],

[5,'07:00','07:20','拍八虚','健康养生'],
[5,'07:20','08:30','早餐 · 晨间准备','生活'],
[5,'08:30','11:00','学习课程二','核心学习','含英语 20–30 分钟'],
[5,'11:00','11:30','散心 · 机动','休息缓冲'],
[5,'11:30','13:00','午饭 · 新闻体态 · 消食散步','生活'],
[5,'13:00','13:30','午睡','健康养生'],
[5,'13:30','14:00','看书本一（硬书）','阅读思考'],
[5,'14:00','16:00','学习课程一','核心学习','含英语 20–30 分钟'],
[5,'16:00','17:00','休息散心 / 空余','休息缓冲'],
[5,'17:00','18:30','做运动','运动健康'],
[5,'18:30','19:30','晚饭 · 看新闻','生活'],
[5,'19:30','21:00','系统调节','系统调节'],
[5,'21:00','21:30','看书本二（软书）','阅读思考'],
[5,'21:30','22:00','写互联网思考','输出表达'],
[5,'22:00','22:30','日复盘 · 读书思考记录','复盘总结'],
[5,'22:30','23:00','洗漱放松','生活'],

[6,'07:00','07:20','拍八虚','健康养生'],
[6,'07:20','08:30','早餐 · 晨间准备','生活'],
[6,'08:30','11:30','学习课程二（3h）','核心学习','中间休 5–10 分钟 · 含英语'],
[6,'11:30','13:00','午饭 · 新闻体态 · 消食散步','生活'],
[6,'13:00','13:30','午睡','健康养生'],
[6,'13:30','14:00','看书本一（硬书）','阅读思考'],
[6,'14:00','15:30','学习课程一','核心学习','含英语 20–30 分钟'],
[6,'15:30','17:00','做运动','运动健康'],
[6,'17:00','18:30','★ 新尝试（弹性）','探索创新','事前 5 分钟写下我想验证什么'],
[6,'18:30','19:30','晚饭 · 看新闻','生活','原表未写，按其他天补齐'],
[6,'19:30','21:00','系统调节（周复盘）','系统调节','数据回顾 + 下周计划 + 新尝试选题'],
[6,'21:00','21:30','看书本二（软书）','阅读思考'],
[6,'21:30','22:00','写互联网思考','输出表达'],
[6,'22:00','22:30','日复盘 · 读书思考记录','复盘总结'],
[6,'22:30','23:00','洗漱放松','生活'],

[7,'07:30','07:50','拍八虚','健康养生','自然醒 · 保留习惯'],
[7,'07:50','09:00','早餐（慢慢来）','生活'],
[7,'09:30','10:00','散步 / 体态运动（弹性）','运动健康','想动就动，不想动就歇'],
[7,'10:00','20:00','全天自由 · 出门 / 发呆 / 放松','休息缓冲','不排课程、运动、系统调节'],
[7,'20:00','20:10','一句话周复盘 + 下周要事','复盘总结'],
[7,'22:00','22:30','洗漱放松','生活','22:30 前睡觉，稳定生物钟']
];
const TOPICS = [null,'深度输入日','体能先行日','新尝试日 ①','上午运动日','深度前置日','新尝试日 ②','休息与自由调整日'];

/* ========== 工具 ========== */
const pad = n => String(n).padStart(2,'0');
const ymd = d => d.getFullYear()+'-'+pad(d.getMonth()+1)+'-'+pad(d.getDate());
const parseYMD = s => { const [y,m,d]=s.split('-').map(Number); return new Date(y,m-1,d); };
const addDays = (d,n) => { const x=new Date(d); x.setDate(x.getDate()+n); return x; };
const mondayOf = d => { const x=new Date(d); x.setHours(0,0,0,0);
  x.setDate(x.getDate()-((x.getDay()+6)%7)); return x; };
const diffDays = (a,b) => Math.round((b-a)/86400000);
const toMin = t => { const [h,m]=String(t||'0:0').split(':').map(Number); return (h||0)*60+(m||0); };
const uid = () => Date.now().toString(36)+Math.random().toString(36).slice(2,7);
const esc = s => String(s??'').replace(/[&<>"']/g,c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
const TODAY = (()=>{const d=new Date();d.setHours(0,0,0,0);return d;})();
const TODAYDOW = ((new Date().getDay()+6)%7)+1;

/* ========== 数据 ========== */
function buildFromRAW(){
  return RAW.map(r => ({ id:uid(), day:r[0], start:r[1], end:r[2],
    title:r[3], color: COLOR[r[4]] || '#64748b', module:r[4], note:r[5]||'' }));
}
function loadState(){
  let d=null; try{ d=JSON.parse(localStorage.getItem(KEY)||'null'); }catch(e){}
  if(!d || typeof d!=='object') d={};
  d.settings = Object.assign({ startHour:6, endHour:23, dark:false,
    week1Monday: ymd(mondayOf(new Date())) }, d.settings||{});
  if(!Array.isArray(d.items)) d.items = buildFromRAW();
  return d;
}
function save(){ localStorage.setItem(KEY, JSON.stringify(state)); }

let state = loadState();
let viewMonday = mondayOf(new Date());
let mode = localStorage.getItem(KEY+'.mode') || 'day';
let selDay = TODAYDOW;

/* ========== 渲染 ========== */
function applyTheme(){
  document.documentElement.dataset.theme = state.settings.dark?'dark':'light';
  document.querySelector('meta[name=theme-color]')
    .setAttribute('content', state.settings.dark?'#0d1117':'#2f6bff');
}
function weekNo(m){ return Math.floor(diffDays(parseYMD(state.settings.week1Monday), m)/7)+1; }

function renderTop(){
  const w = weekNo(viewMonday), end = addDays(viewMonday,6);
  document.getElementById('t1').textContent = '第 '+w+' 周';
  document.getElementById('t2').textContent =
    (viewMonday.getMonth()+1)+'.'+viewMonday.getDate()+' – '+(end.getMonth()+1)+'.'+end.getDate();
  document.getElementById('btnView').textContent = mode==='day'?'日视图':'周视图';
}

function layout(evs){
  evs.sort((a,b)=>a.s-b.s||a.e-b.e);
  const cls=[]; let cur=[], ce=-1;
  for(const ev of evs){
    if(cur.length && ev.s>=ce){ cls.push(cur); cur=[]; ce=-1; }
    cur.push(ev); ce=Math.max(ce,ev.e);
  }
  if(cur.length) cls.push(cur);
  for(const c of cls){
    const ends=[];
    for(const ev of c){ let i=0; while(i<ends.length && ends[i]>ev.s) i++; ends[i]=ev.e; ev.col=i; }
    c.forEach(ev=>ev.cols=ends.length);
  }
}

function renderWeek(){
  const b = document.getElementById('board'), S = state.settings;
  const totalH = (S.endHour-S.startHour)*HOUR_H;
  let h = '<div class="cell corner"></div>';
  for(let d=1; d<=7; d++){
    const dt = addDays(viewMonday,d-1), isT = ymd(dt)===ymd(TODAY);
    h += `<div class="cell dayhead ${isT?'today':''}"><span class="dn">${DAYS[d-1]}</span>
          <span class="dd">${dt.getMonth()+1}/${dt.getDate()}</span></div>`;
  }
  let g = `<div class="cell gutter"><div class="gutter-inner" style="height:${totalH}px">`;
  for(let x=S.startHour; x<=S.endHour; x++)
    g += `<div class="hourlabel" style="top:${(x-S.startHour)*HOUR_H}px">${pad(x)}:00</div>`;
  h += g+'</div></div>';

  const now = new Date(), nowMin = now.getHours()*60+now.getMinutes();
  for(let d=1; d<=7; d++){
    const dt = addDays(viewMonday,d-1), isT = ymd(dt)===ymd(TODAY);
    let inner = '';
    for(let x=0; x<=S.endHour-S.startHour; x++)
      inner += `<div class="hline" style="top:${x*HOUR_H}px"></div>`;
    const evs = state.items.filter(i=>Number(i.day)===d)
      .map(i=>({...i, s:toMin(i.start), e:Math.max(toMin(i.end), toMin(i.start)+15)}))
      .filter(i=> i.e>S.startHour*60 && i.s<S.endHour*60);
    layout(evs);
    for(const ev of evs){
      const top = (Math.max(ev.s,S.startHour*60)-S.startHour*60)/60*HOUR_H;
      const bot = (Math.min(ev.e,S.endHour*60)-S.startHour*60)/60*HOUR_H;
      const w = 100/ev.cols, l = ev.col*w;
      const hh = Math.max(bot-top-2, 14);
      inner += `<div class="event" data-id="${ev.id}"
        style="top:${top}px;height:${hh}px;left:calc(${l}% + 2px);width:calc(${w}% - 4px);--c:${ev.color}">
        <div class="ev-t">${esc(ev.title)}</div>
        ${hh>34?`<div class="ev-h">${ev.start}</div>`:''}</div>`;
    }
    if(isT && nowMin>=S.startHour*60 && nowMin<=S.endHour*60)
      inner += `<div class="nowline" style="top:${(nowMin-S.startHour*60)/60*HOUR_H}px"></div>`;
    h += `<div class="cell daycol" data-day="${d}" style="height:${totalH}px">${inner}</div>`;
  }
  b.innerHTML = h;
}

function renderDay(){
  const w = document.getElementById('daywrap');
  const dt = addDays(viewMonday, selDay-1);
  const isT = ymd(dt)===ymd(TODAY);
  const evs = state.items.filter(i=>Number(i.day)===selDay)
    .sort((a,b)=>toMin(a.start)-toMin(b.start));
  let h = '<div class="dtabs">';
  for(let d=1; d<=7; d++){
    const dd = addDays(viewMonday,d-1);
    const t = ymd(dd)===ymd(TODAY);
    h += `<button class="dtab ${d===selDay?'on':''}" data-d="${d}">
      ${DAYS[d-1].slice(1)}${t?'·':''}</button>`;
  }
  h += '</div>';

  h += `<div class="dsum">
    <b>${(dt.getMonth()+1)}月${dt.getDate()}日 星期${'日一二三四五六'[dt.getDay()]}</b>
    ${isT?' · <b>今天</b>':''} · 第 ${weekNo(viewMonday)} 周<br>
    今日主题：<b>${TOPICS[selDay]}</b>　共 ${evs.length} 项
  </div>`;

  h += '<div class="dlist">';
  if(!evs.length) h += '<p class="tip" style="padding:20px 0;text-align:center">这一天还没有安排。</p>';
  for(const e of evs){
    h += `<div class="ditem" data-id="${e.id}">
      <div class="dtime">${e.start}<br>${e.end}</div>
      <div class="dbar" style="background:${e.color}"></div>
      <div class="dbody">
        <div class="dtitle">${esc(e.title)}</div>
        ${e.note?`<div class="dnote">${esc(e.note)}</div>`:''}
        <span class="dtag" style="color:${e.color}">${esc(e.module||'')}</span>
      </div></div>`;
  }
  h += '</div>';
  w.innerHTML = h;
}

function renderAll(){
  applyTheme(); renderTop();
  const vp = document.getElementById('viewport'), dw = document.getElementById('daywrap');
  if(mode==='day'){ vp.hidden = true; dw.hidden = false; renderDay(); }
  else { dw.hidden = true; vp.hidden = false; renderWeek(); }
}

function scrollNow(){
  const vp = document.getElementById('viewport');
  const now = new Date(), n = now.getHours()*60+now.getMinutes();
  vp.scrollTop = Math.max(0, (n-state.settings.startHour*60)/60*HOUR_H - vp.clientHeight*0.35);
}

/* ========== 弹层 ========== */
const ov = document.getElementById('overlay'), sh = document.getElementById('sheet');
function openSheet(html){ sh.innerHTML = html; ov.hidden = false;
  requestAnimationFrame(()=>ov.classList.add('show')); }
function closeSheet(){ ov.classList.remove('show');
  setTimeout(()=>{ ov.hidden = true; sh.innerHTML=''; },180); }
ov.addEventListener('click', e => { if(e.target===ov) closeSheet(); });

function openEdit(id){
  const found = id ? state.items.find(x=>x.id===id) : null;
  const isNew = !found;
  const it = found || { id:uid(), title:'', day:selDay, start:'08:00', end:'09:00',
    color:COLOR['核心学习'], module:'核心学习', note:'' };
  let color = it.color, mod = it.module;

  openSheet(`
    <h3>${isNew?'添加时间块':'编辑时间块'}</h3>
    <div class="f"><label>标题</label>
      <input id="f-t" type="text" value="${esc(it.title)}" placeholder="例如：学习课程一"></div>
    <div class="f two">
      <div><label>星期</label><select id="f-d">
        ${DAYS.map((n,i)=>`<option value="${i+1}" ${Number(it.day)===i+1?'selected':''}>${n}</option>`).join('')}
      </select></div>
      <div><label>模块</label><select id="f-m">
        ${MODULES.map(m=>`<option value="${m}" ${m===mod?'selected':''}>${m}</option>`).join('')}
      </select></div>
    </div>
    <div class="f two">
      <div><label>开始</label><input id="f-s" type="time" value="${it.start}"></div>
      <div><label>结束</label><input id="f-e" type="time" value="${it.end}"></div>
    </div>
    <div class="f"><label>颜色</label>
      <div class="sws" id="f-c">${Object.values(COLOR).map(c=>
        `<button type="button" class="sw ${c===it.color?'on':''}" data-c="${c}" style="--c:${c}"></button>`).join('')}
      </div></div>
    <div class="f"><label>备注（可选）</label>
      <input id="f-n" type="text" value="${esc(it.note||'')}" placeholder="含英语 20–30 分钟…"></div>
    <div class="acts">
      ${isNew?'':'<button class="btn danger" id="f-x">删除</button>'}
      <button class="btn" id="f-c">取消</button>
      <button class="btn primary" id="f-ok">保存</button>
    </div>`);

  document.getElementById('f-m').onchange = e => {
    mod = e.target.value; color = COLOR[mod] || color;
    document.querySelectorAll('.sws .sw').forEach(x=>x.classList.toggle('on', x.dataset.c===color));
  };
  document.getElementById('f-c').addEventListener('click', e => {
    const b = e.target.closest('.sw'); if(!b) return;
    color = b.dataset.c;
    document.querySelectorAll('.sws .sw').forEach(x=>x.classList.toggle('on', x===b));
  });
  document.getElementById('f-cancel')?.addEventListener('click', closeSheet);
  document.getElementById('f-x')?.addEventListener('click', ()=>{
    if(!confirm('确定删除？')) return;
    state.items = state.items.filter(x=>x.id!==it.id); save(); renderAll(); closeSheet();
  });
  document.getElementById('f-ok').onclick = () => {
    const t = document.getElementById('f-t').value.trim();
    const s = document.getElementById('f-s').value, e2 = document.getElementById('f-e').value;
    if(toMin(e2) <= toMin(s)){ alert('结束时间要晚于开始时间'); return; }
    const next = { id:it.id, title:t||'未命名', day:Number(document.getElementById('f-d').value),
      start:s, end:e2, color, module:mod, note:document.getElementById('f-n').value.trim() };
    if(isNew) state.items.push(next);
    else state.items[state.items.indexOf(found)] = next;
    save(); renderAll(); closeSheet();
  };
}

function openSettings(){
  const S = state.settings;
  openSheet(`
    <h3>设置</h3>
    <div class="f two">
      <div><label>每天从几点显示</label><input id="s-a" type="number" min="0" max="23" value="${S.startHour}"></div>
      <div><label>显示到几点</label><input id="s-b" type="number" min="1" max="24" value="${S.endHour}"></div>
    </div>
    <div class="f"><label>第一周的周一（算周次用）</label>
      <input id="s-w" type="date" value="${S.week1Monday}"></div>
    <label class="switch"><input id="s-d" type="checkbox" ${S.dark?'checked':''}> 深色模式</label>
    <div class="f"><label>模块颜色</label>
      <div class="legend">${Object.entries(COLOR).map(([m,c])=>
        `<span><i style="background:${c}"></i>${m}</span>`).join('')}</div></div>
    <div class="acts">
      <button class="btn" id="s-ex">导出备份</button>
      <button class="btn" id="s-im">导入备份</button>
    </div>
    <button class="btn primary block" id="s-ok">保存设置</button>
    <button class="btn block" id="s-rs">恢复成原始课表</button>
    <button class="btn danger block" id="s-cl">清空全部</button>
    <p class="tip">数据目前存在这台设备的浏览器里。换设备时用「导出备份」生成文件，在另一台设备「导入备份」。</p>`);

  document.getElementById('s-ex').onclick = () => {
    const blob = new Blob([JSON.stringify(state,null,2)],{type:'application/json'});
    const a = document.createElement('a');
    a.href = URL.createObjectURL(blob); a.download = '时间表备份-'+ymd(new Date())+'.json'; a.click();
    setTimeout(()=>URL.revokeObjectURL(a.href),1000);
  };
  document.getElementById('s-im').onclick = () => {
    const inp = document.getElementById('fileInput'); inp.value='';
    inp.onchange = () => { const f = inp.files[0]; if(!f) return;
      const r = new FileReader();
      r.onload = () => { try{
        const d = JSON.parse(r.result);
        if(!d || !Array.isArray(d.items)) throw 0;
        if(!confirm('导入会覆盖当前数据，继续？')) return;
        state = d; state.settings = Object.assign({startHour:6,endHour:23,dark:false,
          week1Monday: ymd(mondayOf(new Date()))}, state.settings||{});
        save(); renderAll(); closeSheet(); alert('导入成功');
      }catch(e){ alert('文件格式不对'); } };
      r.readAsText(f); };
    inp.click();
  };
  document.getElementById('s-ok').onclick = () => {
    let a = Number(document.getElementById('s-a').value), b = Number(document.getElementById('s-b').value);
    if(!(a>=0&&a<=23)) a=0; if(!(b>=1&&b<=24)) b=24; if(b<=a) b=Math.min(24,a+1);
    S.startHour=a; S.endHour=b;
    S.week1Monday = document.getElementById('s-w').value || S.week1Monday;
    S.dark = document.getElementById('s-d').checked;
    save(); renderAll(); closeSheet();
  };
  document.getElementById('s-rs').onclick = () => {
    if(!confirm('会丢弃你的修改，恢复成最初导入的课表。确定？')) return;
    state.items = buildFromRAW(); save(); renderAll(); closeSheet();
  };
  document.getElementById('s-cl').onclick = () => {
    if(!confirm('会删掉全部时间块，无法恢复。确定？')) return;
    state.items = []; save(); renderAll(); closeSheet();
  };
}

/* ========== 交互 ========== */
document.getElementById('prev').onclick = () => { viewMonday = addDays(viewMonday,-7); renderAll(); };
document.getElementById('next').onclick = () => { viewMonday = addDays(viewMonday,7); renderAll(); };
document.getElementById('btnToday').onclick = () => {
  viewMonday = mondayOf(new Date()); selDay = TODAYDOW; renderAll();
  if(mode==='week') requestAnimationFrame(scrollNow);
};
document.getElementById('btnAdd').onclick = () => openEdit(null);
document.getElementById('btnMenu').onclick = openSettings;
document.getElementById('btnView').onclick = () => {
  mode = mode==='day' ? 'week' : 'day';
  localStorage.setItem(KEY+'.mode', mode); renderAll();
  if(mode==='week') requestAnimationFrame(scrollNow);
};
document.getElementById('board').addEventListener('click', e => {
  const el = e.target.closest('.event'); if(el) openEdit(el.dataset.id);
});
document.getElementById('daywrap').addEventListener('click', e => {
  const t = e.target.closest('.dtab');
  if(t){ selDay = Number(t.dataset.d); renderDay(); return; }
  const it = e.target.closest('.ditem'); if(it) openEdit(it.dataset.id);
});
setInterval(()=>{ if(ov.hidden && mode==='week') renderWeek(); }, 60000);

renderAll();
if(mode==='week') requestAnimationFrame(scrollNow);
</script>
</body>
</html># -
