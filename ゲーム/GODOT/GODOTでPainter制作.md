GODOTでPainter制作するという記事。やば。凄すぎる。

お絵かきソフト「Aquamarine Painter」を作ってみた
https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter

---
## はじめに

この記事は [グラフィックス全般 Advent Calendar 2024](https://qiita.com/advent-calendar/2024/graphics) の14日目の記事です。

今年の夏頃に私が制作した「Aquamarine Painter」というお絵かきツールについて技術面を含めて軽く紹介する記事を書いてみようと思います。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--yU-0Ul91--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/c062afad0914440e185ad6ed.png%3Fsha%3D70fdabe3dad1271182f9370f316f40366dbf384f)_Aquamarine Painter のスクリーンショット_

この [グラフィックス全般 Advent Calendar 2024](https://qiita.com/advent-calendar/2024/graphics) には他にもお絵かきツールを作成していらっしゃる方の記事があるようです。合わせて読んでみると面白いかもしれません。こちらは適当に作って終わらせた私とは違い、長年アップデートを続けているちゃんとしたプロダクトのようですよ。

## [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#aquamarine-painter-%E3%81%A8%E3%81%AF)Aquamarine Painter とは

2024年の夏頃に私が開発したベクター系のお絵かきソフトです。  
Godot Engine製で、PPW Curvesというスプライン曲線を採用したツールとなっています。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--776b5f9g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/43372b95cb84cded0ef1104f.png%3Fsha%3D2ff4a6e24185d64b7d9229daa1c969771d690554)_Aquamarine Painter のロゴ_

GitHubのリポジトリはこちらです。私が書いたコード部分についてはMITライセンスで公開しています。

このツールはある種のプロトタイプとして開発しているので、あまり読みやすいコードではないですが。。。

!

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E5%AE%9F%E8%A3%85%E3%81%AE%E7%9B%AE%E7%9A%84%E3%81%A8%E3%83%84%E3%83%BC%E3%83%AB%E3%81%AE%E4%BB%8A%E5%BE%8C)実装の目的とツールの今後

このツールはお絵かきツールのプロトタイプ的に作ったものです。実用できるようなツールを目指したものではありません。 残念ながら今後の機能追加のアップデートなども予定されていません。

今回私がこのツールを作成した目的は、 新しいスプライン曲線を扱ってみたいとか、お絵かきツールのレイヤー機能とかはどうやって作るかとか、お絵かきツールを実装するうえで必要になることを色々考えて実装するのを一度やってみたいというものでした。

この経験を踏まえて今後新しくお絵かきツールを作ったりする事はあるかもしれませんが、このツール自体は一旦これで開発は完了のつもりです。

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#aquamarine-painter-%E3%81%AE%E7%89%B9%E5%BE%B4)Aquamarine Painter の特徴

Aquamarine Painterは次のような特徴を備えています。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E7%89%B9%E6%AE%8A%E3%81%AA%E3%82%B9%E3%83%97%E3%83%A9%E3%82%A4%E3%83%B3%E6%9B%B2%E7%B7%9A%E3%82%92%E6%8E%A1%E7%94%A8%E3%81%97%E3%81%A6%E3%81%84%E3%82%8B)特殊なスプライン曲線を採用している

[PPW Curvesという特殊なスプライン曲線](https://www.jstage.jst.go.jp/article/transinf/E105.D/10/E105.D_2022PCP0006/_pdf)を採用しています。PPW Curvesについて詳しくは後述しますが、大雑把には次のような特徴の曲線です。

- コントロールポイントを全て通る曲線が描けます
- コントロールポイントは曲線の曲率極大な位置に置かれていて操作しやすいようです
- さらに曲線の尖り具合や曲がり方の微妙な調整を`phi`、`psi`、`weight`というパラメータで調整できます
- ベジェ曲線のタンジェントのコントローラーで曲線の調整をするのとは違った操作感です

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%9E%E3%83%86%E3%83%AA%E3%82%A2%E3%83%AB%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0)マテリアルシステム

異なるパスに同じ色を割り当てて一括で色を編集できます。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--lo4k44-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/e39256a7a6cc6ff87c2e7e20.png%3Fsha%3D2e1c5ff1ee7142b30449dd3ccee7722083cc531f)_マテリアルのリスト_

同じ色を持つパーツが複数のパスに分かれている際に、パス毎に色を割り当てているタイプのツールでは色の一括変更が若干難しいですが、このツールではマテリアルを使って色の割り当てを一つ間接的にすることで一括で同じ色を使っているパスの色を変更できます。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%81%8A%E7%B5%B5%E3%81%8B%E3%81%8D%E3%82%BD%E3%83%95%E3%83%88%E3%81%A3%E3%81%BD%E3%81%84%E3%83%AC%E3%82%A4%E3%83%A4%E3%83%BC%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0)お絵かきソフトっぽいレイヤーシステム

よくあるお絵かきソフトっぽいレイヤーのシステムを実装してあります。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--_fBST_J9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/5eeffbf8692ae95696a5ec1e.png%3Fsha%3D88bc52036a9cd79e41db3fa047293f0399703a0f)_よくあるレイヤーシステム_

具体的には以下のような操作が実装されています。

- レイヤーフォルダ
- レイヤー合成モード
- レイヤーの透明度
- 下のレイヤーでクリッピング

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--ojkbe2Cr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/b65fa89be2c4cc9cb3a000d8.png%3Fsha%3Dae113f2bcdfb35a55b1404eafe6e38e9a130c7fe)_レイヤーのフォルダーやクリッピングなどの様子_

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#aquamarine-painter-%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9)Aquamarine Painter の使い方

描画ツール でキャンバスをクリックしていくことで線を描くことが出来ます。  
線はコントロールポイントを持っており、操作ツール でコントロールポイントをドラッグすることで線の形状を編集できます。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--FVozwMnx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/cf3408b4cea4d7e79932d8e9.png%3Fsha%3D812ec5cb4b5ee01a2e7ddb27bf99b15d867d2f8f)_コントロールポイントが表示されている様子_

また、上記の操作ツールに加えて、曲線の微妙な曲がり具合を 調整ツール を利用することで以下のようなパラメータの調整ができます。

|操作|gif|
|---|---|
|コントロールポイントを左右ドラッグ|![weight](https://storage.googleapis.com/zenn-user-upload/deployed-images/d9bd2cfa8298d19fe99c6fd3.gif?sha=522aee3268d2e638295a586c9a7d4e246303323c)|
|コントロールポイントの間のセグメントを上下にドラッグ|![phi](https://storage.googleapis.com/zenn-user-upload/deployed-images/5f8cf59f26086dbf4c932297.gif?sha=50b95aa5828e0075c57c580a6ba0f5228a92a763)|
|コントロールポイントの間のセグメントを左右にドラッグ|![psi](https://storage.googleapis.com/zenn-user-upload/deployed-images/46b4de18557804376ff2dd4f.gif?sha=4eb920face68467db0740c644a7e0dedf556ca2c)|

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%82%AD%E3%83%BC%E3%83%9C%E3%83%BC%E3%83%89%E3%82%B7%E3%83%A7%E3%83%BC%E3%83%88%E3%82%AB%E3%83%83%E3%83%88)キーボードショートカット

|キー|機能|
|---|---|
|`Ctrl` + `Z`|アンドゥ|
|`Ctrl` + `Shift` + `Z`|リドゥ|
|`Ctrl` + `C`|選択中のマテリアル or レイヤーのコピー|
|`Ctrl` + `V`|ペースト|
|`1`|描画ツール|
|`2`|操作ツール|
|`3`|調整ツール|
|`4`|枠内に収める|
|`5`|拡大率を100%に戻す|
|`6`|左右反転|
|`Space`|コントローラーの一時非表示|

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E5%AE%9F%E8%A3%85%E6%A9%9F%E8%83%BD%E3%81%AE%E9%81%B8%E5%AE%9A)実装機能の選定

私はお絵かき自体は経験が浅く、まだ狙った線をきれいに引くのが苦手です。普段お絵かきをする際は何度も線を引いてはCtrl-Zを押してアンドゥするというのを繰り返しています。

さらに課題となるのは、ある程度線画を描き進めてみてからやっぱり全体のバランスが変な気がするとなって線を修正したくなることです。ラフでバランスを取って清書してみて、清書が一通り終わってみてからやっぱりもうちょっと調整したいとなって、もう一度ラフに戻ってバランスを調整し直してということを繰り返しているのですが、どうにも非効率なのが気になっていました。

後から線を調整するというのはベクター系のツールの強みです。ベクター系のお絵かきツールで使いやすいものがあればそれで上記の問題は解決しそうです。

また、私の描きたいイラストは一旦はアニメ塗りのイラストで、細かいペンのタッチなどは必要なく、どちらかというときれいに塗りつぶしができる方が重要です。これもベクター系のツールを選ぶ大きな理由の一つです。

しかし既存のベクター系のツールの挙動があまり個人的に好みではありませんでした。

CLIP STUDIO PAINTのベクターレイヤー

Adobe Illustrator

そこで今回はベクター系のツールを以下の機能にこだわって作ってみることにしました。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E5%BE%8C%E3%81%8B%E3%82%89%E7%B7%9A%E3%81%AE%E8%AA%BF%E6%95%B4%E3%81%8C%E3%81%A7%E3%81%8D%E3%82%8B)後から線の調整ができる

