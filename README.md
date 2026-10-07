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

### BetterSlack パネルから

1. `/Applications/BetterSlack.app` から Slack を起動する
2. Slack で `⌘⇧M` → BetterSlack パネル → **Themes** → **Browse**
3. 「Install from a GitHub URL」に貼る

```
https://github.com/t-tajiri/betterslack-hide-slack-ai-plugin
```

4. **Read it** → Install → enable

### git clone から

自分で編集しながら使う場合はこちら。

```bash
git clone git@github.com:t-tajiri/betterslack-hide-slack-ai-plugin.git \
  ~/.betterslack/mods/themes/hide-slackbot-rail
```

**clone 先のディレクトリ名は `hide-slackbot-rail` にする。** BetterSlack はフォルダ名と `mod.json` の `id` が一致しない mod を読み込まない。リポジトリ名のまま clone すると無視される。

symlink も使えない。BetterSlack の mod 走査は `dirent.isDirectory()` で判定していて、symlink は false になる。実体を直接置く。

そのあと Slack 側で有効化する。

1. `/Applications/BetterSlack.app` から Slack を起動する
2. Slack で `⌘⇧M` → BetterSlack パネル
3. **Browse** → `sidebar` タグ → `Hide Slackbot Rail Tab` → Install → enable

Slack を `Slack.app` から直接起動すると BetterSlack が注入されず、この mod も効かない。

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
