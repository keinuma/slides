[drag=300 30, drop=center, flow=col true]

## Flutter と React Native 比較

2020/11/17 沼田

---

[drag=50 20, drop=top, pad=0px]
## 今回のお話

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- カミナシではReact Native + Expoを利用してる
- FlutterはReact Nativeと比較すると何が嬉しいか知りたい
- 開発・運用観点でまとめてみた
@ul

---

[drag=50 20, drop=top, pad=0px]
## 結論

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- Flutterはエコシステムが充実していてアプリ開発に必要な機能が整っている
- 日本の事例が少ないが今後も増えてきそう
@ul

---

[drag=50 20, drop=top, pad=0px]
## 注意

[drag=100 100, drop=0 50, flow=col true]
React Nativeで補えない部分はExpoと比較してお話します

---
[drag=50 20, drop=top, pad=0px]
## 比較ポイント

@ul[list-spaced-sm-bullets, drag=100 100, drop=-5 10, fit=1.6](false)
1. 　基本情報
1. 　開発環境
2. 　UI
3. 　状態管理
4. 　テスト
@ul

---

[drag=100, drop=center, fit=1.3]
## 基本情報

---

[drag=100, drop=0 0, fit=1.7]
|  | Flutter | React Native |
| --- | --- | --- |
| 企業 | Google | Facebook |
| 言語 | Dart | JavaScript |
| GitHubスター数 | 107k | 91.3k |

---

[drag=100, drop=center, fit=1.3]
## 開発環境

---

[drag=50 20, drop=top, pad=0px]
## React Native

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- パッケージ管理は npm or yarn
- Lint + Formatter は ESLint + Prettier
- ホットリロード
- 開発、リリースはExpo CLIが便利
@ul

---

[drag=50 20, drop=top, pad=0px]
## Flutter

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- パッケージ管理は pub(Yaml)
- 開発環境構築、Lintは Flutter CLI
- アプリ向けビルドできる
- ホットリロード
@ul

---

[drag=50 10, drop=top, pad=0px]
## pubspec.yamlの例

[drag=80 100, drop=10 5, fit=1.0]
```yaml
name: flutter_sample

environment: # Flutter SDKのバージョンを指定
  sdk: ">=2.7.0 <3.0.0"

dependencies: # 依存関係
  flutter:
    sdk: flutter

dev_dependencies:
  flutter_test:
    sdk: flutter

flutter: # アプリの基本Theme、フォントの情報を追加できる
  uses-material-design: true
```

---
[drag=50 20, drop=top, pad=0px]
## 開発環境

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- Flutter CLIはExpo CLI相当の機能を提供している
- 開発、リリース周りは両方とも差はない
@ul

---

[drag=100, drop=0 -10, fit=1.3]
## UI
[drag=100, drop=0 10, fit=1.3]
### レンダリング

---

[drag=50 20, drop=top, pad=0px]
## React Native

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- Reactのコア部分を利用してレンダリング
- React NativeのコンポーネントがネイティブのUIと一致している
  - React NativeのView は Androidの `android.view` であり iOSの `UIView`
@ul

---

[drag=50 20, drop=top, pad=0px]
## Flutter

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- Flutterは FWのAPIを担うflutter/flutterと描画周りの flutter/engineがある
- flutter/engineは独自のレンダリングエンジンになっている
@ul

---
[drag=50 10, drop=top, pad=0px]
## Flutterの構成図

![drag=60 85, drop=20 15, fit=1.2, stretch=true](20201117_flutter/image/archdiagram.png)

---

[drag=100, drop=0 -10, fit=1.3]
## UI
[drag=100, drop=0 10, fit=1.3]
### 実装方法

---

[drag=50 20, drop=top, pad=0px]
## React Native

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- JSXを使ってHTML Likeにかける
- Styleを使ってCSS Likeにレイアウト、見た目を変えられる
- 宣言的な記述
- React Native自体は最低限のコンポーネントのみ
  - UIライブラリを利用することが多い