ベクター系のお絵かきツールで、後からコントロールポイントを操作して線を調整できるツールを作ります。コントロールポイントが少なく操作しやすいツールであるとなお良いです。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E8%89%B2%E3%81%AE%E5%89%B2%E3%82%8A%E5%BD%93%E3%81%A6%E3%81%8C%E4%B8%80%E6%8B%AC%E3%81%A7%E5%A4%89%E6%9B%B4%E3%81%A7%E3%81%8D%E3%82%8B)色の割り当てが一括で変更できる

個人的にはアニメ塗りのお絵かきをしていると複数パスに同じ色を割り当てたい場合が多かったです。そのような用途で使いやすいツールを作ります。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%81%8A%E7%B5%B5%E3%81%8B%E3%81%8D%E3%83%84%E3%83%BC%E3%83%AB%E3%81%A7%E9%A6%B4%E6%9F%93%E3%81%BF%E3%81%AE%E3%81%82%E3%82%8B%E3%83%AC%E3%82%A4%E3%83%A4%E3%83%BC%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0)お絵かきツールで馴染みのあるレイヤーシステム

CLIP STUDIO PAINTなどのメジャーなお絵かきツールっぽいレイヤーシステムを作ります。レイヤーの透明度やレイヤーフォルダー、下のレイヤーでクリッピングなどの仕組みを用意します。

---

上記の機能は Aquamarine Painter に一通り実装されています。

## [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#ppw-curves)PPW Curves

次に Aquamarine Painter の実装にあたって採用したスプライン曲線について紹介します。

Aquamarine Painterはベクターのスプライン曲線として全面的にPPW Curvesという曲線を利用しています。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--ih-3EXyT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/39773db8f5117c73586754bf.png%3Fsha%3Da6f65a1c6858805034dbba22778c488c66659ee1)

PPW Curvesは2022年に発表された比較的新しい曲線です。PPW Curvesは次のような特徴を持っています。

- コントロールポイントをすべて通る曲線
- C2連続の滑らかな曲線
    - ただしコントロールポイントやセグメントのパラメータで折れ線も可能
- コントロールポイントの操作の影響範囲がローカルである
- おおよそ曲率極大の位置にコントロールポイントがある
- 形状の曲がり方の微調整が可能
    - Psi、Phi、Weightというパラメータで調整可能
        - このパラメータの頭文字を取ってPPW Curvesという名前だそうです。

既存のコントロールポイントを曲率極大で通る曲線である κ-Curves や その有理版の κ-Curves それから2020年にSIGGRAPHで発表された「[A Class of C2 Interpolating Splines](https://dl.acm.org/doi/10.1145/3400301)」などと比べても、少ないコントロールポイントで様々な形状が描画できることが論文中で例示されています。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--Xi0tKPh5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/c6abdd403bd994d09047ee4e.png%3Fsha%3D7af81597e98948cf029c99fe8d6e87d6e73c1148)

今回この曲線を選んだ理由は、比較的新しいスプライン曲線を触ってみたいという技術的興味と、ベジェ曲線のコントロールは苦手だったためです。ベジェ曲線のタンジェントのコントローラーで曲線の調整をするより、すべての点を通って滑らかになってくれる曲線の方が操作しやすいのではないかと考えました。

PPW Curvesを見つけた経緯

  

## [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#godot-engine-%E3%81%A7%E3%81%AE%E5%AE%9F%E8%A3%85)Godot Engine での実装

次に Aquamarine Painter の実装について軽く紹介します。

Aquamarine Painter の実装にはGodot Engineを利用しています。Godot Engineの4.2で開発を始めて、途中で4.3がリリースされたのでそちらに切り替えました。

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#godot-engine-%E6%8E%A1%E7%94%A8%E3%81%AE%E7%90%86%E7%94%B1)Godot Engine 採用の理由

ツールの作成にあたってゲームエンジンを利用することにしたのは、ウィジェットを並べて作るようなよくあるGUIフレームワークでペイントソフトみたいな高度なグラフィクスを扱うツールを作れるのかよくわかっておらず、ゲームエンジンならshaderとか書けるし行けるかなと考えてのことです。

特にGodot Engineを選んだ理由は、最近盛り上がっているようなので触ってみたかったというのと、あとはGodot EngineのGUI自体がGodotで作られているという話を聞いてGUIツールが作りやすいかもという期待を込めてのことでした。わりと見切り発車。

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#ppw-curves-%E3%81%AE%E5%AE%9F%E8%A3%85)PPW Curves の実装

PPW Curvesを細かい折れ線にしたり、折れ線をさらにTriangulateしてポリゴンデータにするのには、大量の頂点を扱うため処理速度が必要そうです。そのため勉強も兼ねてRustでGDExtensionを書いてみました。

本当にGDExtension化する必要があったかどうかの計測などは行っていません。ただ、RustでGodotを扱うコードを書いてみたかったというのがRustのGDExtensionを使ってみた一番の理由です。

RustでGDExtensionを書く体験はとても良いものでした。Rust Analyzerによる型の補完なども働いてくれて、スムーズにコードを書くことができました。

PPW Curves の実装そのものは、Web上に公開されている既存のUnity実装をかなり参考にして、ほとんどそのままRustに置き換えた感じで実装しています。PPW Curvesの実装にあたってはニュートン法などを利用して最適化を回す部分があるのですが、そこらへんの初期値をヒューリスティックで決める方法やパラメータまわりの実装などもそのUnityの実装を参考にしました。

PPW Curvesを折れ線に変換した後、塗りつぶしのポリゴンを作成するためのTriangulateに次のRustのライブラリを使いました。

上記ライブラリにはいくつかバグや使いにくい仕様などがあり、PRを送って取り込んでもらったりもしました。

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#gui)GUI

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#dock)dock

GUIのdockの作成にはこちらのアドオンを利用しました。

パネルの幅を狭くしたときに中の要素が重なって被ってしまうなどの問題があり4.3での利用は完璧ではない感じは若干ありますが十分使えます。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%AC%E3%82%A4%E3%83%A4%E3%83%BC)レイヤー

レイヤーフォルダー周りの実装はだいぶ雑に作ったので見通しの悪いコードになってしまいました。

レイヤーをドラッグ・アンド・ドロップするときに、フォルダの子レイヤーも一緒にドラッグする必要があったり、ドラッグ先のフォルダの階層構造とかその開閉状況も適切に扱う必要があり、単純なReorderableなリストというわけにはいきません。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--O_f3VtZv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/2aa4f45de9a2fcf66a70eb21.png%3Fsha%3D8093c24ee6ac1f494634dc8d1f646b01d8f3080e)

