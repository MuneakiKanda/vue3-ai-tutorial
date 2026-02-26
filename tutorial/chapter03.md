## 第3章: Vue アプリケーションの構造

> **読了時間目安**: 30分
> **難易度**: ★★☆☆☆

---

### この章で学ぶこと

この章を終えると、以下のことができるようになります。

- `.vue` ファイル（SFC）の3つのブロック（script / template / style）の役割を理解できる
- `main.ts` から `App.vue` がどのようにブラウザに表示されるか説明できる
- `<script setup lang="ts">` の意味と利点を理解できる
- スコープ付きスタイル（`scoped`）の仕組みを理解できる
- マスタッシュ構文（`{{ }}`）を使って変数をテンプレートに表示できる

---

### 前提

この章を読む前に、以下が完了している必要があります。

- 第2章で環境構築が完了していること（Node.js、VS Code、`create-vue` プロジェクトの作成）
- `npm run dev` で開発サーバーを起動できること

---

### SFC（Single File Component）とは？

#### なぜ SFC が必要なのか

通常の Web 開発では、HTML・CSS・JavaScript を別々のファイルに分けて管理します。しかし、アプリケーションが大きくなると、1つの UI 部品に関連するコードが3つのファイルに散らばってしまいます。

たとえば、「ユーザーカード」という UI 部品を作る場合を考えてみましょう。

```
従来の方式:
├── user-card.html    ← 構造（HTML）
├── user-card.css     ← 見た目（CSS）
└── user-card.js      ← 振る舞い（JavaScript）
```

ユーザーカードを修正したいとき、3つのファイルを行き来する必要があります。関連するコードが離れた場所にあることを **関心の分離の過剰な分散** と呼びます。

Vue の **SFC（Single File Component / シングルファイルコンポーネント）** は、この問題を解決します。1つの `.vue` ファイルの中に、HTML・CSS・JavaScript をまとめて記述します。

```
Vue の SFC 方式:
└── UserCard.vue    ← 構造 + 見た目 + 振る舞い を1ファイルに
```

> SFC は「1つの UI 部品に関するすべてを1つのファイルにまとめる」という考え方です。これにより、コードの見通しが良くなり、修正も簡単になります。

#### SFC の3つのブロック

`.vue` ファイルは、3つのブロックで構成されます。

```vue
<script setup lang="ts">
// ① script ブロック — ロジック（TypeScript）
// データの定義、関数の定義、外部モジュールの読み込みなど
</script>

<template>
  <!-- ② template ブロック — 構造（HTML） -->
  <!-- ブラウザに表示される HTML を記述する -->
</template>

<style scoped>
/* ③ style ブロック — 見た目（CSS） */
/* このコンポーネント専用のスタイルを記述する */
</style>
```

それぞれの役割を整理します。

| ブロック | 言語 | 役割 | 必須？ |
|---|---|---|---|
| `<script setup lang="ts">` | TypeScript | データやロジックの定義 | 任意（ただしほぼ必須） |
| `<template>` | HTML | ブラウザに表示する構造 | **必須** |
| `<style scoped>` | CSS | このコンポーネントのスタイル | 任意 |

> **ポイント**: `<template>` ブロックだけが必須です。`<script>` や `<style>` は省略できます。ただし実際の開発ではほぼすべてのコンポーネントが3つのブロックを持ちます。

---

### Vue アプリの起動フロー

#### ブラウザに表示されるまでの流れ

Vue アプリケーションがブラウザに表示されるまでには、以下の順序で処理が進みます。

```
① index.html    ブラウザが最初に読み込む HTML ファイル
      ↓
② main.ts       Vue アプリを作成し、HTML にマウント（結びつけ）する
      ↓
③ App.vue       アプリのルート（最上位）コンポーネント
      ↓
④ ブラウザ表示    App.vue の内容が画面に描画される
```

この流れを、各ファイルを見ながら具体的に理解していきましょう。

#### ① index.html — すべての起点

プロジェクトのルートにある `index.html` を開いてみましょう。

`index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite App</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

注目すべきポイントは2つです。

1. **`<div id="app"></div>`** — Vue がコンテンツを描画する「器」です。この空の `<div>` の中に、Vue が動的に HTML を生成します。
2. **`<script type="module" src="/src/main.ts">`** — `main.ts` を読み込む指定です。`type="module"` は ES Modules 形式であることを意味します。

> `id="app"` の `<div>` は、いわば「Vue 専用の表示エリア」です。Vue はこの中だけを管理します。

#### ② main.ts — Vue アプリの初期化

`src/main.ts`

```typescript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

