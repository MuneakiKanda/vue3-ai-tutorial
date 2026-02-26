## 第4章: テンプレート構文

> **読了時間目安**: 35分
> **難易度**: ★★☆☆☆

---

### この章で学ぶこと

この章を終えると、以下のことができるようになります。

- `v-bind`（`:` 省略記法）で HTML 属性を動的にバインドできる
- `v-if` / `v-else-if` / `v-else` で条件に応じた表示の切り替えができる
- `v-show` との違いを理解し、適切に使い分けられる
- `v-for` と `:key` を使ってリストを描画できる

---

### 前提

この章を読む前に、以下が完了している必要があります。

- 第2章で環境構築が完了していること
- 第3章で SFC の構造と マスタッシュ構文 `{{ }}` を理解していること

---

### ディレクティブとは？

#### なぜディレクティブが必要なのか

第3章では、マスタッシュ構文 `{{ }}` を使ってテキストを表示しました。しかし、Web アプリケーションでは「テキストの表示」だけでは足りません。

- 画像の `src` 属性を動的に変えたい
- ある条件のときだけ要素を表示したい
- 配列のデータを一覧で表示したい

こういった「HTML に動的な振る舞いを付ける」ために、Vue は **ディレクティブ（Directive）** という仕組みを提供しています。

#### ディレクティブの基本形

**ディレクティブ** とは、HTML 要素に付ける **`v-` で始まる特別な属性** のことです。

```html
<要素 v-ディレクティブ名="値">
```

Vue が提供する主要なディレクティブを紹介します。

| ディレクティブ | 役割 | 省略記法 |
|---|---|---|
| `v-bind` | HTML 属性を動的にバインドする | `:` |
| `v-if` | 条件に応じて要素を表示/非表示にする | なし |
| `v-show` | 条件に応じて要素の表示を切り替える（CSS） | なし |
| `v-for` | 配列やオブジェクトを繰り返し描画する | なし |
| `v-on` | イベントをリスニングする（第6章で学習） | `@` |
| `v-model` | 双方向データバインディング（第6章で学習） | なし |

> この章では `v-bind`、`v-if` 系、`v-show`、`v-for` の4つを学びます。`v-on` と `v-model` は第6章で扱います。

---

### 属性バインディング — `v-bind`

#### マスタッシュ構文が使えない場所

マスタッシュ構文 `{{ }}` はテキストの中でしか使えません。HTML 属性の値には使えないのです。

```html
<!-- ❌ これはエラー — 属性値にマスタッシュ構文は使えない -->
<img src="{{ imageUrl }}" />
```

HTML 属性に動的な値を入れるには、**`v-bind`** ディレクティブを使います。

#### `v-bind` の書き方

`v-bind` は、HTML 属性の値を JavaScript の変数や式に **バインド（結びつけ）** します。

```html
<!-- 基本形 -->
<img v-bind:src="imageUrl" />

<!-- ✅ 省略記法（こちらを推奨） -->
<img :src="imageUrl" />
```

`v-bind:属性名` は **`:属性名`** と省略できます。このチュートリアルでは、常に省略記法を使います。

> **バインド（Bind）** とは「結びつける」という意味です。`v-bind` は「HTML 属性と JavaScript の値を結びつける」ディレクティブです。

---

### 条件付きレンダリング — `v-if` と `v-show`

#### 条件に応じて表示を切り替える

Web アプリでは「ログイン中なら名前を表示」「在庫切れなら注意書きを出す」のように、条件によって表示内容を変えたい場面が頻繁にあります。

Vue では2つの方法で条件付き表示ができます。

| ディレクティブ | 仕組み | 非表示のとき |
|---|---|---|
| `v-if` | DOM 要素ごと追加/削除する | HTML がまったく存在しない |
| `v-show` | CSS の `display` を切り替える | HTML は存在するが非表示 |

#### `v-if` / `v-else-if` / `v-else`

`v-if` は条件が `true` のときだけ要素を **DOM に描画** します。`false` のときは HTML 自体が存在しません。

```html
<p v-if="score >= 80">優秀です</p>
<p v-else-if="score >= 60">合格です</p>
<p v-else>もう少し頑張りましょう</p>
```

`v-else-if` と `v-else` は、**直前の `v-if`（または `v-else-if`）の要素と隣接** していなければなりません。間に別の要素を挟むとエラーになります。

#### `v-show`

`v-show` は要素の CSS `display` プロパティを切り替えます。`false` のときも DOM 上には存在し続けます。

