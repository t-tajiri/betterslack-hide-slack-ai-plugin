# hide-slackbot-rail

Slack デスクトップアプリの左ナビゲーションレールから Slackbot（AI）タブを隠す [BetterSlack](https://github.com/AirOne-dev/BetterSlack) の theme mod。

CSS 1行だけ。JavaScript は実行しない。

```css
[data-qa='tab_rail_slackbot_button'] { display: none !important; }
```

## なぜ必要か

Slack にはレールのタブを選ぶ標準設定がある（レールを右クリック → Customize tabs）。そこで外せるならこの mod は不要。選べない項目が残る場合に、表示だけ CSS で落とす。

Slack.app 本体は一切変更しない。BetterSlack が Chrome DevTools Protocol 経由でレンダラに CSS を注入する方式のため、Slack のアップデートで壊れない。

## 導入

BetterSlack を先に入れておく（`AirOne-dev/BetterSlack` の `install.sh`）。

### git clone から（推奨）

```bash
git clone https://github.com/t-tajiri/betterslack-hide-slack-ai-plugin.git \
  ~/.betterslack/mods/themes/hide-slackbot-rail
```

**clone 先のディレクトリ名は `hide-slackbot-rail` にする。** BetterSlack はフォルダ名と `mod.json` の `id` が一致しない mod を読み込まない。リポジトリ名のまま clone すると無視される。

symlink も使えない。BetterSlack の mod 走査は `dirent.isDirectory()` で判定していて、symlink は false になる。実体を直接置く。

そのあと Slack 側で有効化する。

1. `/Applications/BetterSlack.app` から Slack を起動する
2. Slack で `⌘⇧M` → BetterSlack パネル
3. **Browse** → `sidebar` タグ → `Hide Slackbot Rail Tab` → Install → enable

Slack を `Slack.app` から直接起動すると BetterSlack が注入されず、この mod も効かない。

### BetterSlack パネルの GitHub URL 欄から

パネルの「Install from a GitHub URL」にこの URL を貼る方法もある。

```
https://github.com/t-tajiri/betterslack-hide-slack-ai-plugin/tree/main
```

ただし **NAT 配下など外向き IP を共有する環境では失敗しやすい**。この機能は `api.github.com` を認証なしで叩くため、IP 単位 60 req/h の制限を他の端末と食い合う。枯渇すると次のどちらかが出る。

- `could not reach that repository` — default branch を解決する1回目の API で失敗
- `no mod.json there — point at the folder holding it` — ファイル一覧を取る2回目の API で失敗。mod.json は実際には存在する

URL に `/tree/main` を付けておくと1回目を省けるが、2回目は避けられない。どちらが出ても mod や URL の問題ではないので、clone を使う。`git clone` は `api.github.com` を経由しないためこの制限を受けない。

## 更新

clone して使っている場合。

```bash
git -C ~/.betterslack/mods/themes/hide-slackbot-rail pull
```

theme は保存した時点で再適用される。Slack の再起動は不要。

BetterSlack パネルの mod 自動更新は効かない。更新チェックは BetterSlack 本体リポジトリの `mods/registry.json` を照合する仕組みで、サードパーティのリポジトリに置いた mod は対象外。

## セレクタが効かなくなったとき

Slack が `data-qa` の値を変えた可能性がある。取り直す。

デスクトップアプリ側の DevTools は開けない場合があるので、ブラウザ版を使う。

1. Chrome で https://app.slack.com を開く
2. レールのタブを右クリック → 検証
3. Console で実行する

```js
[...document.querySelectorAll('[data-qa]')].map(e=>e.dataset.qa).filter(q=>/rail|tab-/i.test(q)).join('\n')
```

デスクトップ版とブラウザ版は同じ Web クライアントなので DOM は同一。得られた値を `theme.css` に反映する。

末尾にハッシュの付いたクラス名（`.circleButton__cMiUK` など）は使わない。CSS モジュール由来でビルドごとに変わる。`data-qa` は手書きの属性で安定している。

## アンインストール

```bash
rm -r ~/.betterslack/mods/themes/hide-slackbot-rail
```

BetterSlack ごと消す場合は `~/.betterslack` と `/Applications/BetterSlack.app` を削除する。Slack.app は無変更なので、これで元の状態に戻る。