今回、雑に動くソフトを作りたかったのがあって、レイヤーリストの部分の挙動を適切に切り出したクラスとかも作らず雑にレイヤーのデータを直接操作しているので、かなり見通しの悪いコードになっています。親レイヤーを選択したときにフォルダが折りたたまれているかでも挙動を変えたりとか、中々ダーティーな挙動が多くなっています。次お絵かきツールを作るときにはもう少しこのレイヤー周りのGUIを整理することを考えると良さそう。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%82%AB%E3%83%A9%E3%83%BC%E3%83%94%E3%83%83%E3%82%AB%E3%83%BC)カラーピッカー

カラーピッカーはGodotにデフォルトで入っているものを利用しました。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--SmZ1eDIH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/2526a873e76487742a65af49.png%3Fsha%3D8d7a8778e143e8d4c90489e1dd5519bd8cedaaf5)

エンジンで使われているようなGUIがデフォルトでいろいろはいっているのはGodotでツール開発をする良いところですね。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--l4GgVWtZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/eeb894c9681a2fb619b0d468.png%3Fsha%3Dceedffa1f3eb6c145725aad8d94974db4b974814)_Godot組み込みのColorPickerノードを配置している_

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%82%B0%E3%83%A9%E3%83%87%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%83%84%E3%83%BC%E3%83%AB)グラデーションツール

グラデーションツールは自作しました。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--S5jFAZPw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/2ec47ce7b49553f068627676.png%3Fsha%3De9306fc2f38b2743fa9ab7a728f7e1aff524fe50)

Godotエンジンにも組み込みのグラデーション洗濯用のツールは存在するようですが、エンジンで使われているグラデーションツールにユーザーがアクセスする方法が無いようなので適当なものを自作しています。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--CJrlshzH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/21960f92f83f730c8263d56d.png%3Fsha%3Da994ce4ed4805ba8dbc23ab160e35b17a5c430fb)

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#canvas%E3%81%AE%E6%8F%8F%E7%94%BB%E5%91%A8%E3%82%8A)canvasの描画周り

canvasの合成モードやレイヤーフォルダを考慮した合成は`RenderingServer.call_on_render_thread()`を利用して自前でコンピュートやグラフィクスのパイプラインを用意して実装しています。

CanvasGroupについて

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%91%E3%82%B9%E3%81%AE%E6%8F%8F%E7%94%BB)パスの描画

