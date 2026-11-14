# keiba-v2-reports

v2序列モデルによる競馬予想サマリーの公開ページです。

- 公開ページ: GitHub Pages（`index.html`=予想サマリー、`analysis.html`=解析ビジュアル）
- **閲覧にはパスワードが必要です**（StatiCrypt による AES-256 クライアントサイド暗号化）
- 現在の掲載内容: 2026年11月15日（福島・東京・京都）の予想サマリーと、
  出馬表・脚質/展開・コース適性・騎手相性の解析ビジュアル
- 本ページはツール出力の機械整理であり、的中・回収を保証するものではありません

## 更新方法

平文ソース（`src/*.html`、リポジトリには含まれません）を編集し、
StatiCrypt で暗号化してリポジトリ直下の同名ファイルを差し替え、main に push すると反映されます。

```bash
cd src
npx -y staticrypt@3 index.html analysis.html -d .. -p "<閲覧用パスワード>" --remember 30
```

解析ビジュアルは `src/tools/extract_analysis_1115.py` で yosou_tool.html の DATA と
jvlink.sqlite から JSON を抽出し、`src/tools/analysis_template.html` の `__DATA__` に
埋め込んで `src/analysis.html` を生成します（対象日を変える場合は TARGET/MD を書き換え）。
