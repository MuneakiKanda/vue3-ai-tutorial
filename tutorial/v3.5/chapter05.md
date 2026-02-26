## 第5章: リアクティビティの基礎

> **読了時間目安**: 40分
> **難易度**: ★★★☆☆

---

### この章で学ぶこと

この章を終えると、以下のことができるようになります。

- `ref` でリアクティブな値を作成し、値の変更を自動的にUIに反映できる
- `ref` と `reactive` の違いを理解し、適切に使い分けられる
- `computed` で他のリアクティブな値から算出される値を定義できる
- `watch` / `watchEffect` でリアクティブな値の変更を監視し、副作用を実行できる

---

### 前提

この章を読む前に、以下が完了している必要があります。

- 第3章で SFC の構造（`<script setup lang="ts">`）を理解していること
- 第4章でテンプレート構文（`{{ }}`、`v-bind`、`v-if`、`v-for`）を理解していること

---

### リアクティビティとは？

#### なぜリアクティビティが必要なのか

第3章・第4章で書いたコードには、ある大きな制限がありました。変数の値が **固定** されていて、変更できなかったことです。

たとえば、第4章の商品リストでは `inStock: true` と書いたら、ずっと「在庫あり」のままでした。ボタンを押して値を変えることも、時間が経って値が変わることもありません。

しかし、実際のアプリケーションでは、データは常に変化します。

- ユーザーがカウンターのボタンを押したら、数字が増える
- 商品がカートに追加されたら、合計金額が更新される
- フォームに文字を入力したら、画面にリアルタイムで反映される

こういった「**データが変わったら、UIも自動的に変わる**」仕組みが **リアクティビティ（Reactivity）** です。

#### リアクティビティを日常で例えると

リアクティビティは、スプレッドシート（Excel や Google スプレッドシート）に似ています。

```
┌─────┬──────────┬───────────────┐
│     │    A     │      B        │
├─────┼──────────┼───────────────┤
│  1  │  価格    │    1000       │
│  2  │  税率    │    0.1        │
│  3  │  税込    │  =A1*(1+A2)   │  ← A1 や A2 を変えると B3 が自動で変わる
└─────┴──────────┴───────────────┘
```

セル B3 に数式 `=A1*(1+A2)` を入れると、A1 や A2 の値を変更するだけで B3 が **自動的に** 再計算されます。いちいち B3 を手動で更新する必要はありません。

Vue のリアクティビティも同じです。データ（`ref`）を変更すると、そのデータを使っているテンプレートや算出プロパティ（`computed`）が **自動的に** 更新されます。

#### 通常の変数ではなぜダメなのか

通常の JavaScript 変数を使うとどうなるか見てみましょう。

```typescript
// 通常の変数
let count = 0
count = count + 1  // 変数は変わるが…
// → テンプレートの {{ count }} は更新されない
```

通常の変数では、値を変更してもVueはその変更を **検知できません**。テンプレートは古い値を表示し続けます。

Vue が変更を検知して自動更新するためには、**リアクティブな変数** を使う必要があります。それが `ref` と `reactive` です。

---

### `ref` と `reactive`

#### `ref` — もっとも基本的なリアクティブ変数

**`ref`** は、任意の値を **リアクティブ** にするための関数です。`ref` で包んだ値が変更されると、その値を使っているテンプレートが自動的に更新されます。

```typescript
import { ref } from 'vue'

const count = ref(0)       // リアクティブな変数を作成
count.value = count.value + 1  // .value でアクセス・変更する
```

`ref` の重要なルールは以下の2つです。

1. **`<script>` 内では `.value` でアクセスする** — `count.value`
2. **`<template>` 内では `.value` は不要** — `{{ count }}` で直接アクセスできる（Vue が自動的にアンラップする）

> `ref` は値を「箱」に入れるイメージです。`<script>` の中では箱を開けて取り出す（`.value`）必要がありますが、テンプレートでは Vue が自動で開けてくれます。

#### `reactive` — オブジェクト全体をリアクティブにする

**`reactive`** は、オブジェクトや配列を丸ごとリアクティブにする関数です。

```typescript
import { reactive } from 'vue'

const state = reactive({
  count: 0,
  name: 'Vue',
})

state.count++  // .value は不要。直接プロパティにアクセスする
```

#### `ref` と `reactive` の使い分け

| 特徴 | `ref` | `reactive` |
|---|---|---|
| 扱える値 | プリミティブ（数値、文字列 等）+ オブジェクト | オブジェクト・配列のみ |
| アクセス方法 | `.value` が必要（template 内は不要） | 直接アクセス |
| 再代入 | `count.value = 新しい値` で可能 | 再代入するとリアクティビティが切れる |
| 分割代入 | 安全 | リアクティビティが切れる |

**このチュートリアルでは `ref` を推奨** します。理由は以下のとおりです。

