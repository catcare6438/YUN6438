import { useState, useEffect, useCallback } from "react";

// ══ 班表原始資料 ══
const RAW_SCHEDULE = [
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

const DATA = {};
RAW_SCHEDULE.forEach(r => DATA[r.date] = r);

// ══ 台灣節日 ══
const TW_HOLIDAYS = {
  "2025-01-01":"元旦","2025-01-27":"除夕","2025-01-28":"春節","2025-01-29":"春節","2025-01-30":"春節",
  "2025-02-28":"和平紀念日","2025-04-04":"兒童節","2025-04-05":"清明節",
  "2025-05-01":"勞動節","2025-06-01":"端午節","2025-09-15":"中秋節",
  "2025-10-10":"國慶日","2025-10-31":"萬聖節（非假）",
  "2025-12-25":"聖誕節（非假）",
  // 4月
  "2025-04-03":"兒童節補假","2025-04-04":"兒童節+清明",
};
const HOLIDAY_ICONS = {
  "元旦":"🎊","除夕":"🏮","春節":"🧧","和平紀念日":"🕊️","兒童節":"🎪",
  "清明節":"🌿","勞動節":"💪","端午節":"🎋","中秋節":"🥮","國慶日":"🇹🇼",
  "萬聖節（非假）":"🎃","聖誕節（非假）":"🎄","兒童節補假":"🎊","兒童節+清明":"🌸",
};
// 連假
const LONG_HOLIDAYS = {
  "2025-01-27":"春節連假","2025-01-28":"春節連假","2025-01-29":"春節連假","2025-01-30":"春節連假","2025-01-31":"春節連假",
  "2025-04-03":"清明連假","2025-04-04":"清明連假","2025-04-05":"清明連假","2025-04-06":"清明連假",
};

// ══ 紫微斗數（依月份/日期提供每日提醒，AI 版） ══
const ZIWEI_DAILY = [
  "今日化祿星入命，財運亨通，適合處理金錢事務 💰",
  "天機星動，思緒活躍，是提案或學習新事物的好時機 📚",
  "太陽星照耀，人際關係順暢，多與人互動有意外收穫 ☀️",
  "武曲星主財，今日財氣旺，注意別衝動消費 💎",
  "天同星入宮，情緒平穩，適合放鬆身心做喜歡的事 🌸",
  "廉貞星動，感情運波動，與身邊人多溝通少猜疑 💕",
  "天府星穩固，今日宜做規劃與整理，打好基礎 🏡",
  "太陰星入情，直覺敏銳，夢境或靈感值得記錄下來 🌙",
  "貪狼星旺，桃花運佳，社交場合易結交貴人 🌹",
  "巨門星化忌，今日說話需謹慎，避免誤會產生 🤫",
  "天相星助力，今日貴人運強，遇到困難勇敢開口求助 🙌",
  "天梁星守護，健康運佳，是運動或調整作息的好日子 💪",
  "七殺星入宮，行動力強，適合解決積累已久的問題 ⚡",
  "破軍星動，變動日，保持彈性，隨機應變即是好運 🌀",
  "祿存星臨，穩定財運，適合儲蓄或做長期規劃 💰",
  "文昌文曲雙星，才藝大放光彩，創作或表達都超順 ✨",
  "左輔右弼相助，今日有人在背後默默支持你 🌟",
  "天魁天鉞貴人星，今日易獲賞識，大膽展現自己 🌠",
  "火星入宮，性急易犯錯，深呼吸慢一步反而更快到達 🔥",
  "鈴星動，小心電器與交通，出門前多確認一次 ⚠️",
  "地空地劫，防範無謂消耗，今日宜守不宜攻 🛡️",
  "天刑星，今日宜遵守規則，避免衝突糾紛 ⚖️",
  "紅鸞星動，感情甜蜜，單身者易有緣分出現 💝",
  "天喜星入，今日喜事連連，遇見任何事都往好的想 🎊",
  "孤辰星守，適合獨處充電，自我反思有大收穫 🌙",
  "寡宿星入，今日靜心為上，冥想或散步讓心更清明 🍃",
  "龍池鳳閣，才華受肯定，把握展現實力的機會 🎨",
  "恩光天貴，今日格外受人喜愛，好好享受這份光芒 🌟",
  "截路空亡，某些事暫時受阻是正常的，耐心等待 ⏳",
  "旬空星動，清空雜念，今日冥想效果加倍 🧘",
  "月德星照，今日諸事化吉，安心行動 🌸",
];

const AKASHIC_MESSAGES = [
  "親愛的球球，你的靈魂帶著滿滿的光來到這一天，相信你內心的聲音 🌟",
  "今日宇宙給你的訊息：你已經比昨天的自己更好了，為此好好鼓勵自己 💕",
  "你的高我今天想讓你知道：放手那些你無法控制的，你已盡力做好該做的 🕊️",
  "阿卡西紀錄顯示：今天是播種能量的好日子，你的念頭就是種子 🌱",
  "宇宙在告訴你：你值得被愛，也值得接受一切美好 💖",
  "今日靈魂課題：相信直覺，你的第一個念頭往往是最真實的 ✨",
  "高次元傳訊：有一份禮物正在往你這裡來，保持開放與接收的心 🎁",
  "阿卡西今日訊息：釋放舊有的擔憂，你的未來比你想像的更光明 🌈",
  "宇宙提醒：感謝讓能量流動，今天找三件事表達感謝 🙏",
  "你的守護靈說：你不孤單，有無形的力量一直在你身邊 💫",
  "靈魂今日渴望：多喝水，多休息，照顧好這個承載你靈魂的身體 💧",
  "阿卡西傳訊：你正在人生的正確道路上，即使看不清前方，繼續走 🌙",
  "今日宇宙頻率：創造力大開，把心裡的感受寫下來或畫出來 🎨",
  "高我今日提醒：邊界是愛自己的一種方式，勇敢說不沒有關係 🛡️",
  "宇宙今日祝福：你的付出都被看見了，好事正在準備好要來找你 🌸",
];

const WD = ["日","一","二","三","四","五","六"];
const SHIFT_ICONS = {凌晨:"🌙",早上:"☀️",下午:"🌤️",晚上:"🌆",過夜:"🌛"};
const SHIFT_COLORS = {凌晨:"#b3d4ff",早上:"#ffe08a",下午:"#ffb3cc",晚上:"#c8a0ff",過夜:"#98e0c8"};

function todayStr() {
  const t = new Date();
  return `${t.getFullYear()}-${String(t.getMonth()+1).padStart(2,'0')}-${String(t.getDate()).padStart(2,'0')}`;
}
function dateToStr(d) {
  return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
}

// ══ AI 提醒（呼叫 Claude API） ══
async function fetchDailyMsg(dateStr) {
  const date = new Date(dateStr+"T12:00:00");
  const zodiac = getZiwei(date);
  const akashic = getAkashic(date);
  const prompt = `你是一位溫柔可愛的紫微斗數老師兼靈性導師，用繁體中文回應。
球球（1990年3月8日凌晨02:44出生）的命盤資訊：命宮在子宮，紫微星坐命，屬水二局，財帛宮有武曲星，官祿宮有天府星，感情宮有天同星，有化祿在財帛。
今天是 ${dateStr}（${["日","一","二","三","四","五","六"][date.getDay()]}）。
請給球球：
1. 【紫微提醒】根據命盤和今天星象，給一句溫柔有力量的每日提醒（2-3句）
2. 【阿卡西訊息】從高次元靈性角度給一段貼心建議（2-3句）
請用溫柔可愛的語氣，可以加蠟筆小圖示表情符號。每個項目前加對應標題。`;
  try {
    const res = await fetch("https://api.anthropic.com/v1/messages", {
      method:"POST",
      headers:{"Content-Type":"application/json"},
      body:JSON.stringify({
        model:"claude-sonnet-4-20250514",
        max_tokens:400,
        messages:[{role:"user",content:prompt}]
      })
    });
    const data = await res.json();
    return data.content?.map(c=>c.text||"").join("") || null;
  } catch(e) { return null; }
}

function getZiwei(date) {
  return ZIWEI_DAILY[(date.getDate() + date.getMonth()) % ZIWEI_DAILY.length];
}
function getAkashic(date) {
  return AKASHIC_MESSAGES[(date.getDate() * 2 + date.getMonth()) % AKASHIC_MESSAGES.length];
}

// ══ 主 App ══
export default function App() {
  const today = todayStr();
  const todayDate = new Date(today+"T12:00:00");

  // tabs: schedule / calendar / planner
  const [tab, setTab] = useState("schedule");

  // 班表
  const [selDate, setSelDate] = useState(today);
  const [weekIdx, setWeekIdx] = useState(0);

  // 日曆
  const [calMonth, setCalMonth] = useState(todayDate.getMonth());
  const [calYear, setCalYear] = useState(todayDate.getFullYear());

  // 提醒
  const [aiMsg, setAiMsg] = useState("");
  const [aiLoading, setAiLoading] = useState(false);
  const [aiDate, setAiDate] = useState("");

  // 行程
  const [events, setEvents] = useState(() => {
    try { return JSON.parse(localStorage.getItem("bb_events")||"[]"); } catch(e){ return []; }
  });
  const [evtDate, setEvtDate] = useState("");
  const [evtTime, setEvtTime] = useState("");
  const [evtTitle, setEvtTitle] = useState("");
  const [showReminder, setShowReminder] = useState(false);
  const [reminderEvt, setReminderEvt] = useState(null);

  // 週資料
  const WEEKS = buildWeeks();
  function buildWeeks() {
    const weeks = [];
    let start = new Date("2025-03-02T12:00:00"); // 第一週週一
    // 3/1 單獨一天
    weeks.push({label:"3/1（週日）",dates:["2025-03-01"]});
    let cur = new Date(start);
    while(dateToStr(cur) <= "2025-03-31") {
      const w = [];
      for(let i=0;i<7;i++) {
        const s = dateToStr(cur);
        if(s <= "2025-03-31") w.push(s);
        cur = new Date(cur.getTime()+86400000);
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
          <div style={S.logo}>🌸 球球の魔法日曆</div>
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
