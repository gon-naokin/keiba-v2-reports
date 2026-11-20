# keiba-v2-reports

v2序列モデルによる競馬予想サマリーの公開ページです。

- 公開ページ: GitHub Pages（`index.html`=予想サマリー、`analysis.html`=解析ビジュアル）
- **閲覧にはパスワードが必要です**（StatiCrypt による AES-256 クライアントサイド暗号化）
- 現在の掲載内容: 2026年11月21日（福島・京都）の予想サマリーと、
  出馬表・脚質/展開・コース適性・騎手相性の解析ビジュアル
- 本ページはツール出力の機械整理であり、的中・回収を保証するものではありません

## 更新方法

平文ソース（`src/*.html`、リポジトリには含まれません）を編集し、
StatiCrypt で暗号化してリポジトリ直下の同名ファイルを差し替え、main に push すると反映されます。

```bash
cd src
npx -y staticrypt@3 index.html analysis.html -d .. -p "<閲覧用パスワード>" --remember 30
```

解析ビジュアルは `src/tools/extract_analysis.py YYYY-MM-DD` で yosou_tool.html の DATA と
jvlink.sqlite から JSON を抽出し、`src/tools/analysis_template.html` の `__DATA__`・
`__BUILT__`・`__RESULTS__` を置換して `src/analysis.html` を生成します。

## レース結果の反映

- 手動: 解析ページ各レースの「結果の記録」に1〜3着の馬番を入力して保存（ブラウザ保存）
- 自動: 確定結果を取り込んだ後、以下を実行して push すると、
  ページの「公式結果を一括取得」ボタンで全レースに反映されます

```bash
python src/tools/make_results.py 2026-11-21
git add results_1121.json
git commit -m "data: 結果更新"
git push
```

`results_*.json` は公開ファイルです（JRAの公式確定結果のみで、予想内容は含まれません）。