- プリミティブ値もオブジェクトも扱える
- `ref` で統一すると、コードの書き方が一貫する
- 再代入してもリアクティビティが維持される

> Vue の公式ドキュメントでも `ref` の使用が推奨されています。

---

### ハンズオン

実際にコードを書いて動かしてみましょう。

#### ステップ 1: カウンターアプリで `ref` の基本を学ぶ

もっとも基本的な `ref` の使い方を、カウンターアプリで学びましょう。

`src/App.vue`

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref<number>(0)

const increment = (): void => {
  count.value++
}

const decrement = (): void => {
  count.value--
}

const reset = (): void => {
  count.value = 0
}
</script>

<template>
  <div class="counter">
    <h1>カウンター</h1>
    <p class="count">{{ count }}</p>
    <div class="buttons">
      <button @click="decrement">-1</button>
      <button @click="reset">リセット</button>
      <button @click="increment">+1</button>
    </div>
  </div>
</template>

<style scoped>
.counter {
  max-width: 300px;
  margin: 40px auto;
  text-align: center;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
}

.count {
  font-size: 3rem;
  font-weight: bold;
  color: #42b883;
  margin: 16px 0;
}

.buttons {
  display: flex;
  gap: 8px;
  justify-content: center;
}

button {
  padding: 8px 20px;
  font-size: 1rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #fff;
  cursor: pointer;
}

button:hover {
  background-color: #f0f0f0;
}
</style>
```

- **何をしているか**: `ref<number>(0)` でリアクティブなカウンター変数を作成し、ボタンのクリックで値を増減しています。
- **なぜそう書くのか**: `ref` を使うことで、`count.value` を変更するだけでテンプレートの `{{ count }}` が自動的に更新されます。
- **注目ポイント**: `<script>` 内では `count.value++` と `.value` でアクセスしていますが、`<template>` 内では `{{ count }}` と `.value` なしで使えています。`@click` は第6章で詳しく学びますが、ここでは「クリックしたら関数を実行する」という意味だと理解してください。

> **`@click` について**: `@click="increment"` は「クリックしたら `increment` 関数を実行する」という意味です。イベントハンドリングの詳細は第6章で学びます。

#### ステップ 2: `ref` でオブジェクトと配列を扱う

`ref` はプリミティブ値だけでなく、オブジェクトや配列も扱えます。

`src/App.vue`

```vue
<script setup lang="ts">
import { ref } from 'vue'

interface User {
  name: string
  age: number
}

const user = ref<User>({
  name: '太郎',
  age: 25,
})

const hobbies = ref<string[]>(['読書', 'プログラミング'])

const updateAge = (): void => {
  user.value.age++
}

const addHobby = (): void => {
  hobbies.value.push('ゲーム')
}
</script>

<template>
  <div class="user-info">
    <h1>ユーザー情報</h1>
    <p>名前: {{ user.name }}</p>
    <p>年齢: {{ user.age }}歳</p>
    <button @click="updateAge">年齢を+1</button>

    <h2>趣味</h2>
    <ul>
      <li v-for="(hobby, index) in hobbies" :key="index">
        {{ hobby }}
      </li>
    </ul>
    <button @click="addHobby">趣味を追加</button>
  </div>
</template>

<style scoped>
.user-info {
  max-width: 400px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 12px;
}

h2 {
  color: #42b883;
  margin-top: 20px;
}

button {
  margin-top: 8px;
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #fff;
  cursor: pointer;
}

button:hover {
  background-color: #f0f0f0;
}

ul {
  padding-left: 20px;
}

li {
  margin: 4px 0;
}
</style>
```

- **何をしているか**: `ref` でオブジェクト（`User`）と配列（`string[]`）をリアクティブにしています。
- **なぜそう書くのか**: `ref` はプリミティブ値だけでなく、オブジェクトや配列にも使えることを確認するためです。
- **注目ポイント**: オブジェクトのプロパティ変更（`user.value.age++`）や配列への追加（`hobbies.value.push(...)`）も、Vue が自動的に検知してUIを更新します。テンプレートでは `user.name`（`.value` なし）でアクセスしていることにも注目してください。

#### ステップ 3: `computed` で税込価格を自動計算する

`computed` を使って、他のリアクティブ値から **自動的に算出される値** を作りましょう。

`src/App.vue`

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const price = ref<number>(1000)
const taxRate = ref<number>(0.1)

const taxIncludedPrice = computed<number>(() => {
  return Math.floor(price.value * (1 + taxRate.value))
})

const updatePrice = (newPrice: number): void => {
  price.value = newPrice
}
</script>

<template>
  <div class="price-calculator">
    <h1>税込価格計算</h1>

    <div class="input-group">
      <label>税抜価格:</label>
      <div class="price-buttons">
        <button @click="updatePrice(500)">500円</button>
        <button @click="updatePrice(1000)">1,000円</button>
        <button @click="updatePrice(2000)">2,000円</button>
        <button @click="updatePrice(5000)">5,000円</button>
      </div>
      <p>選択中: {{ price.toLocaleString() }}円</p>
    </div>

    <div class="result">
      <p>税率: {{ (taxRate * 100).toFixed(0) }}%</p>
      <p class="total">
        税込価格: <strong>{{ taxIncludedPrice.toLocaleString() }}円</strong>
      </p>
    </div>
  </div>
</template>

<style scoped>
.price-calculator {
  max-width: 400px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 16px;
}

.input-group {
  margin-bottom: 20px;
}

label {
  font-weight: bold;
  color: #555;
}

.price-buttons {
  display: flex;
  gap: 8px;
  margin: 8px 0;
  flex-wrap: wrap;
}

button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #fff;
  cursor: pointer;
}

button:hover {
  background-color: #42b883;
  color: white;
  border-color: #42b883;
}

.result {
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 16px;
}

.total {
  font-size: 1.3rem;
  color: #42b883;
  margin-top: 8px;
}
</style>
```

