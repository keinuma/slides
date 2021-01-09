[drag=300 30, drop=center, flow=col true]

## Firestoreを実装してみた話

2021/1/5 沼田

---

[drag=50 20, drop=top, pad=0px]
## 今日話すこと

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- Firestoreとは
- クライアントによるFirestoreの使い方
- Firestoreを考慮したクライアントの設計
@ul

---

[drag=50 20, drop=top, pad=0px]
## Firestoreとは

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- NoSQLデータベース
- 他のNoSQLに比べてリレーション、クエリが柔軟
- Web, Mobile両方対応
@ul

---

[drag=80 10, drop=10 5, pad=0px]
## Firestoreのデータ構造

![drag=60 85, drop=20 15, fit=1.0, stretch=true](20210105_firestore/image/structure-data.png)

---

[drag=60 20, drop=top, pad=0px]
## Firestoreのデータ構造

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- CollectionがRDBのテーブル
- Documentがレコード
- CollectionをネストしたSubcollection作れる
@ul

---

[drag=90 20, drop=top, pad=0px]
## Firestoreの使い方

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.6](false)
- 参照
  - Collectionと条件を指定して検索
  - 更新を検知できる
- 追加・更新
  - Document単位で実施
@ul

---

[drag=80 20, drop=top, pad=0px]
## 参照

[drag=90 90, drop=center, pad=10px]
```javascript
import * as firebase from 'firebase/app'

const firestore = firebase.firestore()
const collection = firestore.collection('messages')
collection
  .where('uids', 'array-contains', 'xxxx-xxx-xxx')
  .get()
  .then((document) => {
    const data = document.data()
    console.log(data)
  })
```

---

[drag=80 10, drop=10 5, pad=0px]
## 参照(変更を検知)

[drag=90 90, drop=5 10, pad=10px]
```javascript
const firestore = firebase.firestore()

const collection = firestore.collection('messages')
const unsubscribe = collection
  .where('uids', 'array-contains', 'xxxx-xxx-xxx')
  .onSnapshot()
  .then((snapshot) => {
    const data = snapshot.data()
    console.log(data)
  })
window.addEventListener('unload', unsubscribe)
```

---

[drag=80 20, drop=top, pad=0px]
## 追加

[drag=90 90, drop=center, pad=10px]
```javascript
const firestore = firebase.firestore()

const collection = firestore.collection('messages')

collection.doc('xxxx-xxx-xxx').set({
  text: 'hogehoge'
})
```

---

[drag=90 20, drop=top, pad=0px]
## Firestoreからデータ構造を変換する

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- CollectionはFirestoreのデータとアプリのデータを変換できる
- データの変換は双方向で定義する
@ul

---

[drag=100 100, drop=0 0, fit=0.9, pad=0px]
```javascript
const converter = {
  toFirestore(messageModel) {
    return {
      ...messageModel,
      updatedAt: firestore.Timestamp.fromDate(messageModel.updatedAt)
    }
  },
  fromFirestore(snapshot) {
    const messageData = snapshot.data()
    return new MessageModel({
      ...messageData,
      updatedAt: messageData.updatedAt.toDate()
    })
  }
}
const convertCollection =  collection.withConverter(converter)
```

---

[drag=50 20, drop=top, pad=0px]
## Firestoreの設計

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- Firestoreに対するメソッドをラップしてClass化
- Converterはアプリのデータ構造とFirestore両方知っているため分けて実装
@ul

---

![drag=90 80, drop=5 10, fit=1.1, stretch=true](20210105_firestore/image/domain-architecture.png)

---

[drag=90 10, drop=5 5]
## Firestoreのメソッドをラップ

[drag=90 90, drop=5 15, fit=0.9, pad=0px]
```javascript
class FirestoreRepository {
  private readonly collection

  constructor(path, converter) {
    this.collection = db.collection(path)
      .withConverter(converter)
  }

  subscribeById(id, callback) {
    return this.collection.doc(id).onSnapshot(
      (snapshot) => callback(snapshot.data())
    )
  }
}
```


---

[drag=80 20, drop=10 0]
## ドメイン別に変換方法を定義

[drag=90 90, drop=5 10, fit=1.0, pad=0px]
```typescript
const converter = {
  toFirestore(messageModel) { ... },
  fromFirestore(snapshot) { ... }
}

const MessagesRepository = new FirestoreRepository(
    'messages', 
    converter
)
```

---

[drag=50 20, drop=top, pad=0px]
## まとめ

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- Firestoreはデータ変換のオプション(Converter)がある
- FirestoreのメソッドをRepository化してConverterを分けて実装すると責務を分離できる
@ul
