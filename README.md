# ARAM Mayhem 對戰資料收集器

幫助訓練「**看到這 10 隻，你有多少機率贏？**」的 AI 模型。

Riot 的公開 API 封鎖了 Mayhem (queueId 2400) 的對戰記錄。唯一取得方式是透過你電腦上的 League Client 本地 API。**每個人貢獻的資料越多，模型就越準確。**

---

## 你需要什麼

- ✅ Windows / macOS / Linux（有裝 League of Legends）
- ✅ Python 3.10 以上
- ✅ 會用 command line（Terminal / PowerShell）

---

## 步驟

### 第一步：安裝

```bash
git clone https://github.com/Lanternko/ARAM-mayhem-collector.git
cd ARAM-mayhem-collector
pip install -r requirements.txt
```

### 第二步：開 League Client 並登入

確認你已經登入遊戲大廳（不需要在遊戲中，只要 client 開著就好）。

### 第三步：執行收集器

```bash
python collect.py --platform TW2
```

> 換成你的伺服器：`KR` / `EUW1` / `NA1` / `JP1` / `TW2`

程式會自動從你的對戰記錄出發，往外爬取你對手和隊友的比賽資料（BFS 雪球爬蟲）。預計耗時 **10–30 分鐘**，結束後會產生 `my_games.parquet`。

你會看到這樣的輸出，代表正在收集：
```
[snowball] player 1/20000  depth=3  source=match  target_games=20  pending=850
  [saved] Mayhem  game_id=413476095  patch=16.9.772  total_saved=1
  [saved] Mayhem  game_id=413477201  patch=16.9.772  total_saved=2
...
[export] 3200 games → my_games.parquet
```

### 第四步：上傳資料

開一個 [GitHub Issue](https://github.com/Lanternko/ARAM-mayhem-collector/issues/new)，標題格式：

```
[Data] TW2 - 3200 games
```

然後把 `my_games.parquet` **直接拖進留言框**即可上傳。

---

## 常見問題

**Q：程式卡住了怎麼辦？**
直接 Ctrl+C 中斷，下次重跑會從上次繼續（資料不會遺失）。

**Q：要開多久？**
跑到程式自己結束為止（通常 20 分鐘內）。不用全程盯著。

**Q：我的資料安全嗎？**
`my_games.parquet` 裡只有**英雄 ID、勝負、遊戲時長、版本號**，不含任何帳號名稱或 ID。可以安心分享。

**Q：我不在 TW 伺服器怎麼辦？**
把 `--platform TW2` 換成你的伺服器代碼，其他步驟一樣。

**Q：我可以貢獻多次嗎？**
可以！隔一段時間重跑 `collect.py`，新的 Mayhem 場次會自動加進去。重複的比賽我們會自動去重。

---

## 進階選項

```bash
# 用更多 worker 加速（預設 4，建議最多 8）
python collect.py --workers 8 --platform TW2

# 查看目前收集狀況
python lcu_collector.py status

# 手動 export（如果你之前跑過）
python lcu_collector.py export --queue 2400 --out my_games.parquet --platform TW2
```

---

## 收到什麼資料格式

| 欄位 | 說明 |
|------|------|
| `match_id` | 全球唯一比賽 ID |
| `queue_id` | 2400 = Mayhem |
| `patch` | 版本，例如 `16.9.772` |
| `platform` | 伺服器，例如 `TW2` |
| `blue_champions` | 藍方英雄 ID 列表（升序排列）|
| `red_champions` | 紅方英雄 ID 列表（升序排列）|
| `blue_wins` | 藍方是否獲勝 |
| `duration_sec` | 遊戲時長（秒）|

---

## 這個專案在做什麼

用收集到的 Mayhem 對戰資料訓練神經網路，目標是輸入雙方 5v5 英雄組合，預測藍方獲勝機率。貢獻者越多、資料越多，模型才有意義。

---

*有問題或建議歡迎開 Issue。*
