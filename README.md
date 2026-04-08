# 💕 熙 & 雅 戀愛日記 APP

粉色+天空藍漸層的台灣版戀愛行事曆日記，專屬於熙和雅的甜蜜記錄空間！

## ✨ 功能介紹

| 頁面 | 功能說明 |
|------|----------|
| 🏥 班表 | 顯示球球今天的班次、接班/交班人員、完整班表 |
| 📅 日曆 | 台灣2026年節假日、懶人心情記錄（左右兩邊分別記錄） |
| 🗺️ 行程表 | 分隔男女雙方行程、懶人多選標籤 |
| 💕 紀念日 | 週年天數計算、生日倒數🎂、多個紀念日管理 |
| 🔄 更新班表 | 每月貼上 Google 試算表連結自動同步 |

## 🚀 GitHub Pages 上架步驟

### 步驟一：建立 Supabase 資料庫
1. 登入 [Supabase](https://supabase.com)
2. 進入你的 Project → 點選左側 **SQL Editor**
3. 貼上 `supabase_schema.sql` 的全部內容
4. 按 **Run** 執行建立所有資料表

### 步驟二：上傳到 GitHub
1. 新建一個 GitHub repository（例如：`love-diary`）
2. 上傳 `index.html` 到 repository 根目錄
3. 進入 **Settings → Pages**
4. Source 選 **Deploy from a branch**
5. Branch 選 **main**，資料夾選 **/ (root)**
6. 按 **Save**，等約 1 分鐘後即可訪問！

### 步驟三：訪問 APP
網址格式：`https://你的GitHub帳號.github.io/love-diary/`

## 📊 資料庫結構

### `mood_records` 心情記錄
| 欄位 | 類型 | 說明 |
|------|------|------|
| date | DATE | 日期（唯一） |
| his_mood | TEXT | 熙的心情 emoji |
| her_mood | TEXT | 雅的心情 emoji |
| his_acts | JSONB | 熙的活動陣列 |
| her_acts | JSONB | 雅的活動陣列 |
| both_acts | JSONB | 一起的活動陣列 |
| weather | TEXT | 天氣 emoji |

### `anniversaries` 紀念日
| 欄位 | 類型 | 說明 |
|------|------|------|
| name | TEXT | 紀念日名稱 |
| date | DATE | 日期 |
| emoji | TEXT | 圖示 |
| type | TEXT | anniversary/birthday/other |

### `trips` 行程表
| 欄位 | 類型 | 說明 |
|------|------|------|
| date | DATE | 行程日期 |
| name | TEXT | 行程名稱 |
| owner | TEXT | his/her/both |
| types | JSONB | 類型標籤陣列 |

### `schedule_data` 班表資料
| 欄位 | 類型 | 說明 |
|------|------|------|
| date | DATE | 日期（唯一） |
| data | JSONB | 完整班表 JSON |

### `sheet_urls` 試算表連結紀錄
| 欄位 | 類型 | 說明 |
|------|------|------|
| month | TEXT | 月份（2026-01 格式，唯一） |
| url | TEXT | Google Sheets 連結 |

## 📋 Google 試算表格式建議

為了讓「更新班表」功能自動解析，試算表格式建議如下：

| 日期 | 姓名 | 班別 |
|------|------|------|
| 01/01 | 球球 | 早 |
| 01/01 | 小美 | 中 |

班別代碼：早/白/中/晚/大夜/休/例/國

## 💡 注意事項
- 試算表需設定為「任何人可以查看」才能自動同步
- Supabase 免費方案每月 500MB 儲存、50,000 次請求
- 建議定期備份 Supabase 資料

---
Made with 💕 for 熙 & 雅
