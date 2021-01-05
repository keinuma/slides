[drag=300 30, drop=center, flow=col true]

## JavaScriptでFirestoreを実装する

2021/1/5 沼田

---

[drag=50 20, drop=top, pad=0px]
## Firestoreとは

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- NoSQLデータベース
- 他のNoSQLに比べてリレーション、クエリが柔軟
- Web, Mobile両方対応
@ul

---

[drag=50 10, drop=top, pad=0px]
## Firestoreのデータ構造

![drag=60 85, drop=20 15, fit=1.2, stretch=true](20210105_firestore/image/structure-data.png)

---

[drag=50 20, drop=top, pad=0px]
## Firestoreのデータ構造

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- CollectionがRDSのテーブル
- Collectionに対してSubcollectionやDocumentにCollectionの参照を保存できる
- JavaScript資産をどのくらい使いたいかでReact Nativeと棲み分けになりそう
@ul

---

[drag=50 20, drop=top, pad=0px]
## JSを使ったFirestoreの参照方法

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- getメソッド: 指定されたクエリのデータを取得
- onSnapshotメソッド: 指定されたクエリのデータを取得、変更を監視
@ul

---

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

```javascript
import * as firebase from 'firebase/app'

const firestore = firebase.firestore()
const collection = firestore.collection('messages')
const unsubscribe = collection
  .where('uids', 'array-contains', 'xxxx-xxx-xxx')
  .get()
  .then((snapshot) => {
    const data = snapshot.data()
    console.log(data)
  })
window.addEventListener('unload', function() {
  unsubscribe()
})
```

---

[drag=50 20, drop=top, pad=0px]
## Firestoreからデータ構造を変換する

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- CollectionはFirestoreのデータとアプリのデータを変換するメソッドがある
- データの変換は双方向で定義する
@ul

---

```typescript
const converter = {
  toFirestore(messageModel: MessageModel): firebase.firestore.DocumentData {
    return {
      ...messageModel,
      updatedAt: firestore.Timestamp.fromDate(messageModel.updatedAt)
    }
  },
  fromFirestore(
    snapshot: firestore.QueryDocumentSnapshot<MessageFirestore>,
    options: firestore.SnapshotOptions
  ): MessageModel[] {
    const messageData = snapshot.data()
    return new MessageModel({
      ...messageData,
      updatedAt: messageData.updatedAt.toDate()
    })
  }
}

const collection = firestore.collection('messages').withConverter(converter)
```

---

[drag=50 20, drop=top, pad=0px]
## Firestoreの設計

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- Firestoreに対するメソッドをラップしてClass化
- Converterはアプリのデータ構造とFirestore両方知っているため分けて実装
@ul

---

[drag=90 90, drop=center, fit=1.1]
Firestoreのメソッドをラップする
```typescript
export class FirestoreRepository<T extends { id: string }> {
  private readonly collection: firestore.CollectionReference<T>

  constructor(path: string, converter: firestore.FirestoreDataConverter<T>) {
    this.collection = db.collection(path).withConverter(converter)
  }

  subscribeById(id: string, callback: (data: T | undefined) => void): Unsubscribe {
    return this.collection.doc(id).onSnapshot((snapshot) => {
      callback(snapshot.data())
    })
  }
}
```


---

[drag=90 90, drop=center, fit=1.1]
Converterを注入してドメイン別に変換する

```typescript
export const MessagesFirestoreRepository = new FirestoreRepository<Message>(
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

---

[drag=60 20, drop=top, pad=0px]
## 参考

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- https://gist.github.com/ts020/183c7e51f75edb8635ad88e8d151a262
@ul