@ul

---

[drag=50 20, drop=top, pad=0px]
## Flutter

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- Reactにインスパイアされてるので宣言的記述は一緒
- Widgetを使って実装する
- 用意されているWidgetの種類が多い
  - Material Designはpackage追加なしで使える
@ul

---

[drag=60 100, drop=20 0, fit=0.6]
```dart
class MyAppBar extends StatelessWidget {
  MyAppBar({this.title});
  final Widget title;

  @override
  Widget build(BuildContext context) {
    return Container(
      height: 56.0,
      padding: const EdgeInsets.symmetric(horizontal: 8.0),
      decoration: BoxDecoration(color: Colors.blue[500]),
      child: Row(
        children: <Widget>[
          IconButton(
            icon: Icon(Icons.menu), 
            tooltip: 'Navigation menu', 
            onPressed: null
          ),
          Expanded(
            child: title,
          ),
          IconButton(
            icon: Icon(Icons.search),
            tooltip: 'Search',
            onPressed: null,
          )
        ],
      )
    );
  }
}
```

---

[drag=50 20, drop=top, pad=0px]
## UI

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- React Native, Flutter共に宣言的UIで実装することは一緒
- UIコンポーネントの充実度はFlutterが上
@ul

---

[drag=100, drop=center, fit=1.3]
## 状態管理

---

[drag=50 20, drop=top, pad=0px]
## React Native

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- ローカルはhooksを利用
- GlobalにはRedux, Mobx, Recoil, Rxが選択肢に上がる
- Storeを一つ持って各コンポーネントが参照、更新する
@ul

---

[drag=50 20, drop=top, pad=0px]
## Flutter

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- ローカルはFlutter向けのhooksを利用（パッケージ）
- Global Stateは複数の実装方法がある
  - Redux, RxDart
  - block
  - Riverpod + Freezed + state_notifier
@ul

---

[drag=50 20, drop=top, pad=0px]
## 状態管理

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- React Native, Flutter共に複数の手段がある
- Flutterはまだまだメインの座を奪い合っている段階
  - monoさんは最後の `Riverpod + Freezed + state_notifier` をおすすめしてる
@ul


---

[drag=100, drop=center, fit=1.3]
## テスト

---

[drag=50 20, drop=top, pad=0px]
## React Native

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- Unit Test: Jest
- Integration Test: Jest snapshot test or Storybook
- E2E Test: Cypress, Selenium
@ul

---

[drag=50 20, drop=top, pad=0px]
## Flutter

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- Unit Test: flutter_test
- Integration Test: WidgetTest in flutter_test
- E2E Test: flutter_driver
@ul


---

[drag=50 20, drop=top, pad=0px]
## テスト

@ul[list-spaced-sm-bullets, drag=80, drop=10 20, fit=1.5](false)
- React NativeはJestや複数のライブラリに依存している
- Flutterは公式パッケージ内で各レイヤのテストが完結する
@ul

---

[drag=80 20, drop=top, pad=0px]
## React Nativeとの棲み分けは？

@ul[list-spaced-sm-bullets, drag=90, drop=5 10, fit=1.5](false)
- エコシステムの観点だとFlutterに軍配が上がりそう
- JavaScript資産をWebと共有したい場合はReact Nativeが良さそう
- Flutter for webに期待
@ul

---

[drag=50 20, drop=top, pad=0px]
## まとめ

@ul[list-spaced-sm-bullets, drag=90, drop=5 10, fit=1.5](false)
- Flutterはアプリ開発に必要な機能、運用フローが整っている
- Flutterの不採用理由に機能不足が入ることは少ない印象
@ul

---

[drag=60 20, drop=top, pad=0px]
## 参考

@ul[list-spaced-sm-bullets, drag=100, drop=0 10, fit=1.5](false)
- 公式ドキュメント
- 著名なFlutterエンジニアのTwitter
  - @_mono さん
  - @razokulover さん
- monoさんのMedium
  - https://medium.com/flutter-jp/
@ul
