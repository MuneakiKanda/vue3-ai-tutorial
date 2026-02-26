## 第2章: 環境構築

> **読了時間目安**: 30分
> **難易度**: ★☆☆☆☆

---

### この章で学ぶこと

この章を終えると、以下のことができるようになります。

- Node.js と npm をインストールし、バージョンを確認できる
- `create-vue` を使って Vue3 + TypeScript プロジェクトを作成できる
- 開発サーバーを起動し、ブラウザで初期画面を確認できる
- プロジェクト内の主要なファイルの役割を説明できる
- `App.vue` を編集して変更がリアルタイムに反映されることを確認できる

---

### 前提

この章を読む前に、以下が完了している必要があります。

- HTML / CSS / JavaScript の基礎知識があること
- 第1章で Vue3 の概要を理解していること

---

### 開発に必要なツールを揃える

Vue3 で開発を始めるには、いくつかのツールをインストールする必要があります。料理を始める前にキッチンを整えるようなものです。必要な道具は3つだけです。

1. **Node.js** — JavaScript をブラウザの外で動かすための実行環境
2. **npm** — JavaScript のライブラリ（パッケージ）を管理するツール（Node.js に同梱）
3. **VS Code** — コードを書くためのエディタ（推奨）

#### Node.js とは

**Node.js（ノードジェイエス）** とは、JavaScript をサーバーやPC上で実行するための環境です。

普段 JavaScript はブラウザの中で動きますが、Vue3 の開発ツール（Vite など）はPC上で動作する必要があります。Node.js をインストールすることで、PC上で JavaScript ベースのツールが使えるようになります。

#### npm とは

**npm（エヌピーエム / Node Package Manager）** とは、JavaScript のパッケージ（ライブラリ）をインストール・管理するためのツールです。

npm は Node.js をインストールすると自動的に一緒にインストールされます。Vue3 本体やビルドツールの Vite なども、npm を通じてインストールします。

---

### ハンズオン

実際にコードを書いて動かしてみましょう。

#### ステップ 1: Node.js をインストールする

Node.js の公式サイトからインストーラーをダウンロードします。

1. ブラウザで **https://nodejs.org/** にアクセスする
2. **LTS（Long Term Support / 長期サポート版）** のボタンをクリックしてダウンロードする
3. ダウンロードしたインストーラーを実行し、画面の指示に従ってインストールする

> **LTS版を選ぶ理由**: LTS版は安定性が重視されており、本番環境でも安心して使えます。「最新版（Current）」はまだ安定していない新機能が含まれる場合があるため、学習用途でもLTS版を推奨します。

インストールが完了したら、ターミナル（Windows ならコマンドプロンプトまたは PowerShell、macOS なら Terminal.app）を開いて、以下のコマンドでバージョンを確認します。

```bash
node --version
```

**期待される出力（例）:**

```
v20.18.1
```

> バージョン番号は `v20.x.x` 以上であれば問題ありません。

続けて npm のバージョンも確認します。

```bash
npm --version
```

**期待される出力（例）:**

```
10.8.2
```

- **何をしているか**: Node.js と npm が正しくインストールされたかを確認しています
- **なぜそう書くのか**: `--version` フラグは、ほぼすべてのコマンドラインツールで「バージョン番号を表示する」という共通の使い方です
- **注目ポイント**: バージョン番号が表示されず「コマンドが見つかりません」と出た場合は、インストールが正しく完了していないか、ターミナルの再起動が必要です

#### ステップ 2: VS Code をセットアップする

**VS Code（Visual Studio Code）** は、Microsoft が開発している無料のコードエディタです。Vue3 開発に最適な拡張機能が豊富に揃っています。

1. **https://code.visualstudio.com/** からダウンロード・インストールする
2. VS Code を起動する
3. 拡張機能（Extensions）を追加する

**必須の拡張機能:**

| 拡張機能名 | 説明 |
|---|---|
| **Vue - Official** | Vue 3 の SFC（.vue ファイル）のシンタックスハイライト、型チェック、補完 |

