# Topshell テーマ カスタマイズ リブートガイド

## 基本情報

| 項目 | 値 |
|---|---|
| プレビューテーマ ID | `157265199337` |
| ストア URL | `5aj9ns-tu.myshopify.com` |
| プレビュー確認 URL | `https://5aj9ns-tu.myshopify.com?preview_theme_id=157265199337` |
| テーマエディタ | `https://5aj9ns-tu.myshopify.com/admin/themes/157265199337/editor` |

## 作業ルール

1. **ソース・オブ・トゥルース**: テーマ ID `157265199337`（プレビューテーマ）をマスターとする
2. **同期方法**: `shopify theme push --theme 157265199337 --only <ファイル>` で個別 push
3. **変更禁止ファイル**: header, footer 関連アセットは上書き禁止

---

## 修正済み内容

### SP 商品ページ（3点）

#### 1. メイン画像 フルワイド表示
- `assets/base.css`（L4688 付近）
- `.product__media-wrapper` に `margin-left: -1.5rem; width: calc(100% + 3rem)` でスマホ画面幅いっぱいに表示
- slider-component の peek 効果ネガティブマージンを `0` で解除

#### 2. ズームボタン（虫眼鏡）
- `assets/base.css`（L4757 付近）
- `bottom: 0; transform: translateY(50%)` でメイン画像底辺に半分かかる位置
- ボタンサイズ: `42px × 42px`、虫眼鏡の円 `14px × 14px`、線幅 `2px`

#### 3. サムネイル タップ → メイン画像切り替え（SP）
- `assets/media-gallery.js`: `touchend` ハンドラ追加（10px 以内の移動をタップと判定）
- `assets/base.css`（L4730 付近）: `.product__media-list .product__media-item:not(.is-active) { display: none }` で is-active 方式に切り替え

---

### ホーム タイトル/サブタイトル 間隔統一

各セクションのタイトルとサブタイトルの間隔を統一（PC: 10px / SP: 8px）

| セクション | サブタイトル要素 | 対応状況 |
|---|---|---|
| CATEGORY | `.section-header__subtitle` | ✅ 完了 |
| FEATURE (multicolumn) | `.multicolumn__subtitle` | ✅ 完了 |
| BLOG (custom-liquid) | `.blog-section-wrapper .title-wrapper-with-link::after` | ✅ 完了 |
| NEW ARRIVALS | `.collection__description.subtitle.rte` | ❌ 諦め（別要因のため） |

---

## 未解決・積み残し

### NEW ARRIVALS と FEATURE の間隔が消えた問題

- **症状**: 「NEW ARRIVALS」の「全てを表示する」ボタンと「FEATURE」タイトルの間隔が消えている
- **原因推定**: NEW ARRIVALS のサブタイトル修正作業中に発生。`margin-bottom` が影響している可能性
- **試みた修正**: featured-collection.liquid・index.json の subtitle/description 変更 → リバート済み
- **現在状態**: まだ間隔が戻っていない。次セッションで再調査が必要

### FEATURE セクション CSS の テンプレートID が誤っている可能性

- **詳細**: base.css の FEATURE セクション CSS は `template--21260600934633__multicolumn_6e9cGq` を使用
- しかし、ホームページの実際のテンプレートIDは `20939988861161` の可能性が高い
  （根拠: 他のセクション、ライン 5866 の `.section-template--20939988861161__multicolumn_6e9cGq-padding` の記述）
- **影響**: FEATURE の `margin-top: 60px` 等の CSS が一切適用されていない可能性
- **ユーザー方針**: FEATURE CSS はリスクがあるためいじらない（新しい問題発生を防ぐため）

---

## 変更済みファイル一覧

| ファイル | 変更内容 |
|---|---|
| `assets/base.css` | メインCSS（全カスタム）|
| `assets/media-gallery.js` | サムネイルタッチハンドラ |
| `sections/multicolumn.liquid` | サブタイトル・メディアアスペクト比設定を追加（凍結） |
| `sections/featured-blog.liquid` | サブタイトル設定を追加 |
| `templates/index.json` | featured_collection の description を復元済み |

---

## 次セッションでやること

1. **NEW ARRIVALS ↔ FEATURE 間の隙間を復元する**
   - `#shopify-section-template--20939988861161__featured_collection` の `margin-bottom` を確認・調整
   - もしくは multicolumn_6e9cGq の `padding_top` を settings_data で増やす
2. FEATURE CSS テンプレートIDの正誤を確認（慎重に）

---

## よく使うコマンド

```bash
# ファイルを個別 push
shopify theme push --theme 157265199337 --only assets/base.css

# 複数ファイルを push
shopify theme push --theme 157265199337 --only assets/base.css sections/multicolumn.liquid

# リアルタイム同期（開発時）
shopify theme dev --theme 157265199337
```
