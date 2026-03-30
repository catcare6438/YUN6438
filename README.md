etTime()+86400000);
      }
      if(w.length) weeks.push({label:`${new Date(w[0]+"T12:00:00").getMonth()+1}/${new Date(w[0]+"T12:00:00").getDate()} ～ ${new Date(w[w.length-1]+"T12:00:00").getMonth()+1}/${new Date(w[w.length-1]+"T12:00:00").getDate()}`, dates:w});
    }
    return weeks;
  }

  // 找今天在哪週
  useEffect(() => {
    for(let i=0;i<WEEKS.length;i++) {
      if(WEEKS[i].dates.includes(today)){setWeekIdx(i);break;}
    }
    // 檢查明天行程
    const tomorrow = dateToStr(new Date(new Date(today+"T12:00:00").getTime()+86400000));
    const evtsTomorrow = events.filter(e=>e.date===tomorrow);
    if(evtsTomorrow.length>0){setReminderEvt(evtsTomorrow[0]);setShowReminder(true);}
  }, []);

  // 儲存行程
  useEffect(() => {
    try { localStorage.setItem("bb_events", JSON.stringify(events)); } catch(e){}
  }, [events]);

  // AI 訊息（換日才重新抓）
  useEffect(() => {
    if(tab==="calendar") {
      const key = `${calYear}-${String(calMonth+1).padStart(2,'0')}-01`;
      if(aiDate===key) return;
      loadAiMsg(key);
    }
  }, [tab, calMonth, calYear]);

  async function loadAiMsg(dateKey) {
    setAiLoading(true);
    const msg = await fetchDailyMsg(dateKey);
    if(msg) setAiMsg(msg);
    else {
      const d = new Date(dateKey+"T12:00:00");
      setAiMsg(`【紫微提醒】\n${getZiwei(d)} ✨\n\n【阿卡西訊息】\n${getAkashic(d)} 💕`);
    }
    setAiDate(dateKey);
    setAiLoading(false);
  }

  function addEvent() {
    if(!evtDate||!evtTitle) return;
    const newEvt = {id:Date.now(),date:evtDate,time:evtTime||"",title:evtTitle};
    const updated = [...events,newEvt].sort((a,b)=>a.date.localeCompare(b.date));
    setEvents(updated);
    setEvtDate("");setEvtTime("");setEvtTitle("");
  }
  function delEvent(id){ setEvents(events.filter(e=>e.id!==id)); }

  // ── 班表輔助 ──
  const curWeekDates = WEEKS[weekIdx]?.dates||[];
  const dayData = DATA[selDate];

  function extractBallTime(str) {
    if(!str||!str.includes("球球")) return null;
    const m = str.match(/球球[^\d]*(\d{3,4})[-:~](\d{3,4})/);
    if(!m) return null;
    function fmt(s){ s=s.replace(/\D/g,''); if(s.length===3)s='0'+s; return s.slice(0,2)+':'+s.slice(2); }
    return {start:fmt(m[1]),end:fmt(m[2])};
  }
  function cleanNames(s) {
    return s.split('\n').map(p=>p.replace(/[~]?\d{3,4}[-:~]\d{3,4}/g,'').replace('球球','').trim()).filter(Boolean).join('、');
  }

  const shifts = ["凌晨","早上","下午","晚上","過夜"];
  const myShifts = dayData ? shifts.filter(k=>dayData[k]?.includes("球球")) : [];
  const mainShift = myShifts[0]||null;
  const ballTime = mainShift ? extractBallTime(dayData[mainShift]) : null;

  const earlyHasPeople = dayData ? !!(dayData["早上"]||dayData["凌晨"]) : false;
  const earlyNames = dayData ? [dayData["凌晨"],dayData["早上"]].map(s=>(s||"").replace(/球球[^\n]*/g,'').trim()).filter(Boolean).join("、") : "";

  function prevShift(key) {
    const order = ["過夜","凌晨","早上","下午","晚上"];
    const idx = order.indexOf(key);
    for(let i=idx-1;i>=0;i--){const v=dayData[order[i]]||"";if(v&&!v.includes("球球"))return{key:order[i],val:v};}
    return null;
  }
  function nextShift(key) {
    const order = ["凌晨","早上","下午","晚上","過夜"];
    const idx = order.indexOf(key);
    for(let i=idx+1;i<order.length;i++){const v=dayData[order[i]]||"";if(v&&!v.includes("球球"))return{key:order[i],val:v};}
    return null;
  }
  const prev = mainShift&&dayData ? prevShift(mainShift) : null;
  const next = mainShift&&dayData ? nextShift(mainShift) : null;

  // ── 日曆輔助 ──
  function daysInMonth(y,m){ return new Date(y,m+1,0).getDate(); }
  function firstDayOfMonth(y,m){ return new Date(y,m,1).getDay(); }
  const isHoliday = (y,m,d) => {
    const k = `${y}-${String(m+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
    return TW_HOLIDAYS[k]||LONG_HOLIDAYS[k]||null;
  };
  const isRed = (y,m,d) => {
    const dt = new Date(y,m,d);
    if(dt.getDay()===0||dt.getDay()===6) return true;
    const k = dateToStr(dt);
    return !!(TW_HOLIDAYS[k]||LONG_HOLIDAYS[k]);
  };
  const isBallWork = (y,m,d) => {
    const k = `${y}-${String(m+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
    const r = DATA[k];
    return r && shifts.some(s=>r[s]?.includes("球球"));
  };

  // 今日行程
  const todayEvents = events.filter(e=>e.date===today);
  const tomorrowStr = dateToStr(new Date(new Date(today+"T12:00:00").getTime()+86400000));
  const tomorrowEvents = events.filter(e=>e.date===tomorrowStr);

  // ─── 樣式 ───
  const S = {
    app:{ fontFamily:"'Noto Sans TC',sans-serif",background:"linear-gradient(160deg,#ffd6e7 0%,#ffecf5 40%,#fff0fa 100%)",minHeight:"100vh",padding:"0 0 80px" },
    hdr:{ background:"rgba(255,255,255,.8)",backdropFilter:"blur(12px)",borderBottom:"3px dashed #ffb3cc",padding:"12px 14px",display:"flex",alignItems:"center",justifyContent:"space-between",position:"sticky",top:0,zIndex:50 },
    logo:{ fontWeight:900,color:"#d4547a",fontSize:"1rem",letterSpacing:".05em" },
    date:{ fontSize:".7rem",color:"#e87fa0",fontWeight:700 },
    tabbar:{ display:"flex",justifyContent:"space-around",background:"rgba(255,255,255,.85)",borderTop:"3px dashed #ffb3cc",position:"fixed",bottom:0,left:0,right:0,padding:"8px 0",zIndex:50 },
    tab:{ flex:1,textAlign:"center",padding:"7px 4px",borderRadius:14,border:"2px solid transparent",background:"none",fontFamily:"inherit",fontSize:".72rem",fontWeight:700,color:"#e87fa0",cursor:"pointer" },
    tabActive:{ background:"linear-gradient(135deg,#ff85aa,#ff5070)",borderColor:"#ff3060",color:"#fff" },
    body:{ padding:"14px 12px",maxWidth:420,margin:"0 auto" },
    card:{ background:"rgba(255,255,255,.78)",border:"2.5px solid #ffb3cc",borderRadius:22,padding:"14px 14px",marginBottom:12,boxShadow:"3px 4px 0 #ffcce0",position:"relative",overflow:"hidden" },
    cardTop:{ position:"absolute",top:0,left:0,right:0,height:3,background:"linear-gradient(90deg,#ff85aa,#ffb3cc,#ffe0a0,#98e8b8)" },
    seclbl:{ fontWeight:900,color:"#d4547a",fontSize:".8rem",letterSpacing:".12em",marginBottom:10,display:"flex",alignItems:"center",gap:6 },
    pill:{ display:"inline-block",background:"linear-gradient(135deg,#ff85aa,#ff5070)",color:"#fff",borderRadius:18,padding:"5px 14px",fontSize:".75rem",fontWeight:700,boxShadow:"2px 3px 0 #cc3060" },
  };

  return (
    <div style={S.app}>
      {/* 提醒彈窗 */}
      {showReminder && reminderEvt && (
        <div style={{position:"fixed",inset:0,background:"rgba(80,20,50,.5)",backdropFilter:"blur(8px)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:200,padding:16}}>
          <div style={{background:"#fff",borderRadius:24,padding:26,maxWidth:300,width:"100%",textAlign:"center",border:"3px solid #ffb3cc",animation:"popIn .3s ease"}}>
            <div style={{fontSize:"2.5rem",marginBottom:8}}>🔔🌸</div>
            <div style={{fontWeight:900,color:"#d4547a",fontSize:"1rem",marginBottom:8}}>明天有行程！</div>
            <div style={{background:"linear-gradient(135deg,rgba(255,180,210,.2),rgba(255,230,240,.3))",borderRadius:14,padding:"12px",marginBottom:14}}>
              <div style={{fontWeight:700,color:"#c03060",fontSize:".9rem"}}>{reminderEvt.time||""} {reminderEvt.title}</div>
              <div style={{fontSize:".72rem",color:"#e87fa0",marginTop:4}}>{reminderEvt.date}</div>
            </div>
            <button onClick={()=>setShowReminder(false)} style={{padding:"9px 22px",background:"linear-gradient(135deg,#ff85aa,#ff5070)",color:"#fff",border:"none",borderRadius:14,fontFamily:"inherit",fontWeight:700,cursor:"pointer"}}>知道了 💕</button>
          </div>
        </div>
      )}

      {/* 頂欄 */}
      <div style={S.hdr}>
        <div>
          <div style={S.logo}>🌸 球球の魔法班表</div>
          <div style={S.date}>{todayDate.getFullYear()}/{todayDate.getMonth()+1}/{todayDate.getDate()}（週{WD[todayDate.getDay()]}）</div>
        </div>
        <div style={{fontSize:"1.5rem"}}>🐾</div>
      </div>

      <div style={S.body}>
        {/* ═══ 班表 TAB ═══ */}
        {tab==="schedule" && <>
          {/* 蠟筆小插圖 */}
          <div style={{textAlign:"center",marginBottom:8}}>
            <svg width="260" height="56" viewBox="0 0 260 56">
              <g transform="translate(24,30)">
                {[0,90,180,270].map((a,i)=>{
                  const x=Math.cos(a*Math.PI/180)*12,y=Math.sin(a*Math.PI/180)*12;
                  return <circle key={i} cx={x} cy={y} r={5} fill="#ffb3cc" stroke="#ff85aa" strokeWidth={2}/>;
                })}
                <circle cx={0} cy={0} r={6} fill="#ffe8f4" stroke="#ff85aa" strokeWidth={2}/>
                <line x1={0} y1={13} x2={0} y2={26} stroke="#90d090" strokeWidth={2.5} strokeLinecap="round"/>
              </g>
              <text x="62" y="20" fontSize="12" fill="#ffcc80" opacity=".8">★</text>
              <text x="78" y="44" fontSize="9" fill="#ffb3cc" opacity=".7">✦</text>
              <text x="104" y="28" fontSize="16" fill="#ff85aa" opacity=".65">♥</text>
              <rect x="128" y="14" width="68" height="30" rx="11" fill="#fff0f5" stroke="#ffb3cc" strokeWidth="2"/>
              <text x="162" y="34" textAnchor="middle" fontSize="12" fontWeight="900" fill="#d4547a">球球 🐾</text>
              <g transform="translate(224,28)">
                {[0,90,180,270].map((a,i)=>{
                  const x=Math.cos(a*Math.PI/180)*10,y=Math.sin(a*Math.PI/180)*10;
                  return <circle key={i} cx={x} cy={y} r={4} fill="#ffc8e0" stroke="#ff85aa" strokeWidth={1.8}/>;
                })}
                <circle cx={0} cy={0} r={5} fill="#ffe8f4" stroke="#ff85aa" strokeWidth={1.8}/>
              </g>
            </svg>
          </div>

          {/* 週導航 */}
          <div style={{...S.card,display:"flex",alignItems:"center",justifyContent:"space-between",padding:"10px 14px"}}>
            <div style={S.cardTop}/>
            <button onClick={()=>{if(weekIdx>0){setWeekIdx(weekIdx-1);setSelDate(WEEKS[weekIdx-1].dates[0])}}} style={{width:34,height:34,borderRadius:"50%",border:"2px solid #ffaacc",background:"linear-gradient(135deg,#fff0f5,#ffe0ef)",color:"#d4547a",fontWeight:900,fontSize:"1.1rem",cursor:"pointer"}}>‹</button>
            <div style={{textAlign:"center"}}>
              <div style={{fontWeight:900,color:"#d4547a",fontSize:".88rem
import { useState, useEffect } from "react";

// ══ 班表資料（完整照試算表） ══
const RAW = [
  // 試算表第一週：03/01（週日獨立）
  {date:"2025-03-01",凌晨:"球球 0000-1000",早上:"曾姐 7-11",下午:"Tien Yun 1-5",晚上:"韶芸 1800-2400",過夜:"采淳 ~0200-1200"},
  // 試算表第二週：03/02(一)~03/08(日)
  {date:"2025-03-02",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"小茹 1500-2100\nYing",晚上:"萱汶\n小茹 1500-2100\n群翔",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-03",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"球球 1200-1600\n小茹",晚上:"韶芸 1800-2400\n阿瑛 1800-2200",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-04",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"阿瑛 1300-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n小茹",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-05",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"Ying 1300-1900\n球球 1200-1600",晚上:"阿瑛 1800-2300\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-06",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"Ying 1200-1800\n群翔 1200-1700",晚上:"韶芸 1800-2400\n小茹",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-07",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"萱汶\n群翔",晚上:"韶芸 2100-0300\n小茹",過夜:""},
  {date:"2025-03-08",凌晨:"曾姐 6-10",早上:"蔡小習 1000-1400\nEmily 9-12",下午:"Tien Yun 1-5\n淑珍",晚上:"韶芸 1800-2400",過夜:"采淳 ~0200-1200"},
  // 第三週：03/09(一)~03/15(日)
  {date:"2025-03-09",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800\n球球 1200-1600",晚上:"小茹\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-10",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"小茹\n書書 12-4",晚上:"韶芸 1800-2400\n阿瑛 1800-2200",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-11",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"阿瑛 1300-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-12",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"Ying 1300-1900\n球球 1200-1600",晚上:"阿瑛 1800-2300\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-13",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800",晚上:"韶芸 1800-2400\n小茹",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-14",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"婉寧 12-5\nYing",晚上:"韶芸 1800-2400\n群翔 1800-2300",過夜:"采淳 0200-0900"},
  {date:"2025-03-15",凌晨:"采淳 0200-0900",早上:"Emily 9-12",下午:"Tien Yun 1-5\n淑珍",晚上:"韶芸 1800-2400",過夜:"采淳 ~0200-1200"},
  // 第四週：03/16(一)~03/22(日)
  {date:"2025-03-16",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"球球 1200-1600\n群翔 1200-1700",晚上:"小茹\n書書 1700-2000",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-17",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"Ying 1200-1800\n小茹 1400-2000",晚上:"阿瑛 1800-2200\n小茹 1400-2000",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-18",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"阿瑛 1300-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-19",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"Ying 1300-1900\n球球 1200-1600",晚上:"阿瑛 1800-2300\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-20",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200\nEmily 9-12",下午:"Ying 1200-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n新人",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-21",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"萱汶 12-21\n婉寧 12-5",晚上:"韶芸 2100-0300\n書書",過夜:""},
  {date:"2025-03-22",凌晨:"Emily 0600-1000",早上:"蔡小習 1000-1400",下午:"Tien Yun 1-5\n淑珍",晚上:"韶芸 1800-2400",過夜:""},
  // 第五週：03/23(一)~03/29(日)
  {date:"2025-03-23",凌晨:"曾姐 6-10",早上:"",下午:"Ying 1200-1800\n球球 1200-1600",晚上:"萱汶\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-24",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"Ying",晚上:"韶芸 1800-2400\n阿瑛 1800-2200",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-25",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"阿瑛 1300-1800\n球球 1200-1800",晚上:"韶芸 1800-2400",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-26",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"球球 1200-1600\nYing",晚上:"阿瑛 1800-2300\n萱汶",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-27",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800\n球球 1200-1800",晚上:"韶芸 1800-2400\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-28",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"萱汶 1300-2100\n群翔 1200-1800",晚上:"萱汶 1300-2100\n韶芸 2100-0300",過夜:""},
  {date:"2025-03-29",凌晨:"曾姐 6-10",早上:"蔡小習 1000-1400\nEmily 9-12",下午:"Tien Yun 1-5\n淑珍",晚上:"韶芸 1800-2400\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  // 第六週：03/30(一)~03/31(二)
  {date:"2025-03-30",凌晨:"采淳 ~0200-1200",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800\n球球 1200-1600",晚上:"萱汶\n群翔 1800-2300",過夜:"采淳 ~0200-1200"},
  {date:"2025-03-31",凌晨:"采淳 ~0200-1200\n曾姐 6-10",早上:"采淳 ~0200-1200",下午:"Ying 1200-1800\n群翔 1200-1700",晚上:"韶芸 1800-2400\n阿瑛 1800-2200",過夜:"采淳 ~0200-1200"},
];

// ══ 週清單：直接按試算表定義，週一為起點 ══
// 試算表結構：第一欄是各週「禮拜一」對應日期
// 03/01 是獨立週日；02/03~08 第二週；依此類推
const WEEKS = [
  { label:"3/1（週日）", dates:["2025-03-01"] },
  { label:"3/2（一）～ 3/8（日）", dates:["2025-03-02","2025-03-03","2025-03-04","2025-03-05","2025-03-06","2025-03-07","2025-03-08"] },
  { label:"3/9（一）～ 3/15（日）", dates:["2025-03-09","2025-03-10","2025-03-11","2025-03-12","2025-03-13","2025-03-14","2025-03-15"] },
  { label:"3/16（一）～ 3/22（日）", dates:["2025-03-16","2025-03-17","2025-03-18","2025-03-19","2025-03-20","2025-03-21","2025-03-22"] },
  { label:"3/23（一）～ 3/29（日）", dates:["2025-03-23","2025-03-24","2025-03-25","2025-03-26","2025-03-27","2025-03-28","2025-03-29"] },
  { label:"3/30（一）～ 3/31（二）", dates:["2025-03-30","2025-03-31"] },
];

// 週顯示標籤（依試算表順序，週一到週日）
const WEEK_DAY_LABELS = {
  "2025-03-01":"日",
  "2025-03-02":"一","2025-03-03":"二","2025-03-04":"三","2025-03-05":"四","2025-03-06":"五","2025-03-07":"六","2025-03-08":"日",
  "2025-03-09":"一","2025-03-10":"二","2025-03-11":"三","2025-03-12":"四","2025-03-13":"五","2025-03-14":"六","2025-03-15":"日",
  "2025-03-16":"一","2025-03-17":"二","2025-03-18":"三","2025-03-19":"四","2025-03-20":"五","2025-03-21":"六","2025-03-22":"日",
  "2025-03-23":"一","2025-03-24":"二","2025-03-25":"三","2025-03-26":"四","2025-03-27":"五","2025-03-28":"六","2025-03-29":"日",
  "2025-03-30":"一","2025-03-31":"二",
};

const DATA = {};
RAW.forEach(r => DATA[r.date] = r);

// ══ 台灣國定假日 2025-2030（行政院人事行政總處官方版）══
const TW_RED = {
  // 2025
  "2025-01-01":"元旦","2025-01-27":"除夕","2025-01-28":"春節","2025-01-29":"春節","2025-01-30":"春節","2025-01-31":"春節補假",
  "2025-02-28":"和平紀念日","2025-04-03":"兒童節補假","2025-04-04":"兒童節","2025-04-05":"清明節",
  "2025-05-01":"勞動節","2025-05-30":"端午節","2025-09-28":"教師節","2025-09-29":"教師節補假",
  "2025-10-10":"國慶日","2025-10-24":"光復節補假","2025-10-25":"台灣光復節","2025-12-25":"行憲紀念日",
  // 2026（官方確認版）
  "2026-01-01":"元旦",
  "2026-02-15":"小年夜補假","2026-02-16":"小年夜","2026-02-17":"春節","2026-02-18":"春節","2026-02-19":"春節",
  "2026-02-20":"春節補假","2026-02-21":"春節補假",
  "2026-02-27":"和平紀念日補假","2026-02-28":"和平紀念日",
  "2026-04-03":"兒童節補假","2026-04-04":"兒童節","2026-04-05":"清明節","2026-04-06":"清明節補假",
  "2026-05-01":"勞動節",
  "2026-06-19":"端午節","2026-06-20":"端午節補假",
  "2026-09-25":"中秋節","2026-09-26":"中秋節補假","2026-09-27":"中秋節補假","2026-09-28":"教師節",
  "2026-10-09":"國慶補假","2026-10-10":"國慶日",
  "2026-10-25":"台灣光復節","2026-10-26":"光復節補假",
  "2026-12-25":"行憲紀念日","2026-12-26":"行憲紀念日補假","2026-12-27":"行憲紀念日補假",
  // 2027
  "2027-01-01":"元旦","2027-02-17":"除夕","2027-02-18":"春節","2027-02-19":"春節","2027-02-20":"春節",
  "2027-02-28":"和平紀念日","2027-04-04":"兒童節","2027-04-05":"清明節",
  "2027-05-01":"勞動節","2027-06-09":"端午節",
  "2027-09-15":"中秋節","2027-09-28":"教師節","2027-10-10":"國慶日",
  "2027-10-25":"台灣光復節","2027-12-25":"行憲紀念日",
  // 2028
  "2028-01-01":"元旦","2028-02-05":"除夕","2028-02-06":"春節","2028-02-07":"春節","2028-02-08":"春節",
  "2028-02-28":"和平紀念日","2028-04-04":"兒童節+清明","2028-05-01":"勞動節","2028-05-28":"端午節",
  "2028-09-03":"中秋節","2028-09-28":"教師節","2028-10-10":"國慶日",
  "2028-10-25":"台灣光復節","2028-12-25":"行憲紀念日",
  // 2029
  "2029-01-01":"元旦","2029-01-26":"除夕","2029-01-27":"春節","2029-01-28":"春節","2029-01-29":"春節",
  "2029-02-28":"和平紀念日","2029-04-04":"兒童節","2029-04-05":"清明節",
  "2029-05-01":"勞動節","2029-06-16":"端午節",
  "2029-09-22":"中秋節","2029-09-28":"教師節","2029-10-10":"國慶日",
  "2029-10-25":"台灣光復節","2029-12-25":"行憲紀念日",
  // 2030
  "2030-01-01":"元旦","2030-02-12":"除夕","2030-02-13":"春節","2030-02-14":"春節","2030-02-15":"春節",
  "2030-02-28":"和平紀念日","2030-04-04":"兒童節","2030-04-05":"清明節",
  "2030-05-01":"勞動節","2030-06-05":"端午節",
  "2030-09-12":"中秋節","2030-09-28":"教師節","2030-10-10":"國慶日",
  "2030-10-25":"台灣光復節","2030-12-25":"行憲紀念日",
};
const H_ICON = {
  "元旦":"🎊","小年夜":"🏮","小年夜補假":"🏮","除夕":"🏮",
  "春節":"🧧","春節補假":"🧧","和平紀念日":"🕊️","和平紀念日補假":"🕊️",
  "兒童節":"🎈","兒童節補假":"🎈","兒童節+清明":"🌸",
  "清明節":"🌿","清明節補假":"🌿","勞動節":"💪",
  "端午節":"🎋","端午節補假":"🎋","中秋節":"🥮","中秋節補假":"🥮",
  "教師節":"📚","教師節補假":"📚","國慶日":"🇹🇼","國慶補假":"🇹🇼",
  "台灣光復節":"🎌","光復節補假":"🎌","行憲紀念日":"📜","行憲紀念日補假":"📜",
};

const SHIFTS = ["凌晨","早上","下午","晚上","過夜"];
const SICON = {凌晨:"🌙",早上:"☀️",下午:"🌤️",晚上:"🌆",過夜:"🌛"};
const SCOL  = {凌晨:"#b3d4ff",早上:"#ffe08a",下午:"#ffb3cc",晚上:"#c8a0ff",過夜:"#98e0c8"};

function d2s(d){ return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`; }
function todayStr(){ return d2s(new Date()); }
function parseDate(s){ const p=s.split("-"); return new Date(+p[0],+p[1]-1,+p[2]); }
function isRedDay(k){ return !!(TW_RED[k]); }
function isWeekend(y,m,d){ const w=new Date(y,m,d).getDay(); return w===0||w===6; }
// 日曆週一offset
function calOffset(y,m){ const dow=new Date(y,m,1).getDay(); return dow===0?6:dow-1; }
function dim(y,m){ return new Date(y,m+1,0).getDate(); }

const FALLBACK_SPIRIT = (y,m)=>`【💰 財運提醒】武曲化祿入財帛，${y}年${m+1}月財氣穩健，適合規劃積蓄或投資 ⭐⭐⭐⭐
【💕 感情提醒】天同星守感情宮，本月感情溫馨平靜，多表達關心更貼心 ⭐⭐⭐⭐
【✨ 工作提醒】天府官祿宮穩固，工作表現受肯定，把握展現實力的機會 ⭐⭐⭐⭐⭐
【🌙 整體運勢】紫微坐命，氣場強大，這個月你的光芒會被看見 💕 相信自己的直覺，宇宙支持你每一步。
【🌅 早晨能量】起床後深呼吸三次，告訴自己今天一切順流。帶著感恩的心出門，好事自然靠近你 🌸
【🌞 午間提醒】中午找五分鐘放空，讓身心重新充電。午後你的直覺特別靈敏，相信第一個感覺 ✨
【🌙 夜晚功課】睡前回想今天三件值得感謝的事。把所有不愉快交給夜晚帶走，明天又是全新的開始 💤`;

export default function App(){
  const today = todayStr();
  const todayD = parseDate(today);

  const [tab,setTab] = useState("schedule");
  const [selDate,setSelDate] = useState(()=>{
    for(const w of WEEKS) if(w.dates.includes(today)) return today;
    return WEEKS[WEEKS.length-1].dates[0];
  });
  const [weekIdx,setWeekIdx] = useState(()=>{
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
