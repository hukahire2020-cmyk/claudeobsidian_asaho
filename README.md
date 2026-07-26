# 朝報AI（あさほ）

Obsidianの「朝刊ニュース日報」に人格を与え、Twitter風タイムラインとしてスクロール表示するデモアプリ（PWA対応）。

## ファイル構成

- `index.html` — 表示ロジックのみ（約23KB固定）。日ごとのデータは持たない
- `data/index.json` — 収録済みの日付一覧 `{ "days": ["2026-07-24", ...] }`
- `data/YYYY-MM-DD.json` — その日の投稿データ（1日あたり約14KB）
- `data/svg/<key>.svg` — インフォグラフィック1枚＝1ファイル
- `manifest.json` — PWA用マニフェスト
- `sw.js` — service worker（network-first）
- `icons/`, `favicon-32.png`, `apple-touch-icon.png` — アプリアイコン

## データ形式

`data/YYYY-MM-DD.json`:

```json
{
  "date": "2026-07-24",
  "posts": [
    {
      "t": "06:00",
      "thread": null,
      "tag": "1/3",
      "ref": "日報の見出しテキスト（一字一句一致）",
      "svg": "redsea_20260724",
      "talk": true,
      "text": "本文（**太字** と [表示名](URL) が使える）",
      "eng": [4, 2, 22, 940]
    }
  ]
}
```

| フィールド | 必須 | 内容 |
|---|---|---|
| `t` | ✅ | 投稿時刻 `HH:MM` |
| `text` | ✅ | 本文。`**太字**`、`[表示名](URL)`、★☆△ が使える |
| `eng` | ✅ | `[返信, リポスト, いいね, 表示回数]`（ダミー値） |
| `ref` | ✅ | 対応する日報の見出し。Obsidianディープリンクの解決に使う |
| `thread` | | `null` / `"start"` / `"mid"` / `"end"` |
| `tag` | | スレッドの `"1/3"` 表記 |
| `svg` | | `data/svg/<key>.svg` のキー。セクション先頭の1件だけ |
| `talk` | | `true` で💬商談トーク例カードとして表示 |

**SVGキーの命名規則: `<slug>_YYYYMMDD`**（例 `redsea_20260724`）。日をまたいだ衝突を防ぐため必須。
※ 2026-07-20 分のみ、分離前の名残でスラッグのみのキー（`iran` 等）になっている。

## 壊れにくさ

- `index.json` に載っているが JSON が無い日は、警告を出してスキップする（タイムライン全体は表示される）
- SVG が無い投稿は、図なしで本文だけ表示する
- `index.json` の内容を新しい順に並べ替えて描画するので、追記順は問わない

## 更新のしかた

新しい日を追加するには **`index.html` を触らない**：

1. `data/YYYY-MM-DD.json` を新規作成
2. 図があれば `data/svg/<slug>_YYYYMMDD.svg` を追加
3. `data/index.json` の `days` にその日付を追加
4. commit & push

自動更新は `asaho-feed-daily` スケジュールタスクが行う（`~/.claude/scheduled-tasks/asaho-feed-daily/SKILL.md`）。
台帳 `ops/state/ledger.json` の `feed: false` の日をすべて拾う方式なので、実行が遅れても取りこぼさない。

## ローカルで確認する

```
node ../ops/bin/serve.js . 8899
```

`data/*.json` を `fetch` で読むため、`file://` で直接開くと動作しない（HTTP経由で開くこと）。

## デプロイ（GitHub Pages）

1. このフォルダの内容をリポジトリのルートにpush
2. GitHubリポジトリの **Settings → Pages** を開く
3. **Source**: `Deploy from a branch` / **Branch**: `main` / `/(root)` を選択して **Save**
4. 数十秒〜数分で `https://<ユーザー名>.github.io/<リポジトリ名>/` が公開される

## スマホでアプリ化

- **iPhone (Safari)**: 公開URLを開く → 共有アイコン → 「ホーム画面に追加」
- **Android (Chrome)**: 公開URLを開く → メニュー(⋮) → 「アプリをインストール」/「ホーム画面に追加」