- **何をしているか**: `computed` で `price` と `taxRate` から税込価格を自動計算しています。`price` の値が変わると、`taxIncludedPrice` も **自動的に** 再計算されます。
- **なぜそう書くのか**: `computed` はスプレッドシートの数式のように、依存する値が変わったときだけ再計算されます。結果は **キャッシュ** されるので、何度参照しても計算は1回だけです。
- **注目ポイント**: `computed` の中では `.value` でアクセスしていますが（`price.value`）、テンプレートでは `{{ taxIncludedPrice }}` と `.value` なしで使えます。`computed` も `ref` と同じく、テンプレートで自動アンラップされます。

#### ステップ 4: `watch` でデータの変更を監視する

`watch` を使って、特定の値が変更されたときに **副作用（サイドエフェクト）** を実行する方法を学びましょう。

`src/App.vue`

```vue
<script setup lang="ts">
import { ref, watch } from 'vue'

const searchQuery = ref<string>('')
const searchLog = ref<string[]>([])

watch(searchQuery, (newValue, oldValue) => {
  if (newValue.length >= 2) {
    searchLog.value.push(
      `"${oldValue}" → "${newValue}" に変更されました`
    )
  }
})

const updateQuery = (event: Event): void => {
  const target = event.target as HTMLInputElement
  searchQuery.value = target.value
}
</script>

<template>
  <div class="search-demo">
    <h1>watch デモ — 検索入力の監視</h1>

    <div class="input-group">
      <label>検索ワード:</label>
      <input
        type="text"
        :value="searchQuery"
        @input="updateQuery"
        placeholder="2文字以上入力してください"
      />
    </div>

    <p>現在の入力: 「{{ searchQuery }}」</p>

    <div v-if="searchLog.length > 0" class="log">
      <h2>変更ログ（2文字以上のとき記録）</h2>
      <ul>
        <li v-for="(log, index) in searchLog" :key="index">
          {{ log }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.search-demo {
  max-width: 500px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 16px;
}

h2 {
  color: #42b883;
  font-size: 1rem;
  margin-bottom: 8px;
}

.input-group {
  margin-bottom: 12px;
}

label {
  display: block;
  font-weight: bold;
  color: #555;
  margin-bottom: 4px;
}

input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
  box-sizing: border-box;
}

input:focus {
  outline: none;
  border-color: #42b883;
}

.log {
  margin-top: 16px;
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 16px;
}

.log ul {
  padding-left: 20px;
}

.log li {
  margin: 4px 0;
  font-size: 0.9rem;
  color: #555;
}
</style>
```

- **何をしているか**: `watch` で `searchQuery` の変更を監視し、2文字以上になったらログに記録しています。
- **なぜそう書くのか**: `watch` は「値が変わったときに何かを実行する」ためのものです。API 通信のトリガーやログ記録など、値の変更に伴う **副作用** を実行するのに使います。
- **注目ポイント**: `watch` のコールバック関数は2つの引数を受け取ります。第1引数が **新しい値**（`newValue`）、第2引数が **変更前の値**（`oldValue`）です。

> **`@input` と `:value` について**: ここでは第6章で学ぶ `v-model` の内部動作を手動で書いています。`:value` で値を表示し、`@input` で入力変更を検知しています。第6章で `v-model` を使えば、もっと簡潔に書けます。

#### ステップ 5: すべてを組み合わせた商品価格シミュレーター

`ref`、`computed`、`watch` をすべて組み合わせたアプリを作りましょう。

`src/App.vue`

