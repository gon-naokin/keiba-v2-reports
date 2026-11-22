# keiba-v2-reports

v2序列モデルによる競馬予想サマリーの公開ページです。

- 公開ページ: GitHub Pages（`index.html`=予想サマリー、`analysis.html`=解析ビジュアル、
  `results.html`=結果まとめ）
- **閲覧にはパスワードが必要です**（StatiCrypt による AES-256 クライアントサイド暗号化）
- 現在の掲載内容: 2026年11月22日（東京・京都）の予想サマリーと、
  出馬表・脚質/展開・コース適性・騎手相性の解析ビジュアル
- 本ページはツール出力の機械整理であり、的中・回収を保証するものではありません

## 更新方法

平文ソース（`src/*.html`、リポジトリには含まれません）を編集し、
StatiCrypt で暗号化してリポジトリ直下の同名ファイルを差し替え、main に push すると反映されます。

```bash
cd src
npx -y staticrypt@3 index.html analysis.html results.html -d .. -p "<閲覧用パスワード>" --remember 30
```

解析ビジュアルと結果まとめは、次の順で平文ソースを生成します。

```bash
python src/tools/extract_analysis.py 2026-11-22        # yosou_tool.html と jvlink.sqlite から JSON を抽出
python src/tools/build_analysis.py 2026-11-22          # analysis_template.html -> src/analysis.html
python src/tools/build_results_page.py 2026-11-22 2026-11-21   # results_template.html -> src/results.html
```

`build_results_page.py` は指定した日付（先頭が初期表示）の予想を埋め込みます。
結果は公開後にページ側が `results_<MMDD>.json` を取得して突き合わせるため、
結果が確定するたびにページを作り直す必要はありません。

## レース結果の反映

- 手動: 解析ページ各レースの「結果の記録」に1〜3着の馬番を入力して保存（ブラウザ保存）
- 自動: 確定結果を取り込んだ後、以下を実行して push すると、
  解析ページの「公式結果を一括取得」ボタンで全レースに反映されます。
  結果まとめページは開くだけで自動取得し、未確定がある間は5分ごとに再取得します

```bash
python src/tools/make_results.py 2026-11-22
git add results_1122.json
git commit -m "data: 結果更新"
git push
```

`results_*.json` は公開ファイルです（JRAの公式確定結果のみで、予想内容は含まれません）。
レースキーごとに 1〜3着の馬番（`"1"`/`"2"`/`"3"`）、全馬の着順・人気・単勝オッズ（`ord`）、
式別の払戻（`pay`）を持ちます。

## ペースマップの着順線

解析ページで結果を記録すると、前走ペースマップに 1〜3着馬を通る最小二乗直線（着順線）を描き、
式 `y = ax + b`（x=前走PCI・y=前走脚質度）をコース署名の式と並べて表示します。