たった3行ですが、非常に重要な処理を行っています。

| コード | 意味 |
|---|---|
| `import { createApp } from 'vue'` | Vue ライブラリから `createApp` 関数を読み込む |
| `import App from './App.vue'` | ルートコンポーネント `App.vue` を読み込む |
| `createApp(App)` | `App.vue` を起点とした Vue アプリケーションを作成する |
| `.mount('#app')` | `index.html` の `<div id="app">` にアプリを **マウント**（結びつけ）する |

**マウント（Mount）** とは、Vue アプリケーションを HTML 要素に結びつけて描画を開始することです。マウントされると、`<div id="app"></div>` の中に `App.vue` の内容が表示されます。

#### ③ App.vue — ルートコンポーネント

`App.vue` は、アプリの **最上位コンポーネント** です。ブラウザに表示されるすべての内容は、`App.vue` を起点としています。

後のハンズオンで実際に `App.vue` を編集します。

---

### `<script setup lang="ts">` の意味

`<script>` ブロックに書かれている `setup` と `lang="ts"` には、それぞれ重要な意味があります。

#### `setup` — Composition API の簡潔な書き方

`setup` は、Vue の **Composition API** を簡潔に記述するための特別なキーワードです。

`<script setup>` を使うと、以下のメリットがあります。

- 変数や関数を定義するだけで、テンプレートから直接使える
- `return` 文が不要（通常の `<script>` では `return` でテンプレートに公開する必要がある）
- コードが短くなり、見通しが良くなる

```vue
<!-- ✅ <script setup> を使う場合 — シンプル -->
<script setup lang="ts">
const message = 'こんにちは'
</script>

<template>
  <p>{{ message }}</p>
</template>
```

> このチュートリアルでは、すべてのコンポーネントで `<script setup>` を使います。

#### `lang="ts"` — TypeScript を使う宣言

`lang="ts"` は、このスクリプトブロックが **TypeScript** で書かれていることを示します。

TypeScript を使うことで、以下のメリットがあります。

- **型チェック** — データの型（文字列・数値・配列など）を明示し、間違ったデータの代入を事前に防げる
- **エディタ補完** — VS Code が的確なコード候補を表示してくれる
- **ドキュメント効果** — 型定義がそのままコードの説明になる

```vue
<script setup lang="ts">
// TypeScript: 型を明示してコードの意図を明確にする
const userName: string = 'Taro'
const userAge: number = 25
</script>
```

> `lang="ts"` を書き忘れると、TypeScript の構文がエラーになります。必ず記述してください。

---

### ハンズオン

実際にコードを書いて動かしてみましょう。

#### ステップ 1: プロジェクトの初期状態を確認する

第2章で作成したプロジェクトの `src/App.vue` を開いてください。`create-vue` が生成した初期コードが書かれているはずです。

まず、このファイルの中身を **すべて削除** してください。まっさらな状態から書き始めます。

#### ステップ 2: 最小限の SFC を書く

`App.vue` をテンプレートだけの最小構成で書いてみましょう。

`src/App.vue`

```vue
<template>
  <h1>Hello, Vue3!</h1>
</template>
```

ターミナルで `npm run dev` を実行し、ブラウザで確認してください。画面に「Hello, Vue3!」と大きく表示されます。

- **何をしているか**: `<template>` ブロックだけを持つ最小限のコンポーネントです。
- **なぜそう書くのか**: SFC にはテンプレートブロックだけが必須であることを確認するためです。
- **注目ポイント**: `<script>` と `<style>` がなくてもエラーにならず、ブラウザに表示されます。

#### ステップ 3: script ブロックを追加し、変数をテンプレートに表示する

次に、`<script setup lang="ts">` を追加して、TypeScript で定義した変数をテンプレートに表示します。

`src/App.vue`

```vue
<script setup lang="ts">
const greeting: string = 'Hello, Vue3!'
const description: string = 'Vue アプリケーションの構造を学んでいます'
</script>

<template>
  <h1>{{ greeting }}</h1>
  <p>{{ description }}</p>
</template>
```

- **何をしているか**: `<script setup>` 内で文字列変数を定義し、テンプレートの `{{ }}` で表示しています。
- **なぜそう書くのか**: `<script setup>` 内で宣言した変数は、自動的にテンプレートで使えるようになります。特別な登録作業は不要です。
- **注目ポイント**: `{{ }}` は **マスタッシュ構文（Mustache Syntax）** と呼ばれます。二重波括弧の中に変数名を書くと、その値がテキストとして表示されます。「マスタッシュ」は英語で「口ひげ」の意味で、`{{ }}` の形が口ひげに似ていることが名前の由来です。