ポリゴン化したパスの塗りを描画するために、[RenderingDevice.render_pipeline_create](https://docs.godotengine.org/ja/4.x/classes/class_renderingdevice.html#class-renderingdevice-method-render-pipeline-create)でRenderPipelineを作成しています。以下はその部分のコードの抜粋です。

path_paint_compositor.gd

```gdscript
...

func _init() -> void:
	texture = Texture2DRD.new()
	RenderingServer.call_on_render_thread(_initialize_compositor)

...


func _initialize_compositor() -> void:
	var rd := RenderingServer.get_rendering_device()

	var attachments: Array[RDAttachmentFormat] = []
	var attachment_format := RDAttachmentFormat.new()
	attachment_format.set_format(RenderingDevice.DATA_FORMAT_R8G8_SNORM)
	attachment_format.set_samples(RenderingDevice.TEXTURE_SAMPLES_1)
	attachment_format.usage_flags = RenderingDevice.TEXTURE_USAGE_SAMPLING_BIT + \
		RenderingDevice.TEXTURE_USAGE_COLOR_ATTACHMENT_BIT + \
		RenderingDevice.TEXTURE_USAGE_STORAGE_BIT + \
		RenderingDevice.TEXTURE_USAGE_CAN_UPDATE_BIT + \
		RenderingDevice.TEXTURE_USAGE_CAN_COPY_TO_BIT + \
		RenderingDevice.TEXTURE_USAGE_CAN_COPY_FROM_BIT
	attachments.push_back(attachment_format)
	_rasterize_framebuffer_format = rd.framebuffer_format_create(attachments)

	var vertex_attrs := [RDVertexAttribute.new()]
	vertex_attrs[0].format = RenderingDevice.DATA_FORMAT_R32G32_SFLOAT
	vertex_attrs[0].location = 0
	vertex_attrs[0].stride = 4 * 2
	_rasterize_vertex_format = rd.vertex_format_create(vertex_attrs)

	var blend := RDPipelineColorBlendState.new()
	var blend_attachment := RDPipelineColorBlendStateAttachment.new()
	blend_attachment.write_r = true
	blend_attachment.write_g = true
	blend_attachment.write_b = true
	blend_attachment.enable_blend = false
	blend.attachments.push_back(blend_attachment)

	var rasterize_render_shader_file: RDShaderFile = load("res://shaders/composite/rasterize.glsl")
	var rasterize_render_shader_spirv := rasterize_render_shader_file.get_spirv()
	_rasterize_render_shader = rd.shader_create_from_spirv(rasterize_render_shader_spirv)
	_rasterize_render_pipeline = rd.render_pipeline_create(
		_rasterize_render_shader,
		_rasterize_framebuffer_format,
		_rasterize_vertex_format,
		RenderingDevice.RENDER_PRIMITIVE_TRIANGLES,
		RDPipelineRasterizationState.new(),
		RDPipelineMultisampleState.new(),
		RDPipelineDepthStencilState.new(),
		blend)

  ...
```

やっていることとしては`attachments`でアタッチするテクスチャのフォーマットとusageのフラグを指定して、頂点シェーダーの頂点のフォーマットを指定して、ブレンドモードを指定して、シェーダーを読み込んで、パイプラインを作るという感じです。

VulkanやWebGPUなどのAPIを触ったことがあれば、パイプラインに必要な構造体を詰め込んでパイプラインを作るというよく見慣れた構造であることがわかると思います。Godotのレンダリング周りのクラスはVulkanなどの現代的なAPIをきれいにラップしており、それらを利用したことがあれば素直に使えると思います。

つぎにこのパイプラインを利用して実際に描画している部分の抜粋です。

path_paint_compositor.gd

```gdscript
  ...

	var rd := RenderingServer.get_rendering_device()

	if not rd.texture_is_valid(composite_texture.texture_rid):
		return

	var x_group := (texture_size.x * 2 - 1) / 8 + 1
	var y_group := (texture_size.y * 2 - 1) / 8 + 1

	# === ラスタライズ ===

	rd.texture_clear(_rasterize_texture_rids[0], Color(0, 0, 0, 0), 0, 1, 0, 1)
	rd.texture_clear(_rasterize_texture_rids[1], Color(0, 0, 0, 0), 0, 1, 0, 1)
	var framebuffer_index := 0

	var clear_color_values := PackedColorArray([Color(0, 0, 0, 0)])
	var draw_list := rd.draw_list_begin(
		_rasterize_framebuffers[framebuffer_index],
		RenderingDevice.INITIAL_ACTION_KEEP,
		RenderingDevice.FINAL_ACTION_READ,
		RenderingDevice.INITIAL_ACTION_KEEP,
		RenderingDevice.FINAL_ACTION_READ,
		clear_color_values)
	rd.draw_list_bind_render_pipeline(draw_list, _rasterize_render_pipeline)

	for path in path_layer.paths:
		if not path.visible:
			continue
		if path.indices.size() <= 2:
			continue

		# backbufferとの交換
		if path.boolean == Path.Boolean.Intersect or path.boolean == Path.Boolean.Xor:
			rd.draw_list_end()
			framebuffer_index = 1 - framebuffer_index
			rd.texture_copy(
				_rasterize_texture_rids[1 - framebuffer_index],
				_rasterize_texture_rids[framebuffer_index],
				Vector3(0, 0, 0),
				Vector3(0, 0, 0),
				Vector3(texture_size.x * 2, texture_size.y * 2, 0),
				0, 0, 0, 0)
			draw_list = rd.draw_list_begin(
				_rasterize_framebuffers[framebuffer_index],
				RenderingDevice.INITIAL_ACTION_KEEP,
				RenderingDevice.FINAL_ACTION_READ,
				RenderingDevice.INITIAL_ACTION_KEEP,
				RenderingDevice.FINAL_ACTION_READ,
				clear_color_values)
			rd.draw_list_bind_render_pipeline(draw_list, _rasterize_render_pipeline)

		# index bufferの作成
		var index_bytes := path.indices.to_byte_array()
		var index_buffer := rd.index_buffer_create(path.indices.size(), RenderingDevice.INDEX_BUFFER_FORMAT_UINT32, index_bytes)
		var index_array := rd.index_array_create(index_buffer, 0, path.indices.size())

		# vertex bufferの作成
		var vertex_bytes := path.vertices.to_byte_array()
		var vertex_buffers := [rd.vertex_buffer_create(vertex_bytes.size(), vertex_bytes)]
		var vertex_array := rd.vertex_array_create(path.vertices.size(), _rasterize_vertex_format, vertex_buffers)

		# framebufferのuniformのバインド
		var framebuffer_texture_uniform := RDUniform.new()
		framebuffer_texture_uniform.uniform_type = RenderingDevice.UNIFORM_TYPE_SAMPLER_WITH_TEXTURE
		framebuffer_texture_uniform.binding = 0
		framebuffer_texture_uniform.add_id(_sampler)
		framebuffer_texture_uniform.add_id(_rasterize_texture_rids[1 - framebuffer_index])
		var framebuffer_texture_set := rd.uniform_set_create([framebuffer_texture_uniform], _rasterize_render_shader, 0)

		# push_counstantsを詰める
		var rasterize_push_constant := PackedInt32Array()
		rasterize_push_constant.push_back(int(Main.document_size.x))
		rasterize_push_constant.push_back(int(Main.document_size.y))
		if path.boolean == Path.Boolean.Union:
			rasterize_push_constant.push_back(0)
		elif path.boolean == Path.Boolean.Diff:
			rasterize_push_constant.push_back(1)
		elif path.boolean == Path.Boolean.Intersect:
			rasterize_push_constant.push_back(2)
		elif path.boolean == Path.Boolean.Xor:
			rasterize_push_constant.push_back(3)
		rasterize_push_constant.push_back(0) # dummy
		var rasterize_push_constant_bytes := rasterize_push_constant.to_byte_array()

		rd.draw_list_bind_index_array(draw_list, index_array)
		rd.draw_list_bind_vertex_array(draw_list, vertex_array)
		rd.draw_list_bind_uniform_set(draw_list, framebuffer_texture_set, 0)
		rd.draw_list_set_push_constant(draw_list, rasterize_push_constant_bytes, rasterize_push_constant_bytes.size())
		rd.draw_list_draw(draw_list, true, 1)

		# intersectの2つ目のパス
		if path.boolean == Path.Boolean.Intersect:
			rd.draw_list_end()
			framebuffer_index = 1 - framebuffer_index
			rd.texture_copy(
				_rasterize_texture_rids[1 - framebuffer_index],
				_rasterize_texture_rids[framebuffer_index],
				Vector3(0, 0, 0),
				Vector3(0, 0, 0),
				Vector3(texture_size.x * 2, texture_size.y * 2, 0),
				0, 0, 0, 0)
			draw_list = rd.draw_list_begin(
				_rasterize_framebuffers[framebuffer_index],
				RenderingDevice.INITIAL_ACTION_KEEP,
				RenderingDevice.FINAL_ACTION_READ,
				RenderingDevice.INITIAL_ACTION_KEEP,
				RenderingDevice.FINAL_ACTION_READ,
				clear_color_values)
			rd.draw_list_bind_render_pipeline(draw_list, _rasterize_render_pipeline)

			# vertex bufferの作成
			var rect_vertices := PackedVector2Array([
				Vector2(0, 0),
				Vector2(texture_size.x * 2, 0),
				Vector2(texture_size.x * 2, texture_size.y * 2),
				Vector2(0, 0),
				Vector2(texture_size.x * 2, texture_size.y * 2),
				Vector2(0, texture_size.y * 2),
			])
			var rect_vertex_bytes := rect_vertices.to_byte_array()
			var rect_vertex_buffers := [rd.vertex_buffer_create(rect_vertex_bytes.size(), rect_vertex_bytes)]
			var rect_vertex_array := rd.vertex_array_create(6, _rasterize_vertex_format, rect_vertex_buffers)

			# framebufferのuniformのバインド
			var rect_framebuffer_texture_uniform := RDUniform.new()
			rect_framebuffer_texture_uniform.uniform_type = RenderingDevice.UNIFORM_TYPE_SAMPLER_WITH_TEXTURE
			rect_framebuffer_texture_uniform.binding = 0
			rect_framebuffer_texture_uniform.add_id(_sampler)
			rect_framebuffer_texture_uniform.add_id(_rasterize_texture_rids[1 - framebuffer_index])
			var rect_framebuffer_texture_set := rd.uniform_set_create([rect_framebuffer_texture_uniform], _rasterize_render_shader, 0)

			# push_counstantsを詰める
			var rect_rasterize_push_constant := PackedInt32Array()
			rect_rasterize_push_constant.push_back(texture_size.x * 2)
			rect_rasterize_push_constant.push_back(texture_size.y * 2)
			rect_rasterize_push_constant.push_back(5)
			rect_rasterize_push_constant.push_back(0) # dummy
			var rect_rasterize_push_constant_bytes := rect_rasterize_push_constant.to_byte_array()

			rd.draw_list_bind_vertex_array(draw_list, rect_vertex_array)
			rd.draw_list_bind_uniform_set(draw_list, rect_framebuffer_texture_set, 0)
			rd.draw_list_set_push_constant(draw_list, rect_rasterize_push_constant_bytes, rect_rasterize_push_constant_bytes.size())
			rd.draw_list_draw(draw_list, false, 1)

	rd.draw_list_end()

  ...
```

back bufferの交換やIntersect用の2パス目があるのでちょっと見通しが悪く長いですが、やっていることとしては`rd.draw_list_***`でdraw_listといういわゆるコマンドバッファ的なものに対してindex buffer、vertex bufferをバインドして、uniformをバインドして、push_constantsを詰めて、描画するというコマンドを積んでいます。

パスの描画の際にパスのbooleanを解決しています。Aquamarine Painter ではパスのブーリアンをパスレイヤーに対して設定できます。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--Zqczh1wX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/2a2141f49e697a7b55a492b2.png%3Fsha%3D8eac707d22583b6e950951abe92a0366b9a2a7ef)

以下のようなshaderを利用して、テクスチャのr成分とg成分に0や1を入れて、テクスチャを読んでboolean合成をして書き出すことでパスのブーリアンを実現しています。

rasterize.glsl

```glsl
void main()
{
  vec2 rg = texture(backBufferImage, gl_FragCoord.xy / vec2(pushConstants.width, pushConstants.height)).rg;

  if (pushConstants.booleanMode == 0) {
    // Add
    out_color = vec4(1, 0, 0, 0);
  } else if (pushConstants.booleanMode == 1) {
    // Diff
    out_color = vec4(0, 0, 0, 0);
  } else if (pushConstants.booleanMode == 2) {
    // Intersect first pass
    out_color = vec4(rg.r, 1, 0, 0);
  } else if (pushConstants.booleanMode == 3) {
    // Xor
    out_color = vec4(1 - rg.r, 0, 0, 0);
  } else {
    // Intersect second pass
    out_color = vec4(rg.r* rg.g, 0, 0, 0);
  }
}
```

Intersectだけは描画した三角形の外部も0に戻す必要があるため、1パスめでg成分に新しいパスを書き込んで、フルスクリーンの2パス目でr成分とg成分を掛け合わせて出力しています。

ブーリアンを描画時にshaderで実装しているのは、リアルタイムでブーリアンが評価される必要があったためです。Aquamarine Painterの特性として、よくあるベクター系のツールのようにパスとパスを選択してbooleanツールを選んだときにbooleanが実行されれば良いというものではありません。一つのレイヤーに複数のパスを入れて、それのboolean合成が全部非破壊で生きた状況なツールとなっています。booleanで毎回pathのbooleanをちゃんと計算していてはリアルタイムでの操作が難しくなってしまいました。そのためパス自体のboolean計算は行わず、描画時にshaderでかいけつをすることにしています。

