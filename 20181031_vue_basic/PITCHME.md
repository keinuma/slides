## Vue入門輪読会

2018/10/31  
沼田

---

### 3.3章 コンポーネント間の通信
Vue.jsでは

- 親子コンポーネントのみデータのやり取りが許可されている
- 親から子へはデータを送り、子はイベントを親に伝えつことしかできない

---

### 親から子へデータを送る
- `props` を使う (`data` などと同じ)
- バリデーションなどオプションを指定できる
- 親からテンプレートの `v-bind` で渡す

---

### 子供の定義

- `item-desc` というコンポーネントを作る
- `props` に `itemName` を加える
    - 必須かつ文字列

---

```html
<script>
Vue.component('item-desc',  // 子供Componentの名前
  props: {
    itemName: {
      type: Number,
      required: true
    }
  }
)
</script>
```

---

### 親の定義

- 子供コンポーネントをHTML内で使用する
- `v-bind` で自分のデータを `props` に渡す
- `itemName` は属性ではケバブケースになる

---

```html
<template>
<item-desc 
    v-bind:item-name="MyItem"
></item-desc>
</template>

<script>
new Vue({
  el: #app,
  data: {
    myItem: 1
  }
})
</script>
```

---

### 子供から親へイベントを送る
- 子から親へは `$emit` メソッドを使ってカスタムイベントを送る
- 親は `v-on` で受け取っと処理する

---

### 子供の定義
- 子供にボタンを配置
- ボタンが押されたら親にイベント `increment` を発火する

---

```html
<div id="child">
  <button v-on:click="eventFunc">押してね</button>
</div>

<script>
Vue.component('item-desc',  // 子供Componentの名前
  props: {
    itemName: {
      type: Number,
      required: true
    }
  },
  methods: {
    eventFunc: function() {
      this.$emit('increment')
    }
  }
)
</script>
```

---

### 親の定義
- 子供から `increment` イベントで監視
- イベントが来たら `doInc` メソッドを呼び出し
- `data` の変更は子供に反映される

---

```html
<div id="app">
  <item-desc
    v-bind:item-name="MyItem"
    v-on:increment="doInc"
  ></item-desc>
</div>

<script>
new Vue({
  el: #app,
  data: {
    myItem: 1
  },
  methods: {
    doInc: function() {
      this.myItem += 1
    }
  }
  
})
</script>
```

---

### `props` と イベントを使わないとき
- 子供から `this.$parent` とかでアクセスできる
- けどやっちゃダメ

---

### 親子以外とやりとりするとき
- 複雑になるときはステートを導入する
- Vuexを使おう (7章参照)

---

### `props`を双方向バインディングする

- `$emit()` と同時に `props` を変えたいとき
- [詳しくはこちら](https://jp.vuejs.org/v2/guide/components-custom-events.html#sync-%E4%BF%AE%E9%A3%BE%E5%AD%90)

---

おわり