#### ステップ 4: 複数のデータを表示する

もう少しデータを追加して、マスタッシュ構文の使い方に慣れましょう。

`src/App.vue`

```vue
<script setup lang="ts">
const appName: string = 'Vue3 学習サイト'
const author: string = 'Vue ビギナー'
const year: number = 2026
const isPublished: boolean = true
</script>

<template>
  <h1>{{ appName }}</h1>
  <p>著者: {{ author }}</p>
  <p>公開年: {{ year }}年</p>
  <p>公開状態: {{ isPublished ? '公開中' : '非公開' }}</p>
</template>
```

- **何をしているか**: `string`、`number`、`boolean` の3つの型の変数をテンプレートに表示しています。
- **なぜそう書くのか**: マスタッシュ構文の中では、単純な変数だけでなく、**JavaScript の式** も書けることを確認するためです。
- **注目ポイント**: `{{ isPublished ? '公開中' : '非公開' }}` のように、三項演算子などの式もマスタッシュ構文の中で使えます。ただし、`if` 文のような **文（ステートメント）** は使えません。

> **マスタッシュ構文で使えるもの・使えないもの**
>
> ✅ 使える: 変数、計算式（`{{ count + 1 }}`）、三項演算子、メソッド呼び出し
>
> ❌ 使えない: `if` 文、`for` 文、変数宣言（`const x = 1`）

#### ステップ 5: style ブロックでスタイルを追加する

最後に `<style scoped>` を追加して、見た目を整えましょう。

`src/App.vue`

```vue
<script setup lang="ts">
const appName: string = 'Vue3 学習サイト'
const author: string = 'Vue ビギナー'
const year: number = 2026
const isPublished: boolean = true
</script>

<template>
  <div class="app-container">
    <h1 class="app-title">{{ appName }}</h1>
    <div class="app-info">
      <p>著者: {{ author }}</p>
      <p>公開年: {{ year }}年</p>
      <p>公開状態: {{ isPublished ? '公開中' : '非公開' }}</p>
    </div>
  </div>
</template>

<style scoped>
.app-container {
  max-width: 600px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

.app-title {
  color: #42b883;
  font-size: 2rem;
  margin-bottom: 16px;
}

.app-info {
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 16px;
}

.app-info p {
  margin: 8px 0;
  color: #333;
}
</style>
```

- **何をしているか**: CSS クラスを使ってコンポーネントにスタイルを当てています。
- **なぜそう書くのか**: `<style scoped>` の `scoped` 属性を使うことで、スタイルをこのコンポーネントだけに限定しています。
- **注目ポイント**: `scoped` を付けると、ここで定義した `.app-title` のスタイルは **このコンポーネント内の要素にだけ** 適用されます。他のコンポーネントに同じクラス名があっても影響しません。

#### ステップ 6: main.ts の内容を確認する

最後に、`src/main.ts` の内容を確認しましょう。第2章でプロジェクトを作成したときに自動生成されたファイルです。

`src/main.ts`

```typescript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

このコードが、先ほどまで編集していた `App.vue` を `index.html` の `<div id="app">` にマウントしています。

- **何をしているか**: Vue アプリの起点となる初期化処理です。
- **なぜそう書くのか**: Vue アプリケーションを動かすには、`createApp` でアプリを作成し、`mount` で HTML に結びつける必要があります。
- **注目ポイント**: `main.ts` は通常変更する必要がありません。Vue Router や Pinia を導入する際（第12章・第13章以降）に修正します。

---

### コード全体

ここまでのハンズオンで作成したコードの最終形をまとめます。

`src/App.vue`

```vue
<script setup lang="ts">
const appName: string = 'Vue3 学習サイト'
const author: string = 'Vue ビギナー'
const year: number = 2026
const isPublished: boolean = true
</script>

<template>
  <div class="app-container">
    <h1 class="app-title">{{ appName }}</h1>
    <div class="app-info">
      <p>著者: {{ author }}</p>
      <p>公開年: {{ year }}年</p>
      <p>公開状態: {{ isPublished ? '公開中' : '非公開' }}</p>
    </div>
  </div>
</template>

