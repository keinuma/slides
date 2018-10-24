## Vue入門輪読会

2018/10/24  
沼田

---

### 2.9章 ディレクティブ
```html
<a href="https://google.com" v-bind:class="hogehoge"></a>
```

のなかの→`v-bind`  
Vue版属性値的な

---

### ディレクティブ の特徴
- Vueの中の`data`や`methods`などJavaScriptの式を使える
- 属性と異なる記法がある

---

### 条件分岐
- `v-if`と`v-show`がある
- 引数に条件式を入れて評価する

例:
```html
<p v-show="isWinter">さむい</p>
```

---

### ifとshowの使い分け
- ifはDOMのレンダリングを制御 -> 一度きり描画する時
- showはstyleにdisplayが付与される -> 頻繁に表示が変わる時

```css
display: none
```

---

### クラスのバインディング
- UIの見た目を変えたい時に使用
- classやstyleを挿入できる

---

### classを使う時
 - `{'クラス名': '真偽値'}`の形
 - 規模が大きい時はクラス用の算出プロパティを作る
 
 例:
 ```html
<p v-bind:class="{tag: true, mecha: false}">
  テキスト
</p>
```

---

### styleを使うとき
- `{'スタイル名': '値'}`

例:
```html
<p v-bind:style="{color: 'red'}"></p>
```

---

### 省略方法
- `v-bind`は省略可能

例:
```html
<p :class="{tag: true, mecha: false}">
  テキスト
</p>
```


---

### 繰り返し処理
- `v-for="要素 in 配列"`形式で繰り返し処理できる(Pythonと一緒)
- v`-bind:key`を使って要素の変更を追跡している

例: 
```html
<ul>
  <li
    v-for="item in arr" v-bind:key="item"
      {{ item }}
  ></li>
</ul>
```

---

### イベントハンドリング
- `v-on:event="実行したい式"`
- イベントは`click`や`input`など使用可能
- イベントはJavaScriptのイベント属性

フォームの入力を描画する例:
```vue
<input v-on:input="value=$event.target.value"/>
{{ value }}
```

---

### イベントハンドリングの省略記法
- `@`で書き換え可能
```html
<input @input="value=$event.target.value"/>
{{ value }}
```

---

### フォーム入力バインディング
- `v-model`は双方向バインディング
- 意味的には

`v-bind:value` + `v-on:change`

です

例:
```html
<input v-model="value"/>
```