上記方法で問題になるのが輪郭線です。ブーリアンした後のパスの点列が手に入らないため、その点列をそのままラインとして描画するみたいな形の線の描画は出来ません。そこで描画したパスに対してポスプロ的に輪郭線を描画しています。

---

Godotの4.3に切り替えたことでテクスチャの間のバリアを手動で入れなくても勝手にリソースの利用状況からバリアを挟んでくれるようになったようで、コードがシンプルになり助かりました。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%91%E3%82%B9%E3%81%AE%E8%BC%AA%E9%83%AD%E7%B7%9A%E3%81%AE%E6%8F%8F%E7%94%BB)パスの輪郭線の描画

輪郭線はCompute ShaderでJump Flood Algorithmベースで描画しています。Jump Flood Algorithmでアウトラインを描画するのには、次の記事を参考にしました。

Jump Flood Algorithmを利用するとアウトラインの太さのpx数に対してlog2の回数のパスで描画が出来ます。

輪郭線描画はsobelとかいろいろなフィルタがありますが、より太い20pxとか40pxとかの太さのラインを描画するにはそのようなカーネルベースのフィルタでは難しいと思われます。しかしイラスト用途ではデカいキャンバスに太い線を描画することもあります。常に1pxの線を描画すれば良いというわけではありません。そこでパスからの距離をJFAで求めてライン描画しています。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%AC%E3%82%A4%E3%83%A4%E3%83%BC%E5%90%88%E6%88%90)レイヤー合成

上述したパスのブーリアンは01の値をテクスチャに書き込んでいます。ここで作成したマスクに実際に色を与えて合成をするのは別のCompute Shaderで行っています。

上記合成のコンピュートシェーダーを起動しているコードの抜粋を次に示します。

group_paint_compositor.gd

```gdscript
...

func _render_process(output_texture: PaintCompositor.TextureHandle, texture_rids: Array[RID], blend_modes: Array[PaintLayer.BlendMode], clippings: Array[bool], alphas: Array[int]) -> void:
	var rd := RenderingServer.get_rendering_device()

	if not rd.texture_is_valid(output_texture.texture_rid):
		return
	for rid in texture_rids:
		if not rd.texture_is_valid(rid):
			return

	var x_group := (texture_size.x * 1 - 1) / 8 + 1
	var y_group := (texture_size.y * 1 - 1) / 8 + 1

	var texture_uniform := RDUniform.new()
	texture_uniform.uniform_type = RenderingDevice.UNIFORM_TYPE_IMAGE
	texture_uniform.binding = 0
	texture_uniform.add_id(output_texture.texture_rid)
	var texture_set := rd.uniform_set_create([texture_uniform], _shader, 0)
	rd.texture_clear(output_texture.texture_rid, Color(0, 0, 0, 0), 0, 1, 0, 1)

	for index in texture_rids.size():
		# 合成のforeground画像をbindする
		var foreground := RDUniform.new()
		foreground.uniform_type = RenderingDevice.UNIFORM_TYPE_IMAGE
		foreground.binding = 0
		foreground.add_id(texture_rids[index])
		var foreground_set := rd.uniform_set_create([foreground], _shader, 1)

		# 合成のclippingの親に当たる画像を探してbindする
		var clipping_index := -1
		if clippings[index]:
			for i in index:
				if not clippings[index - i - 1]:
					clipping_index = index - i - 1
					break
		var clipping := RDUniform.new()
		clipping.uniform_type = RenderingDevice.UNIFORM_TYPE_IMAGE
		clipping.binding = 0
		if clipping_index == -1:
			clipping.add_id(output_texture.texture_rid)
		else:
			clipping.add_id(texture_rids[clipping_index])
		var clipping_set := rd.uniform_set_create([clipping], _shader, 2)

		# クリッピングのアルファ値も計算する
		var clipping_alpha := 100
		if clipping_index != -1:
			clipping_alpha = alphas[clipping_index]

		# push_counstantsを詰める
		var push_constant := PackedInt32Array()
		push_constant.push_back(blend_modes[index])
		push_constant.push_back(clippings[index])
		push_constant.push_back(texture_size.x * 1)
		push_constant.push_back(texture_size.y * 1)
		push_constant.push_back(alphas[index])
		push_constant.push_back(clipping_alpha)
		push_constant.push_back(0)
		push_constant.push_back(0) # dummy

		# コマンドを発行
		var compute_list := rd.compute_list_begin()
		rd.compute_list_bind_compute_pipeline(compute_list, _pipeline)
		rd.compute_list_bind_uniform_set(compute_list, texture_set, 0)
		rd.compute_list_bind_uniform_set(compute_list, foreground_set, 1)
		rd.compute_list_bind_uniform_set(compute_list, clipping_set, 2)
		rd.compute_list_set_push_constant(compute_list, push_constant.to_byte_array(), push_constant.size() * 4)
		rd.compute_list_dispatch(compute_list, x_group, y_group, 1)
		rd.compute_list_end()

...
```

レイヤーフォルダーのグループに含まれるレイヤーのテクスチャのリストを元に順番に合成を重ねていきます。その過程で下のレイヤーでクリッピングが有効になっている場合、クリッピングが有効になっているレイヤーを辿っていって、クリッピング元になるレイヤーを見つけるような処理をしています。クリッピング元のレイヤーがアルファを持っている場合はそのアルファも利用します。

下記は下のレイヤーでクリッピングのためのclippingのテクスチャの値とbaseとforegroundをもとに合成するglslです。

group_composite.glsl

```glsl
#[compute]
#version 460

layout(local_size_x = 8, local_size_y = 8, local_size_z = 1) in;

layout(rgba8, set = 0, binding = 0) uniform image2D base_image;
layout(rgba8, set = 1, binding = 0) uniform readonly image2D foreground_image;
layout(rgba8, set = 2, binding = 0) uniform readonly image2D clipping_image;

layout(push_constant, std430) uniform PushConstants {
    int blendMode;
    int clipping;
    int width;
    int height;
    int alpha;
    int clippingAlpha;
    int mirror;
} pushConstants;

void main() {
  ivec2 size = ivec2(pushConstants.width, pushConstants.height);
  ivec2 uv = ivec2(gl_GlobalInvocationID.xy);

  if (uv.x >= size.x || uv.y >= size.y) {
    return;
  }

  vec4 base = imageLoad(base_image, uv);

  if (pushConstants.mirror == 1) {
    uv.x = size.x - uv.x - 1;
  }

  vec4 foreground = imageLoad(foreground_image, uv);
  vec4 clipping = imageLoad(clipping_image, uv);

  foreground.a *= float(pushConstants.alpha) / 100.0;
  clipping.a *= float(pushConstants.clippingAlpha) / 100.0;
  if (pushConstants.clipping == 1) {
    foreground.a *= clipping.a;
  }

  // Porter Dugg, Source Over
  float f_b = 1 - foreground.a;
  float f_f = 1;


  vec4 color;
  color.a = base.a * f_b + foreground.a * f_f;

  vec3 blendColor;

  switch (pushConstants.blendMode) {
    case 0:  // Normal
      blendColor = foreground.rgb;
      break;
    case 1:  // Add
      blendColor =min(vec3(1), base.rgb + foreground.rgb);
      break;
    case 2:  // Multiply
      blendColor = base.rgb * foreground.rgb;
      break;
    case 3:  // Screen
      blendColor = 1 - (1 - base.rgb) * (1 - foreground.rgb);
      break;
    case 4:  // Overlay
      if (base.a < 0.5) {
        blendColor = 2 * base.rgb * foreground.rgb;
      } else {
        blendColor = 1 - 2 * (1 - base.rgb) * (1 - foreground.rgb);
      }
      break;
  }

  vec3 color_prime = blendColor * base.a + foreground.rgb * (1 - base.a);
  color.rgb = (base.rgb * f_b * base.a + color_prime * f_f * foreground.a) / color.a;

  if (pushConstants.clipping == 0) {
    if (color.a == 0.0) {
      color.rgb = vec3(0.0);
    }
  } else {
    if (color.a == 0.0) {
      return;
    }
  }


  if (pushConstants.mirror == 1) {
    uv.x = size.x - uv.x - 1;
  }

  imageStore(base_image, uv, color);
}
```