```html
<p v-show="isVisible">この文は表示/非表示が切り替わります</p>
```

#### `v-if` と `v-show` の使い分け

| 基準 | `v-if` を使う | `v-show` を使う |
|---|---|---|
| 切り替え頻度 | めったに切り替えない | 頻繁に切り替える |
| 初期コスト | `false` なら描画しない（軽い） | 常に描画する |
| 切り替えコスト | DOM の追加/削除（やや重い） | CSS の切り替え（軽い） |

**判断の目安**: 頻繁にトグル（ON/OFF）するなら `v-show`、それ以外は `v-if` を使いましょう。

---

### ハンズオン

実際にコードを書いて動かしてみましょう。このハンズオンでは、**商品リスト** を表示するアプリを段階的に作ります。

#### ステップ 1: `v-bind` で画像とリンクを動的に設定する

まず、`v-bind`（`:` 省略記法）を使って、HTML 属性に変数の値をバインドしてみましょう。

`src/App.vue`

```vue
<script setup lang="ts">
const siteName: string = 'Vue3 ショップ'
const logoUrl: string = 'https://vuejs.org/images/logo.png'
const siteUrl: string = 'https://vuejs.org/'
</script>

<template>
  <div class="shop-header">
    <img :src="logoUrl" :alt="siteName + 'のロゴ'" class="logo" />
    <h1>{{ siteName }}</h1>
    <a :href="siteUrl" target="_blank">Vue.js 公式サイト</a>
  </div>
</template>

<style scoped>
.shop-header {
  text-align: center;
  padding: 24px;
  font-family: sans-serif;
}

.logo {
  width: 80px;
  height: 80px;
}

h1 {
  color: #42b883;
  margin: 12px 0;
}

a {
  color: #35495e;
}
</style>
```

- **何をしているか**: `:src`、`:alt`、`:href` で3つの HTML 属性に変数の値を結びつけています。
- **なぜそう書くのか**: 画像のパスやリンク先が固定値ではなく、データから動的に決まることを示すためです。実際のアプリでは API から取得したデータを使うことになります。
- **注目ポイント**: `:alt="siteName + 'のロゴ'"` のように、バインドの値には JavaScript の式（文字列結合）も使えます。

#### ステップ 2: `v-if` / `v-else` で条件分岐する

商品の在庫状態に応じて表示を切り替えます。

`src/App.vue`

```vue
<script setup lang="ts">
const productName: string = 'Vue3 入門書'
const price: number = 2980
const inStock: boolean = true
const stockCount: number = 3
</script>

<template>
  <div class="product">
    <h2>{{ productName }}</h2>
    <p>価格: {{ price.toLocaleString() }}円</p>

    <p v-if="stockCount > 5" class="stock-many">
      在庫あり（残り {{ stockCount }} 個）
    </p>
    <p v-else-if="stockCount > 0" class="stock-few">
      残りわずか（残り {{ stockCount }} 個）
    </p>
    <p v-else class="stock-none">
      在庫切れ
    </p>

    <button v-if="inStock" class="buy-button">
      カートに入れる
    </button>
    <button v-else class="buy-button disabled" disabled>
      購入できません
    </button>
  </div>
</template>

<style scoped>
.product {
  max-width: 400px;
  margin: 40px auto;
  padding: 24px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-family: sans-serif;
}

h2 {
  color: #2c3e50;
  margin-bottom: 8px;
}

.stock-many {
  color: #27ae60;
  font-weight: bold;
}

.stock-few {
  color: #e67e22;
  font-weight: bold;
}

.stock-none {
  color: #e74c3c;
  font-weight: bold;
}

.buy-button {
  margin-top: 16px;
  padding: 10px 24px;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
}

.buy-button.disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
```

- **何をしているか**: `stockCount` の値に応じて3つのメッセージを出し分けています。また、`inStock` の値でボタンの状態を切り替えています。
- **なぜそう書くのか**: `v-if` → `v-else-if` → `v-else` の連鎖で、JavaScript の `if / else if / else` と同じ感覚で条件分岐ができます。
- **注目ポイント**: `v-else-if` と `v-else` は、**直前の `v-if`（または `v-else-if`）の要素の直後** に書く必要があります。間に別の要素を挟むと正しく動きません。

#### ステップ 3: `v-show` との違いを確認する

`v-if` と `v-show` の違いを体験してみましょう。

`src/App.vue`

