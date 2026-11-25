# english-notes 語塊筆記

使用者（多益 860）每讀一本書開一個 Claude 對話，分段貼英文文章，Claude 挑出語塊、進階單字、句型。**每次回覆一段後，立即把該段同步到這個網站。** 請用繁體中文回覆，精簡、適合手機閱讀（使用者用 Android 的 Brave）。使用者沒有電腦，請直接修改並推送。

- 網站：https://xiu1468.github.io/english-notes/ （GitHub Pages，`main` 分支根目錄，推送後 1～2 分鐘更新）
- 推送：`git -c push.negotiate=true push origin HEAD:main`

## 檔案

| 檔案 | 用途 |
|---|---|
| `index.html` | 整個網站（純 HTML/JS，無依賴）。書架 → 書 → 分段列表；可勾選、搜尋、篩選、展開，底部按鈕複製勾選的編號（如 `2, 4, 9, 13`）。 |
| `books/<YYYY-MM-DD>-<slug>.json` | 一本書（＝一個對話）一個檔。 |
| `books.json` | 書架索引，由 `tools/vocab_index.py` 產生，不要手改。 |
| `tools/vocab_index.py` | 檢查每本書編號連續、欄位齊全，並重建 `books.json`。 |

## 資料格式

```
{id, title, created, segments: [{seg, top: [推薦編號], items: [
  {n, cat, term, ipa, pos, meaning, usage, example, trans}
]}]}
```
- `cat`：`chunk`（語塊與搭配詞）、`word`（進階單字）、`pattern`（句型）
- 語塊的 `ipa` 只標關鍵字，寫成 `swoop /swuːp/`；句型的 `ipa`、`pos` 留空字串
- 編號整本書連續，不重來

## 新增一段

1. 在該書 JSON 的 `segments` 後面加一筆（新書就新建檔案，`segments` 從第 1 段開始）
2. `python3 tools/vocab_index.py`
3. commit、推送

## 其他

- 書名可在網頁上改，但只存在該瀏覽器 localStorage（`vocabTitle:<id>`）；使用者要求改書名時改 JSON 的 `title`。勾選存在 `vocabSel:<id>`。
- 網頁抓 JSON 時加 `?t=` 避開快取。
- 網站沒有刪除功能。使用者要求刪除某本書時，刪掉 `books/<檔名>.json`，跑 `tools/vocab_index.py` 後推送。
- 做 Anki 卡片時，從書的 JSON 取使用者給的編號：卡片不顯示編號；正面 term、ipa、pos；背面 meaning、usage、example、trans；支援夜間模式（`.nightMode`／`.night_mode`）。
- 測試：`python3 -m http.server` + Playwright（`/usr/bin/google-chrome`，390×844），淺色／深色都截圖檢查。