上記フォルダーレイヤーの合成は、フォルダー内のいずれかのレイヤーがdirtyになったときに実行されます。フォルダー内のレイヤーに変更がない場合は合成をスキップして合成済みのテクスチャを次の合成に回します。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%9F%E3%83%83%E3%83%97%E3%83%9E%E3%83%83%E3%83%97%E3%81%AE%E7%94%9F%E6%88%90)ミップマップの生成

絵を書いている最中に場合によってはキャンバスを小さく縮小したりします。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--EsQ7gWDk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/ccfa6de299214da95f1d3eb5.png%3Fsha%3D42c9de71404ec94d8243938cd28e7d7879cca1f0)

そのさいにmipmapが存在しないとガビガビになってしまうので、一番ルートのコンポジット時にはmipmapを生成しています。

root_paint_compositor.gd

```gdscript
...

	# mipmapの生成
	var mipmap_in_texture_sets: Array[RID] = []
	var mipmap_out_texture_sets: Array[RID] = []
	for i in output_texture.mip_size - 1:
		var in_uniform := RDUniform.new()
		in_uniform.uniform_type = RenderingDevice.UNIFORM_TYPE_SAMPLER_WITH_TEXTURE
		in_uniform.binding = 0
		in_uniform.add_id(_sampler)
		in_uniform.add_id(output_texture.mipmap_texture_rids[i])
		mipmap_in_texture_sets.append(rd.uniform_set_create([in_uniform], _mipmap_shader, 1))
		var out_uniform := RDUniform.new()
		out_uniform.uniform_type = RenderingDevice.UNIFORM_TYPE_IMAGE
		out_uniform.binding = 0
		out_uniform.add_id(output_texture.mipmap_texture_rids[i + 1])
		mipmap_out_texture_sets.append(rd.uniform_set_create([out_uniform], _mipmap_shader, 0))

	for i in output_texture.mip_size - 1:
		var x_group_mipmap := (texture_size.x * 1 / (2 ** (i + 1)) - 1) / 8 + 1
		var y_group_mipmap := (texture_size.y * 1 / (2 ** (i + 1)) - 1) / 8 + 1

		# push_counstantsを詰める
		var push_constant := PackedInt32Array()
		push_constant.push_back(texture_size.x * 1 / (2 ** (i + 1)))
		push_constant.push_back(texture_size.y * 1 / (2 ** (i + 1)))
		push_constant.push_back(0) # dummy
		push_constant.push_back(0) # dummy

		# コマンドを発行
		var compute_list := rd.compute_list_begin()
		rd.compute_list_bind_compute_pipeline(compute_list, _mipmap_pipeline)
		rd.compute_list_bind_uniform_set(compute_list, mipmap_out_texture_sets[i], 0)
		rd.compute_list_bind_uniform_set(compute_list, mipmap_in_texture_sets[i], 1)
		rd.compute_list_set_push_constant(compute_list, push_constant.to_byte_array(), push_constant.size() * 4)
		rd.compute_list_dispatch(compute_list, x_group_mipmap, y_group_mipmap, 1)
		rd.compute_list_end()

...
```

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%82%A2%E3%83%B3%E3%83%81%E3%82%A8%E3%82%A4%E3%83%AA%E3%82%A2%E3%82%B9)アンチエイリアス

パスのブーリアンを行ったりJFAでアウトラインをポスプロで描画する都合で、パスの描画にはMSAAを利用していません。そのため若干ジャギっているのが気になる仕上がりになっていたため、パスの合成時にFXAAでアンチエイリアスを書けるようにしています。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--n3h4vpa---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/37a780fb2673ff9efcf48d20.png%3Fsha%3D9d81f6ffd016e2fa4a27872de74e0870a2e6bdfd)

下記はパスのbooleanを行った結果に色を与えてレイヤーのテクスチャに書き出すglslです。下記とは別にパスとアウトラインを合成するためのglslも別にあります。どちらもFXAAの処理が入っています。

path_compositor.glsl