```vue
<script setup lang="ts">
const showWithIf: boolean = false
const showWithShow: boolean = false
</script>

<template>
  <div class="comparison">
    <h2>v-if vs v-show の比較</h2>

    <div class="box">
      <h3>v-if (false のとき)</h3>
      <p v-if="showWithIf">v-if で表示しています</p>
      <p class="note">↑ ブラウザの開発者ツールで確認すると HTML が存在しません</p>
    </div>

    <div class="box">
      <h3>v-show (false のとき)</h3>
      <p v-show="showWithShow">v-show で表示しています</p>
      <p class="note">↑ ブラウザの開発者ツールで確認すると HTML は存在しますが display: none です</p>
    </div>
  </div>
</template>

<style scoped>
.comparison {
  max-width: 500px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

.box {
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 16px;
}

h2 {
  color: #2c3e50;
  margin-bottom: 16px;
}

h3 {
  color: #42b883;
  margin-bottom: 8px;
}

.note {
  color: #888;
  font-size: 0.85rem;
  font-style: italic;
}
</style>
```

- **何をしているか**: 両方 `false` に設定して、`v-if` と `v-show` の非表示時の違いを確認しています。
- **なぜそう書くのか**: ブラウザの開発者ツール（F12 → Elements タブ）で DOM を確認することで、違いを実際に目で見て理解するためです。
- **注目ポイント**: `v-if="false"` の要素は DOM に存在しませんが、`v-show="false"` の要素は `<p style="display: none;">` として DOM に残っています。

> **確認方法**: ブラウザで F12 を押して開発者ツールを開き、Elements タブで HTML を確認してみましょう。

#### ステップ 4: `v-for` で商品リストを表示する

いよいよ `v-for` を使って、配列のデータをリストとして描画します。

`src/App.vue`

```vue
<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
  inStock: boolean
}

const products: Product[] = [
  { id: 1, name: 'Vue3 入門書', price: 2980, inStock: true },
  { id: 2, name: 'TypeScript ハンドブック', price: 3480, inStock: true },
  { id: 3, name: 'Vite 実践ガイド', price: 2680, inStock: false },
  { id: 4, name: 'Pinia 状態管理入門', price: 1980, inStock: true },
]
</script>

<template>
  <div class="product-list">
    <h1>商品一覧</h1>
    <div
      v-for="product in products"
      :key="product.id"
      class="product-card"
    >
      <h2>{{ product.name }}</h2>
      <p class="price">{{ product.price.toLocaleString() }}円</p>
      <p v-if="product.inStock" class="in-stock">在庫あり</p>
      <p v-else class="out-of-stock">在庫切れ</p>
    </div>
  </div>
</template>

<style scoped>
.product-list {
  max-width: 500px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
}

.product-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 12px;
}

.product-card h2 {
  color: #35495e;
  font-size: 1.1rem;
  margin-bottom: 8px;
}

.price {
  font-size: 1.2rem;
  font-weight: bold;
  color: #2c3e50;
}

.in-stock {
  color: #27ae60;
  font-weight: bold;
}

.out-of-stock {
  color: #e74c3c;
  font-weight: bold;
}
</style>
```

- **何をしているか**: `v-for="product in products"` で `products` 配列の各要素を1つずつ `product` という名前で取り出し、カードとして描画しています。
- **なぜそう書くのか**: 配列データを一覧表示するのは、Web アプリで最も頻繁に行う操作の1つです。`v-for` を使えば、配列の要素数に応じて自動的に繰り返し描画されます。
- **注目ポイント**: `:key="product.id"` は **必須** です。Vue が各要素を識別するために使います。key の役割は次のステップで詳しく説明します。

#### ステップ 5: `v-for` に `:key` が必要な理由を理解する

`:key` はなぜ必要なのでしょうか。`v-for` に `:key` を付けないと、Vue は「どの要素がどのデータに対応するか」を正確に把握できません。

`:key` の役割を、ロッカー（靴箱）にたとえて考えてみましょう。

```
キーなし: 番号のないロッカー
→ 中身が変わったとき、どのロッカーを更新すべきか分からない
→ とりあえず全部開けて確認する（非効率）

キーあり: 番号付きのロッカー
→ 「3番のロッカーの中身が変わった」と特定できる
→ 3番だけ更新すれば良い（効率的）
```

`:key` のルール は以下のとおりです。

- **一意（ユニーク）な値** を指定する（配列内で重複しない）
- **安定した値** を使う（`id` やデータベースの主キーなど）
- **配列のインデックスは避ける**（要素の追加・削除でインデックスがずれるため）

