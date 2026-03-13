# Topshell テーマ カスタマイズ進捗メモ

## 作業ルール
1. **ソース・オブ・トゥルース**: テーマ ID `157265199337`（プレビューテーマ）をマスターとする
2. **同期方法**: `shopify theme dev --theme 157265199337` による自動同期を優先（手動 push は緊急時のみ）
3. **変更禁止ファイル**: header, footer, multicolumn 関連アセットは上書き禁止
   - `sections/header.liquid`
   - `sections/footer.liquid`
   - `sections/multicolumn.liquid` ← サブタイトル追加済みのため凍結
   - `assets/component-header.css`
   - `assets/component-footer.css`

---

## 現在の完了済み修正（SP商品ページ）

### 1. メイン画像 アスペクト比・幅
- **内容**: スマホ画面幅いっぱいにメイン画像を表示、オリジナルアスペクト比を維持
- **実装**: `assets/base.css` (L4688〜)
  - `.product__media-wrapper` に `margin-left: -1.5rem; width: calc(100% + 3rem)`
  - `slider-component` の peek 効果ネガティブマージンを `0` で解除
  - `.product-media-container.constrain-height .media` に `padding-top: var(--ratio-percent)` で強制

### 2. ズームボタン（虫眼鏡）
- **内容**: メイン画像下端中央にボタンを半分かけて配置、線を太く
- **実装**: `assets/base.css` (L4757〜)
  - `bottom: 0; transform: translateY(50%)` でメイン画像底辺に半分かかる位置
  - ボタンサイズ: `42px × 42px`（白い座布団）
  - 虫眼鏡の円: `14px × 14px`、線幅 `2px`
  - ハンドル: `8px × 2px`、`rotate(45deg)`

### 3. サムネイル タップ → メイン画像切り替え（SP）
- **内容**: サムネイルタップでメイン画像を即時切り替え（スクロール方式から `is-active` 方式へ変更）
- **実装ファイル**:
  - `assets/media-gallery.js`: `touchend` ハンドラ追加（10px 以内の移動をタップと判定）
  - `assets/base.css` (L4730〜): `.product__media-list .product__media-item:not(.is-active) { display: none }` で非表示切り替え
- **CSS 特異性の注意点**: セレクタを `.product__media-list .product__media-item:not(.is-active)` と (0,3,0) にすることで `.slider--mobile.grid--peek .grid__item` の (0,3,0) に勝てる（後に宣言されるため）

---

## セクション別 タイトル/サブタイトル スタイル

### 共通ルール（`assets/base.css` L5391〜）
| 要素 | font-size (PC) | font-size (SP) | font-weight | margin-top |
|---|---|---|---|---|
| サブタイトル全般 | 16px | 13px | 600 (PC) / 700 (SP) | 6px |

- CATEGORY / FEATURE / BLOG / NEW ARRIVALS すべて `margin-top: 6px` で統一済み

---

## 未対応・確認待ち
- [ ] サムネイルタップ切り替えの実機動作確認（最新 push 後）
- [ ] PC表示での全セクションレイアウト最終確認

---

## 対象ファイル一覧（変更済み）
| ファイル | 内容 |
|---|---|
| `assets/base.css` | メインCSS（全カスタム）|
| `assets/media-gallery.js` | サムネイルタッチハンドラ |
| `sections/multicolumn.liquid` | サブタイトル・メディアアスペクト比設定を追加 ← 凍結 |
| `sections/featured-blog.liquid` | サブタイトル設定を追加 |