**推奨の拡張機能:**

| 拡張機能名 | 説明 |
|---|---|
| **TypeScript Vue Plugin (Volar)** | TypeScript の型チェック強化（Vue - Official に含まれる場合あり） |
| **ESLint** | コードの品質チェック |
| **Prettier** | コードの自動フォーマット |

拡張機能のインストール方法:

1. VS Code の左サイドバーで **四角いアイコン（Extensions）** をクリック
2. 検索ボックスに拡張機能名を入力
3. **Install** ボタンをクリック

- **何をしているか**: Vue3 開発に最適な開発環境を構築しています
- **なぜそう書くのか**: VS Code + Vue - Official の組み合わせが、Vue3 公式に推奨されている開発環境です。コード補完、エラー検出、型チェックなどが自動で行われ、開発効率が大きく向上します
- **注目ポイント**: 古い拡張機能「Vetur」は Vue 2 用です。Vue 3 では **「Vue - Official」** を使ってください

#### ステップ 3: Vue3 プロジェクトを作成する

いよいよ Vue3 プロジェクトを作成します。ターミナルで、プロジェクトを作りたいディレクトリに移動してから以下のコマンドを実行してください。

```bash
npm create vue@latest
```

このコマンドを実行すると、対話形式でいくつかの質問が表示されます。以下のように回答してください。

```
✔ Project name: … my-vue-app
✔ Add TypeScript? … Yes
✔ Add JSX Support? … No
✔ Add Vue Router for Single Page Application development? … No
✔ Add Pinia for state management? … No
✔ Add Vitest for Unit Testing? … No
✔ Add an End-to-End Testing Solution? … No
✔ Add ESLint for code quality? … Yes
✔ Add Prettier for code formatting? … Yes
✔ Add Vue DevTools 7 extension for debugging? (experimental) … No
```

> **なぜこの選択なのか**: このチュートリアルでは、まず Vue3 の基本に集中します。Vue Router（第12章）や Pinia（第13章）は、必要になった章で個別に追加します。TypeScript、ESLint、Prettier は最初から有効にしておきます。

コマンドが完了すると、`my-vue-app` というフォルダが作成されます。

- **何をしているか**: `create-vue` という Vue3 公式のプロジェクト雛形生成ツールを使って、新しいプロジェクトを作成しています
- **なぜそう書くのか**: `npm create vue@latest` は `create-vue` パッケージの最新版を自動でダウンロードして実行する npm の仕組みです
- **注目ポイント**: プロジェクト名は半角英数字とハイフンで構成するのが一般的です。日本語や空白は避けてください

#### ステップ 4: 依存パッケージをインストールして開発サーバーを起動する

作成したプロジェクトフォルダに移動し、必要なパッケージをインストールします。

```bash
cd my-vue-app
npm install
```

`npm install` は、`package.json` に記載されたパッケージ（Vue3, Vite, TypeScript など）をダウンロードして `node_modules` フォルダに配置するコマンドです。

インストールが完了したら、開発サーバーを起動します。

```bash
npm run dev
```

**期待される出力:**