```vue
<script setup lang="ts">
import { ref, computed, watch } from 'vue'

const quantity = ref<number>(1)
const unitPrice = ref<number>(1500)
const discountRate = ref<number>(0)
const history = ref<string[]>([])

const subtotal = computed<number>(() => {
  return unitPrice.value * quantity.value
})

const discountAmount = computed<number>(() => {
  return Math.floor(subtotal.value * discountRate.value)
})

const total = computed<number>(() => {
  return subtotal.value - discountAmount.value
})

const increaseQuantity = (): void => {
  if (quantity.value < 99) {
    quantity.value++
  }
}

const decreaseQuantity = (): void => {
  if (quantity.value > 1) {
    quantity.value--
  }
}

const setDiscount = (rate: number): void => {
  discountRate.value = rate
}

watch(total, (newTotal, oldTotal) => {
  const diff = newTotal - oldTotal
  const sign = diff >= 0 ? '+' : ''
  history.value.unshift(
    `合計: ${newTotal.toLocaleString()}円（${sign}${diff.toLocaleString()}円）`
  )
  if (history.value.length > 5) {
    history.value.pop()
  }
})
</script>

<template>
  <div class="simulator">
    <h1>商品価格シミュレーター</h1>

    <div class="section">
      <h2>商品情報</h2>
      <p>単価: {{ unitPrice.toLocaleString() }}円</p>
      <div class="quantity-control">
        <span>数量:</span>
        <button @click="decreaseQuantity">-</button>
        <span class="quantity">{{ quantity }}</span>
        <button @click="increaseQuantity">+</button>
      </div>
    </div>

    <div class="section">
      <h2>割引率</h2>
      <div class="discount-buttons">
        <button
          v-for="rate in [0, 0.1, 0.2, 0.3]"
          :key="rate"
          :class="{ active: discountRate === rate }"
          @click="setDiscount(rate)"
        >
          {{ (rate * 100).toFixed(0) }}%
        </button>
      </div>
    </div>

    <div class="section result">
      <h2>計算結果</h2>
      <p>小計: {{ subtotal.toLocaleString() }}円</p>
      <p v-if="discountAmount > 0" class="discount">
        割引: -{{ discountAmount.toLocaleString() }}円
      </p>
      <p class="total">合計: {{ total.toLocaleString() }}円</p>
    </div>

    <div v-if="history.length > 0" class="section">
      <h2>変更履歴（直近5件）</h2>
      <ul>
        <li v-for="(entry, index) in history" :key="index">
          {{ entry }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.simulator {
  max-width: 480px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
}

h2 {
  color: #42b883;
  font-size: 1rem;
  margin-bottom: 8px;
}

.section {
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 12px;
}

.quantity-control {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-top: 8px;
}

.quantity {
  font-size: 1.5rem;
  font-weight: bold;
  min-width: 40px;
  text-align: center;
}

.discount-buttons {
  display: flex;
  gap: 8px;
}

button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #fff;
  cursor: pointer;
  font-size: 0.95rem;
}

button:hover {
  background-color: #e8f5e9;
}

button.active {
  background-color: #42b883;
  color: white;
  border-color: #42b883;
}

.discount {
  color: #e74c3c;
}

.total {
  font-size: 1.3rem;
  font-weight: bold;
  color: #2c3e50;
  margin-top: 8px;
}

.result {
  background-color: #e8f5e9;
}

ul {
  padding-left: 20px;
}

li {
  margin: 4px 0;
  font-size: 0.85rem;
  color: #555;
}
</style>
```

- **何をしているか**: `ref` で数量・単価・割引率を管理し、`computed` で小計・割引額・合計を自動計算し、`watch` で合計の変更を履歴に記録しています。
- **なぜそう書くのか**: 実際のアプリケーションでは、この3つの仕組み（`ref` / `computed` / `watch`）を組み合わせて使うのが基本です。
- **注目ポイント**: `computed` がスプレッドシートの数式のように連鎖しています。`subtotal` → `discountAmount` → `total` の順に、依存する値が変わると自動で再計算されます。

---

### コード全体

ここまでのハンズオンで作成したコードの最終形をまとめます。

`src/App.vue`