```html
<!-- ✅ 推奨: 一意な id を使う -->
<div v-for="product in products" :key="product.id">

<!-- ❌ 非推奨: インデックスを使う -->
<div v-for="(product, index) in products" :key="index">
```

#### ステップ 6: すべてを組み合わせた商品リストアプリ

これまで学んだ `v-bind`、`v-if`、`v-for` をすべて使った、完成版の商品リストを作りましょう。

`src/App.vue`

```vue
<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
  image: string
  inStock: boolean
  category: string
}

const shopName: string = 'Vue3 Book Store'
const products: Product[] = [
  {
    id: 1,
    name: 'Vue3 入門書',
    price: 2980,
    image: 'https://via.placeholder.com/80x100/42b883/ffffff?text=Vue3',
    inStock: true,
    category: 'フレームワーク',
  },
  {
    id: 2,
    name: 'TypeScript ハンドブック',
    price: 3480,
    image: 'https://via.placeholder.com/80x100/3178c6/ffffff?text=TS',
    inStock: true,
    category: '言語',
  },
  {
    id: 3,
    name: 'Vite 実践ガイド',
    price: 2680,
    image: 'https://via.placeholder.com/80x100/646cff/ffffff?text=Vite',
    inStock: false,
    category: 'ツール',
  },
  {
    id: 4,
    name: 'Pinia 状態管理入門',
    price: 1980,
    image: 'https://via.placeholder.com/80x100/ffd859/333333?text=Pinia',
    inStock: true,
    category: 'ライブラリ',
  },
]
</script>

<template>
  <div class="shop">
    <h1>{{ shopName }}</h1>
    <p class="subtitle">商品数: {{ products.length }}冊</p>

    <div
      v-for="product in products"
      :key="product.id"
      class="product-card"
      :class="{ 'sold-out': !product.inStock }"
    >
      <img
        :src="product.image"
        :alt="product.name + 'の表紙'"
        class="product-image"
      />
      <div class="product-info">
        <span class="category">{{ product.category }}</span>
        <h2>{{ product.name }}</h2>
        <p class="price">{{ product.price.toLocaleString() }}円</p>
        <p v-if="product.inStock" class="stock in-stock">在庫あり</p>
        <p v-else class="stock out-of-stock">在庫切れ</p>
      </div>
    </div>

    <p v-if="products.length === 0" class="empty">商品がありません。</p>
  </div>
</template>

<style scoped>
.shop {
  max-width: 560px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #42b883;
  margin-bottom: 4px;
}

.subtitle {
  color: #888;
  margin-bottom: 24px;
}

.product-card {
  display: flex;
  gap: 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 12px;
  transition: box-shadow 0.2s;
}

.product-card:hover {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.product-card.sold-out {
  opacity: 0.5;
}

.product-image {
  border-radius: 4px;
  flex-shrink: 0;
}

.product-info {
  flex: 1;
}

.category {
  display: inline-block;
  background-color: #f0f0f0;
  color: #666;
  font-size: 0.75rem;
  padding: 2px 8px;
  border-radius: 4px;
  margin-bottom: 4px;
}

.product-info h2 {
  color: #2c3e50;
  font-size: 1.1rem;
  margin: 4px 0 8px;
}

.price {
  font-size: 1.2rem;
  font-weight: bold;
  color: #2c3e50;
  margin-bottom: 4px;
}

.stock {
  font-weight: bold;
  font-size: 0.9rem;
}

.in-stock {
  color: #27ae60;
}

.out-of-stock {
  color: #e74c3c;
}

.empty {
  text-align: center;
  color: #888;
  padding: 40px 0;
}
</style>
```

- **何をしているか**: `v-bind`（`:src`、`:alt`、`:class`）、`v-for`（商品リストの繰り返し）、`v-if`/`v-else`（在庫状態の分岐）を組み合わせて、実用的な商品一覧を表示しています。
- **なぜそう書くのか**: 実際の Web アプリでは、これらのディレクティブを組み合わせて使うのが基本です。1つずつ学んだ要素を実践的に統合する経験が大切です。
- **注目ポイント**: `:class="{ 'sold-out': !product.inStock }"` は **オブジェクト構文** と呼ばれる `:class` の書き方です。条件に応じて CSS クラスを動的に追加できます。`product.inStock` が `false` のとき `sold-out` クラスが付きます。

---

### コード全体

ここまでのハンズオンで作成したコードの最終形をまとめます。

`src/App.vue`

