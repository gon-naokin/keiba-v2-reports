# keiba-v2-reports

v2序列モデルによる競馬予想サマリーの公開ページです。

- 公開ページ: GitHub Pages（`index.html`）
- **閲覧にはパスワードが必要です**（StatiCrypt による AES-256 クライアントサイド暗号化）
- 現在の掲載内容: 2026年11月15日（福島・東京・京都）の予想サマリー
- 本ページはツール出力の機械整理であり、的中・回収を保証するものではありません

## 更新方法

平文ソース（`src/index.html`、リポジトリには含まれません）を編集し、
StatiCrypt で暗号化して `index.html` を差し替え、main に push すると反映されます。

```bash
cd src
npx -y staticrypt@3 index.html -d .. -p "<閲覧用パスワード>" --remember 30
```