```vue
<script setup lang="ts">
import { ref, computed, watch } from 'vue'

const quantity = ref<number>(1)
const unitPrice = ref<number>(1500)
const discountRate = ref<number>(0)
const history = ref<string[]>([])

const subtotal = computed<number>(() => {
  return unitPrice.value * quantity.value
})

const discountAmount = computed<number>(() => {
  return Math.floor(subtotal.value * discountRate.value)
})

const total = computed<number>(() => {
  return subtotal.value - discountAmount.value
})

const increaseQuantity = (): void => {
  if (quantity.value < 99) {
    quantity.value++
  }
}

const decreaseQuantity = (): void => {
  if (quantity.value > 1) {
    quantity.value--
  }
}

const setDiscount = (rate: number): void => {
  discountRate.value = rate
}

watch(total, (newTotal, oldTotal) => {
  const diff = newTotal - oldTotal
  const sign = diff >= 0 ? '+' : ''
  history.value.unshift(
    `合計: ${newTotal.toLocaleString()}円（${sign}${diff.toLocaleString()}円）`
  )
  if (history.value.length > 5) {
    history.value.pop()
  }
})
</script>

<template>
  <div class="simulator">
    <h1>商品価格シミュレーター</h1>

    <div class="section">
      <h2>商品情報</h2>
      <p>単価: {{ unitPrice.toLocaleString() }}円</p>
      <div class="quantity-control">
        <span>数量:</span>
        <button @click="decreaseQuantity">-</button>
        <span class="quantity">{{ quantity }}</span>
        <button @click="increaseQuantity">+</button>
      </div>
    </div>

    <div class="section">
      <h2>割引率</h2>
      <div class="discount-buttons">
        <button
          v-for="rate in [0, 0.1, 0.2, 0.3]"
          :key="rate"
          :class="{ active: discountRate === rate }"
          @click="setDiscount(rate)"
        >
          {{ (rate * 100).toFixed(0) }}%
        </button>
      </div>
    </div>

    <div class="section result">
      <h2>計算結果</h2>
      <p>小計: {{ subtotal.toLocaleString() }}円</p>
      <p v-if="discountAmount > 0" class="discount">
        割引: -{{ discountAmount.toLocaleString() }}円
      </p>
      <p class="total">合計: {{ total.toLocaleString() }}円</p>
    </div>

    <div v-if="history.length > 0" class="section">
      <h2>変更履歴（直近5件）</h2>
      <ul>
        <li v-for="(entry, index) in history" :key="index">
          {{ entry }}
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.simulator {
  max-width: 480px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
}

h2 {
  color: #42b883;
  font-size: 1rem;
  margin-bottom: 8px;
}

.section {
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 12px;
}

.quantity-control {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-top: 8px;
}

.quantity {
  font-size: 1.5rem;
  font-weight: bold;
  min-width: 40px;
  text-align: center;
}

.discount-buttons {
  display: flex;
  gap: 8px;
}

button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #fff;
  cursor: pointer;
  font-size: 0.95rem;
}

button:hover {
  background-color: #e8f5e9;
}

button.active {
  background-color: #42b883;
  color: white;
  border-color: #42b883;
}

.discount {
  color: #e74c3c;
}

.total {
  font-size: 1.3rem;
  font-weight: bold;
  color: #2c3e50;
  margin-top: 8px;
}

.result {
  background-color: #e8f5e9;
}

ul {
  padding-left: 20px;
}

li {
  margin: 4px 0;
  font-size: 0.85rem;
  color: #555;
}
</style>
```

> **動作確認**: `npm run dev` でブラウザに表示される結果を確認してください。

**期待される表示結果:**

```
┌──────────────────────────────────────┐
│                                      │
│  商品価格シミュレーター               │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 商品情報                     │    │
│  │ 単価: 1,500円                │    │
│  │ 数量: [-]  1  [+]            │    │
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 割引率                       │    │
│  │ [0%] [10%] [20%] [30%]       │    │
│  └──────────────────────────────┘    │
│                                      │
│  ┌──────────────────────────────┐    │
│  │ 計算結果          (緑背景)   │    │
│  │ 小計: 1,500円                │    │
│  │ 合計: 1,500円                │    │
│  └──────────────────────────────┘    │
│                                      │
└──────────────────────────────────────┘

数量を3に、割引率を10%にすると:
小計: 4,500円 / 割引: -450円 / 合計: 4,050円
```

---

### 解説

コードの重要な部分を詳しく解説します。

#### `ref` と `.value` の仕組み

```typescript
const count = ref<number>(0)
count.value++  // script 内では .value でアクセス
```

```html
<p>{{ count }}</p>  <!-- template 内では .value は不要 -->
```

`ref()` は、渡された値を `{ value: 値 }` というオブジェクトで包みます。これが Vue の **Proxy** という仕組みを通して監視され、`.value` が変更されるとVue が自動的にテンプレートを再描画します。

テンプレート内で `.value` が不要なのは、Vue のテンプレートコンパイラが **自動アンラップ（Auto Unwrapping）** を行うためです。テンプレートが `{{ count }}` を見つけると、内部的に `count.value` として処理してくれます。

#### `computed` のキャッシュとリアクティブな依存関係

```typescript
const subtotal = computed<number>(() => {
  return unitPrice.value * quantity.value
})
```

`computed` には2つの重要な特性があります。

1. **リアクティブな依存関係の自動追跡**: `computed` の関数内で使われたリアクティブな値（`unitPrice.value` と `quantity.value`）を Vue が自動的に追跡します。いずれかの値が変更されると、`computed` が再計算されます。