```vue
<script setup lang="ts">
interface Product {
  id: number
  name: string
  price: number
  image: string
  inStock: boolean
  category: string
}

const shopName: string = 'Vue3 Book Store'
const products: Product[] = [
  {
    id: 1,
    name: 'Vue3 入門書',
    price: 2980,
    image: 'https://via.placeholder.com/80x100/42b883/ffffff?text=Vue3',
    inStock: true,
    category: 'フレームワーク',
  },
  {
    id: 2,
    name: 'TypeScript ハンドブック',
    price: 3480,
    image: 'https://via.placeholder.com/80x100/3178c6/ffffff?text=TS',
    inStock: true,
    category: '言語',
  },
  {
    id: 3,
    name: 'Vite 実践ガイド',
    price: 2680,
    image: 'https://via.placeholder.com/80x100/646cff/ffffff?text=Vite',
    inStock: false,
    category: 'ツール',
  },
  {
    id: 4,
    name: 'Pinia 状態管理入門',
    price: 1980,
    image: 'https://via.placeholder.com/80x100/ffd859/333333?text=Pinia',
    inStock: true,
    category: 'ライブラリ',
  },
]
</script>

<template>
  <div class="shop">
    <h1>{{ shopName }}</h1>
    <p class="subtitle">商品数: {{ products.length }}冊</p>

    <div
      v-for="product in products"
      :key="product.id"
      class="product-card"
      :class="{ 'sold-out': !product.inStock }"
    >
      <img
        :src="product.image"
        :alt="product.name + 'の表紙'"
        class="product-image"
      />
      <div class="product-info">
        <span class="category">{{ product.category }}</span>
        <h2>{{ product.name }}</h2>
        <p class="price">{{ product.price.toLocaleString() }}円</p>
        <p v-if="product.inStock" class="stock in-stock">在庫あり</p>
        <p v-else class="stock out-of-stock">在庫切れ</p>
      </div>
    </div>

    <p v-if="products.length === 0" class="empty">商品がありません。</p>
  </div>
</template>

<style scoped>
.shop {
  max-width: 560px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #42b883;
  margin-bottom: 4px;
}

.subtitle {
  color: #888;
  margin-bottom: 24px;
}

.product-card {
  display: flex;
  gap: 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 12px;
  transition: box-shadow 0.2s;
}

.product-card:hover {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.product-card.sold-out {
  opacity: 0.5;
}

.product-image {
  border-radius: 4px;
  flex-shrink: 0;
}

.product-info {
  flex: 1;
}

.category {
  display: inline-block;
  background-color: #f0f0f0;
  color: #666;
  font-size: 0.75rem;
  padding: 2px 8px;
  border-radius: 4px;
  margin-bottom: 4px;
}

.product-info h2 {
  color: #2c3e50;
  font-size: 1.1rem;
  margin: 4px 0 8px;
}

.price {
  font-size: 1.2rem;
  font-weight: bold;
  color: #2c3e50;
  margin-bottom: 4px;
}

.stock {
  font-weight: bold;
  font-size: 0.9rem;
}

.in-stock {
  color: #27ae60;
}

.out-of-stock {
  color: #e74c3c;
}

.empty {
  text-align: center;
  color: #888;
  padding: 40px 0;
}
</style>
```

> **動作確認**: `npm run dev` でブラウザに表示される結果を確認してください。

**期待される表示結果:**

```
┌─────────────────────────────────────────────┐
│                                             │
│  Vue3 Book Store                            │
│  商品数: 4冊                                │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │ [Vue3]  フレームワーク              │    │
│  │         Vue3 入門書                 │    │
│  │         2,980円                     │    │
│  │         在庫あり                    │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │ [TS]    言語                        │    │
│  │         TypeScript ハンドブック     │    │
│  │         3,480円                     │    │
│  │         在庫あり                    │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │ [Vite]  ツール          ← 半透明   │    │
│  │         Vite 実践ガイド             │    │
│  │         2,680円                     │    │
│  │         在庫切れ                    │    │
│  └─────────────────────────────────────┘    │
│                                             │
│  ┌─────────────────────────────────────┐    │
│  │ [Pinia] ライブラリ                  │    │
│  │         Pinia 状態管理入門          │    │
│  │         1,980円                     │    │
│  │         在庫あり                    │    │
│  └─────────────────────────────────────┘    │
│                                             │
└─────────────────────────────────────────────┘
```

---

### 解説

コードの重要な部分を詳しく解説します。

#### `v-for` の構文と TypeScript の型定義

