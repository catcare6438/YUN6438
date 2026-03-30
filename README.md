=>{
    for(let i=0;i<WEEKS.length;i++) if(WEEKS[i].dates.includes(today)) return i;
    return WEEKS.length-1;
  });

  const [calM,setCalM] = useState(todayD.getMonth());
  const [calY,setCalY] = useState(todayD.getFullYear());
  const [spirit,setSpirit] = useState("");
  const [spLoading,setSpLoading] = useState(false);
  const [spKey,setSpKey] = useState("");
  const [spError,setSpError] = useState("");

  const [events,setEvents] = useState(()=>{ try{return JSON.parse(localStorage.getItem("bb_ev")||"[]")}catch{return []} });
  const [evDate,setEvDate] = useState("");
  const [evTime,setEvTime] = useState("");
  const [evTitle,setEvTitle] = useState("");
  const [banners,setBanners] = useState([]);

  useEffect(()=>{ try{localStorage.setItem("bb_ev",JSON.stringify(events))}catch{}; checkBanners(); },[events]);
  useEffect(()=>{ checkBanners(); },[]);
  useEffect(()=>{ if(tab==="calendar") loadSpirit(calY,calM); },[tab,calY,calM]);

  function checkBanners(){
    const now=new Date(), tmr=d2s(new Date(now.getTime()+86400000)), bs=[];
    events.filter(e=>e.date===tmr).forEach(e=>bs.push({...e,type:"tomorrow"}));
    events.filter(e=>{
      if(e.date!==today||!e.time) return false;
      const [h,m]=e.time.split(":").map(Number);
      const diff=(h*60+m)-(now.getHours()*60+now.getMinutes());
      return diff>0&&diff<=60;
    }).forEach(e=>bs.push({...e,type:"soon"}));
    setBanners(bs);
  }

  async function loadSpirit(y,m){
    const key=`${y}-${m}`;
    if(spKey===key&&spirit) return;
    setSpLoading(true); setSpError("");
    const prompt=`你是球球（1990年3月8日凌晨02:44台灣時間出生）的靈性導師。用繁體中文、溫柔可愛語氣，多用可愛emoji。
球球命盤：紫微坐命子宮，水二局，財帛武曲化祿，官祿天府，感情天同，性格溫柔敏感直覺強。
請針對 ${y}年${m+1}月，給出以下七段（每段用【】包住標題，空行分隔）：
【💰 財運提醒】一句話＋⭐評分
【💕 感情提醒】一句話＋⭐評分
【✨ 工作提醒】一句話＋⭐評分
【🌙 整體運勢】二句鼓勵
【🌅 早晨能量】二句，出發前心理準備
【🌞 午間提醒】二句，中午靈性提示
【🌙 夜晚功課】二句，睡前靈魂整合
語氣像閨蜜，溫暖有力量。`;
    try{
      const res=await fetch("https://api.anthropic.com/v1/messages",{
        method:"POST",headers:{"Content-Type":"application/json"},
        body:JSON.stringify({model:"claude-sonnet-4-20250514",max_tokens:700,messages:[{role:"user",content:prompt}]})
      });
      if(!res.ok) throw new Error(`HTTP ${res.status}`);
      const data=await res.json();
      const txt=data.content?.map(c=>c.type==="text"?c.text:"").join("")||"";
      if(txt.trim()){setSpirit(txt);setSpKey(key);}
      else throw new Error("空回應");
    }catch(e){
      setSpError("✨ 改用本地能量解讀");
      setSpirit(FALLBACK_SPIRIT(y,m));
      setSpKey(key);
    }
    setSpLoading(false);
  }

  function parseSections(txt){
    if(!txt) return {ziwei:[],akashic:[]};
    const ziwei=[],akashic=[];
    const akashicKw=["早晨","午間","夜晚"];
    txt.split(/\n(?=【)/).forEach(p=>{
      const m=p.match(/【(.+?)】([\s\S]*)/);
      if(!m) return;
      const title=m[1].trim(),content=m[2].trim();
      (akashicKw.some(k=>title.includes(k))?akashic:ziwei).push({title,content});
    });
    return {ziwei,akashic};
  }

  function addEvent(){ if(!evDate||!evTitle)return; setEvents(p=>[...p,{id:Date.now(),date:evDate,time:evTime,title:evTitle,done:false}].sort((a,b)=>a.date.localeCompare(b.date))); setEvDate("");setEvTime("");setEvTitle(""); }
  function delEvent(id){ setEvents(p=>p.filter(e=>e.id!==id)); }
  function toggleDone(id){ setEvents(p=>p.map(e=>e.id===id?{...e,done:!e.done}:e)); }

  const cwd=WEEKS[weekIdx]?.dates||[];
  const dd=DATA[selDate];
  const myS=dd?SHIFTS.filter(k=>dd[k]?.includes("球球")):[];
  const mainS=myS[0]||null;
  function xTime(str){ const m=str?.match(/球球[^\d]*(\d{3,4})[-:](\d{3,4})/); if(!m)return null; const f=s=>{s=s.replace(/\D/,'');if(s.length===3)s='0'+s;return s.slice(0,2)+':'+s.slice(2);}; return{start:f(m[1]),end:f(m[2])}; }
  const bt=mainS?xTime(dd[mainS]):null;
  function cln(s){ return s.split('\n').map(p=>p.replace(/[~]?\d{3,4}[-:~]\d{3,4}/g,'').replace('球球','').trim()).filter(Boolean).join('、'); }
  function prevS(k){ const o=["過夜","凌晨","早上","下午","晚上"],i=o.indexOf(k); for(let j=i-1;j>=0;j--){const v=dd[o[j]]||"";if(v&&!v.includes("球球"))return{key:o[j],val:v};} return null; }
  function nextS(k){ const o=["凌晨","早上","下午","晚上","過夜"],i=o.indexOf(k); for(let j=i+1;j<o.length;j++){const v=dd[o[j]]||"";if(v&&!v.includes("球球"))return{key:o[j],val:v};} return null; }
  const pv=mainS&&dd?prevS(mainS):null;
  const nx=mainS&&dd?nextS(mainS):null;
  const earlyPpl=dd?[dd["凌晨"],dd["早上"]].map(s=>(s||"").replace(/球球[^\n]*/g,'').trim()).filter(Boolean).join("、"):"";
  const isBW=(y,m,d)=>{const k=`${y}-${String(m+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;const r=DATA[k];return r&&SHIFTS.some(s=>r[s]?.includes("球球"));};
  const todayEvs=events.filter(e=>e.date===today);
  const tmrStr=d2s(new Date(parseDate(today).getTime()+86400000));
  const tmrEvs=events.filter(e=>e.date===tmrStr);
  const sections=parseSections(spirit);

  const C={
    app:{fontFamily:"'Noto Sans TC',sans-serif",background:"linear-gradient(160deg,#ffd6e7 0%,#ffecf5 45%,#fff0fa 100%)",minHeight:"100vh",paddingBottom:74},
    hdr:{background:"rgba(255,255,255,.84)",backdropFilter:"blur(14px)",borderBottom:"3px dashed #ffb3cc",padding:"11px 14px",display:"flex",alignItems:"center",justifyContent:"space-between",position:"sticky",top:0,zIndex:50},
    body:{padding:"12px 12px 0",maxWidth:430,margin:"0 auto"},
    card:{background:"rgba(255,255,255,.82)",border:"2.5px solid #ffb3cc",borderRadius:22,padding:"14px",marginBottom:12,boxShadow:"3px 4px 0 #ffcce0",position:"relative",overflow:"hidden"},
    bar:{position:"absolute",top:0,left:0,right:0,height:3,background:"linear-gradient(90deg,#ff85aa,#ffb3cc,#ffe0a0,#98e8b8)"},
    lbl:{fontWeight:900,color:"#d4547a",fontSize:".78rem",letterSpacing:".12em",marginBottom:10,display:"flex",alignItems:"center",gap:6},
    tabbar:{display:"flex",justifyContent:"space-around",background:"rgba(255,255,255,.92)",borderTop:"3px dashed #ffb3cc",position:"fixed",bottom:0,left:0,right:0,padding:"7px 0",zIndex:50},
    tb:a=>({flex:1,textAlign:"center",padding:"6px 4px",borderRadius:14,border:"2px solid transparent",background:a?"linear-gradient(135deg,#ff85aa,#ff5070)":"none",fontFamily:"inherit",fontSize:".7rem",fontWeight:700,color:a?"#fff":"#e87fa0",cursor:"pointer"}),
    navBtn:{width:33,height:33,borderRadius:"50%",border:"2px solid #ffaacc",background:"linear-gradient(135deg,#fff0f5,#ffe0ef)",color:"#d4547a",fontWeight:900,fontSize:"1.05rem",cursor:"pointer",display:"flex",alignItems:"center",justifyContent:"center"},
    inp:{width:"100%",padding:"9px 11px",border:"2px solid #ffb3cc",borderRadius:12,fontFamily:"inherit",fontSize:".82rem",color:"#c03060",background:"#fff9fb",outline:"none"},
  };

  return(
    <div style={C.app}>
      {/* 橫幅 */}
      <div style={{position:"fixed",top:0,left:0,right:0,zIndex:200,display:"flex",flexDirection:"column"}}>
        {banners.map(b=>(
          <div key={b.id} style={{background:b.type==="soon"?"linear-gradient(135deg,#ff5070,#ff85aa)":"linear-gradient(135deg,#ff85aa,#ffb3cc)",padding:"10px 14px",display:"flex",alignItems:"center",gap:10,boxShadow:"0 3px 12px rgba(255,80,120,.35)"}}>
            <div style={{fontSize:"1.2rem"}}>{b.type==="soon"?"⏰":"🔔"}</div>
            <div style={{flex:1}}>
              <div style={{fontWeight:900,color:"#fff",fontSize:".82rem"}}>{b.type==="soon"?"⚡ 一小時後！":"🌸 明天有行程"}</div>
              <div style={{color:"rgba(255,255,255,.92)",fontSize:".74rem"}}>{b.time&&`${b.time} `}{b.title}</div>
            </div>
            <button onClick={()=>setBanners(p=>p.filter(x=>x.id!==b.id))} style={{background:"rgba(255,255,255,.25)",border:"none",color:"#fff",borderRadius:"50%",width:26,height:26,cursor:"pointer",fontWeight:900}}>✕</button>
          </div>
        ))}
      </div>

      {/* 頂欄 */}
      <div style={{...C.hdr,marginTop:banners.length*46}}>
        <div>
          <div style={{fontWeight:900,color:"#d4547a",fontSize:"1rem"}}>🌸 球球の魔法班表</div>
          <div style={{fontSize:".67rem",color:"#e87fa0",fontWeight:700}}>{todayD.getFullYear()}/{todayD.getMonth()+1}/{todayD.getDate()}（週{["日","一","二","三","四","五","六"][todayD.getDay()]}）</div>
        </div>
        <div style={{fontSize:"1.4rem"}}>🐾</div>
      </div>

      <div style={C.body}>

      {/* ══ 班表 ══ */}
      {tab==="schedule"&&<>
        <div style={{...C.card,display:"flex",alignItems:"center",justifyContent:"space-between",padding:"10px 14px"}}>
          <div style={C.bar}/>
          <button style={C.navBtn} onClick={()=>{if(weekIdx>0){const ni=weekIdx-1;setWeekIdx(ni);setSelDate(WEEKS[ni].dates[0]);}}}>‹</button>
          <div style={{textAlign:"center"}}>
            <div style={{fontWeight:900,color:"#d4547a",fontSize:".88rem"}}>第 {weekIdx+1} 週</div>
            <div style={{fontSize:".65rem",color:"#e87fa0"}}>{WEEKS[weekIdx]?.label}</div>
          </div>
          <button style={C.navBtn} onClick={()=>{if(weekIdx<WEEKS.length-1){const ni=weekIdx+1;setWeekIdx(ni);setSelDate(WEEKS[ni].dates[0]);}}}>›</button>
        </div>

        {/* 日期列 — 直接用 WEEK_DAY_LABELS，不靠 JS getDay() */}
        <div style={{display:"grid",gridTemplateColumns:`repeat(${cwd.length},1fr)`,gap:4,marginBottom:12}}>
          {cwd.map(d=>{
            const dt=parseDate(d);
            const act=d===selDate, isT=d===today;
            const red=isRedDay(d)||isWeekend(dt.getFullYear(),dt.getMonth(),dt.getDate());
            const wdLabel=WEEK_DAY_LABELS[d]||"";
            return(
              <div key={d} onClick={()=>setSelDate(d)} style={{background:act?"linear-gradient(160deg,#ff85aa,#ff5070)":"rgba(255,255,255,.65)",border:`2px solid ${isT&&!act?"#ff6090":red&&!act?"#ffaacc":"#ffd0e4"}`,borderRadius:13,padding:"6px 2px",textAlign:"center",cursor:"pointer"}}>
                <div style={{fontSize:".55rem",color:act?"#ffe0ec":red?"#d4547a":"#e87fa0",fontWeight:700,marginBottom:2}}>{wdLabel}</div>
                <div style={{fontSize:".8rem",fontWeight:900,color:act?"#fff":red?"#d4547a":"#b08a9a"}}>{dt.getDate()}</div>
                {isRedDay(d)&&<div style={{fontSize:".45rem",color:act?"#ffe0ec":"#ff5060"}}>●</div>}
              <
<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<meta name="apple-mobile-web-app-title" content="球球日曆">
<meta name="theme-color" content="#ffb3cc">
<title>球球の魔法日曆 🌸</title>
<!-- PWA manifest inline -->
<script>
const manifestData = {
  name:"球球の魔法日曆",short_name:"球球日曆",
  start_url:"./",display:"standalone",
  background_color:"#ffd6e7",theme_color:"#ffb3cc",orientation:"portrait",
  icons:[{src:"data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 192 192'%3E%3Crect width='192' height='192' rx='40' fill='%23ffb3cc'/%3E%3Ctext x='96' y='130' text-anchor='middle' font-size='110'%3E🌸%3C/text%3E%3C/svg%3E",sizes:"192x192",type:"image/svg+xml"}]
};
const blob = new Blob([JSON.stringify(manifestData)],{type:'application/json'});
const mLink = document.createElement('link');
mLink.rel='manifest'; mLink.href=URL.createObjectURL(blob);
document.head.appendChild(mLink);
</script>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700;900&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent}
body{font-family:'Noto Sans TC',sans-serif;background:linear-gradient(160deg,#ffd6e7 0%,#ffecf5 45%,#fff0fa 100%);min-height:100vh;padding-bottom:74px;overflow-x:hidden}
.hdr{background:rgba(255,255,255,.84);backdrop-filter:blur(14px);border-bottom:3px dashed #ffb3cc;padding:11px 14px;display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;z-index:50;transition:margin-top .2s}
.hdr-logo{font-weight:900;color:#d4547a;font-size:1rem}
.hdr-date{font-size:.67rem;color:#e87fa0;font-weight:700}
#banners{position:fixed;top:0;left:0;right:0;z-index:200}
.banner{padding:10px 14px;display:flex;align-items:center;gap:10px;box-shadow:0 3px 12px rgba(255,80,120,.35)}
.banner.tomorrow{background:linear-gradient(135deg,#ff85aa,#ffb3cc)}
.banner.soon{background:linear-gradient(135deg,#ff5070,#ff85aa)}
.banner-close{background:rgba(255,255,255,.25);border:none;color:#fff;border-radius:50%;width:26px;height:26px;cursor:pointer;font-weight:900;font-size:.8rem;margin-left:auto;flex-shrink:0}
.body{padding:12px 12px 0;max-width:430px;margin:0 auto}
.card{background:rgba(255,255,255,.82);border:2.5px solid #ffb3cc;border-radius:22px;padding:14px;margin-bottom:12px;box-shadow:3px 4px 0 #ffcce0;position:relative;overflow:hidden}
.card::before{content:'';position:absolute;top:0;left:0;right:0;height:3px;background:linear-gradient(90deg,#ff85aa,#ffb3cc,#ffe0a0,#98e8b8)}
.sec-lbl{font-weight:900;color:#d4547a;font-size:.78rem;letter-spacing:.12em;margin-bottom:10px;display:flex;align-items:center;gap:6px}
.nav-btn{width:33px;height:33px;border-radius:50%;border:2px solid #ffaacc;background:linear-gradient(135deg,#fff0f5,#ffe0ef);color:#d4547a;font-weight:900;font-size:1.05rem;cursor:pointer;display:flex;align-items:center;justify-content:center;flex-shrink:0}
.days-row{display:grid;gap:4px;margin-bottom:12px}
.day-pill{background:rgba(255,255,255,.65);border:2px solid #ffd0e4;border-radius:13px;padding:6px 2px;text-align:center;cursor:pointer;transition:all .15s}
.day-pill.active{background:linear-gradient(160deg,#ff85aa,#ff5070);border-color:#ff6090}
.day-pill.today-border{border-color:#ff6090}
.day-wd{font-size:.55rem;font-weight:700;color:#e87fa0;margin-bottom:2px}
.day-num{font-size:.8rem;font-weight:900;color:#b08a9a}
.day-pill.active .day-wd,.day-pill.active .day-num{color:#fff}
.day-pill.red-day .day-num{color:#d4547a}
.day-red-dot{font-size:.45rem;color:#ff5060}
.day-pill.active .day-red-dot{color:#ffe0ec}
.off-kanji{font-size:5.5rem;font-weight:900;color:#ffb3cc;-webkit-text-stroke:2.5px #ff85aa;text-shadow:4px 4px 0 #ff85aa;line-height:1;display:block;text-align:center}
.shift-main{text-align:center;background:linear-gradient(135deg,rgba(255,200,220,.3),rgba(255,230,240,.25));border-radius:18px;padding:13px;margin-bottom:11px;border:2px dashed #ffb3cc}
.info-grid{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.info-box{border-radius:15px;padding:10px;text-align:center;border:2px solid #ffd4e4;background:rgba(255,255,255,.65)}
.info-box.full{grid-column:1/-1}
.shift-row{display:flex;align-items:center;gap:9px;background:rgba(255,255,255,.65);border:1.5px solid #ffd4e4;border-radius:13px;padding:8px 11px;margin-bottom:5px}
.me-tag{background:rgba(255,100,150,.15);border-radius:7px;padding:0 5px;color:#ff3060;font-weight:900}
.cal-grid{display:grid;grid-template-columns:repeat(7,1fr);gap:2px}
.cal-cell{border-radius:9px;padding:4px 1px;text-align:center;background:rgba(255,255,255,.5);border:2px solid #ffd8e8}
.cal-cell.is-today{background:linear-gradient(135deg,#ff85aa,#ff5070);border-color:#ff3060}
.cal-cell.is-holiday{background:rgba(255,200,200,.22)}
.cal-cell.is-red{border-color:#ffaacc}
.cal-cell-num{font-size:.74rem;font-weight:700;color:#b08a9a}
.cal-cell.is-today .cal-cell-num{color:#fff}
.cal-cell.is-red .cal-cell-num,.cal-cell.is-holiday .cal-cell-num{color:#d4547a}
.cal-cell-dot{width:4px;height:4px;border-radius:50%;background:#7ecfa0;margin:1px auto 0}
.cal-cell.is-today .cal-cell-dot{background:#fff}
.holiday-row{display:flex;align-items:center;gap:10px;background:rgba(255,200,200,.15);border:1.5px solid #ffcccc;border-radius:13px;padding:8px 11px;margin-bottom:5px}
.spirit-section{background:rgba(255,255,255,.7);border:1.5px solid rgba(200,160,255,.45);border-radius:14px;padding:10px 12px;margin-bottom:8px}
.spirit-title{font-weight:900;color:#7030b0;font-size:.75rem;margin-bottom:3px}
.spirit-content{font-size:.8rem;color:#5a2080;line-height:1.85;white-space:pre-wrap}
.ak-box{border-radius:16px;padding:12px 13px;margin-bottom:9px}
.ak-box:nth-child(1){background:linear-gradient(135deg,rgba(255,240,200,.65),rgba(255,225,175,.5));border:1.5px solid #ffd080}
.ak-box:nth-child(2){background:linear-gradient(135deg,rgba(255,220,235,.65),rgba(255,200,220,.5));border:1.5px solid #ffaacc}
.ak-box:nth-child(3){background:linear-gradient(135deg,rgba(210,205,255,.6),rgba(230,210,255,.45));border:1.5px solid #c0a0ff}
.ak-title{font-weight:900;font-size:.75rem;margin-bottom:5px}
.ak-box:nth-child(1) .ak-title{color:#8a5000}
.ak-box:nth-child(2) .ak-title{color:#b02060}
.ak-box:nth-child(3) .ak-title{color:#4020a0}
.ak-content{font-size:.8rem;color:#5a3060;line-height:1.88;white-space:pre-wrap}
.spirit-btn{width:100%;margin-top:10px;padding:10px;background:linear-gradient(135deg,#c090ff,#e060b0);border:2px solid #c080e0;border-radius:13px;color:#fff;font-family:inherit;font-weight:700;font-size:.78rem;cursor:pointer;box-shadow:2px 3px 0 #9050c0}
.spirit-btn:disabled{background:rgba(200,180,220,.5);box-shadow:none;cursor:not-allowed}
.spin{display:inline-block;animation:spin 1.2s linear infinite}
.event-row{display:flex;align-items:center;gap:9px;border-radius:13px;padding:9px 12px;margin-bottom:6px;border:1.5px solid #ffd4e4;background:rgba(255,255,255,.65)}
.event-row.done-row{background:rgba(180,240,200,.25);border-color:#80d0a0}
.event-row.today-row{background:rgba(255,200,220,.25);border-color:#ff85aa}
.event-row.past-row{opacity:.6}
.check-circle{width:22px;height:22px;border-radius:50%;border:2.5px solid #ffb3cc;background:transparent;display:flex;align-items:center;justify-content:center;cursor:pointer;flex-shrink:0;transition:all .2s}
.check-circle.checked{background:linear-gradient(135deg,#80d0a0,#50b070);border-color:#60c080}
.inp{width:100%;padding:9px 11px;border:2px solid #ffb3cc;border-radius:12px;font-family:inherit;font-size:.82rem;color:#c03060;background:#fff9fb;outline:none}
.inp-lbl{font-size:.63rem;font-weight:700;color:#e87fa0;margin-bottom:3px}
.add-btn{width:100%;padding:11px;background:linear-gradient(135deg,#ff85aa,#ff5070);color:#fff;border:none;border-radius:14px;font-family:inherit;font-weight:900;font-size:.88rem;cursor:pointer;box-shadow:2px 3px 0 #cc3060}
.del-btn{background:none;border:none;color:#ffaacc;cursor:pointer;font-weight:900;font-size:.95rem}
.tabbar{display:flex;justify-content:space-around;background:rgba(255,255,255,.92);border-top:3px dashed #ffb3cc;position:fixed;bottom:0;left:0;right:0;padding:7px 0;z-index:50}
.tab-btn{flex:1;text-align:center;padding:6px 4px;border-radius:14px;border:2px solid transparent;background:none;font-family:inherit;font-size:.7rem;font-weight:700;color:#e87fa0;cursor:pointer}
.tab-btn.active{background:linear-gradient(135deg,#ff85aa,#ff5070);border-color:#ff3060;color:#fff}
@keyframes spin{to{transform:rotate(360deg)}}
</style>
</head>
<body>
<div id="banners"></div>
<div class="hdr" id="hdr">
  <div>
    <div class="hdr-logo">🌸 球球の魔法日曆</div>
    <div class="hdr-date" id="hdr-date"></div>
  </div>
  <div style="font-size:1.4rem">🐾</div>
</div>
<div class="body">
  <div id="tab-schedule"></div>
  <div id="tab-calendar" style="display:none"></div>
  <div id="tab-planner" style="display:none"></div>
</div>
<div class="tabbar">
  <button class="tab-btn active" onclick="switchTab('schedule')" id="tb-schedule"><div style="font-size:1.05rem">🐾</div>班表</button>
  <button class="tab-btn" onclick="switchTab('calendar')" id="tb-calendar"><div style="font-size:1.05rem">🌸</div>日曆</button>
  <button class="tab-btn" onclick="switchTab('planner')" id="tb-planner"><div style="font-size:1.05rem">📝</div>行程</button>
</div>

<script>
// ════════════════════════════════════
// ⚠️  在這裡填入你的 Anthropic API Key
// ════════════════════════════════════
const API_KEY = "在這裡填入你的API_KEY";

// ══ 班表資料 ══
const RAW=[
  {date:"2025-03-01",凌晨:"球球 0000-1000",早上:"曾姐 7-11",下午:"Tien Yun 1-5",晚上:"韶芸 1800-2400",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-02",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"小茹 1500-2100\nYing",晚上:"萱汶\n小茹 1500-2100\n群翔",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-03",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"球球 1200-1600\n小茹",晚上:"韶芸 1800-2400\n阿瑛 1800-2200",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-04",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"阿瑛 1300-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n小茹",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-05",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"Ying 1300-1900\n球球 1200-1600",晚上:"阿瑛 1800-2300\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-06",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"Ying 1200-1800\n群翔 1200-1700",晚上:"韶芸 1800-2400\n小茹",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-07",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"萱汶\n群翔",晚上:"韶芸 2100-0300\n小茹",過夜:""},
  {date:"2025-03-08",凌晨:"曾姐 6-10",早上:"蔡小習 1000-1400\nEmily 9-12",下午:"Tien Yun 1-5\n淑珍",晚上:"韶芸 1800-2400",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-09",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800\n球球 1200-1600",晚上:"小茹\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-10",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"小茹\n書書 12-4",晚上:"韶芸 1800-2400\n阿瑛 1800-2200",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-11",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"阿瑛 1300-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-12",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"Ying 1300-1900\n球球 1200-1600",晚上:"阿瑛 1800-2300\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-13",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800",晚上:"韶芸 1800-2400\n小茹",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-14",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"婉寧 12-5\nYing",晚上:"韶芸 1800-2400\n群翔 1800-2300",過夜:"采淳 0200-0900"},
  {date:"2025-03-15",凌晨:"采淳 0200-0900",早上:"Emily 9-12",下午:"Tien Yun 1-5\n淑珍",晚上:"韶芸 1800-2400",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-16",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"球球 1200-1600\n群翔 1200-1700",晚上:"小茹\n書書 1700-2000",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-17",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"Ying 1200-1800\n小茹 1400-2000",晚上:"阿瑛 1800-2200\n小茹 1400-2000",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-18",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"阿瑛 1300-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-19",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"Ying 1300-1900\n球球 1200-1600",晚上:"阿瑛 1800-2300\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-20",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"Ying 1200-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n新人",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-21",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"萱汶 12-21\n婉寧 12-5",晚上:"韶芸 2100-0300\n書書",過夜:""},
  {date:"2025-03-22",凌晨:"Emily 0600-1000",早上:"蔡小習 1000-1400",下午:"Tien Yun 1-5\n淑珍",晚上:"韶芸 1800-2400",過夜:""},
  {date:"2025-03-23",凌晨:"曾姐 6-10",早上:"",下午:"Ying 1200-1800\n球球 1200-1600",晚上:"萱汶\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-24",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"Ying",晚上:"韶芸 1800-2400\n阿瑛 1800-2200",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-25",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"阿瑛 1300-1800\n球球 1200-1800",晚上:"韶芸 1800-2400",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-26",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"球球 1200-1600\nYing",晚上:"阿瑛 1800-2300\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-27",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-28",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"萱汶 1300-2100\n群翔 1200-1800",晚上:"萱汶 1300-2100\n韶芸 2100-0300",過夜:""},
  {date:"2025-03-29",凌晨:"曾姐 6-10",早上:"蔡小習 1000-1400\nEmily 9-12",下午:"Tien Yun 1-5\n淑珍",晚上:"韶芸 1800-2400\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-30",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800\n球球 1200-1600",晚上:"萱汶\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-31",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800\n群翔 1200-1700",晚上:"韶芸 1800-2400\n阿瑛 1800-2200",過夜:"采淳 ~0200-1200"},
];
const DATA={};RAW.forEach(r=>DATA[r.date]=r);

// ══ 週清單（直接照試算表，不靠JS算星期）══
const WEEKS=[
  {label:"3/1（週日）",          dates:["2025-03-01"]},
  {label:"3/2（一）～ 3/8（日）", dates:["2025-03-02","2025-03-03","2025-03-04","2025-03-05","2025-03-06","2025-03-07","2025-03-08"]},
  {label:"3/9（一）～ 3/15（日）",dates:["2025-03-09","2025-03-10","2025-03-11","2025-03-12","2025-03-13","2025-03-14","2025-03-15"]},
  {label:"3/16（一）～ 3/22（日）",dates:["2025-03-16","2025-03-17","2025-03-18","2025-03-19","2025-03-20","2025-03-21","2025-03-22"]},
  {label:"3/23（一）～ 3/29（日）",dates:["2025-03-23","2025-03-24","2025-03-25","2025-03-26","2025-03-27","2025-03-28","2025-03-29"]},
  {label:"3/30（一）～ 3/31（二）",dates:["2025-03-30","2025-03-31"]},
];
const WD_LABEL={
  "2025-03-01":"日",
  "2025-03-02":"一","2025-03-03":"二","2025-03-04":"三","2025-03-05":"四","2025-03-06":"五","2025-03-07":"六","2025-03-08":"日",
  "2025-03-09":"一","2025-03-10":"二","2025-03-11":"三","2025-03-12":"四","2025-03-13":"五","2025-03-14":"六","2025-03-15":"日",
  "2025-03-16":"一","2025-03-17":"二","2025-03-18":"三","2025-03-19":"四","2025-03-20":"五","2025-03-21":"六","2025-03-22":"日",
  "2025-03-23":"一","2025-03-24":"二","2025-03-25":"三","2025-03-26":"四","2025-03-27":"五","2025-03-28":"六","2025-03-29":"日",
  "2025-03-30":"一","2025-03-31":"二",
};

// ══ 台灣國定假日 2025–2030 ══
const TW_RED={
  "2025-01-01":"元旦","2025-01-27":"除夕","2025-01-28":"春節","2025-01-29":"春節","2025-01-30":"春節","2025-01-31":"春節補假",
  "2025-02-28":"和平紀念日","2025-04-03":"兒童節補假","2025-04-04":"兒童節","2025-04-05":"清明節",
  "2025-05-01":"勞動節","2025-05-30":"端午節","2025-09-28":"教師節","2025-10-10":"國慶日","2025-12-25":"行憲紀念日",
  "2026-01-01":"元旦","2026-02-15":"小年夜補假","2026-02-16":"小年夜","2026-02-17":"春節","2026-02-18":"春節","2026-02-19":"春節",
  "2026-02-20":"春節補假","2026-02-21":"春節補假","2026-02-27":"和平紀念日補假","2026-02-28":"和平紀念日",
  "2026-04-03":"兒童節補假","2026-04-04":"兒童節","2026-04-05":"清明節","2026-04-06":"清明節補假",
  "2026-05-01":"勞動節","2026-06-19":"端午節","2026-06-20":"端午節補假",
  "2026-09-25":"中秋節","2026-09-26":"中秋節補假","2026-09-27":"中秋節補假","2026-09-28":"教師節",
  "2026-10-09":"國慶補假","2026-10-10":"國慶日","2026-10-25":"台灣光復節","2026-10-26":"光復節補假",
  "2026-12-25":"行憲紀念日","2026-12-26":"行憲紀念日補假","2026-12-27":"行憲紀念日補假",
  "2027-01-01":"元旦","2027-02-17":"除夕","2027-02-18":"春節","2027-02-19":"春節","2027-02-20":"春節",
  "2027-02-28":"和平紀念日","2027-04-04":"兒童節","2027-04-05":"清明節","2027-05-01":"勞動節","2027-06-09":"端午節",
  "2027-09-15":"中秋節","2027-09-28":"教師節","2027-10-10":"國慶日","2027-10-25":"台灣光復節","2027-12-25":"行憲紀念日",
  "2028-01-01":"元旦","2028-02-05":"除夕","2028-02-06":"春節","2028-02-07":"春節","2028-02-08":"春節",
  "2028-02-28":"和平紀念日","2028-04-04":"兒童節+清明","2028-05-01":"勞動節","2028-05-28":"端午節",
  "2028-09-03":"中秋節","2028-09-28":"教師節","2028-10-10":"國慶日","2028-10-25":"台灣光復節","2028-12-25":"行憲紀念日",
  "2029-01-01":"元旦","2029-01-26":"除夕","2029-01-27":"春節","2029-01-28":"春節","2029-01-29":"春節",
  "2029-02-28":"和平紀念日","2029-04-04":"兒童節","2029-04-05":"清明節","2029-05-01":"勞動節","2029-06-16":"端午節",
  "2029-09-22":"中秋節","2029-09-28":"教師節","2029-10-10":"國慶日","2029-10-25":"台灣光復節","2029-12-25":"行憲紀念日",
  "2030-01-01":"元旦","2030-02-12":"除夕","2030-02-13":"春節","2030-02-14":"春節","2030-02-15":"春節",
  "2030-02-28":"和平紀念日","2030-04-04":"兒童節","2030-04-05":"清明節","2030-05-01":"勞動節","2030-06-05":"端午節",
  "2030-09-12":"中秋節","2030-09-28":"教師節","2030-10-10":"國慶日","2030-10-25":"台灣光復節","2030-12-25":"行憲紀念日",
};
const H_ICON={"元旦":"🎊","小年夜":"🏮","小年夜補假":"🏮","除夕":"🏮","春節":"🧧","春節補假":"🧧","和平紀念日":"🕊️","和平紀念日補假":"🕊️","兒童節":"🎈","兒童節補假":"🎈","兒童節+清明":"🌸","清明節":"🌿","清明節補假":"🌿","勞動節":"💪","端午節":"🎋","端午節補假":"🎋","中秋節":"🥮","中秋節補假":"🥮","教師節":"📚","國慶日":"🇹🇼","國慶補假":"🇹🇼","台灣光復節":"🎌","光復節補假":"🎌","行憲紀念日":"📜","行憲紀念日補假":"📜"};

const SHIFTS=["凌晨","早上","下午","晚上","過夜"];
const SICON={凌晨:"🌙",早上:"☀️",下午:"🌤️",晚上:"🌆",過夜:"🌛"};
const SCOL={凌晨:"#b3d4ff",早上:"#ffe08a",下午:"#ffb3cc",晚上:"#c8a0ff",過夜:"#98e0c8"};

// ══ 工具 ══
function p2(n){return String(n).padStart(2,'0')}
function todayStr(){const t=new Date();return`${t.getFullYear()}-${p2(t.getMonth()+1)}-${p2(t.getDate())}`}
function parseD(s){const a=s.split('-');return new Date(+a[0],+a[1]-1,+a[2])}
function d2s(d){return`${d.getFullYear()}-${p2(d.getMonth()+1)}-${p2(d.getDate())}`}
function dim(y,m){return new Date(y,m+1,0).getDate()}
function calOff(y,m){const w=new Date(y,m,1).getDay();return w===0?6:w-1}
function cln(s){return s.split('\n').map(p=>p.replace(/[~]?\d{3,4}[-:~]\d{3,4}/g,'').replace('球球','').trim()).filter(Boolean).join('、')}
function isRed(k){return!!(TW_RED[k])}
function isWE(y,m,d){const w=new Date(y,m,d).getDay();return w===0||w===6}

const TODAY=todayStr();
let curTab='schedule';
let curWeekI=Math.max(0,WEEKS.findIndex(w=>w.dates.includes(TODAY)));
let selDate=WEEKS[curWeekI].dates.includes(TODAY)?TODAY:WEEKS[curWeekI].dates[0];
let calY=parseD(TODAY).getFullYear(),calM=parseD(TODAY).getMonth();
let spiritTxt='',spiritKey='',spLoading=false;
let events=(()=>{try{return JSON.parse(localStorage.getItem('bb_ev')||'[]')}catch{return[]}})();

// ══ 初始化 ══
(function(){
  const td=parseD(TODAY);
  document.getElementById('hdr-date').textContent=`${td.getFullYear()}/${td.getMonth()+1}/${td.getDate()}（週${['日','一','二','三','四','五','六'][td.getDay()]}）`;
  renderSchedule();checkBanners();
})();

// ══ Tab ══
function switchTab(t){
  curTab=t;
  ['schedule','calendar','planner'].forEach(id=>{
    document.getElementById('tab-'+id).style.display=id===t?'block':'none';
    document.getElementById('tb-'+id).classList.toggle('active',id===t);
  });
  if(t==='calendar'){renderCalendar();if(!spiritTxt||spiritKey!==calY+'-'+calM)loadSpirit();}
  if(t==='planner')renderPlanner();
  window.scrollTo(0,0);
}

// ══ 班表 ══
function renderSchedule(){
  const w=WEEKS[curWeekI],dd=DATA[selDate];
  const myS=dd?SHIFTS.filter(k=>dd[k]&&dd[k].includes('球球')):[];
  const mainS=myS[0]||null;
  function xT(str){const m=str&&str.match(/球球[^\d]*(\d{3,4})[-:](\d{3,4})/);if(!m)return null;const f=s=>{s=s.replace(/\D/,'');if(s.length===3)s='0'+s;return s.slice(0,2)+':'+s.slice(2)};return{start:f(m[1]),end:f(m[2])}}
  const bt=mainS?xT(dd[mainS]):nu