2. **キャッシュ**: `computed` の結果はキャッシュされます。依存する値が変更されない限り、何度テンプレートで参照しても計算は **1回だけ** です。

通常の関数との違いを比較してみましょう。

```typescript
// ❌ 通常の関数 — 呼ばれるたびに毎回計算される
const getSubtotal = (): number => {
  return unitPrice.value * quantity.value
}

// ✅ computed — 依存値が変更されたときだけ再計算される
const subtotal = computed<number>(() => {
  return unitPrice.value * quantity.value
})
```

#### `watch` と `watchEffect` の違い

```typescript
// watch — 監視対象を明示的に指定する
watch(total, (newTotal, oldTotal) => {
  console.log(`${oldTotal} → ${newTotal}`)
})
```

`watch` の他に `watchEffect` というAPIもあります。

```typescript
import { watchEffect } from 'vue'

// watchEffect — 使用するリアクティブ値を自動追跡する
watchEffect(() => {
  console.log(`合計は ${total.value} 円です`)
})
```

| 特徴 | `watch` | `watchEffect` |
|---|---|---|
| 監視対象 | 明示的に指定する | 関数内のリアクティブ値を自動追跡 |
| 旧値の取得 | `(newVal, oldVal)` で取得可能 | 取得できない |
| 初回実行 | デフォルトでは実行しない | **即座に** 1回実行される |
| 用途 | 特定の値の変更に反応したいとき | 複数の値の変更にまとめて反応したいとき |

**使い分けの目安**: 旧値と新値を比較したい場合や、特定の値だけを監視したい場合は `watch` を使います。「値を使っている処理を自動的に再実行したい」場合は `watchEffect` が便利です。

---

### よくある間違い・FAQ

初心者がつまずきやすいポイントをまとめます。

#### ❌ よくある間違い 1: `<script>` 内で `.value` を忘れる

**間違ったコード:**

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref<number>(0)

const increment = (): void => {
  count++  // ❌ .value を忘れている
}
</script>
```

**何が問題か:**

`count` は `ref` オブジェクト自体を指しています。`count++` では ref オブジェクトにインクリメントしようとしており、意図した動作になりません。TypeScript でもエラーが出ます。

**正しいコード:**

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref<number>(0)

const increment = (): void => {
  count.value++  // ✅ .value を使う
}
</script>
```

#### ❌ よくある間違い 2: `computed` の中で値を変更しようとする

**間違ったコード:**

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const price = ref<number>(1000)

const taxIncluded = computed<number>(() => {
  price.value = price.value * 1.1  // ❌ computed 内で値を変更
  return price.value
})
</script>
```

**何が問題か:**

`computed` は **読み取り専用** の算出値です。`computed` の関数内でリアクティブな値を変更すると、無限ループや予期しない動作を引き起こす可能性があります。Vue は開発モードで警告を表示します。

**正しいコード:**

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const price = ref<number>(1000)

const taxIncluded = computed<number>(() => {
  return Math.floor(price.value * 1.1)  // ✅ 読み取りだけ行い、新しい値を返す
})
</script>
```

#### ❌ よくある間違い 3: `reactive` を再代入してリアクティビティを失う

**間違ったコード:**

```vue
<script setup lang="ts">
import { reactive } from 'vue'

let state = reactive({ count: 0 })

const reset = (): void => {
  state = reactive({ count: 0 })  // ❌ 再代入するとリアクティビティが切れる
}
</script>
```

**何が問題か:**

`reactive` で作った変数を再代入すると、テンプレートが参照しているオブジェクトとの紐づけが切れてしまいます。UIが更新されなくなります。

**正しいコード:**

```vue
<script setup lang="ts">
import { ref } from 'vue'

const state = ref({ count: 0 })

const reset = (): void => {
  state.value = { count: 0 }  // ✅ ref なら再代入してもリアクティビティが維持される
}
</script>
```

> これも `ref` を推奨する理由の1つです。

#### 💬 FAQ

**Q: `ref` と `computed` はどう使い分けますか？**

A: `ref` は「自分で値を設定する」変数に使います。`computed` は「他の値から自動的に計算される」値に使います。たとえば、税抜価格は `ref`（ユーザーが設定する）、税込価格は `computed`（税抜価格と税率から自動計算される）です。

**Q: `watch` はいつ使うべきですか？**

A: `watch` は **副作用** を実行したいときに使います。具体的には、値の変更をきっかけに API 通信を行う、ログを記録する、外部ライブラリと連携するなど、テンプレートの描画に直接関係しない処理を行いたい場合です。テンプレートに表示する値の計算には `computed` を使ってください。

**Q: テンプレートで `ref` の `.value` を書いてしまったらどうなりますか？**

A: `{{ count.value }}` と書いても動作はしますが、冗長です。Vue が自動アンラップしてくれるので、`{{ count }}` と書くのが正しい方法です。