```
  VITE v6.x.x  ready in xxx ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

ブラウザで **http://localhost:5173/** を開いてください。Vue3 のウェルカムページが表示されれば成功です。

- **何をしているか**: 開発用のローカルサーバーを起動しています。このサーバーは **HMR（Hot Module Replacement / ホットモジュールリプレースメント）** という機能を持ち、コードを変更するとブラウザが自動的に更新されます
- **なぜそう書くのか**: `npm run dev` は `package.json` の `scripts` セクションに定義された `dev` コマンドを実行します。内部的には Vite の開発サーバーが起動します
- **注目ポイント**: 開発サーバーを停止するには、ターミナルで `Ctrl + C` を押します

#### ステップ 5: プロジェクト構造を確認する

VS Code で `my-vue-app` フォルダを開きましょう（`ファイル` → `フォルダーを開く`）。以下のようなファイル構成が確認できます。

```
my-vue-app/
├── node_modules/          ← インストールされたパッケージ（触らない）
├── public/                ← 静的ファイル（favicon等）
├── src/                   ← ★ ソースコード（ここで開発する）
│   ├── assets/            ← 画像やCSS等のリソース
│   ├── components/        ← Vue コンポーネント
│   ├── App.vue            ← ★ ルートコンポーネント（アプリの起点）
│   └── main.ts            ← ★ エントリーポイント（最初に実行されるファイル）
├── index.html             ← ★ HTMLテンプレート
├── package.json           ← プロジェクト設定・依存パッケージ一覧
├── tsconfig.json          ← TypeScript 設定
├── vite.config.ts         ← Vite 設定
└── eslint.config.js       ← ESLint 設定
```

★ マークが付いたファイルが、最初に理解すべき重要なファイルです。

| ファイル | 役割 |
|---|---|
| `index.html` | ブラウザが最初に読み込む HTML。`<div id="app"></div>` が Vue アプリのマウント先 |
| `src/main.ts` | JavaScript のエントリーポイント。Vue アプリを作成して `#app` にマウントする |
| `src/App.vue` | ルートコンポーネント。アプリ全体の起点となる Vue コンポーネント |
| `package.json` | プロジェクトの設定ファイル。依存パッケージや実行スクリプトが記載されている |

- **何をしているか**: プロジェクトの全体像を把握しています
- **なぜそう書くのか**: ファイル構成を理解することで、「どこに何を書くか」が明確になります
- **注目ポイント**: `node_modules` フォルダは非常に大きく（数百MB）、Git にはコミットしません（`.gitignore` で除外済み）

#### ステップ 6: App.vue を編集して動作を確認する

`src/App.vue` を開いて、内容をすべて以下のコードに置き換えてみましょう。

`src/App.vue`

```vue
<script setup lang="ts">
const message: string = 'Hello, Vue3!'
const year: number = 2026
</script>

<template>
  <div class="container">
    <h1>{{ message }}</h1>
    <p>{{ year }}年のVue3チュートリアルへようこそ。</p>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
  font-family: sans-serif;
}

h1 {
  color: #42b883;
  font-size: 2rem;
}

p {
  color: #333;
  font-size: 1.2rem;
}
</style>
```

ファイルを保存（`Ctrl + S`）すると、ブラウザが自動的に更新されます。これが **HMR（ホットモジュールリプレースメント）** の効果です。

- **何をしているか**: Vue3 のルートコンポーネントを自分で書いたコードに差し替え、変更がリアルタイムに反映されることを確認しています
- **なぜそう書くのか**: `<script setup lang="ts">` 内で宣言した変数は、そのまま `<template>` 内の `{{ }}` で使えます。これは第1章で学んだ「宣言的レンダリング」の実例です
- **注目ポイント**: 変数に `: string` や `: number` という型注釈が付いています。これが TypeScript の型指定です。今は「変数の種類を明示している」とだけ理解しておいてください

---

### コード全体

ここまでのハンズオンで作成したコードの最終形をまとめます。

`src/main.ts`（`create-vue` が自動生成。変更不要）

```typescript
import { createApp } from 'vue'
import App from './App.vue'

import './assets/main.css'

createApp(App).mount('#app')
```

`src/App.vue`（ステップ6で編集）

```vue
<script setup lang="ts">
const message: string = 'Hello, Vue3!'
const year: number = 2026
</script>

<template>
  <div class="container">
    <h1>{{ message }}</h1>
    <p>{{ year }}年のVue3チュートリアルへようこそ。</p>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
  font-family: sans-serif;
}

h1 {
  color: #42b883;
  font-size: 2rem;
}

p {
  color: #333;
  font-size: 1.2rem;
}
</style>
```

