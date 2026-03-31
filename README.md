Content is user-generated and unverified.
1
<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<meta name="apple-mobile-web-app-title" content="球球日曆">
<meta name="theme-color" content="#e8b8d8">
<title>球球の魔法日曆 🌸</title>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;700;900&family=Nunito:wght@700;800;900&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent}
body{font-family:'Noto Sans TC',sans-serif;min-height:100vh;padding-bottom:74px;overflow-x:hidden;transition:background .4s}
body.tab-schedule{background:linear-gradient(160deg,#ffe0f0 0%,#f0e0ff 50%,#dde8ff 100%)}
body.tab-calendar{background:linear-gradient(160deg,#dde8ff 0%,#e8d8ff 50%,#d8f0ff 100%)}
body.tab-planner{background:linear-gradient(160deg,#ffe8f8 0%,#ffd8f0 50%,#ffe8d8 100%)}

/* 頂欄 */
.hdr{background:rgba(255,255,255,.85);backdrop-filter:blur(14px);padding:11px 14px;display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;z-index:50;transition:border-color .4s,top .2s}
.hdr-logo{font-family:'Nunito',sans-serif;font-weight:900;font-size:1.1rem;background-clip:text;-webkit-background-clip:text;-webkit-text-fill-color:transparent}
.hdr-date{font-size:.67rem;font-weight:700}

/* 橫幅 */
#banners{position:fixed;top:0;left:0;right:0;z-index:200}
.banner{padding:10px 14px;display:flex;align-items:center;gap:10px;box-shadow:0 3px 12px rgba(180,80,150,.4)}
.banner.tomorrow{background:linear-gradient(135deg,#c060a0,#8060c0)}
.banner.soon{background:linear-gradient(135deg,#e05080,#e09030)}
.banner-close{background:rgba(255,255,255,.25);border:none;color:#fff;border-radius:50%;width:26px;height:26px;cursor:pointer;font-weight:900;font-size:.8rem;margin-left:auto;flex-shrink:0}

/* 主體 */
.body{padding:12px 12px 0;max-width:430px;margin:0 auto}

/* 卡片 */
.card{background:rgba(255,255,255,.82);border-radius:22px;padding:14px;margin-bottom:12px;box-shadow:3px 4px 0;position:relative;overflow:hidden;transition:border-color .4s,box-shadow .4s}
.card::before{content:'';position:absolute;top:0;left:0;right:0;height:3px}

/* 可愛浮動 emoji */
.deco-row{text-align:center;margin:4px 0 10px;font-size:1.8rem;letter-spacing:8px}
.float{display:inline-block;animation:float 3s ease-in-out infinite}

/* 週日期列 */
.days-row{display:grid;gap:4px;margin-bottom:12px}
.day-pill{background:rgba(255,255,255,.65);border-radius:13px;padding:6px 2px;text-align:center;cursor:pointer;transition:all .2s;border:2px solid rgba(255,255,255,.5)}
.day-pill.active{color:#fff}
.day-wd{font-size:.55rem;font-weight:700;margin-bottom:2px}
.day-num{font-family:'Nunito',sans-serif;font-size:.85rem;font-weight:900}
.day-red-dot{font-size:.42rem}

/* 休假 */
.off-kanji{font-family:'Nunito',sans-serif;font-size:5rem;font-weight:900;line-height:1;display:block;text-align:center}

/* 班次主卡 */
.shift-main{text-align:center;border-radius:18px;padding:13px;margin-bottom:11px;border-width:2px;border-style:dashed}
.shift-time-big{font-family:'Nunito',sans-serif;font-size:2rem;font-weight:900;margin:4px 0;letter-spacing:.03em}

/* 資訊格 */
.info-grid{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.info-box{background:rgba(255,255,255,.7);border-radius:15px;padding:10px;text-align:center;border-width:2px;border-style:solid}
.info-box.full{grid-column:1/-1}
.info-icon{font-size:1.2rem;margin-bottom:3px}
.info-lbl{font-size:.58rem;font-weight:900;margin-bottom:3px}
.info-val{font-size:.72rem;font-weight:700}

/* 全日班表 */
.shift-row{display:flex;align-items:center;gap:9px;background:rgba(255,255,255,.6);border-radius:13px;padding:8px 11px;margin-bottom:5px;border-width:1.5px;border-style:solid}
.shift-dot{width:8px;height:8px;border-radius:50%;flex-shrink:0}
.shift-slot{font-size:.65rem;font-weight:700;min-width:52px}
.shift-people{font-size:.75rem;font-weight:700;flex:1;line-height:1.5}
.me-tag{border-radius:7px;padding:0 5px;font-weight:900}

/* 日曆 */
.cal-grid{display:grid;grid-template-columns:repeat(7,1fr);gap:2px}
.cal-cell{border-radius:9px;padding:4px 1px;text-align:center;background:rgba(255,255,255,.5);border:2px solid rgba(255,255,255,.3)}
.cal-num{font-family:'Nunito',sans-serif;font-size:.74rem;font-weight:700}
.cal-dot{width:4px;height:4px;border-radius:50%;background:#60c080;margin:1px auto 0}
.holiday-row{display:flex;align-items:center;gap:10px;border-radius:13px;padding:8px 11px;margin-bottom:5px;border-width:1.5px;border-style:solid}

/* 靈性 */
.spirit-sec{background:rgba(255,255,255,.7);border-radius:14px;padding:10px 12px;margin-bottom:8px;border-width:1.5px;border-style:solid}
.spirit-title{font-weight:900;font-size:.75rem;margin-bottom:3px}
.spirit-content{font-size:.8rem;line-height:1.85;white-space:pre-wrap}
.ak-box{border-radius:16px;padding:12px 13px;margin-bottom:9px;border-width:1.5px;border-style:solid}
.ak-title{font-weight:900;font-size:.75rem;margin-bottom:5px}
.ak-content{font-size:.8rem;line-height:1.88;white-space:pre-wrap}
.spirit-btn{width:100%;margin-top:8px;padding:10px;border:none;border-radius:13px;color:#fff;font-family:'Nunito',sans-serif;font-weight:700;font-size:.78rem;cursor:pointer}
.spirit-btn:disabled{cursor:not-allowed;opacity:.6}

/* 行程 */
.event-row{display:flex;align-items:center;gap:9px;border-radius:13px;padding:9px 12px;margin-bottom:6px;border-width:1.5px;border-style:solid;background:rgba(255,255,255,.65)}
.event-row.done-row{background:rgba(180,240,200,.25);border-color:#80d0a0!important}
.check-circle{width:22px;height:22px;border-radius:50%;border-width:2.5px;border-style:solid;background:transparent;display:flex;align-items:center;justify-content:center;cursor:pointer;flex-shrink:0;transition:all .2s}
.check-circle.checked{background:linear-gradient(135deg,#80d0a0,#50b070);border-color:#60c080!important}
.ev-title{font-weight:700;font-size:.8rem}
.ev-meta{font-size:.62rem}
.del-btn{background:none;border:none;cursor:pointer;font-weight:900;font-size:.95rem}
.inp{width:100%;padding:9px 11px;border-radius:12px;font-family:inherit;font-size:.82rem;background:rgba(255,255,255,.9);outline:none;border-width:2px;border-style:solid}
.inp-lbl{font-size:.63rem;font-weight:700;margin-bottom:3px}
.inp-grid{display:grid;grid-template-columns:1fr 1fr;gap:8px;margin-bottom:9px}
.add-btn{width:100%;padding:11px;border:none;border-radius:14px;color:#fff;font-family:'Nunito',sans-serif;font-weight:900;font-size:.88rem;cursor:pointer}
.sec-lbl{font-family:'Nunito',sans-serif;font-weight:900;font-size:.8rem;margin-bottom:10px}
.nav-btn{width:34px;height:34px;border-radius:50%;font-weight:900;font-size:1.1rem;cursor:pointer;display:flex;align-items:center;justify-content:center;background:rgba(255,255,255,.7);border-width:2px;border-style:solid}
.empty-hint{text-align:center;font-size:.82rem;padding:14px}

/* Tab Bar */
.tabbar{display:flex;justify-content:space-around;background:rgba(255,255,255,.92);backdrop-filter:blur(12px);position:fixed;bottom:0;left:0;right:0;padding:7px 0;z-index:50;border-top-width:3px;border-top-style:dashed;transition:border-color .4s}
.tab-btn{flex:1;text-align:center;padding:6px 4px;border-radius:14px;border:2px solid transparent;background:none;font-family:'Nunito',sans-serif;font-size:.7rem;font-weight:700;cursor:pointer;transition:all .2s}
.tab-icon{font-size:1.05rem}

@keyframes float{0%,100%{transform:translateY(0)}50%{transform:translateY(-6px)}}
@keyframes spin{to{transform:rotate(360deg)}}
.spin{display:inline-block;animation:spin 1.2s linear infinite}
</style>
</head>
<body class="tab-schedule">

<div id="banners"></div>
<div class="hdr" id="hdr">
  <div>
    <div class="hdr-logo" id="hdr-logo">🌸 球球の魔法日曆</div>
    <div class="hdr-date" id="hdr-date"></div>
  </div>
  <div style="font-size:1.6rem" id="hdr-pet" class="float">🐾</div>
</div>

<div class="body">
  <div id="tab-schedule"></div>
  <div id="tab-calendar" style="display:none"></div>
  <div id="tab-planner" style="display:none"></div>
</div>

<div class="tabbar" id="tabbar">
  <button class="tab-btn" id="tb-schedule" onclick="switchTab('schedule')"><div class="tab-icon">🐾</div>班表</button>
  <button class="tab-btn" id="tb-calendar" onclick="switchTab('calendar')"><div class="tab-icon">🌸</div>日曆</button>
  <button class="tab-btn" id="tb-planner" onclick="switchTab('planner')"><div class="tab-icon">📝</div>行程</button>
</div>

<script>
// ══ 主題 ══
const TH={
  schedule:{
    bg:'linear-gradient(160deg,#ffe0f0 0%,#f0e0ff 50%,#dde8ff 100%)',
    border:'#e8b8d8',accent:'#c060a0',accent2:'#8050c0',
    pill:'linear-gradient(135deg,#e060a0,#9060d0)',
    shadow:'3px 4px 0 #e8c8e0',bar:'linear-gradient(90deg,#e060a0,#c070d0,#8080e0,#60b0f0)',
    deco:['🌸','🎀','🐱','✨','🌸'],hdrPet:'🐾',
  },
  calendar:{
    bg:'linear-gradient(160deg,#dde8ff 0%,#e8d8ff 50%,#d8f0ff 100%)',
    border:'#b8c8f0',accent:'#5060c0',accent2:'#3090c0',
    pill:'linear-gradient(135deg,#6070d0,#30a0d0)',
    shadow:'3px 4px 0 #c0c8f0',bar:'linear-gradient(90deg,#6070d0,#5090d0,#30b0c0,#40c0d0)',
    deco:['🌙','⭐','🔮','⭐','🌙'],hdrPet:'🌙',
  },
  planner:{
    bg:'linear-gradient(160deg,#ffe8f8 0%,#ffd8f0 50%,#ffe8d8 100%)',
    border:'#f0b8d8',accent:'#d05080',accent2:'#e08030',
    pill:'linear-gradient(135deg,#e05080,#e09030)',
    shadow:'3px 4px 0 #f0c8d8',bar:'linear-gradient(90deg,#e05080,#e07050,#e09030,#d0b030)',
    deco:['🌺','🐝','🌻','🐝','🌺'],hdrPet:'🌺',
  }
};

// ══ 班表資料 ══
const RAW=[
  // 3月
  {date:'2025-03-01',凌晨:'球球 0000-1000',早上:'曾姐 7-11',下午:'Tien Yun 1-5',晚上:'韶芸 1800-2400'},
  {date:'2025-03-02',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'小茹 1500-2100\nYing',晚上:'萱汶\n小茹 1500-2100\n群翔'},
  {date:'2025-03-03',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'球球 1200-1600\n小茹',晚上:'韶芸 1800-2400\n阿瑛 1800-2200'},
  {date:'2025-03-04',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'阿瑛 1300-1800\n球球 1200-1800',晚上:'韶芸 1800-2400\n小茹'},
  {date:'2025-03-05',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 1300-1900\n球球 1200-1600',晚上:'阿瑛 1800-2300\n萱汶'},
  {date:'2025-03-06',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 1200-1800\n群翔 1200-1700',晚上:'韶芸 1800-2400\n小茹'},
  {date:'2025-03-07',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'萱汶\n群翔',晚上:'韶芸 2100-0300\n小茹'},
  {date:'2025-03-08',凌晨:'曾姐 6-10',早上:'蔡小習 1000-1400\nEmily 9-12',下午:'Tien Yun 1-5\n淑珍',晚上:'韶芸 1800-2400'},
  {date:'2025-03-09',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'Ying 1200-1800\n球球 1200-1600',晚上:'小茹\n萱汶'},
  {date:'2025-03-10',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'小茹\n書書 12-4',晚上:'韶芸 1800-2400\n阿瑛 1800-2200'},
  {date:'2025-03-11',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'阿瑛 1300-1800\n球球 1200-1800',晚上:'韶芸 1800-2400\n萱汶'},
  {date:'2025-03-12',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 1300-1900\n球球 1200-1600',晚上:'阿瑛 1800-2300\n群翔 1800-2300'},
  {date:'2025-03-13',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'Ying 1200-1800',晚上:'韶芸 1800-2400\n小茹'},
  {date:'2025-03-14',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'婉寧 12-5\nYing',晚上:'韶芸 1800-2400\n群翔 1800-2300'},
  {date:'2025-03-15',凌晨:'采淳 0200-0900',早上:'Emily 9-12',下午:'Tien Yun 1-5\n淑珍',晚上:'韶芸 1800-2400'},
  {date:'2025-03-16',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'球球 1200-1600\n群翔 1200-1700',晚上:'小茹\n書書 1700-2000'},
  {date:'2025-03-17',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 1200-1800\n小茹 1400-2000',晚上:'阿瑛 1800-2200\n小茹 1400-2000'},
  {date:'2025-03-18',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'阿瑛 1300-1800\n球球 1200-1800',晚上:'韶芸 1800-2400\n群翔 1800-2300'},
  {date:'2025-03-19',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 1300-1900\n球球 1200-1600',晚上:'阿瑛 1800-2300\n萱汶'},
  {date:'2025-03-20',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 1200-1800\n球球 1200-1800',晚上:'韶芸 1800-2400\n新人'},
  {date:'2025-03-21',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'萱汶 12-21\n婉寧 12-5',晚上:'韶芸 2100-0300\n書書'},
  {date:'2025-03-22',凌晨:'Emily 0600-1000',早上:'蔡小習 1000-1400',下午:'Tien Yun 1-5\n淑珍',晚上:'韶芸 1800-2400'},
  {date:'2025-03-23',凌晨:'曾姐 6-10',早上:'',下午:'Ying 1200-1800\n球球 1200-1600',晚上:'萱汶\n群翔 1800-2300'},
  {date:'2025-03-24',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'Ying',晚上:'韶芸 1800-2400\n阿瑛 1800-2200'},
  {date:'2025-03-25',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'阿瑛 1300-1800\n球球 1200-1800',晚上:'韶芸 1800-2400'},
  {date:'2025-03-26',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'球球 1200-1600\nYing',晚上:'阿瑛 1800-2300\n萱汶'},
  {date:'2025-03-27',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 1200-1800\n球球 1200-1800',晚上:'韶芸 1800-2400\n群翔 1800-2300'},
  {date:'2025-03-28',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'萱汶 1300-2100\n群翔 1200-1800',晚上:'萱汶 1300-2100\n韶芸 2100-0300'},
  {date:'2025-03-29',凌晨:'曾姐 6-10',早上:'蔡小習 1000-1400\nEmily 9-12',下午:'Tien Yun 1-5\n淑珍',晚上:'韶芸 1800-2400\n群翔 1800-2300'},
  {date:'2025-03-30',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 1200-1800\n球球 1200-1600',晚上:'萱汶\n群翔 1800-2300'},
  {date:'2025-03-31',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'Ying 1200-1800\n群翔 1200-1700',晚上:'韶芸 1800-2400\n阿瑛 1800-2200'},
  // 4月
  {date:'2025-04-01',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'萱汶 1200-2200\n群翔 12-6',晚上:'韶芸 1800-2400\n萱汶 1200-2200'},
  {date:'2025-04-02',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'球球 12-4\n萱汶 1300-2300',晚上:'萱汶 1300-2300\n群翔 1800-2300'},
  {date:'2025-04-03',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'Ying 12-6\n球球 2-530',晚上:'韶芸 1800-2400\n萱汶 1700-2300'},
  {date:'2025-04-04',凌晨:'采淳 ~0200-1100',早上:'采淳 ~0200-1100\n淑珍',下午:'Ying 12-6\n小茹 1400-2100',晚上:'韶芸 2100-0300\n小茹 1400-2100'},
  {date:'2025-04-05',凌晨:'曾姐 6-10',早上:'蔡小習 1000-1400',下午:'Tien Yun 1-5\n淑珍',晚上:'小茹 1800-2300'},
  {date:'2025-04-06',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 12-6\n球球 12-4',晚上:'萱汶'},
  {date:'2025-04-07',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'球球 12-4',晚上:'韶芸 1800-2400\n阿瑛 1800-2200'},
  {date:'2025-04-08',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'阿瑛 1-6\n萱汶',晚上:'韶芸 1800-2400\n萱汶'},
  {date:'2025-04-09',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'球球 12-4\n萱汶',晚上:'阿瑛 1800-2300\n萱汶'},
  {date:'2025-04-10',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 12-6\n球球 2-530',晚上:'韶芸 1800-2400'},
  {date:'2025-04-11',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'萱汶',晚上:'韶芸 1800-2400'},
  {date:'2025-04-12',凌晨:'曾姐 6-10',早上:'蔡小習 1000-1400\nEmily 9-12',下午:'Tien Yun 1-5\n淑珍',晚上:'萱汶'},
  {date:'2025-04-13',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 12-6\n球球 12-4',晚上:'萱汶'},
  {date:'2025-04-14',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 12-6\n球球 12-4',晚上:'韶芸 1800-2400\n阿瑛 1800-2200'},
  {date:'2025-04-15',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'阿瑛 1-6\n萱汶',晚上:'韶芸 1800-2400'},
  {date:'2025-04-16',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 1-7\n球球 12-4',晚上:'阿瑛 1800-2300\n萱汶'},
  {date:'2025-04-17',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 12-6\n球球 2-530',晚上:'韶芸 1800-2400'},
  {date:'2025-04-18',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\n淑珍',下午:'萱汶',晚上:'韶芸 1800-2400'},
  {date:'2025-04-19',凌晨:'',早上:'Emily 9-12',下午:'Tien Yun 1-5\n淑珍',晚上:'萱汶'},
  {date:'2025-04-20',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 12-6\n球球 12-4',晚上:'萱汶'},
  {date:'2025-04-21',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'球球 12-4',晚上:'韶芸 1800-2400'},
  {date:'2025-04-22',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'阿瑛 1-6\n萱汶',晚上:'韶芸 1800-2400'},
  {date:'2025-04-23',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 1-7\n球球 12-4',晚上:'阿瑛 1800-2300\n萱汶'},
  {date:'2025-04-24',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200\nEmily 9-12',下午:'Ying 12-6\n球球 2-530',晚上:'韶芸 1800-2400\n萱汶'},
  {date:'2025-04-25',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200\n淑珍',下午:'萱汶',晚上:'韶芸 1800-2400'},
  {date:'2025-04-26',凌晨:'曾姐 6-10',早上:'蔡小習 1000-1400',下午:'Tien Yun 1-5\n淑珍',晚上:''},
  {date:'2025-04-27',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'Ying 12-6\n球球 12-4',晚上:'萱汶'},
  {date:'2025-04-28',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 12-6\n球球 12-4',晚上:'韶芸 1800-2400\n阿瑛 1800-2200'},
  {date:'2025-04-29',凌晨:'采淳 ~0200-1200\n曾姐 6-10',早上:'采淳 ~0200-1200',下午:'阿瑛 1-6\n萱汶',晚上:'韶芸 1800-2400'},
  {date:'2025-04-30',凌晨:'采淳 ~0200-1200',早上:'采淳 ~0200-1200',下午:'Ying 1-7\n球球 12-4',晚上:'阿瑛 1800-2300\n萱汶'},
];
const DATA={};RAW.forEach(r=>DATA[r.date]=r);

// ══ 週清單 ══
const WEEKS=[
  {label:'3/1（週日）',dates:['2025-03-01']},
  {label:'3/2（一）～ 3/8（日）',dates:['2025-03-02','2025-03-03','2025-03-04','2025-03-05','2025-03-06','2025-03-07','2025-03-08']},
  {label:'3/9（一）～ 3/15（日）',dates:['2025-03-09','2025-03-10','2025-03-11','2025-03-12','2025-03-13','2025-03-14','2025-03-15']},
  {label:'3/16（一）～ 3/22（日）',dates:['2025-03-16','2025-03-17','2025-03-18','2025-03-19','2025-03-20','2025-03-21','2025-03-22']},
  {label:'3/23（一）～ 3/29（日）',dates:['2025-03-23','2025-03-24','2025-03-25','2025-03-26','2025-03-27','2025-03-28','2025-03-29']},
  {label:'3/30（一）～ 4/5（日）',dates:['2025-03-30','2025-03-31','2025-04-01','2025-04-02','2025-04-03','2025-04-04','2025-04-05']},
  {label:'4/6（一）～ 4/12（日）',dates:['2025-04-06','2025-04-07','2025-04-08','2025-04-09','2025-04-10','2025-04-11','2025-04-12']},
  {label:'4/13（一）～ 4/19（日）',dates:['2025-04-13','2025-04-14','2025-04-15','2025-04-16','2025-04-17','2025-04-18','2025-04-19']},
  {label:'4/20（一）～ 4/26（日）',dates:['2025-04-20','2025-04-21','2025-04-22','2025-04-23','2025-04-24','2025-04-25','2025-04-26']},
  {label:'4/27（一）～ 4/30（四）',dates:['2025-04-27','2025-04-28','2025-04-29','2025-04-30']},
];
const WD_LABEL={
  '2025-03-01':'日',
  '2025-03-02':'一','2025-03-03':'二','2025-03-04':'三','2025-03-05':'四','2025-03-06':'五','2025-03-07':'六','2025-03-08':'日',
  '2025-03-09':'一','2025-03-10':'二','2025-03-11':'三','2025-03-12':'四','2025-03-13':'五','2025-03-14':'六','2025-03-15':'日',
  '2025-03-16':'一','2025-03-17':'二','2025-03-18':'三','2025-03-19':'四','2025-03-20':'五','2025-03-21':'六','2025-03-22':'日',
  '2025-03-23':'一','2025-03-24':'二','2025-03-25':'三','2025-03-26':'四','2025-03-27':'五','2025-03-28':'六','2025-03-29':'日',
  '2025-03-30':'一','2025-03-31':'二',
  '2025-04-01':'三','2025-04-02':'四','2025-04-03':'五','2025-04-04':'六','2025-04-05':'日',
  '2025-04-06':'一','2025-04-07':'二','2025-04-08':'三','2025-04-09':'四','2025-04-10':'五','2025-04-11':'六','2025-04-12':'日',
  '2025-04-13':'一','2025-04-14':'二','2025-04-15':'三','2025-04-16':'四','2025-04-17':'五','2025-04-18':'六','2025-04-19':'日',
  '2025-04-20':'一','2025-04-21':'二','2025-04-22':'三','2025-04-23':'四','2025-04-24':'五','2025-04-25':'六','2025-04-26':'日',
  '2025-04-27':'一','2025-04-28':'二','2025-04-29':'三','2025-04-30':'四',
};

// ══ 台灣節假日 2025-2030 ══
const TW_RED={
  '2025-01-01':'元旦','2025-02-28':'和平紀念日',
  '2025-04-03':'兒童節補假','2025-04-04':'兒童節','2025-04-05':'清明節',
  '2025-05-01':'勞動節','2025-05-30':'端午節','2025-10-10':'國慶日',
  '2026-01-01':'元旦','2026-02-16':'小年夜','2026-02-17':'春節','2026-02-18':'春節','2026-02-19':'春節','2026-02-28':'和平紀念日',
  '2026-04-03':'兒童節補假','2026-04-04':'兒童節','2026-04-05':'清明節','2026-04-06':'清明節補假',
  '2026-05-01':'勞動節','2026-06-19':'端午節','2026-09-25':'中秋節','2026-10-10':'國慶日',
  '2027-01-01':'元旦','2027-02-17':'除夕','2027-02-18':'春節','2027-02-19':'春節','2027-02-20':'春節',
  '2027-02-28':'和平紀念日','2027-04-04':'兒童節','2027-04-05':'清明節','2027-05-01':'勞動節',
  '2027-06-09':'端午節','2027-09-15':'中秋節','2027-10-10':'國慶日',
  '2028-01-01':'元旦','2028-02-05':'除夕','2028-02-06':'春節','2028-02-07':'春節','2028-02-08':'春節',
  '2028-02-28':'和平紀念日','2028-04-04':'兒童節+清明','2028-05-01':'勞動節','2028-05-28':'端午節',
  '2028-09-03':'中秋節','2028-10-10':'國慶日',
  '2029-01-01':'元旦','2029-01-26':'除夕','2029-01-27':'春節','2029-01-28':'春節','2029-01-29':'春節',
  '2029-02-28':'和平紀念日','2029-04-04':'兒童節','2029-04-05':'清明節','2029-05-01':'勞動節',
  '2029-06-16':'端午節','2029-09-22':'中秋節','2029-10-10':'國慶日',
  '2030-01-01':'元旦','2030-02-12':'除夕','2030-02-13':'春節','2030-02-14':'春節','2030-02-15':'春節',
  '2030-02-28':'和平紀念日','2030-04-04':'兒童節','2030-04-05':'清明節','2030-05-01':'勞動節',
  '2030-06-05':'端午節','2030-09-12':'中秋節','2030-10-10':'國慶日',
};
const H_ICON={'元旦':'🎊','小年夜':'🏮','除夕':'🏮','春節':'🧧','春節補假':'🧧','和平紀念日':'🕊️',
  '兒童節':'🎈','兒童節補假':'🎈','兒童節+清明':'🌸','清明節':'🌿','清明節補假':'🌿',
  '勞動節':'💪','端午節':'🎋','中秋節':'🥮','國慶日':'🇹🇼'};

const SHIFTS=['凌晨','早上','下午','晚上'];
const SICON={凌晨:'🌙',早上:'☀️',下午:'🌸',晚上:'🌆'};

// ══ 工具 ══
function p2(n){return String(n).padStart(2,'0')}
function todayStr(){const t=new Date();return`${t.getFullYear()}-${p2(t.getMonth()+1)}-${p2(t.getDate())}`}
function parseD(s){const a=s.split('-');return new Date(+a[0],+a[1]-1,+a[2])}
function d2s(d){return`${d.getFullYear()}-${p2(d.getMonth()+1)}-${p2(d.getDate())}`}
function dim(y,m){return new Date(y,m+1,0).getDate()}
function calOff(y,m){const w=new Date(y,m,1).getDay();return w===0?6:w-1}
function isRed(k){return!!(TW_RED[k])}
function isWE(y,m,d){const w=new Date(y,m,d).getDay();return w===0||w===6}
function cln(s){return(s||'').split('\n').map(p=>p.replace(/[~]?\d{1,4}[-:~]\d{2,4}/g,'').replace('球球','').trim()).filter(Boolean).join('、')}

function toHHMM(raw){
  raw=raw.replace(/\D/g,'');
  if(!raw)return'??';
  if(raw.length<=2)return raw.padStart(2,'0')+':00';
  if(raw.length===3)return'0'+raw[0]+':'+raw.slice(1);
  return raw.slice(0,2)+':'+raw.slice(2,4);
}
function extractBallTime(shiftVal){
  if(!shiftVal)return null;
  const line=shiftVal.split('\n').find(l=>l.includes('球球'));if(!line)return null;
  const m=line.match(/球球\s*(\d{1,4})[-:](\d{1,4})/);if(!m)return null;
  return{start:toHHMM(m[1]),end:toHHMM(m[2])};
}

// ══ 靈性備用 ══
function fallbackSpirit(y,m){
  return`【💰 財運提醒】武曲化祿入財帛，${y}年${m+1}月財氣穩健 ⭐⭐⭐⭐\n【💕 感情提醒】天同星守感情宮，感情溫馨甜蜜 ⭐⭐⭐⭐\n【✨ 工作提醒】天府官祿宮穩固，工作表現亮眼 ⭐⭐⭐⭐⭐\n【🌙 整體運勢】紫微坐命氣場強大，這個月你的光芒會被看見 💕\n【🌅 早晨能量】深呼吸三次告訴自己今天一切順流，帶著感恩出門好事靠近 🌸\n【🌞 午間提醒】中午放空五分鐘讓身心充電，午後直覺特別靈敏 ✨\n【🌙 夜晚功課】睡前感謝今天三件事，把不愉快交給夜晚帶走 💤`;
}

// ══ 狀態 ══
const TODAY=todayStr();
let curTab='schedule';
let curWeekI=(()=>{for(let i=0;i<WEEKS.length;i++)if(WEEKS[i].dates.includes(TODAY))return i;return WEEKS.length-1})();
let selDate=WEEKS[curWeekI].dates.includes(TODAY)?TODAY:WEEKS[curWeekI].dates[0];
let calY=parseD(TODAY).getFullYear(),calM=parseD(TODAY).getMonth();
let spiritTxt='',spiritKey='',spLoading=false;
let events=(()=>{try{return JSON.parse(localStorage.getItem('bb_ev')||'[]')}catch{return[]}})();

// ══ 初始化 ══
(function(){
  const td=parseD(TODAY);
  document.getElementById('hdr-date').textContent=`${td.getFullYear()}/${td.getMonth()+1}/${td.getDate()}（週${['日','一','二','三','四','五','六'][td.getDay()]}）`;
  applyTheme('schedule');
  renderSchedule();
  checkBanners();
  // 浮動裝飾延遲
  ['0s','.3s','.6s','.9s','1.2s'].forEach((d,i)=>{
    const el=document.querySelectorAll('.float')[i];
    if(el)el.style.animationDelay=d;
  });
})();

function applyTheme(t){
  const T=TH[t];
  document.body.className='tab-'+t;
  // hdr
  document.getElementById('hdr').style.borderBottom=`3px dashed ${T.border}`;
  document.getElementById('hdr-logo').style.background=T.pill;
  document.getElementById('hdr-logo').style.webkitBackgroundClip='text';
  document.getElementById('hdr-logo').style.webkitTextFillColor='transparent';
  document.getElementById('hdr-date').style.color=T.accent2;
  document.getElementById('hdr-pet').textContent=T.hdrPet;
  // tabbar
  document.getElementById('tabbar').style.borderTopColor=T.border;
  // tab buttons
  ['schedule','calendar','planner'].forEach(id=>{
    const btn=document.getElementById('tb-'+id);
    if(id===t){btn.style.background=TH[id].pill;btn.style.color='#fff';btn.style.borderColor=TH[id].accent;}
    else{btn.style.background='none';btn.style.color=T.accent2;btn.style.borderColor='transparent';}
  });
}

function switchTab(t){
  curTab=t;
  ['schedule','calendar','planner'].forEach(id=>{
    document.getElementById('tab-'+id).style.display=id===t?'block':'none';
  });
  applyTheme(t);
  if(t==='calendar'){renderCalendar();if(!spiritTxt||spiritKey!==calY+'-'+calM)loadSpirit();}
  if(t==='planner')renderPlanner();
  window.scrollTo(0,0);
}

// ══ 班表 ══
function renderSchedule(){
  const T=TH.schedule,w=WEEKS[curWeekI],dd=DATA[selDate];
  const myS=dd?SHIFTS.filter(k=>dd[k]&&dd[k].includes('球球')):[];
  const mainS=myS[0]||null;
  const bt=mainS?extractBallTime(dd[mainS]):null;
  function pS(k){const o=['凌晨','早上','下午','晚上'],i=o.indexOf(k);for(let j=i-1;j>=0;j--){const v=dd&&dd[o[j]]||'';if(v&&!v.includes('球球'))return{key:o[j],val:v}}return null}
  function nS(k){const o=['凌晨','早上','下午','晚上'],i=o.indexOf(k);for(let j=i+1;j<o.length;j++){const v=dd&&dd[o[j]]||'';if(v&&!v.includes('球球'))return{key:o[j],val:v}}return null}
  const pv=mainS&&dd?pS(mainS):null,nx=mainS&&dd?nS(mainS):null;
  const earlyPpl=dd?cln(dd['早上']||''):'';

  let h=`<div class="deco-row">`;
  TH.schedule.deco.forEach((e,i)=>h+=`<span class="float" style="animation-delay:${i*.3}s">${e}</span>`);
  h+=`</div>`;

  // 週導航
  h+=`<div class="card" style="display:flex;align-items:center;justify-content:space-between;padding:10px 14px;border-color:${T.border};box-shadow:${T.shadow}">
    <div class="card-bar" style="background:${T.bar}"></div>
    <button class="nav-btn" style="border-color:${T.border};color:${T.accent}" onclick="prevW()">‹</button>
    <div style="text-align:center">
      <div style="font-family:'Nunito',sans-serif;font-weight:900;color:${T.accent};font-size:.9rem">第 ${curWeekI+1} 週</div>
      <div style="font-size:.65rem;color:${T.accent2}">${w.label}</div>
    </div>
    <button class="nav-btn" style="border-color:${T.border};color:${T.accent}" onclick="nextW()">›</button>
  </div>`;

  // 日期列
  h+=`<div class="days-row" style="grid-template-columns:repeat(${w.dates.length},1fr)">`;
  w.dates.forEach(d=>{
    const dt=parseD(d),act=d===selDate,isT=d===TODAY;
    const red=isRed(d)||isWE(dt.getFullYear(),dt.getMonth(),dt.getDate());
    const wdL=WD_LABEL[d]||'';
    const bg=act?T.pill:'rgba(255,255,255,.65)';
    const bc=isT&&!act?T.accent:red&&!act?T.border+'99':'rgba(255,255,255,.5)';
    h+=`<div class="day-pill${act?' active':''}" style="background:${bg};border-color:${bc}" onclick="selDay('${d}')">
      <div class="day-wd" style="color:${act?'rgba(255,255,255,.8)':red?T.accent:T.accent2}">${wdL}</div>
      <div class="day-num" style="color:${act?'#fff':red?T.accent:T.accent2+'99'}">${dt.getDate()}</div>
      ${isRed(d)?`<div class="day-red-dot" style="color:${act?'#fff':'#ff5060'}">●</div>`:''}
    </div>`;
  });
  h+=`</div>`;

  // 主卡
  h+=`<div class="card" style="border-color:${T.border};box-shadow:${T.shadow}"><div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>`;
  if(!myS.length){
    h+=`<div style="text-align:center;padding:24px 10px">
      <span class="off-kanji" style="color:${T.border};-webkit-text-stroke:2px ${T.accent};text-shadow:3px 3px 0 ${T.accent}44">休</span>
      <div style="font-weight:700;color:${T.accent2};margin-top:8px;font-size:.85rem;letter-spacing:.1em">✦ 好好休息唷 球球 ✦</div>
      <div style="font-size:1.8rem;margin-top:8px">🌸💤🛌</div>
    </div>`;
  } else {
    h+=`<div class="shift-main" style="background:linear-gradient(135deg,${T.border}33,${T.border}11);border-color:${T.border}">
      <div style="font-size:.63rem;font-weight:900;color:${T.accent2};letter-spacing:.18em;margin-bottom:3px">✦ 球球今天的班 ✦</div>
      <div style="font-family:'Nunito',sans-serif;font-size:1.2rem;font-weight:900;color:${T.accent}">${SICON[mainS]} ${mainS}班</div>
      ${bt?`<div class="shift-time-big" style="color:${T.accent}">${bt.start} ～ ${bt.end}</div>`:''}
      <div style="font-size:.7rem;color:${T.accent2};font-weight:700">⏰ 上班 ${bt?bt.start:'?'} ／ 下班 ${bt?bt.end:'?'}</div>
    </div>
    <div class="info-grid">
      <div class="info-box" style="border-color:${T.border}">
        <div class="info-icon">👩‍🏫</div>
        <div class="info-lbl" style="color:${T.accent2}">早班是誰</div>
        <div class="info-val" style="color:${earlyPpl?T.accent:'#ccc'}">${earlyPpl||'沒有早班'}</div>
      </div>
      <div class="info-box" style="border-color:${T.border}">
        <div class="info-icon">🔁</div>
        <div class="info-lbl" style="color:${T.accent2}">誰來接班</div>
        <div class="info-val" style="color:${nx?T.accent:'#ccc'}">${nx?cln(nx.val):'無接班'}</div>
      </div>
      <div class="info-box full" style="border-color:${T.border}">
        <div class="info-icon">🤝</div>
        <div class="info-lbl" style="color:${T.accent2}">我接誰的班</div>
        <div class="info-val" style="color:${pv?T.accent:'#ccc'}">${pv?`接 ${SICON[pv.key]} ${cln(pv.val)} 的班`:'今天第一班！'}</div>
      </div>
    </div>`;
  }
  h+=`</div>`;

  // 全日班表
  if(dd){
    h+=`<div class="card" style="border-color:${T.border};box-shadow:${T.shadow}"><div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>
    <div class="sec-lbl" style="color:${T.accent}">✦ 今日全班表</div>`;
    SHIFTS.forEach(k=>{
      const v=dd[k]||'';if(!v)return;
      const ppl=v.split('\n').map(p=>{
        const isMe=p.includes('球球');
        const nm=p.replace(/[~]?\d{1,4}[-:~]\d{2,4}/g,'').replace('~','').trim();
        return isMe?`<span class="me-tag" style="background:${T.accent}22;color:${T.accent}">🐾球球</span>`:`<span>${nm}</span>`;
      }).join('・');
      h+=`<div class="shift-row" style="border-color:${T.border}55">
        <div class="shift-dot" style="background:${T.accent}"></div>
        <div class="shift-slot" style="color:${T.accent2}">${SICON[k]} ${k}</div>
        <div class="shift-people" style="color:${T.accent}">${ppl}</div>
      </div>`;
    });
    h+=`</div>`;
  }
  document.getElementById('tab-schedule').innerHTML=h;
}
function selDay(d){selDate=d;renderSchedule()}
function prevW(){if(curWeekI>0){curWeekI--;selDate=WEEKS[curWeekI].dates[0];renderSchedule()}}
function nextW(){if(curWeekI<WEEKS.length-1){curWeekI++;selDate=WEEKS[curWeekI].dates[0];renderSchedule()}}

// ══ 日曆 ══
function renderCalendar(){
  const T=TH.calendar,days=dim(calY,calM),off=calOff(calY,calM);
  let grid='';
  for(let i=0;i<off;i++)grid+=`<div></div>`;
  for(let d=1;d<=days;d++){
    const k=`${calY}-${p2(calM+1)}-${p2(d)}`,hN=TW_RED[k],dow=new Date(calY,calM,d).getDay();
    const red=hN||dow===0||dow===6,isT=TODAY===k;
    const work=DATA[k]&&SHIFTS.some(s=>DATA[k][s]&&DATA[k][s].includes('球球'));
    const hasEvt=events.some(e=>e.date===k);
    const bg=isT?T.pill:hN?`${T.accent}22`:'rgba(255,255,255,.5)';
    const bc=isT?T.accent:red?T.border:'rgba(255,255,255,.3)';
    grid+=`<div class="cal-cell" style="background:${bg};border-color:${bc}">
      <div class="cal-num" style="color:${isT?'#fff':red?T.accent:T.accent2+'88'}">${d}</div>
      ${hN?`<div style="font-size:.5rem">${H_ICON[hN]||'🎌'}</div>`:''}
      ${work&&!hN?`<div style="font-size:.48rem;color:${isT?'#fff':T.accent}">🐾</div>`:''}
      ${hasEvt?`<div class="cal-dot" style="background:${isT?'#fff':'#60c080'}"></div>`:''}
    </div>`;
  }

  let hList='';
  for(let d=1;d<=days;d++){
    const k=`${calY}-${p2(calM+1)}-${p2(d)}`,hN=TW_RED[k];if(!hN)continue;
    const dow=['日','一','二','三','四','五','六'][new Date(calY,calM,d).getDay()];
    hList+=`<div class="holiday-row" style="background:${T.accent}11;border-color:${T.border}">
      <div style="font-size:1.2rem">${H_ICON[hN]||'🎌'}</div>
      <div><div style="font-weight:700;color:${T.accent};font-size:.8rem">${calM+1}/${d}（${dow}）</div><div style="font-size:.7rem;color:${T.accent2}">${hN}</div></div>
    </div>`;
  }

  const secs=parseSecs(spiritTxt);
  const zH=secs.ziwei.map(s=>`<div class="spirit-sec" style="border-color:${T.border}">
    <div class="spirit-title" style="color:${T.accent}">【${s.title}】</div>
    <div class="spirit-content" style="color:${T.accent2}">${s.content}</div>
  </div>`).join('');
  const aH=secs.akashic.map((s,i)=>{
    const bgs=['linear-gradient(135deg,rgba(255,240,200,.7),rgba(255,220,160,.5))','linear-gradient(135deg,rgba(200,210,255,.7),rgba(180,200,255,.5))','linear-gradient(135deg,rgba(210,240,255,.7),rgba(190,230,255,.5))'];
    const bds=[T.accent+'88',T.border,T.accent2+'88'];
    return`<div class="ak-box" style="background:${bgs[i]||bgs[0]};border-color:${bds[i]||bds[0]}">
      <div class="ak-title" style="color:${T.accent}">【${s.title}】</div>
      <div class="ak-content" style="color:${T.accent2}">${s.content}</div>
    </div>`;
  }).join('');

  document.getElementById('tab-calendar').innerHTML=`
  <div class="deco-row">${TH.calendar.deco.map((e,i)=>`<span class="float" style="animation-delay:${i*.3}s">${e}</span>`).join('')}</div>
  <div class="card" style="border-color:${T.border};box-shadow:${T.shadow}">
    <div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:12px">
      <button class="nav-btn" style="border-color:${T.border};color:${T.accent}" onclick="calPrev()">‹</button>
      <div style="font-family:'Nunito',sans-serif;font-weight:900;color:${T.accent};font-size:1rem">🌸 ${calY}年 ${calM+1}月</div>
      <button class="nav-btn" style="border-color:${T.border};color:${T.accent}" onclick="calNext()">›</button>
    </div>
    <div class="cal-grid" style="margin-bottom:3px">${['一','二','三','四','五','六','日'].map((d,i)=>`<div style="font-size:.58rem;font-weight:700;color:${i>=5?T.accent:T.accent2};text-align:center;padding:2px 0">${d}</div>`).join('')}</div>
    <div class="cal-grid">${grid}</div>
    <div style="display:flex;gap:10px;flex-wrap:wrap;margin-top:8px;font-size:.6rem;font-weight:700">
      <span style="color:${T.accent}">🔴 假日</span><span style="color:${T.accent2}">🐾 上班</span><span style="color:#60c080">● 行程</span>
    </div>
  </div>
  ${hList?`<div class="card" style="border-color:${T.border};box-shadow:${T.shadow}"><div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div><div class="sec-lbl" style="color:${T.accent}">🎌 ${calM+1}月節假日</div>${hList}</div>`:''}
  <div class="card" style="border-color:${T.border};box-shadow:${T.shadow};background:linear-gradient(135deg,${T.border}22,${T.accent2}11)">
    <div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>
    <div class="sec-lbl" style="color:${T.accent}">🔮 紫微命盤 ${calY}年${calM+1}月</div>
    ${spLoading?`<div style="text-align:center;padding:18px;color:${T.accent}"><span class="spin" style="font-size:2rem">🌀</span><div style="font-size:.8rem;margin-top:6px">連線宇宙能量中...</div></div>`:''}
    ${zH}
    <button class="spirit-btn" style="background:${spLoading?'rgba(200,200,220,.5)':T.pill}" onclick="loadSpirit()" ${spLoading?'disabled':''}>
      ${spLoading?'🌀 連線中...':'✨ 重新連線宇宙能量'}
    </button>
  </div>
  <div class="card" style="border-color:${T.border};box-shadow:${T.shadow};background:linear-gradient(135deg,${T.accent2}11,${T.border}22)">
    <div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>
    <div class="sec-lbl" style="color:${T.accent}">🌟 阿卡西靈性建議</div>
    ${aH||`<div style="text-align:center;color:${T.border};font-size:.8rem;padding:12px">點上方按鈕連線後顯示 🌸</div>`}
  </div>`;
}
function calPrev(){calM--;if(calM<0){calM=11;calY--;}spiritTxt='';spiritKey='';renderCalendar();loadSpirit();}
function calNext(){calM++;if(calM>11){calM=0;calY++;}spiritTxt='';spiritKey='';renderCalendar();loadSpirit();}

// ══ AI 靈性 ══
async function loadSpirit(){
  const key=calY+'-'+calM;
  spLoading=true;renderCalendar();
  const prompt=`你是球球（1990年3月8日凌晨02:44台灣時間出生）的靈性導師。繁體中文，溫柔可愛語氣，多用emoji。命盤：紫微坐命子宮，水二局，財帛武曲化祿，官祿天府，感情天同。針對${calY}年${calM+1}月給七段（【】標題空行分隔）：【💰 財運提醒】一句+⭐評分 【💕 感情提醒】一句+⭐評分 【✨ 工作提醒】一句+⭐評分 【🌙 整體運勢】二句 【🌅 早晨能量】二句 【🌞 午間提醒】二句 【🌙 夜晚功課】二句`;
  try{
    const r=await fetch('https://api.anthropic.com/v1/messages',{
      method:'POST',
      headers:{'Content-Type':'application/json','x-api-key':'在這裡填入API_KEY','anthropic-version':'2023-06-01','anthropic-dangerous-direct-browser-access':'true'},
      body:JSON.stringify({model:'claude-sonnet-4-20250514',max_tokens:700,messages:[{role:'user',content:prompt}]})
    });
    const data=await r.json();
    const txt=(data.content||[]).map(c=>c.type==='text'?c.text:'').join('').trim();
    spiritTxt=txt||fallbackSpirit(calY,calM);spiritKey=key;
  }catch{spiritTxt=fallbackSpirit(calY,calM);spiritKey=key;}
  spLoading=false;renderCalendar();
}
function parseSecs(txt){
  if(!txt)return{ziwei:[],akashic:[]};
  const z=[],a=[],ak=['早晨','午間','夜晚'];
  txt.split(/\n(?=【)/).forEach(p=>{
    const m=p.match(/【(.+?)】([\s\S]*)/);if(!m)return;
    const t=m[1].trim(),c=m[2].trim();
    (ak.some(k=>t.includes(k))?a:z).push({title:t,content:c});
  });
  return{ziwei:z,akashic:a};
}

// ══ 行程 ══
function saveEvts(){try{localStorage.setItem('bb_ev',JSON.stringify(events))}catch{}}
function addEvt(){
  const d=document.getElementById('ev-d').value,ti=document.getElementById('ev-t').value,ttl=document.getElementById('ev-ttl').value;
  if(!d||!ttl)return;
  events.push({id:Date.now(),date:d,time:ti,title:ttl,done:false});
  events.sort((a,b)=>a.date.localeCompare(b.date));
  saveEvts();renderPlanner();checkBanners();
}
function delEvt(id){events=events.filter(e=>e.id!==id);saveEvts();renderPlanner();checkBanners();}
function togDone(id){const e=events.find(e=>e.id===id);if(e)e.done=!e.done;saveEvts();renderPlanner();}

function evRow(e,showChk=true){
  const T=TH.planner;
  const isPast=e.date<TODAY,isT=e.date===TODAY;
  const cls='event-row'+(e.done?' done-row':'');
  const chk=showChk
    ?`<div class="check-circle${e.done?' checked':''}" style="border-color:${e.done?'#60c080':T.border}" onclick="togDone(${e.id})">${e.done?'<span style="color:#fff;font-size:.7rem;font-weight:900">✓</span>':''}</div>`
    :`<div style="font-size:1rem">🗓️</div>`;
  return`<div class="${cls}" style="border-color:${e.done?'#80d0a0':T.border}">${chk}
    <div style="flex:1">
      <div class="ev-title" style="color:${e.done?'#60a070':isPast?'#b090a0':T.accent};text-decoration:${e.done?'line-through':'none'}">${e.time?e.time+' ':''}${e.title}</div>
      <div class="ev-meta" style="color:${T.accent2}">${e.date}${isT?' 今天':''}${e.done?' ✨完成':''}</div>
    </div>
    <button class="del-btn" style="color:${T.border}" onclick="delEvt(${e.id})">✕</button>
  </div>`;
}

function renderPlanner(){
  const T=TH.planner;
  const tmrStr=d2s(new Date(parseD(TODAY).getTime()+86400000));
  const todayEvs=events.filter(e=>e.date===TODAY);
  const tmrEvs=events.filter(e=>e.date===tmrStr);
  let h=`<div class="deco-row">${TH.planner.deco.map((e,i)=>`<span class="float" style="animation-delay:${i*.3}s">${e}</span>`).join('')}</div>`;

  if(tmrEvs.length)h+=`<div class="card" style="border-color:${T.accent};border-width:2.5px;box-shadow:${T.shadow};background:linear-gradient(135deg,${T.accent}22,${T.border}33)">
    <div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>
    <div class="sec-lbl" style="color:${T.accent}">🔔 明天的提醒</div>${tmrEvs.map(e=>evRow(e,false)).join('')}</div>`;

  if(todayEvs.length)h+=`<div class="card" style="border-color:${T.border};box-shadow:${T.shadow}">
    <div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>
    <div class="sec-lbl" style="color:${T.accent}">⭐ 今天的行程</div>${todayEvs.map(e=>evRow(e)).join('')}</div>`;

  h+=`<div class="card" style="border-color:${T.border};box-shadow:${T.shadow}">
    <div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>
    <div class="sec-lbl" style="color:${T.accent}">📝 新增行程</div>
    <div class="inp-grid">
      <div><div class="inp-lbl" style="color:${T.accent2}">📅 日期 *</div><input class="inp" type="date" id="ev-d" style="border-color:${T.border};color:${T.accent}"></div>
      <div><div class="inp-lbl" style="color:${T.accent2}">⏰ 時間</div><input class="inp" type="time" id="ev-t" style="border-color:${T.border};color:${T.accent}"></div>
    </div>
    <div style="margin-bottom:10px"><div class="inp-lbl" style="color:${T.accent2}">🌸 事情 *</div><input class="inp" type="text" id="ev-ttl" placeholder="例：看醫生、約朋友…" style="border-color:${T.border};color:${T.accent}"></div>
    <button class="add-btn" style="background:${T.pill}" onclick="addEvt()">🌸 加入行程</button>
  </div>
  <div class="card" style="border-color:${T.border};box-shadow:${T.shadow}">
    <div style="position:absolute;top:0;left:0;right:0;height:3px;background:${T.bar}"></div>
    <div class="sec-lbl" style="color:${T.accent}">📋 所有行程</div>
    ${events.length?events.map(e=>evRow(e)).join(''):`<div class="empty-hint" style="color:${T.border}">還沒有行程，快加入吧 🌸</div>`}
  </div>`;
  document.getElementById('tab-planner').innerHTML=h;
}

// ══ 橫幅 ══
function checkBanners(){
  const now=new Date(),tmr=d2s(new Date(now.getTime()+86400000)),bs=[];
  events.filter(e=>e.date===tmr).forEach(e=>bs.push({...e,type:'tomorrow'}));
  events.filter(e=>{
    if(e.date!==TODAY||!e.time)return false;
    const[h,m]=e.time.split(':').map(Number);
    const diff=(h*60+m)-(now.getHours()*60+now.getMinutes());
    return diff>0&&diff<=60;
  }).forEach(e=>bs.push({...e,type:'soon'}));
  const el=document.getElementById('banners');
  el.innerHTML=bs.map(b=>`<div class="banner ${b.type}">
    <div style="font-size:1.2rem">${b.type==='soon'?'⏰':'🔔'}</div>
    <div style="flex:1">
      <div style="font-weight:900;color:#fff;font-size:.82rem">${b.type==='soon'?'⚡ 一小時後！':'🌸 明天有行程'}</div>
      <div style="color:rgba(255,255,255,.9);font-size:.74rem">${b.time?b.time+' ':''}${b.title}</div>
    </div>
    <button class="banner-close" onclick="this.parentElement.remove();updateHdr()">✕</button>
  </div>`).join('');
  updateHdr();
}
function updateHdr(){
  const n=document.getElementById('banners').children.length;
  document.getElementById('hdr').style.top=n*46+'px';
}
</script>
</body>
</html>