---

### 練習問題

学んだ内容を定着させるための練習問題です。

#### 練習 1: BMI 計算機を作ろう

**課題:**

身長（cm）と体重（kg）を `ref` で定義し、BMI を `computed` で自動計算するアプリを作ってください。BMI の計算式は `体重(kg) ÷ 身長(m)²` です。ボタンで身長・体重を変更できるようにしてください。

BMI の判定も `computed` で行い、以下の基準で表示を切り替えてください。
- 18.5 未満: 低体重
- 18.5〜25 未満: 普通体重
- 25 以上: 肥満

**ヒント:**

身長は cm で管理し、計算時に m に変換（÷100）しましょう。`computed` は連鎖させて使えます。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const height = ref<number>(170)
const weight = ref<number>(65)

const bmi = computed<number>(() => {
  const heightInMeters = height.value / 100
  return Number((weight.value / (heightInMeters * heightInMeters)).toFixed(1))
})

const category = computed<string>(() => {
  if (bmi.value < 18.5) return '低体重'
  if (bmi.value < 25) return '普通体重'
  return '肥満'
})

const adjustHeight = (diff: number): void => {
  height.value = Math.max(100, Math.min(250, height.value + diff))
}

const adjustWeight = (diff: number): void => {
  weight.value = Math.max(30, Math.min(200, weight.value + diff))
}
</script>

<template>
  <div class="bmi-calculator">
    <h1>BMI 計算機</h1>

    <div class="control">
      <label>身長: {{ height }} cm</label>
      <div class="buttons">
        <button @click="adjustHeight(-5)">-5</button>
        <button @click="adjustHeight(-1)">-1</button>
        <button @click="adjustHeight(1)">+1</button>
        <button @click="adjustHeight(5)">+5</button>
      </div>
    </div>

    <div class="control">
      <label>体重: {{ weight }} kg</label>
      <div class="buttons">
        <button @click="adjustWeight(-5)">-5</button>
        <button @click="adjustWeight(-1)">-1</button>
        <button @click="adjustWeight(1)">+1</button>
        <button @click="adjustWeight(5)">+5</button>
      </div>
    </div>

    <div class="result">
      <p class="bmi-value">BMI: {{ bmi }}</p>
      <p class="bmi-category">判定: {{ category }}</p>
    </div>
  </div>
</template>

<style scoped>
.bmi-calculator {
  max-width: 400px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
}

.control {
  margin-bottom: 16px;
}

label {
  display: block;
  font-weight: bold;
  color: #555;
  margin-bottom: 4px;
}

.buttons {
  display: flex;
  gap: 8px;
}

button {
  padding: 6px 14px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: #fff;
  cursor: pointer;
}

button:hover {
  background-color: #e8f5e9;
}

.result {
  background-color: #f0f4f8;
  border-radius: 8px;
  padding: 16px;
  margin-top: 16px;
}

.bmi-value {
  font-size: 1.3rem;
  font-weight: bold;
  color: #42b883;
}

.bmi-category {
  margin-top: 4px;
  color: #555;
}
</style>
```

**解説:**

`height` と `weight` を `ref` で管理し、BMI を `computed` で自動計算しています。さらに BMI の値をもとに判定カテゴリも `computed` で求めています。`computed` が連鎖する実用的なパターンです。

</details>

#### 練習 2: 文字数カウンターを作ろう

**課題:**

テキスト入力欄を設け、入力された文字数を `computed` でリアルタイムにカウントしてください。さらに `watch` を使って、文字数が 10 文字を超えたらコンソールに「10文字を超えました」と表示してください。

**ヒント:**

テキストは `ref<string>('')` で管理します。文字数は `.length` で取得できます。入力欄は `:value` と `@input` で接続するか、第6章を先取りして `v-model` を使ってもOKです。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
import { ref, computed, watch } from 'vue'

const text = ref<string>('')
const warnings = ref<string[]>([])

const charCount = computed<number>(() => {
  return text.value.length
})

const updateText = (event: Event): void => {
  const target = event.target as HTMLTextAreaElement
  text.value = target.value
}

watch(charCount, (newCount, oldCount) => {
  if (newCount > 10 && oldCount <= 10) {
    warnings.value.push('10文字を超えました')
    console.log('10文字を超えました')
  }
})
</script>

<template>
  <div class="char-counter">
    <h1>文字数カウンター</h1>
    <textarea
      :value="text"
      @input="updateText"
      placeholder="ここに入力してください..."
      rows="4"
    ></textarea>
    <p :class="{ over: charCount > 10 }">
      文字数: {{ charCount }}
    </p>
    <ul v-if="warnings.length > 0">
      <li v-for="(warning, index) in warnings" :key="index">
        ⚠️ {{ warning }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
.char-counter {
  max-width: 400px;
  margin: 40px auto;
  padding: 24px;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 12px;
}

textarea {
  width: 100%;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
  resize: vertical;
  box-sizing: border-box;
}

textarea:focus {
  outline: none;
  border-color: #42b883;
}

p {
  margin-top: 8px;
  font-weight: bold;
  color: #42b883;
}

p.over {
  color: #e74c3c;
}

ul {
  margin-top: 8px;
  padding-left: 20px;
}

li {
  color: #e67e22;
  font-size: 0.85rem;
}
</style>
```