`index.html`（`create-vue` が自動生成。変更不要）

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <link rel="icon" href="/favicon.ico">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
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
┌─────────────────────────────────────┐
│                                       │
│         Hello, Vue3!                  │
│    （緑色 #42b883 の大きな文字）       │
│                                       │
│   2026年のVue3チュートリアルへ         │
│   ようこそ。                          │
│    （グレーの文字）                    │
│                                       │
└─────────────────────────────────────┘
```

---

### 解説

コードの重要な部分を詳しく解説します。

#### アプリの起動フロー

Vue3 アプリケーションがブラウザに表示されるまでの流れを追ってみましょう。

```
index.html
  └→ <script type="module" src="/src/main.ts">
       └→ main.ts
            ├→ import App from './App.vue'   （ルートコンポーネントを読み込み）
            └→ createApp(App).mount('#app')  （#app に Vue アプリをマウント）
                 └→ App.vue の <template> がブラウザに表示される
```

1. ブラウザが `index.html` を読み込む
2. `index.html` 内の `<script>` タグが `src/main.ts` を読み込む
3. `main.ts` が `App.vue` をインポートし、`createApp()` で Vue アプリケーションを作成する
4. `.mount('#app')` で、`index.html` にある `<div id="app"></div>` の中に Vue アプリの内容を展開する

**`createApp(App)`** は「`App.vue` をルート（起点）として Vue アプリケーションのインスタンスを作る」という意味です。**`.mount('#app')`** は「HTML 内の `id="app"` の要素にマウント（取り付け）する」という意味です。

#### `<script setup lang="ts">` の役割

```typescript
<script setup lang="ts">
const message: string = 'Hello, Vue3!'
const year: number = 2026
</script>
```

この1行にはいくつかの情報が詰まっています。

- **`<script>`** — JavaScript / TypeScript のコードを書くブロック
- **`setup`** — Composition API の `setup` 関数をファイル全体に適用する省略記法。`<script setup>` 内で宣言した変数や関数は、そのまま `<template>` で使える
- **`lang="ts"`** — TypeScript を使うことを宣言。これにより型注釈（`: string`, `: number`）が使える

`<script setup>` がない場合、同じコードはこう書く必要があります：

```typescript
// <script setup> を使わない場合（参考）
export default {
  setup() {
    const message: string = 'Hello, Vue3!'
    const year: number = 2026
    return { message, year }  // テンプレートで使うには return が必要
  }
}
```

`<script setup>` を使うと、`return` を書く必要がなくなり、コードが格段にシンプルになります。このチュートリアルでは常に `<script setup>` を使います。

#### マスタッシュ構文 `{{ }}`

```html
<h1>{{ message }}</h1>
<p>{{ year }}年のVue3チュートリアルへようこそ。</p>
```

`{{ }}` は **マスタッシュ構文（Mustache Syntax）** と呼ばれ、`<script setup>` で定義した変数の値をテンプレートに埋め込むための構文です。「マスタッシュ」は英語で「口ひげ」という意味で、`{{` と `}}` の形が口ひげに似ていることが名前の由来です。

`{{ }}` の中には JavaScript の式を書くことができます。

```html
<!-- 変数の値をそのまま表示 -->
<p>{{ message }}</p>

<!-- 計算式 -->
<p>{{ 1 + 1 }}</p>

<!-- メソッド呼び出し -->
<p>{{ message.toUpperCase() }}</p>
```

ただし、`{{ }}` の中に **文（statement）** を書くことはできません。

```html
<!-- ❌ これはエラー: if文は式ではない -->
<p>{{ if (true) { return 'hello' } }}</p>

