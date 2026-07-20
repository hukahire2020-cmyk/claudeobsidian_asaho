# 朝報AI（あさほ）

Obsidianの「朝刊ニュース日報」に人格を与え、Twitter風タイムラインとしてスクロール表示するデモアプリ（PWA対応）。

## ファイル構成

- `index.html` — 本体（フィード表示・データ埋め込み）
- `manifest.json` — PWA用マニフェスト（ホーム画面追加時のアイコン・アプリ名など）
- `sw.js` — オフライン表示用のservice worker（最小構成）
- `icons/`, `favicon-32.png`, `apple-touch-icon.png` — アプリアイコン

## デプロイ（GitHub Pages）

1. このフォルダの内容をリポジトリのルートにpush
2. GitHubリポジトリの **Settings → Pages** を開く
3. **Source**: `Deploy from a branch` / **Branch**: `main` / `/(root)` を選択して **Save**
4. 数十秒〜数分で `https://<ユーザー名>.github.io/<リポジトリ名>/` が公開される

## スマホでアプリ化

- **iPhone (Safari)**: 公開URLを開く → 共有アイコン → 「ホーム画面に追加」
- **Android (Chrome)**: 公開URLを開く → メニュー(⋮) → 「アプリをインストール」/「ホーム画面に追加」

## 更新のしかた

`index.html` 内の `posts` 配列・`SVGS` オブジェクトが日報1本分のデータ。新しい日報を追加する場合はこの配列に投稿を追記する。