**解説:**

`text` を `ref` で管理し、文字数を `computed` で算出しています。`watch` で文字数の変化を監視し、10文字を超えた瞬間（`newCount > 10 && oldCount <= 10`）にのみ警告を記録します。旧値と新値の比較ができるのは `watch` ならではの機能です。

</details>

#### 練習 3: 温度変換器を作ろう

**課題:**

摂氏（℃）の温度を `ref` で管理し、華氏（℉）を `computed` で自動計算してください。さらに `watch` を使って、温度が 0℃ 以下になったら「氷点下です」、100℃ 以上になったら「沸点以上です」とメッセージを表示してください。

変換式: `華氏 = 摂氏 × 9 / 5 + 32`

**ヒント:**

ボタンで摂氏を増減させましょう。`watch` のコールバック内で条件分岐するだけです。

<details>
<summary>解答例を見る</summary>

`src/App.vue`

```vue
<script setup lang="ts">
import { ref, computed, watch } from 'vue'

const celsius = ref<number>(20)
const message = ref<string>('')

const fahrenheit = computed<number>(() => {
  return Number((celsius.value * 9 / 5 + 32).toFixed(1))
})

const adjustTemp = (diff: number): void => {
  celsius.value += diff
}

watch(celsius, (newTemp) => {
  if (newTemp <= 0) {
    message.value = '🧊 氷点下です'
  } else if (newTemp >= 100) {
    message.value = '🔥 沸点以上です'
  } else {
    message.value = ''
  }
})
</script>

<template>
  <div class="temp-converter">
    <h1>温度変換器</h1>

    <div class="controls">
      <button @click="adjustTemp(-10)">-10</button>
      <button @click="adjustTemp(-1)">-1</button>
      <span class="temp">{{ celsius }}℃</span>
      <button @click="adjustTemp(1)">+1</button>
      <button @click="adjustTemp(10)">+10</button>
    </div>

    <div class="result">
      <p>摂氏: {{ celsius }}℃</p>
      <p>華氏: {{ fahrenheit }}℉</p>
    </div>

    <p v-if="message" class="message">{{ message }}</p>
  </div>
</template>

<style scoped>
.temp-converter {
  max-width: 400px;
  margin: 40px auto;
  padding: 24px;
  text-align: center;
  font-family: sans-serif;
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
}

.controls {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  margin-bottom: 20px;
}

.temp {
  font-size: 2rem;
  font-weight: bold;
  color: #42b883;
  min-width: 80px;
}

button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: #fff;
  cursor: pointer;
}

button:hover {
  background-color: #e8f5e9;
}

.result {
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 16px;
}

.result p {
  margin: 4px 0;
  font-size: 1.1rem;
}

.message {
  margin-top: 12px;
  font-size: 1.2rem;
  font-weight: bold;
  color: #e67e22;
}
</style>
```

**解説:**

`celsius` を `ref` で管理し、`fahrenheit` を `computed` で自動計算しています。`watch` で摂氏の変化を監視し、氷点下または沸点以上のときにメッセージを表示します。`ref` → `computed` → `watch` の3つの仕組みを連携させた実用例です。

</details>

---

### まとめ

この章では以下の内容を学びました。

| 学んだこと | ポイント |
|---|---|
| `ref` | 値をリアクティブにする。`<script>` 内は `.value` でアクセス、`<template>` 内は自動アンラップ |
| `computed` | 他のリアクティブ値から自動計算される読み取り専用の値。結果はキャッシュされる |
| `watch` / `watchEffect` | リアクティブ値の変更を監視して副作用を実行する。`watch` は対象を明示、`watchEffect` は自動追跡 |

**キーワード**: `ref`, `.value`, `reactive`, `computed`, `watch`, `watchEffect`, `リアクティビティ`, `自動アンラップ`

---

### 次の章へ

この章でリアクティビティを学んだことで、データの変更が画面に自動反映される仕組みを理解しました。しかし、現在のコードではボタンクリック（`@click`）を使っただけで、ユーザー操作への対応は限定的です。次の第6章では、**イベントハンドリングとフォーム** を学びます。`v-on`（`@`）でさまざまなイベントに応答する方法や、`v-model` でフォーム入力と双方向バインディングする方法を身につけ、ユーザーと対話するアプリを作れるようになりましょう。