<style scoped>
.app-container {
  max-width: 600px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

.app-title {
  color: #42b883;
  font-size: 2rem;
  margin-bottom: 16px;
}

.app-info {
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 16px;
}

.app-info p {
  margin: 8px 0;
  color: #333;
}
</style>
```

`src/main.ts`

```typescript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

`index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite App</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

> **動作確認**: `npm run dev` でブラウザに表示される結果を確認してください。

**期待される表示結果:**

```
┌──────────────────────────────────┐
│                                  │
│  Vue3 学習サイト                  │  ← 緑色（#42b883）の大見出し
│                                  │
│  ┌──────────────────────────┐    │
│  │ 著者: Vue ビギナー        │    │
│  │ 公開年: 2026年            │    │  ← 薄いグレーの背景
│  │ 公開状態: 公開中           │    │
│  └──────────────────────────┘    │
│                                  │
└──────────────────────────────────┘
```

---

### 解説

コードの重要な部分を詳しく解説します。

#### マスタッシュ構文と式の評価

```typescript
<p>公開状態: {{ isPublished ? '公開中' : '非公開' }}</p>
```

マスタッシュ構文 `{{ }}` の中には、**JavaScript の式（Expression）** を書くことができます。ここでは三項演算子を使って、`isPublished` が `true` なら「公開中」、`false` なら「非公開」を表示しています。

式と文の違いを理解しておきましょう。

| 種類 | 意味 | マスタッシュ構文内で使えるか | 例 |
|---|---|---|---|
| **式（Expression）** | 値を生成するコード | ✅ 使える | `count + 1`, `name.toUpperCase()` |
| **文（Statement）** | 処理を実行するコード | ❌ 使えない | `if (x) { ... }`, `const y = 1` |

Vue は `{{ }}` 内の式を評価し、結果の文字列をテンプレートに挿入します。

#### scoped スタイルの仕組み

```css
<style scoped>
.app-title {
  color: #42b883;
}
</style>
```

`scoped` を付けると、Vue は **各 HTML 要素に固有の属性**（例: `data-v-7a7a37b1`）を自動で追加します。そして CSS セレクタにもその属性を付与することで、スタイルがこのコンポーネント内だけに適用されるようにします。

実際にブラウザの開発者ツール（F12）で確認すると、以下のようになっています。

```html
<!-- ブラウザで実際に生成される HTML -->
<h1 class="app-title" data-v-7a7a37b1>Vue3 学習サイト</h1>
```

```css
/* ブラウザで実際に適用される CSS */
.app-title[data-v-7a7a37b1] {
  color: #42b883;
}
```

このように `scoped` は **属性セレクタ** を使ってスタイルの適用範囲を限定しています。コンポーネントが増えても、クラス名の衝突を心配する必要がありません。

#### createApp と mount の流れ

```typescript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

この3行のコードが行っている処理を詳しく見てみましょう。

1. **`createApp(App)`** — `App.vue` を起点とする Vue アプリケーションの **インスタンス**（実体）を作成します。この時点ではまだ画面には何も表示されません。
2. **`.mount('#app')`** — CSS セレクタ `#app` に一致する HTML 要素（`<div id="app">`）を見つけ、その中に Vue コンポーネントの内容を描画します。

`mount` は **1回だけ** 呼びます。マウント後は、Vue が自動的にデータの変更を検知してUIを更新してくれます（これがリアクティビティの仕組みで、第5章で詳しく学びます）。

---

### よくある間違い・FAQ

初心者がつまずきやすいポイントをまとめます。

#### ❌ よくある間違い 1: `lang="ts"` を書き忘れる

**間違ったコード:**

```vue
<script setup>
const message: string = 'Hello'
</script>

<template>
  <p>{{ message }}</p>
</template>
```

**何が問題か:**

`lang="ts"` がないため、このスクリプトは JavaScript として解釈されます。JavaScript には `string` のような型注釈がないため、構文エラーになります。

**正しいコード:**

```vue
<script setup lang="ts">
const message: string = 'Hello'
</script>

<template>
  <p>{{ message }}</p>
</template>
```

#### ❌ よくある間違い 2: マスタッシュ構文の中で文を書く

**間違ったコード:**

```vue
<script setup lang="ts">
const count: number = 5
</script>

<template>
  <p>{{ if (count > 3) { return '多い' } }}</p>
</template>
```

**何が問題か:**

`{{ }}` の中に `if` 文を書いています。マスタッシュ構文で使えるのは **式（値を返すコード）** だけで、**文（処理を実行するコード）** は使えません。

**正しいコード:**

```vue
<script setup lang="ts">
const count: number = 5
</script>

<template>
  <p>{{ count > 3 ? '多い' : '少ない' }}</p>
</template>
```

> 三項演算子（`条件 ? 真の値 : 偽の値`）は式なので、マスタッシュ構文の中で使えます。

#### ❌ よくある間違い 3: `scoped` を付け忘れてスタイルが漏れる

**間違ったコード:**

```vue
<style>
h1 {
  color: red;
}
</style>
```

**何が問題か:**

`scoped` がないため、この `h1` スタイルはアプリ全体のすべての `<h1>` 要素に適用されてしまいます。他のコンポーネントの `<h1>` も赤色になります。

**正しいコード:**

```vue
<style scoped>
h1 {
  color: red;
}
</style>
```

> `scoped` を付けることで、スタイルがこのコンポーネント内の `<h1>` だけに適用されます。

#### 💬 FAQ

**Q: SFC の3つのブロックの記述順序は決まっていますか？**

A: 技術的にはどの順番で書いても動作します。しかし、このチュートリアルでは `<script>` → `<template>` → `<style>` の順序で統一します。この順序は Vue 公式のスタイルガイドでも推奨されており、多くの Vue プロジェクトで採用されています。

**Q: `<template>` の中に複数のルート要素を置けますか？**

A: はい、Vue 3 では可能です。Vue 2 ではルート要素が1つだけという制約がありましたが、Vue 3 ではその制約がなくなりました。以下のようにルート要素が複数あっても問題ありません。

```vue
<template>
  <h1>タイトル</h1>
  <p>説明文</p>
</template>
```

ただし、要素をまとめたい場合は `<div>` で囲むのも良い方法です。

**Q: `main.ts` のファイル名は変更できますか？**

A: 変更は技術的には可能ですが、推奨しません。`main.ts` は `create-vue` が生成する標準の名前で、Vue 開発者なら誰でも「エントリーポイント」と認識できます。変更する場合は `index.html` の `<script>` タグの `src` 属性も合わせて変更する必要があります。

---

### 練習問題

学んだ内容を定着させるための練習問題です。

#### 練習 1: 自己紹介カードを作ろう

**課題:**

`App.vue` を編集して、自分の名前・好きな食べ物・学習目標を表示する「自己紹介カード」を作ってください。以下の要件を満たすこと。

- `<script setup lang="ts">` で3つ以上の変数を定義する
- 変数には型注釈を付ける
- `<template>` でマスタッシュ構文を使って変数を表示する
- `<style scoped>` でカードらしいスタイルを適用する

**ヒント:**

ステップ5のコードを参考に、変数名と値を自分のものに変えてみましょう。`background-color` や `border-radius` などのCSSプロパティでカードらしさを出せます。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
const name: string = '太郎'
const favoriteFood: string = 'カレーライス'
const goal: string = 'Vue3 でオリジナルアプリを作る'
const startDate: string = '2026年2月'
</script>

<template>
  <div class="profile-card">
    <h1 class="profile-name">{{ name }}</h1>
    <ul class="profile-list">
      <li>好きな食べ物: {{ favoriteFood }}</li>
      <li>学習目標: {{ goal }}</li>
      <li>学習開始: {{ startDate }}</li>
    </ul>
  </div>
</template>

<style scoped>
.profile-card {
  max-width: 400px;
  margin: 40px auto;
  padding: 24px;
  background-color: #ffffff;
  border: 2px solid #42b883;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  font-family: sans-serif;
}

.profile-name {
  color: #42b883;
  font-size: 1.8rem;
  margin-bottom: 16px;
  text-align: center;
}

.profile-list {
  list-style: none;
  padding: 0;
}

.profile-list li {
  padding: 8px 0;
  border-bottom: 1px solid #eee;
  color: #333;
}

.profile-list li:last-child {
  border-bottom: none;
}
</style>
```

**解説:**

4つの文字列変数を `<script setup>` で定義し、テンプレートのマスタッシュ構文で表示しています。`<ul>` と `<li>` で情報を箇条書きにし、`scoped` スタイルでカードデザインを適用しています。

</details>

#### 練習 2: マスタッシュ構文で式を使ってみよう

**課題:**

以下の変数を定義し、マスタッシュ構文の中で **式** を使って表示してください。

- `price`（数値: 商品の税抜価格）
- `taxRate`（数値: 消費税率、例: `0.1`）
- `productName`（文字列: 商品名）
- `inStock`（真偽値: 在庫の有無）

テンプレートには以下を表示してください。

1. 商品名（大文字に変換して表示）
2. 税込価格（`price * (1 + taxRate)` を計算して表示）
3. 在庫状態（`inStock` が `true` なら「在庫あり」、`false` なら「在庫なし」）

**ヒント:**

文字列を大文字にするには `.toUpperCase()` メソッドが使えます。計算式や三項演算子はマスタッシュ構文の中で直接使えます。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
const price: number = 1000
const taxRate: number = 0.1
const productName: string = 'vue book'
const inStock: boolean = true
</script>

<template>
  <div class="product-card">
    <h1>{{ productName.toUpperCase() }}</h1>
    <p>税抜価格: {{ price }}円</p>
    <p>税込価格: {{ price * (1 + taxRate) }}円</p>
    <p>在庫: {{ inStock ? '在庫あり ✅' : '在庫なし ❌' }}</p>
  </div>
</template>

<style scoped>
.product-card {
  max-width: 400px;
  margin: 40px auto;
  padding: 24px;
  background-color: #f0f4f8;
  border-radius: 8px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 12px;
}

p {
  margin: 8px 0;
  color: #555;
}
</style>
```

**解説:**

- `{{ productName.toUpperCase() }}` — メソッド呼び出しは式なのでマスタッシュ構文で使えます。「VUE BOOK」と表示されます。
- `{{ price * (1 + taxRate) }}` — 計算式を直接書いて「1100」と表示されます。
- `{{ inStock ? '在庫あり ✅' : '在庫なし ❌' }}` — 三項演算子で条件分岐しています。

</details>

#### 練習 3: 起動フローを自分の言葉で説明しよう

**課題:**

以下の3つのファイルが Vue アプリの起動時にどのような順序で処理されるか、それぞれの役割と合わせて **自分の言葉で** 説明してください（コードを書く問題ではありません）。

1. `index.html`
2. `src/main.ts`
3. `src/App.vue`

**ヒント:**

「起動フロー」セクションの図を思い出してください。各ファイルの中で、他のファイルを参照している部分に注目しましょう。

<details>
<summary>解答例を見る</summary>

**解答例:**

1. **`index.html`** — ブラウザが最初に読み込むファイルです。`<div id="app">` という空のコンテナと、`<script>` タグで `main.ts` の読み込み指定を持っています。Vue の表示エリアとエントリーポイントへの橋渡しの役割があります。

2. **`src/main.ts`** — Vue アプリの初期化を行うエントリーポイントです。`createApp(App)` で `App.vue` を起点とした Vue アプリケーションを作成し、`.mount('#app')` で `index.html` の `<div id="app">` にマウント（結びつけ）します。

3. **`src/App.vue`** — アプリのルート（最上位）コンポーネントです。`<script setup>` でデータやロジックを定義し、`<template>` で HTML 構造を記述し、`<style scoped>` でスタイルを指定します。マウントされると、この SFC の内容が `<div id="app">` の中に描画されます。

**処理の流れ**: `index.html` → `main.ts`（`createApp` + `mount`）→ `App.vue`（描画）→ ブラウザに表示

</details>

---

### まとめ

この章では以下の内容を学びました。

| 学んだこと | ポイント |
|---|---|
| SFC（Single File Component） | 1つの `.vue` ファイルに `<script>` / `<template>` / `<style>` の3ブロックをまとめる |
| Vue アプリの起動フロー | `index.html` → `main.ts`（`createApp` + `mount`）→ `App.vue` → ブラウザ描画 |
| `<script setup lang="ts">` | Composition API の簡潔な記述方式。`setup` で自動公開、`lang="ts"` で TypeScript 有効化 |
| マスタッシュ構文 `{{ }}` | テンプレート内で変数や JavaScript 式を表示する構文。文（Statement）は使えない |
| `scoped` スタイル | `<style scoped>` で CSS をコンポーネント内に限定。属性セレクタで実現される |

**キーワード**: `SFC`, `<script setup>`, `createApp`, `mount`, `{{ }}（マスタッシュ構文）`, `scoped`

---

### 次の章へ

この章では変数をテンプレートに表示する方法を学びました。しかし、今のままではすべての変数が固定値です。次の第4章では、**テンプレート構文** を深く学び、`v-bind` で HTML 属性を動的に変えたり、`v-if` で条件分岐したり、`v-for` でリストを表示したりする方法を身につけます。テンプレートの表現力が一気に広がります。