```typescript
interface Product {
  id: number
  name: string
  price: number
  image: string
  inStock: boolean
  category: string
}

const products: Product[] = [...]
```

```html
<div v-for="product in products" :key="product.id">
```

`v-for="product in products"` の構文は「`products` 配列の中の各要素を、`product` という名前で取り出す」という意味です。JavaScript の `for...of` ループに似ています。

TypeScript で `interface` を定義しておくと、テンプレートの中でも `product.name` などのプロパティに対して **エディタの補完** が効くようになります。データの構造を明示することは、コードの品質向上に直結します。

> `v-for` では配列以外に、数値範囲（`v-for="n in 5"`）やオブジェクトのプロパティ（`v-for="(value, key) in object"`）を繰り返すこともできます。

#### 動的クラスバインディング（`:class` のオブジェクト構文）

```html
<div
  :class="{ 'sold-out': !product.inStock }"
  class="product-card"
>
```

`:class` に **オブジェクト** を渡す構文です。オブジェクトの「キー」がクラス名、「値」が `true` / `false` の条件を表します。

| `product.inStock` | `!product.inStock` | 適用されるクラス |
|---|---|---|
| `true` | `false` | `product-card` のみ |
| `false` | `true` | `product-card sold-out` |

通常の `class` 属性と `:class` は **共存できます**。Vue が自動的にマージします。

#### `v-if` と `v-for` の組み合わせ

```html
<div v-for="product in products" :key="product.id">
  <p v-if="product.inStock" class="stock in-stock">在庫あり</p>
  <p v-else class="stock out-of-stock">在庫切れ</p>
</div>
```

`v-for` で繰り返される各要素の **中** で `v-if` を使うことは問題ありません。ただし、**`v-for` と `v-if` を同じ要素に書く** ことは推奨されません。

```html
<!-- ❌ 非推奨: 同じ要素に v-for と v-if を書く -->
<div v-for="product in products" v-if="product.inStock" :key="product.id">

<!-- ✅ 推奨: v-for の中の子要素で v-if を使う -->
<template v-for="product in products" :key="product.id">
  <div v-if="product.inStock">
```

`v-if` は `v-for` より優先度が高いため、同じ要素に書くと `product` がまだ定義されていない状態で `v-if` が評価され、エラーになります。

---

### よくある間違い・FAQ

初心者がつまずきやすいポイントをまとめます。

#### ❌ よくある間違い 1: `v-for` に `:key` を付け忘れる

**間違ったコード:**

```vue
<template>
  <div v-for="product in products" class="product-card">
    <h2>{{ product.name }}</h2>
  </div>
</template>
```

**何が問題か:**

`:key` がないと、Vue は各要素を効率的に追跡できません。リストの要素が追加・削除・並び替えされたとき、予期しない表示の崩れが起きる可能性があります。また、開発ツールで警告が表示されます。

**正しいコード:**

```vue
<template>
  <div v-for="product in products" :key="product.id" class="product-card">
    <h2>{{ product.name }}</h2>
  </div>
</template>
```

#### ❌ よくある間違い 2: `v-else` の前に関係のない要素を挟む

**間違ったコード:**

```vue
<template>
  <p v-if="isLoggedIn">ようこそ</p>
  <hr />
  <p v-else>ログインしてください</p>
</template>
```

**何が問題か:**

`v-if` と `v-else` の間に `<hr />` が挟まっているため、Vue は `v-else` がどの `v-if` に対応するか判断できません。コンパイルエラーになります。

**正しいコード:**

```vue
<template>
  <p v-if="isLoggedIn">ようこそ</p>
  <p v-else>ログインしてください</p>
  <hr />
</template>
```

#### ❌ よくある間違い 3: 属性にマスタッシュ構文を使う

**間違ったコード:**

```vue
<script setup lang="ts">
const imageUrl: string = 'https://example.com/photo.jpg'
</script>

<template>
  <img src="{{ imageUrl }}" />
</template>
```

**何が問題か:**

HTML 属性の中では `{{ }}` は使えません。`{{ imageUrl }}` がそのまま文字列として解釈され、画像が表示されません。

**正しいコード:**

```vue
<script setup lang="ts">
const imageUrl: string = 'https://example.com/photo.jpg'
</script>

<template>
  <img :src="imageUrl" />
</template>
```

> テキスト表示は `{{ }}`、属性のバインドは `:属性名` と覚えましょう。

#### 💬 FAQ

**Q: `v-bind:class` と通常の `class` 属性は同時に使えますか？**

