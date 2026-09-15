# 豊中JC 人脈グラフ（静的版）

**公開URL: https://toyonakajc2027.github.io/jinmyaku/**
リポジトリ: https://github.com/toyonakajc2027/jinmyaku （JC名義 toyonakajc2027。2026-09-15 に jin1jfk で作成→移管。jin1jfk に push 権限あり）

2026年度のメンバー60名の人間関係（紹介・CL・趣味・部門・役職・業種・JC歴・誕生月）を D3.js で描く。
2027年度版に改良する前提の「一度置いた」もの。

## 構成（出欠アプリと同じ型）
- 画面: この `index.html` 1枚（GitHub Pages）。noindex。
- データ: jconnecttrust@gmail.com の Apps Script「豊中JC 人脈グラフ」＋スプレッドシート（メンバー／関係シート）。
  `index.html` の `API_URL`（GAS の /exec）へ `fetch(API_URL+'?fn=getDataForClient')` で取りに行く。
  `credentials:'omit'` なので複数Googleアカウントにログインしたブラウザでも開ける（GAS直配信の「ページが見つかりません」を回避）。
- 取れなかった時は前回の結果（localStorage `jcdata`）を出して「表示は前回のもの」と知らせる。
- Apps Script 直配信（google.script.run）でも動くよう、`fetchData()` が経路を切り替える。

## GAS 側（正本は `../【Apps Script】Code.gs`）
`doGet` に `?fn=` の JSON API 分岐がある。**Apps Script エディタに貼ったら「デプロイを管理 → 新しいバージョン → デプロイ」まで必要**（URLは不変）。
確認: `curl -sL "<API_URL>?fn=getDataForClient" | head -c 200` が `{"ok":true,"result":{"members":[...` で始まればOK。

## ローカルで見る
```
cd JCアプリ && python3 -m http.server 8791 --bind 127.0.0.1
open "http://127.0.0.1:8791/JC%E3%83%A1%E3%83%B3%E3%83%90%E3%83%BC/site/preview.html"
```
`preview.html` = `index.html` の本体 `<script>` の直前に `<script src="../mock.js">` を挟んだもの（架空40名）。**preview.html と mock.js は公開しない**（`.gitignore`）。

## 更新
```
cd site && git add -A && git commit -m "..." && git push origin main
```
反映は最大10分。

## 2027年度版にするとき
1. スプレッドシートを toyonakajc2027 側にコピーし、名簿を2027年度の委員会構成に入れ替える（`COMMITTEE_COLORS` の委員会名も合わせる）
2. GAS を toyonakajc2027 で新規作成（`Code.gs` の `openById` を新IDに）→ ウェブアプリとしてデプロイ（実行ユーザー: 自分／アクセス: 全員）
3. `index.html` の `API_URL` を差し替えて push
4. 出欠アプリ GAS のスクリプトプロパティ `GRAPH_URL` にこのURLを入れると、役員メニューに「人脈グラフを開く」が出る