```glsl
#[compute]
#version 460

layout(local_size_x = 8, local_size_y = 8, local_size_z = 1) in;

layout(rgba8, set = 0, binding = 0) uniform image2D base_image;
layout(set = 1, binding = 0) uniform sampler2D viewport_image;
layout(set = 2, binding = 0) uniform sampler2D gradientTexture;

const float FIXED_THRESHOLD = 0.0833;
const float RELATIVE_THRESHOLD = 0.166;
const float SUBPIXEL_BLENDING = 0.75;
const float EDGE_STEP_SIZES_ARRAY[10] = float[](
  1.0, 1.0, 1.0, 1.0, 1.5, 2.0, 2.0, 2.0, 2.0, 4.0
);
const float LAST_EDGE_STEP_GUESS = 8.0;

layout(push_constant, std430) uniform PushConstants {
  int width;
  int height;
  int materialType;
  int r8;
  int g8;
  int b8;
  int a8;
  int padding1;
  vec2 pos0;
  vec2 pos1;
  vec2 pos2;
  int padding2[2];
} pushConstants;

float getMaskOffset(vec2 uv, float uOffset, float vOffset) {
  uv += vec2(uOffset + 0.5, vOffset + 0.5) / vec2(pushConstants.width, pushConstants.height);
  return textureLod(viewport_image, uv, 0.0).r;
}

struct MaskNeighbor {
  float center;
  float topLeft;
  float top;
  float topRight;
  float left;
  float right;
  float bottomLeft;
  float bottom;
  float bottomRight;
  float highest;
  float lowest;
  float range;
};

MaskNeighbor getMaskNeighbor(vec2 uv) {
  MaskNeighbor neighbor;
  neighbor.center = getMaskOffset(uv, 0.0, 0.0);
  neighbor.topLeft = getMaskOffset(uv, -1.0, -1.0);
  neighbor.top = getMaskOffset(uv, 0.0, -1.0);
  neighbor.topRight = getMaskOffset(uv, 1.0, -1.0);
  neighbor.left = getMaskOffset(uv, -1.0, 0.0);
  neighbor.right = getMaskOffset(uv, 1.0, 0.0);
  neighbor.bottomLeft = getMaskOffset(uv, -1.0, 1.0);
  neighbor.bottom = getMaskOffset(uv, 0.0, 1.0);
  neighbor.bottomRight = getMaskOffset(uv, 1.0, 1.0);
  neighbor.highest = max(max(max(max(neighbor.center, neighbor.top), neighbor.left), neighbor.right), neighbor.bottom);
  neighbor.lowest = min(min(min(min(neighbor.center, neighbor.top), neighbor.left), neighbor.right), neighbor.bottom);
  neighbor.range = neighbor.highest - neighbor.lowest;
  return neighbor;
}

bool isHorizontal(MaskNeighbor neighbor) {
  float horizontal = 2.0 * abs(neighbor.top + neighbor.bottom - 2.0 * neighbor.center) + abs(neighbor.topLeft+ neighbor.bottomLeft - 2.0 * neighbor.left) + abs(neighbor.topRight + neighbor.bottomRight - 2.0 * neighbor.right);
  float vertical = 2.0 * abs(neighbor.left + neighbor.right - 2.0 * neighbor.center) + abs(neighbor.topLeft + neighbor.topRight - 2.0 * neighbor.top) + abs(neighbor.bottomLeft + neighbor.bottomRight - 2.0 * neighbor.bottom);
  return horizontal >= vertical;
}

struct FxaaEdge {
  bool isHorizontal;
  float pixelStep;
  float maskGradient;
  float otherMask;
};

FxaaEdge getFxaaEdge(MaskNeighbor neighbor) {
  FxaaEdge edge;
  edge.isHorizontal = isHorizontal(neighbor);
  float maskP, maskN;
  if (edge.isHorizontal) {
    edge.pixelStep = 1.0 / float(pushConstants.height);
    maskP = neighbor.top;
    maskN = neighbor.bottom;
  } else {
    edge.pixelStep = 1.0 / float(pushConstants.width);
    maskP = neighbor.left;
    maskN = neighbor.right;
  }
  float gradientP = abs(maskP - neighbor.center);
  float gradientN = abs(maskN - neighbor.center);
  if (gradientP < gradientN) {
    edge.pixelStep = -edge.pixelStep;
    edge.maskGradient = gradientN;
    edge.otherMask = maskN;
  } else {
    edge.maskGradient = gradientP;
    edge.otherMask = maskP;
  }
  return edge;
}

bool canSkipFxaa(MaskNeighbor neighbor) {
  return neighbor.range < max(FIXED_THRESHOLD, RELATIVE_THRESHOLD * neighbor.highest);
}

float getSubpixelBlendFactor(MaskNeighbor neighbor) {
  float filterValue = 2.0 * (neighbor.top + neighbor.bottom + neighbor.left + neighbor.right);
  filterValue += neighbor.topLeft + neighbor.topRight + neighbor.bottomLeft + neighbor.bottomRight;
  filterValue *= 1.0 / 12.0;
  filterValue = abs(filterValue - neighbor.center);
  filterValue = clamp(filterValue / neighbor.range, 0.0, 1.0);
  filterValue = smoothstep(0.0, 1.0, filterValue);
  return filterValue * filterValue * SUBPIXEL_BLENDING;
}

float getEdgeBlendFactor(MaskNeighbor neighbor, FxaaEdge edge, vec2 uv) {
  vec2 edgeUV = uv;
  vec2 uvStep = vec2(0.0);
  if (edge.isHorizontal) {
    uvStep.y += 0.5 * edge.pixelStep;
    uvStep.x = 1.0 / float(pushConstants.width);
  } else {
    uvStep.x += 0.5 * edge.pixelStep;
    uvStep.y = 1.0 / float(pushConstants.height);
  }

  float edgeMask = 0.5 * (neighbor.center + edge.otherMask);
  float gradientThreshold = 0.25 * edge.maskGradient;

  vec2 uvP = edgeUV + uvStep;
  float maskDeltaP = getMaskOffset(uvP, 0.0, 0.0) - edgeMask;
  bool atEndP = abs(maskDeltaP) >= gradientThreshold;

  int i;
  for (i = 0; i < 10 && !atEndP; i++) {
    uvP += uvStep * EDGE_STEP_SIZES_ARRAY[i];
    maskDeltaP = getMaskOffset(uvP, 0.0, 0.0) - edgeMask;
    atEndP = abs(maskDeltaP) >= gradientThreshold;
  }
  if (!atEndP) {
    uvP = edgeUV + uvStep * LAST_EDGE_STEP_GUESS;
  }

  vec2 uvN = edgeUV - uvStep;
  float maskDeltaN = getMaskOffset(uvN, 0.0, 0.0) - edgeMask;
  bool atEndN = abs(maskDeltaN) >= gradientThreshold;

  for (i = 0; i < 10 && !atEndN; i++) {
    uvN -= uvStep * EDGE_STEP_SIZES_ARRAY[i];
    maskDeltaN = getMaskOffset(uvN, 0.0, 0.0) - edgeMask;
    atEndN = abs(maskDeltaN) >= gradientThreshold;
  }
  if (!atEndN) {
    uvN = edgeUV - uvStep * LAST_EDGE_STEP_GUESS;
  }

  float distanceToEndP, distanceToEndN;
  if (edge.isHorizontal) {
    distanceToEndP = uvP.x - uv.x;
    distanceToEndN = uv.x - uvN.x;
  } else {
    distanceToEndP = uvP.y - uv.y;
    distanceToEndN = uv.y - uvN.y;
  }

  float distanceToNearestEnd;
  bool deltaSign;
  if (distanceToEndP <= distanceToEndN) {
    distanceToNearestEnd = distanceToEndP;
    deltaSign = maskDeltaP >= 0;
  } else {
    distanceToNearestEnd = distanceToEndN;
    deltaSign = maskDeltaN >= 0;
  }

  if (deltaSign == (neighbor.center - edgeMask >= 0)) {
    return 0.0;
  } else {
    return 0.5 - distanceToNearestEnd / (distanceToEndP + distanceToEndN);
  }
}

float getMask(vec2 uv) {
  MaskNeighbor neighbor = getMaskNeighbor(uv);
  if (canSkipFxaa(neighbor)) {
    return neighbor.center;
  }

  FxaaEdge edge = getFxaaEdge(neighbor);
  float blendFactor = max(getSubpixelBlendFactor(neighbor), getEdgeBlendFactor(neighbor, edge, uv));
  if (edge.isHorizontal) {
    return getMaskOffset(uv, 0.0, blendFactor);
  } else {
    return getMaskOffset(uv, blendFactor, 0.0);
  }
}

vec4 getColor() {
  vec4 color;
  if (pushConstants.materialType == 0) {
    color.r = float(pushConstants.r8) / 255.0;
    color.g = float(pushConstants.g8) / 255.0;
    color.b = float(pushConstants.b8) / 255.0;
    color.a = float(pushConstants.a8) / 255.0;
  } else if (pushConstants.materialType == 1) {
    vec2 v = (pushConstants.pos1 - pushConstants.pos0);
    vec2 uv = vec2(gl_GlobalInvocationID.xy) / 2.0;
    float t = dot(uv - pushConstants.pos0, v) / dot(v, v);
    color = texture(gradientTexture, vec2(t, 0.5));
  } else if (pushConstants.materialType == 2) {
    vec2 a = pushConstants.pos1 - pushConstants.pos0;
    vec2 b = pushConstants.pos2 - pushConstants.pos0;
    float aLength = length(a);
    float bLength = length(b);
    vec2 aNorm = a / aLength;
    vec2 bNorm = b / bLength;
    if (aLength < 0.0001 || bLength < 0.0001) {
      color = texture(gradientTexture, vec2(1.0, 0.5));
      color = vec4(1.0, 0.0, 1.0, 1.0);
    } else {
      vec2 uv = vec2(gl_GlobalInvocationID.xy) / 2.0;
      vec2 p = uv - pushConstants.pos0;
      vec2 pp = dot(p, aNorm) * aNorm + dot(p, bNorm) * bNorm / bLength * aLength;
      float t = length(pp) / aLength;
      color = texture(gradientTexture, vec2(t, 0.5));
    }
  } else if (pushConstants.materialType == 3) {
    vec2 checker_count = vec2(pushConstants.width, pushConstants.height) / 96.0;
    vec2 uv = vec2(gl_GlobalInvocationID.xy) / vec2(pushConstants.width, pushConstants.height);
    float checker = mod(dot(vec2(1.0), step(vec2(0.5), fract(uv * checker_count))), 2.0);
    color.rgb = mix(vec3(1.0, 0.0, 1.0), vec3(0.0, 1.0, 1.0), checker);
    color.a = 1.0;
  }
  return color;
}

void main() {
  ivec2 size = ivec2(pushConstants.width, pushConstants.height);
  ivec2 iuv = ivec2(gl_GlobalInvocationID.xy);

  if (iuv.x >= size.x || iuv.y >= size.y) {
    return;
  }

  vec2 uv = (vec2(iuv) + vec2(0.5)) / vec2(size);

  vec4 color = getColor();

  float mask = getMask(uv);
  color.a *= mask;

  imageStore(base_image, iuv, color);
}
```

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#godot%E3%81%A7%E5%AE%9F%E8%A3%85%E3%81%97%E3%81%9F%E6%84%9F%E6%83%B3)Godotで実装した感想

今回は一部をRustのGDExtensionで実装しましたが、残りの大半はGDScriptで実装しました。GDScriptはサクッと動くものが作れて、そういう用途には強いという感想です。また、GodotのGUIはデフォルトでいろいろなものが入っているので、それを使うとサクッとGUIを作れるのも良い感じでした。

一方で、Godotのシグナルとかを使って色々なクラスが依存したりするのは、サクッと動くものを作るのには便利ですが、何も考えずに使うと結構設計が大変なことになりそうな印象を受けました。今回は取りあえず動くツールを作ってみるのを優先したので、設計が結構乱雑で後からコードを触って機能追加とかはあまりしたくない感じになっています。

