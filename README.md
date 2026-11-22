# ShiryouKaidoku ステータスページ

ShiryouKaidoku の稼働状況・障害履歴・メンテナンス予定を掲載する公開ページ。
アプリ本体とは**別の場所（GitHub Pages）で動く**ため、アプリが使えない間でも
利用者が状況を確認できます。

## 構成（どこで動いているか）

| 項目 | 内容 |
|---|---|
| ホスティング | **GitHub Pages**（このリポジトリの `main` ブランチのルートをそのまま配信） |
| URL | `https://<アカウント名>.github.io/<リポジトリ名>/` |
| 中身 | `index.html` 1枚＋ JSON 3個（`status.json` / `incidents.json` / `maintenance.json`）。ビルド不要 |
| 更新方法 | JSON を書き換えて `main` へ push するだけ。1〜2分でページに反映 |

## 各ファイル

- `index.html` — ページ本体。中の JavaScript が同じ場所の JSON を読んで表示を組み立てる（JSON駆動）
- `status.json` — 今の状態。`overall`（全体）と `components`（サービスごと）の状態
- `incidents.json` — 障害履歴。新しいもの順に並べる
- `maintenance.json` — 予定メンテナンス

## 状態の値（status.json の `status` / `overall`）

| 値 | 意味 |
|---|---|
| `operational` | 正常 |
| `degraded` | 遅い・一部不調 |
| `outage` | 止まっている |
| `maintenance` | メンテナンス中 |

## 障害が起きたときの更新手順

1. `status.json` の `overall` と該当 `components[].status` を `outage`（または `degraded`）へ、
   `updated_at` を今の日時へ直す。必要なら `note` に一言書く
2. `incidents.json` の `incidents` の先頭へ追加:

   ```json
   {
       "title": "AIチャットが使えない",
       "started_at": "2026-11-22T10:30:00+09:00",
       "ended_at": null,
       "is_resolved": false,
       "description": "現在調査中です。"
   }
   ```

3. `main` へ push する（1〜2分で公開ページに反映）
4. 復旧したら `status.json` を `operational` へ戻し、incident の
   `ended_at` と `is_resolved: true` を入れて push

## ローカルでの見え方の確認

```sh
cd このフォルダ
python3 -m http.server 8000
# ブラウザで http://localhost:8000 を開く
```

（file:// で直接開くとブラウザの制約で JSON が読めないため、簡易サーバー経由で見る）