A: はい、同時に使えます。Vue が自動的にマージします。`class="product-card"` で固定のクラスを指定し、`:class="{ active: isActive }"` で動的なクラスを追加するのが一般的なパターンです。

**Q: `v-for` で配列のインデックスを取得できますか？**

A: はい、`v-for="(product, index) in products"` と書くことで第2引数としてインデックス（0始まり）を取得できます。ただし、`:key` にインデックスを使うのは推奨しません。要素の追加・削除でインデックスが変わるためです。

**Q: `v-for` で数値の範囲を繰り返すことはできますか？**

A: はい、`v-for="n in 5"` と書くと `n` が `1` から `5` まで繰り返されます。星の数だけ要素を表示するなど、決まった回数の繰り返しに便利です。ただし **1始まり** であることに注意してください（0始まりではありません）。

---

### 練習問題

学んだ内容を定着させるための練習問題です。

#### 練習 1: メンバー一覧を表示しよう

**課題:**

以下のメンバーデータを `v-for` で一覧表示してください。各メンバーの `isActive` が `true` のときは「アクティブ」、`false` のときは「休止中」と表示してください。

```typescript
interface Member {
  id: number
  name: string
  role: string
  isActive: boolean
}

const members: Member[] = [
  { id: 1, name: '田中太郎', role: 'フロントエンド', isActive: true },
  { id: 2, name: '鈴木花子', role: 'バックエンド', isActive: true },
  { id: 3, name: '佐藤次郎', role: 'デザイナー', isActive: false },
]
```

**ヒント:**

`v-for` で配列を繰り返し、各メンバーの中で `v-if` / `v-else` を使って状態を出し分けましょう。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
interface Member {
  id: number
  name: string
  role: string
  isActive: boolean
}

const members: Member[] = [
  { id: 1, name: '田中太郎', role: 'フロントエンド', isActive: true },
  { id: 2, name: '鈴木花子', role: 'バックエンド', isActive: true },
  { id: 3, name: '佐藤次郎', role: 'デザイナー', isActive: false },
]
</script>

<template>
  <div class="member-list">
    <h1>メンバー一覧</h1>
    <div
      v-for="member in members"
      :key="member.id"
      class="member-card"
      :class="{ inactive: !member.isActive }"
    >
      <h2>{{ member.name }}</h2>
      <p>役割: {{ member.role }}</p>
      <p v-if="member.isActive" class="active">アクティブ</p>
      <p v-else class="paused">休止中</p>
    </div>
  </div>
</template>

<style scoped>
.member-list {
  max-width: 500px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 16px;
}

.member-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 12px;
}

.member-card.inactive {
  opacity: 0.5;
}

.member-card h2 {
  font-size: 1.1rem;
  margin-bottom: 4px;
}

.active {
  color: #27ae60;
  font-weight: bold;
}

.paused {
  color: #e67e22;
  font-weight: bold;
}
</style>
```

**解説:**

`v-for` で `members` 配列を繰り返し、各メンバーの `isActive` プロパティに応じて `v-if` / `v-else` で状態表示を切り替えています。`:class` のオブジェクト構文で、非アクティブなメンバーに `inactive` クラスを適用して半透明にしています。

</details>

#### 練習 2: 動的な画像リンクを作ろう

**課題:**

以下の画像データを使って、画像ギャラリーを作ってください。各画像を `v-for` で表示し、`:src`、`:alt`、`:href` を使ってすべての属性を動的にバインドしてください。

```typescript
interface Photo {
  id: number
  title: string
  url: string
  link: string
}