GDScriptもシグナルの仕組みも、サクッと動くものを作るには良いが、大規模なものを作っていくにはちょっとスクリプト言語っぽすぎるかなあというのが個人的な感想でした。GodotはC#対応もあったりするので、C#を使ってインタフェースとか切って丁寧に設計をしてみるというのが、大規模の開発では良かったりするのかもしれません？

Godotでグラフィクス周りを触って自分でパイプラインを作ってGPUに投げるコマンドを積むのはとてもやりやすく感じました。Vulkan的な構造体を用意してコマンドを記録するタイプで現代的で、それでいてVulkanみたいな面倒さがだいぶ排除されているように感じます。Godot 4.3からの非巡回有向グラフを利用したリソースの追跡は手動でバリアをいれる手間も省けて実装が用意になりました。

## [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E4%BB%8A%E5%BE%8C%E3%81%AE%E8%AA%B2%E9%A1%8C)今後の課題

今回作ったツールは取りあえず動くお絵かきツールを一個雑に作ることを目標に作っており、コードも乱雑であまり今後アップデートをすることは考えていませんが、今回やり残していて今後また別のお絵かきツールを作るときには実装してみたい機能があります。

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%9A%E3%83%B3%E3%81%AE%E6%8F%8F%E7%94%BB%E7%B5%90%E6%9E%9C%E3%82%92%E3%82%B7%E3%83%B3%E3%83%97%E3%83%AB%E5%8C%96%E3%81%97%E3%81%9F%E3%83%99%E3%82%AF%E3%82%BF%E3%83%BC%E3%81%AB%E3%81%99%E3%82%8B%E6%A9%9F%E8%83%BD)ペンの描画結果をシンプル化したベクターにする機能

Aquamarine Painterでは現状ではポチポチとクリックすることでコントロールポイントを配置して絵を作っていきます。でも、せっかくならペンタブで絵をスイスイ描きたいですよね。しかし、ペンタブで描いた線をそのままベクターとするだけでは編集しやすさとかがいまいちです。そこで、ペンタブで線を描いたときの点列をシンプルなスプライン曲線にする機能を実装してみたいです。

点列からベジェにフィッティングするアルゴリズムははGPU Gems 1に「AN ALGORITHM FOR AUTOMATICALLY FITTING DIGITIZED GURVES」という章で載っているものがあったりします。もととなった論文は以下のもののようです。

Kritaというツールにも似たようなものが搭載されていて、フリーハンドパスツールで描くとそれが扱いやすいベジェになってくれるので、そういう機能を入れてみたいです。

![screenshot](https://storage.googleapis.com/zenn-user-upload/deployed-images/10d659d5060ee789e9d1db0b.gif?sha=d1787519faa1993b8505fea8264787ca677fdbe9)![screenshot](https://storage.googleapis.com/zenn-user-upload/deployed-images/53f26f5e17f14149735bb595.gif?sha=17b73d8700f911ccfbf8159ec9386ab281f8aa13)

また、Clip Studio Paintのベクターレイヤーだとデフォルトでもう少し点が多いベクターになるようですが、追加でベクター線を「ベクター線単純化」を利用してシンプルなベクター線にできるようです。

![screenshot](https://storage.googleapis.com/zenn-user-upload/deployed-images/919b8ea0f0fdb2f9ea6dfabf.gif?sha=97187241f6e171674914997b34a861a9efc75299)

PPW Curvesはベジェ曲線ではありません。ベジェ曲線ではないスプライン曲線へのフィッティングとなると、なにか新しくアルゴリズムを作る必要があるかもしれません。

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%A9%E3%82%B9%E3%82%BF%E3%83%BC%E7%94%BB%E5%83%8F%E3%82%82%E5%8F%96%E3%82%8A%E8%BE%BC%E3%82%81%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%99%E3%82%8B)ラスター画像も取り込めるようにする

Aquamarine Painterは現状では完全にベクターの線しか描画できません。しかし、絵の下絵であったり、テクスチャに利用したりというのでラスター画像を使えると便利そうです。今回は実装に至りませんでしたが、実用的なお絵かきツールにするのであればぜひ実装したい機能です。

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%83%99%E3%82%B8%E3%82%A7%E3%81%AB%E3%82%82%E5%AF%BE%E5%BF%9C%E3%81%97%E3%80%81svg%E3%82%92%E3%82%A4%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%88%E3%83%BB%E3%82%A8%E3%82%AF%E3%82%B9%E3%83%9D%E3%83%BC%E3%83%88%E3%81%A7%E3%81%8D%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%99%E3%82%8B)ベジェにも対応し、svgをインポート・エクスポートできるようにする

現状はベクターの一般的なファイルフォーマットであるSVGもインポート・エクスポートできません。これはSVGがベジェベースであり、このツールではベジェ曲線を対応していないというのが理由です。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%82%A4%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%88)インポート

ベジェ曲線に一切対応せずPPW Curvesにのみ対応しているというのはだいぶ尖り過ぎな気がするので、ベジェに限らずいくつかのパラメトリック曲線を使えるようにしてみても良いかもしれません。そうすればSVGのパスデータをそのままインポートできるようになるはずです。

#### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%82%A8%E3%82%AF%E3%82%B9%E3%83%9D%E3%83%BC%E3%83%88)エクスポート

今回利用したPPW Curvesはベジェ曲線とは異なる曲線になるので、残念ながらそのままSVGとしては書き出せません。PPW Curvesを複数のベジェ曲線の連結で近似して誤差評価などもしつつベジェ曲線として表現してSVGとして書き出すような機能を作ってみるのも面白そうです。

例えば有理ベジェ曲線をベジェ曲線の列で近似するような技術や3次ベジェ曲線を2次のベジェ曲線の列で近似するような技術は既存の論文があったりするので、このあたりを参考にして特殊な曲線をベジェ曲線で近似する方法を考えてみるのも良いかもしれません。

### [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%82%88%E3%82%8A%E5%8A%B9%E7%8E%87%E7%9A%84%E3%81%AA%E6%8F%8F%E7%94%BB%E3%81%AE%E5%AE%9F%E8%A3%85)より効率的な描画の実装

非常にスペックの低いノートPCでデモに使っているイラストを開いてみると若干コントロールポイントを動かしたときにカクつく感じがありました。レイヤー数が増えるほど合成負荷も上がるような実装になっているので、よりレイヤー数の多いイラストを描くには、もう少し最適化をしたいかもしれません。

また、今回はだいぶ富豪的にGPUメモリを使っている気がしていて、もう少しなんとかならないかという気がしないでもないです。ちゃんと計測していないのでメモリ使用量を計測するところからではありますが、現状の実装だとイラストのフル解像度のテクスチャがレイヤーの数だけ全部GPUメモリに乗っています。レイヤーを大量に使えば使うほどメモリ使用量が大きくなっているので、もう少しテクスチャのGPUメモリを開放するようにしてみたほうが良いかもしれません。フォルダに入っているレイヤーはフォルダの内部が変更されない限り合成語のレイヤーだけを保持しておけば良いかもしれません。そういう合成したテクスチャを開放するなどをして、GPUメモリの消費量を減らすことも考えてみたほうが良いかもしれません。

より実用的なお絵かきツールを作るには、あまり強くないPCでもサクサク動いてほしいものです。

## [](https://zenn.dev/matcha_choco010/articles/2024-12-14-aquamarine-painter#%E3%81%8A%E3%82%8F%E3%82%8A%E3%81%AB)おわりに

今回はお試しでサクッとお絵かきツールを作るつもりで一つ作ってみました。  
実際に制作期間は趣味の時間の大半をぶち込んで2ヶ月ちょっとかかっていますが良い経験になった気がします。

![screenshot](https://res.cloudinary.com/zenn/image/fetch/s--VY1Rd1JG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/6141dc3ea9eb19fcde525b43.png%3Fsha%3D9b28a8755cca420602970d9599931923031d57f0)_Aquamarine Painterで描いたイラスト_

みんなもお絵かきツールを自作してみよう！