<!-- ✅ 三項演算子なら使える（式なので） -->
<p>{{ true ? 'hello' : 'goodbye' }}</p>
```

---

### よくある間違い・FAQ

初心者がつまずきやすいポイントをまとめます。

#### ❌ よくある間違い 1: `npm install` を忘れて `npm run dev` を実行する

**間違ったコマンド:**

```bash
cd my-vue-app
npm run dev   # ← node_modules がないのでエラーになる
```

**何が問題か:**

`npm create vue@latest` でプロジェクトを作成しただけでは、依存パッケージ（Vue3, Vite 等）はまだダウンロードされていません。`npm install` を実行して `node_modules` フォルダを作成する必要があります。

**正しいコマンド:**

```bash
cd my-vue-app
npm install     # ← まず依存パッケージをインストール
npm run dev     # ← その後に開発サーバーを起動
```

#### ❌ よくある間違い 2: 古い拡張機能「Vetur」をインストールしてしまう

**何が問題か:**

VS Code の拡張機能検索で「Vue」と検索すると、「Vetur」という拡張機能が見つかることがあります。Vetur は **Vue 2 用** の拡張機能であり、Vue 3 では正しく動作しません。

**正しい対応:**

Vue 3 では **「Vue - Official」** （旧名: Volar）を使ってください。もし Vetur がインストールされている場合は、アンインストールしてから Vue - Official をインストールしてください。

#### ❌ よくある間違い 3: `node_modules` を Git にコミットしてしまう

**何が問題か:**

`node_modules` フォルダには数万ファイル・数百MBのパッケージが含まれています。これを Git リポジトリにコミットすると、リポジトリが巨大になり、クローンに非常に時間がかかります。

**正しい対応:**

`create-vue` で生成したプロジェクトには `.gitignore` ファイルが含まれており、`node_modules` は自動的に Git の管理対象外になっています。他の人がプロジェクトをクローンした場合は、`npm install` を実行すれば同じ `node_modules` が再現されます。

#### 💬 FAQ

**Q: ポート番号 5173 は変更できますか？**

A: はい。`vite.config.ts` で設定できます。また、5173番ポートが既に使われている場合、Vite は自動的に 5174, 5175... と別のポートを使います。

**Q: `npm` の代わりに `yarn` や `pnpm` を使っても良いですか？**

A: はい、問題ありません。`yarn create vue@latest` や `pnpm create vue@latest` でも同様にプロジェクトを作成できます。このチュートリアルでは npm を使って説明しますが、コマンドの対応関係さえ把握していれば他のパッケージマネージャーでも同じ結果になります。

**Q: Windows / macOS / Linux で違いはありますか？**

A: Node.js と npm のインストール方法が OS ごとに異なりますが、プロジェクト作成以降のコマンドや Vue3 のコードは完全に同じです。ターミナルの操作（`cd` でディレクトリ移動等）も基本的に共通です。

---

### 練習問題

学んだ内容を定着させるための練習問題です。

#### 練習 1: 表示内容を変更する

**課題:**

`App.vue` を編集して、以下の内容を表示してください。

- `<h1>` に自分の名前を表示する（例: 「こんにちは、太郎です」）
- `<p>` に今日の日付を表示する（変数で定義して `{{ }}` で表示）

**ヒント:**

`<script setup lang="ts">` 内で `string` 型の変数を宣言し、テンプレートで `{{ }}` を使って表示します。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
const name: string = '太郎'
const today: string = '2026年2月27日'
</script>

<template>
  <div class="container">
    <h1>こんにちは、{{ name }}です</h1>
    <p>今日は {{ today }} です。</p>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
  font-family: sans-serif;
}

h1 {
  color: #42b883;
  font-size: 2rem;
}

p {
  color: #333;
  font-size: 1.2rem;
}
</style>
```

**解説:**

`<script setup lang="ts">` で宣言した変数 `name` と `today` を、テンプレート内の `{{ }}` で表示しています。文字列と `{{ }}` を組み合わせて「こんにちは、太郎です」のように表示できます。

</details>

#### 練習 2: 複数の変数を使って自己紹介カードを作る

**課題:**

以下の情報を変数に定義して、自己紹介カードを作成してください。

- 名前（`string` 型）
- 年齢（`number` 型）
- 趣味（`string` 型）
- 好きなプログラミング言語（`string` 型）