const photos: Photo[] = [
  { id: 1, title: 'Vue.js', url: 'https://via.placeholder.com/150/42b883/ffffff?text=Vue', link: 'https://vuejs.org/' },
  { id: 2, title: 'TypeScript', url: 'https://via.placeholder.com/150/3178c6/ffffff?text=TS', link: 'https://www.typescriptlang.org/' },
  { id: 3, title: 'Vite', url: 'https://via.placeholder.com/150/646cff/ffffff?text=Vite', link: 'https://vite.dev/' },
]
```

**ヒント:**

`<a>` タグの `:href` と `<img>` タグの `:src`、`:alt` を組み合わせましょう。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
interface Photo {
  id: number
  title: string
  url: string
  link: string
}

const photos: Photo[] = [
  { id: 1, title: 'Vue.js', url: 'https://via.placeholder.com/150/42b883/ffffff?text=Vue', link: 'https://vuejs.org/' },
  { id: 2, title: 'TypeScript', url: 'https://via.placeholder.com/150/3178c6/ffffff?text=TS', link: 'https://www.typescriptlang.org/' },
  { id: 3, title: 'Vite', url: 'https://via.placeholder.com/150/646cff/ffffff?text=Vite', link: 'https://vite.dev/' },
]
</script>

<template>
  <div class="gallery">
    <h1>Tech Gallery</h1>
    <div class="photo-grid">
      <a
        v-for="photo in photos"
        :key="photo.id"
        :href="photo.link"
        target="_blank"
        class="photo-item"
      >
        <img :src="photo.url" :alt="photo.title" />
        <p>{{ photo.title }}</p>
      </a>
    </div>
  </div>
</template>

<style scoped>
.gallery {
  max-width: 600px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
  text-align: center;
}

h1 {
  color: #2c3e50;
  margin-bottom: 24px;
}

.photo-grid {
  display: flex;
  gap: 16px;
  justify-content: center;
  flex-wrap: wrap;
}

.photo-item {
  text-decoration: none;
  color: #333;
  text-align: center;
}

.photo-item img {
  border-radius: 8px;
  transition: transform 0.2s;
}

.photo-item img:hover {
  transform: scale(1.05);
}

.photo-item p {
  margin-top: 8px;
  font-weight: bold;
}
</style>
```

**解説:**

`<a>` タグに `:href="photo.link"` でリンク先を、`<img>` タグに `:src="photo.url"` と `:alt="photo.title"` でそれぞれ動的に値を設定しています。`v-for` で配列を繰り返して、3つの画像を並べています。

</details>

#### 練習 3: 星の数をレンダリングしよう

**課題:**

`v-for="n in 5"` の「数値範囲の繰り返し」を使って、5つの★を表示してください。`rating` という変数（1〜5の整数）を定義し、`rating` 以下の★を黄色（`#f1c40f`）で、それより上の★をグレー（`#ddd`）で表示してください。

**ヒント:**

`v-for="n in 5"` で `n` は 1 から 5 になります。`n <= rating` かどうかで色を切り替えましょう。`:style` を使って `color` を動的に変えることができます。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
const rating: number = 3
</script>

<template>
  <div class="rating-display">
    <h1>レビュー評価</h1>
    <div class="stars">
      <span
        v-for="n in 5"
        :key="n"
        class="star"
        :style="{ color: n <= rating ? '#f1c40f' : '#ddd' }"
      >
        ★
      </span>
    </div>
    <p>{{ rating }} / 5</p>
  </div>
</template>

<style scoped>
.rating-display {
  max-width: 300px;
  margin: 40px auto;
  padding: 24px;
  text-align: center;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 16px;
}

.stars {
  font-size: 2rem;
  margin-bottom: 8px;
}

.star {
  cursor: default;
}

p {
  color: #666;
}
</style>
```

**解説:**

`v-for="n in 5"` で `n` が 1〜5 の数値を順に取ります。`:style` のオブジェクト構文で、`n <= rating` のとき黄色、それ以外はグレーを `color` に設定しています。`:style` は `:class` と同様に、動的にインラインスタイルを適用できるディレクティブです。

</details>

---

### まとめ

この章では以下の内容を学びました。

| 学んだこと | ポイント |
|---|---|
| `v-bind`（`:` 省略記法） | HTML 属性に JavaScript の値をバインドする。テキスト表示は `{{ }}`、属性は `:属性名` |
| `v-if` / `v-else-if` / `v-else` | 条件に応じて DOM 要素を追加/削除する。`v-else` は直前の `v-if` と隣接させる |
| `v-show` | CSS の `display` で表示/非表示を切り替える。頻繁なトグルに向く |
| `v-for` + `:key` | 配列やオブジェクトを繰り返し描画する。`:key` には一意な値を指定する |

**キーワード**: `v-bind`, `:（省略記法）`, `v-if`, `v-else`, `v-show`, `v-for`, `:key`, `:class（オブジェクト構文）`

---

### 次の章へ

この章では、データを画面に「表示する」ためのテンプレート構文を学びました。しかし、現在のコードではデータがすべて固定値です。ボタンを押しても値は変わりませんし、ユーザーの操作に応答することもできません。次の第5章では、Vue3 の核心である **リアクティビティ** を学びます。`ref` を使って「値が変わったら画面も自動で更新される」仕組みを理解し、本当にインタラクティブなアプリの作り方を身につけましょう。