表示イメージ：
```
🧑 自己紹介カード
名前: ○○
年齢: ○○歳
趣味: ○○
好きな言語: ○○
```

**ヒント:**

`number` 型の変数もテンプレート内で `{{ }}` を使って表示できます。`{{ age }}歳` のように文字列と組み合わせることも可能です。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
const name: string = '田中太郎'
const age: number = 25
const hobby: string = '映画鑑賞'
const language: string = 'TypeScript'
</script>

<template>
  <div class="card">
    <h1>🧑 自己紹介カード</h1>
    <ul>
      <li>名前: {{ name }}</li>
      <li>年齢: {{ age }}歳</li>
      <li>趣味: {{ hobby }}</li>
      <li>好きな言語: {{ language }}</li>
    </ul>
  </div>
</template>

<style scoped>
.card {
  max-width: 400px;
  margin: 2rem auto;
  padding: 2rem;
  border: 2px solid #42b883;
  border-radius: 12px;
  font-family: sans-serif;
}

h1 {
  color: #42b883;
  font-size: 1.5rem;
  margin-bottom: 1rem;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  padding: 0.5rem 0;
  border-bottom: 1px solid #eee;
  color: #333;
}

li:last-child {
  border-bottom: none;
}
</style>
```

**解説:**

4つの変数をそれぞれ異なる型（`string`, `number`）で定義し、テンプレート内の `<li>` 要素で表示しています。`<style scoped>` でカードのデザインを整えています。`scoped` を付けることで、このスタイルは `App.vue` の中だけに適用されます。

</details>

#### 練習 3: マスタッシュ構文で式を使う

**課題:**

`{{ }}` の中で JavaScript の式を使って、以下を表示してください。

1. 税抜価格 1000円 に対する税込価格（税率10%）を計算して表示する
2. 自分の名前を大文字に変換して表示する（`toUpperCase()` を使用）

**ヒント:**

`{{ }}` の中に計算式やメソッド呼び出しを直接書くことができます。例: `{{ 100 * 1.1 }}`

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
const price: number = 1000
const taxRate: number = 0.1
const name: string = 'taro'
</script>

<template>
  <div class="container">
    <h1>マスタッシュ構文で式を使う</h1>
    <p>税抜価格: {{ price }}円</p>
    <p>税込価格: {{ price + price * taxRate }}円</p>
    <p>名前（小文字）: {{ name }}</p>
    <p>名前（大文字）: {{ name.toUpperCase() }}</p>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
  font-family: sans-serif;
}

h1 {
  color: #42b883;
  font-size: 1.5rem;
  margin-bottom: 1rem;
}

p {
  color: #333;
  margin: 0.5rem 0;
}
</style>
```

**解説:**

`{{ price + price * taxRate }}` のように、`{{ }}` の中で直接計算ができます。`{{ name.toUpperCase() }}` のようにメソッド呼び出しも可能です。ただし、複雑な計算はテンプレートに直接書かず、第5章で学ぶ `computed` を使う方がベストプラクティスです。

</details>

---

### まとめ

この章では以下の内容を学びました。

| 学んだこと | ポイント |
|---|---|
| 開発環境のセットアップ | Node.js（20.x LTS）+ VS Code + Vue - Official 拡張機能 |
| プロジェクト作成 | `npm create vue@latest` で Vue3 + TypeScript プロジェクトを生成 |
| プロジェクト構造 | `index.html` → `main.ts` → `App.vue` の起動フロー。開発は `src/` 内で行う |

**キーワード**: `Node.js`, `npm`, `create-vue`, `Vite`, `SFC`, `HMR`, `<script setup>`

---

### 次の章へ

次の第3章では、`.vue` ファイル（SFC）の構造をより深く理解し、`<script setup>` / `<template>` / `<style scoped>` の3つのブロックの役割と、Vue アプリがブラウザに表示されるまでの仕組みを詳しく学びます。
