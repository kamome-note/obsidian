【Godot】Godot EngineのTIPS
https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab

とりあえず記事をコピペ。
時間のあるときに読みながら清書したい。

---

# 剛体の種類

- Area2D: 衝突検出のみ
- KinematicBody2D: 衝突検出と応答を行う。直接 positionを変更せずに、move_and_slide() / move_and_collide() を使って移動処理を行う
- RigidBody2D: 衝突検出と応答が自動で行われる。移動させる場合は add_force() などを現実世界に近い剛体
- StaticBody2D: 移動しない床や壁など。衝突検出と応答が自動で行われる

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%83%9E%E3%82%A6%E3%82%B9%E3%81%AE%E5%BA%A7%E6%A8%99%E3%81%A8%E3%82%AF%E3%83%AA%E3%83%83%E3%82%AF%E5%88%A4%E5%AE%9A)マウスの座標とクリック判定

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#get_viewport-%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%99%E3%82%8B)`get_viewport()` を使用する

```
# マウスのX座標
var mx = get_viewport().get_mouse_position().x
# マウスのY座標
var my = get_viewport().get_mouse_position().y
```

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#_input-%E3%81%A7%E5%88%A4%E5%AE%9A%E3%82%92%E8%A1%8C%E3%81%86%E6%96%B9%E6%B3%95)`_input()` で判定を行う方法

```
func _input(event):
  if event is InputEventMouseButton:
    # マウスイベントが発生
    if event.pressed:
      # クリックしたときの処理
      var b = Block.instance()
      # クリックの位置にブロックを発生させる
      b.position = event.position
      add_child(b)
```

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%82%A4%E3%83%B3%E3%83%97%E3%83%83%E3%83%88%E3%83%9E%E3%83%83%E3%83%97%E3%82%92%E8%BF%BD%E5%8A%A0%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95)インプットマップを追加する方法

1. メニューから `プロジェクト > プロジェクト設定` を選ぶ。
2. `インプットマップ` タブを選んで、アクションに "ui_click" と入力して、「追加」ボタンをクリック
3. 追加された "ui_click" の行の右側にある「＋」をクリックして　`マウスボタン` を選択
4. 「左ボタン」を選んで「追加」ボタンをクリック

[![Godot_Engine_-_TestTree.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F1a2acab2-5127-ffd4-2058-285439393ea0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=847074eaab7ab2ff790178494846b627)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F1a2acab2-5127-ffd4-2058-285439393ea0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=847074eaab7ab2ff790178494846b627)  
[![Godot_Engine_-TestTree___.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F761a8e01-3cf3-866f-a115-a8d84d4e598b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=91668c042255e8336be24511390fb75c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F761a8e01-3cf3-866f-a115-a8d84d4e598b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=91668c042255e8336be24511390fb75c)  
[![Godot_Engine_-TestTree___.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fd581d13f-f889-bec4-53ef-4b16e483f148.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=362dde9f7e3f22c6714f0e056c3caf79)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fd581d13f-f889-bec4-53ef-4b16e483f148.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=362dde9f7e3f22c6714f0e056c3caf79)

この設定をすることで、以下のコードでクリック判定ができる

```
func _process(delta: float) -> void:
	if Input.is_action_just_pressed("ui_click"):
		print("クリックしました")
```

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%83%97%E3%83%AD%E3%83%91%E3%83%86%E3%82%A3%E3%81%AE%E5%AE%9F%E8%A3%85%E6%96%B9%E6%B3%95)プロパティの実装方法

変数に `setget` キーワードを指定することでプロパティの setter/getterを指定できる

```
# "id" には 0〜52が指定可能
export(int, 0, 52) var id = 0 setget _set_id, _get_id

# 値を設定するときに呼び出される関数
func _get_id():
	return id

# 値を取得する場合に呼び出される関数	
func _set_id(v):
	id = v
```

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E7%94%BB%E5%83%8F%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9%E3%81%AE%E5%8B%95%E7%9A%84%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%81%BF)画像リソースの動的読み込み

`load()` で画像リソースのパスを指定することで動的読み込み（※同期ロード）することができる

```

var id = 12

# 画像リソースへのパス "res://assets/cards/012.png" を作成する
var path = "res://assets/cards/c%03d.png"%id

# 子ノードに "Sprite" が存在する前提
$Sprite.texture = load(path)	

```

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E5%9B%9B%E8%A7%92%E5%BD%A2%E3%82%92%E3%81%A8%E3%82%8A%E3%81%82%E3%81%88%E3%81%9A%E8%A1%A8%E7%A4%BA%E3%81%97%E3%81%9F%E3%81%84)四角形をとりあえず表示したい

画像リソースを用意せずに、ひとまず四角形を表示したい場合、`ColorRect` ノードを追加すると四角形を表示できます。

[![Godot_Engine_-gd_pyramid-_Card_tscn.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F9e997636-8cf9-100f-ffbb-c9b2ef576378.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bc8552ce3c7f35d5c329a9680c51c448)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F9e997636-8cf9-100f-ffbb-c9b2ef576378.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bc8552ce3c7f35d5c329a9680c51c448)  
[![Godot_Engine_-gd_pyramid-Card_tscn___.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F27e0ccd6-51f1-8bcc-0294-c6cbf2607dc9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6761ea65e8018e8a394ad295060ac620)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F27e0ccd6-51f1-8bcc-0294-c6cbf2607dc9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6761ea65e8018e8a394ad295060ac620)

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#colorrect-%E3%81%AE%E8%89%B2%E3%82%92%E5%A4%89%E6%9B%B4%E3%81%97%E3%81%9F%E3%81%84)ColorRect の色を変更したい

colorプロパティから変更できます

```
# 緑色に変更する
$ColorRect.color = Color.green
```

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#sprite%E9%96%A2%E9%80%A3)Sprite関連

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#sprite%E3%81%AE%E8%89%B2%E3%82%92%E5%A4%89%E6%9B%B4%E3%81%97%E3%81%9F%E3%81%84)Spriteの色を変更したい

`Sprite`ノードの色は `modulate` となります

```
## スプライトの頂点カラーを緑色にする
$Sprite.modulate = Color.green
```

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#sprite%E3%81%8C%E6%B6%88%E3%81%88%E3%82%8B%E3%81%A0%E3%81%91%E3%81%AE%E3%82%A8%E3%83%95%E3%82%A7%E3%82%AF%E3%83%88%E3%82%92%E4%BD%9C%E3%82%8B)Spriteが消えるだけのエフェクトを作る

Spriteが消えるだけのエフェクト（当たり判定がない）場合は、Spriteノードだけ作成して、テクスチャを設定するだけでよい。Spriteが Node2D を継承しているので Area2D を使わなくても良い  
（※スプライトシート[連番画像。等間隔でパターンが並んだ画像] を使わないのであれば、`TextureRect` の方がパフォーマンスが良いかも？）

[![Godot_Engine_-gd_pyramid-_CardEffect_tscn.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F3154ee43-2440-c4b1-d609-026a125c5467.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9b72fa04389e162640bb8d11fbff8815)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F3154ee43-2440-c4b1-d609-026a125c5467.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9b72fa04389e162640bb8d11fbff8815)  
このような縁がぼやけているエフェクトの画像を用意する

0.5秒で消えるスクリプトの例

```
extends Sprite

# 0.5病で消える
var _time = 0.5

func start(pos:Vector2, color:Color) -> void:
	# 起動パラメータ
	position = pos   # 座標
	modulate = color # 色
	
func _process(delta):
	_time -= delta
	
	# アルファ値を 0.5 -> 0.0 にする
	var rate = 0.5 * _time / 0.5
	modulate.a = rate
	
	# 0.5秒経過したら消える
	if _time <= 0:
		queue_free()
```

[![shot.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fd95cf349-3ce0-56b1-7291-6abbfa7f11f7.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6dbce8a53100a4a013233bffd3a40929)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fd95cf349-3ce0-56b1-7291-6abbfa7f11f7.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6dbce8a53100a4a013233bffd3a40929)

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#sprite%E3%81%AE%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B3%E3%82%A2%E3%83%8B%E3%83%A1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E3%81%97%E3%81%9F%E3%81%84)Spriteのパターンアニメーションをしたい

`Sprite`のスプライトシートの機能、もしくは `AnimatedSprite` を使う。  
詳細は以下のページにまとめました

- [【Godot】2Dスプライトアニメーションの基本的な使い方](https://qiita.com/2dgames_jp/items/1fa01466a8b2df2e6d97)

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#enum-%E3%82%92%E5%AE%9A%E7%BE%A9%E3%81%99%E3%82%8B)enum を定義する

```
# 場所
enum ePlace {
	Deck,          # 山札
	Pyramid,       # ピラミッド
	Hand,          # 手札
	HandToDiscard, # 捨札
}
```

enum 定義を外部から利用する場合はクラス定義する必要がある

```
# 外部からアクセスする場合はこの定義が必要
class_name MyCard

# 場所
enum ePlace {
	Deck,          # 山札
	Pyramid,       # ピラミッド
	Hand,          # 手札
	HandToDiscard, # 捨札
}
```

これにより外部のクラスから呼び出しが可能となる

```
if card.place == MyCard.ePlace.Deck:
  # 山札にカードが存在する
```

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#switchcase%E6%96%87%E3%81%AE%E6%9B%B8%E3%81%8D%E6%96%B9)switch〜case文の書き方

```
var v = 0

match v:
  0:
    # vが0のときの処理
  1:
    # vが1のときの処理
  _:
    # それ以外
```

数値だけでなく、文字列や配列、連想配列などを条件に使うことができる

- [GDScriptの基本 / match](https://docs.godotengine.org/ja/stable/getting_started/scripting/gdscript/gdscript_basics.html#match)

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#area2d%E3%81%AE%E3%83%9E%E3%82%A6%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%83%E3%82%AF%E3%82%A4%E3%83%99%E3%83%B3%E3%83%88%E3%81%8C%E3%81%86%E3%81%BE%E3%81%8F%E5%8B%95%E3%81%8B%E3%81%AA%E3%81%84%E5%A0%B4%E5%90%88)Area2Dのマウスクリックイベントがうまく動かない場合

`*Rect` 系ノード (`TextureRect` や `ColorRect`)を使っているとそちらにマウスイベントを取られてしまうことがあるので、もし `*Rect` 系ノードを使っている場合は `Mouse > Filter` を `Ignore` にする  
[![Godot_Engine_-gd_pyramid-Card_tscn___.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F26f4351c-77ba-6285-b4f5-f9e42e5466ec.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=804520c9bdaa1a33d8cdefb7c2753275)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F26f4351c-77ba-6285-b4f5-f9e42e5466ec.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=804520c9bdaa1a33d8cdefb7c2753275)

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#array%E3%81%A7%E3%81%AE%E7%8B%AC%E8%87%AA%E3%81%AE%E3%82%BD%E3%83%BC%E3%83%88%E5%87%A6%E7%90%86)Arrayでの独自のソート処理

`Array.sort()` は標準の比較演算子によるソートとなるので、独自のソート処理を行いたい場合には、`Array.sort_custom()` を使用する

```
# カードを "pos_index" でソートするクラス
class CardSorter:
	static func sort_ascending(a:Card, b:Card) -> bool:
		# 昇順ソート
		if a.pos_index < b.pos_index:
			return true
		return false

	static func sort_descending(a:Card, b:Card) -> bool:
		# 降順ソート
		if a.pos_index > b.pos_index:
			return true
		return false

```

呼び出しは、以下のように行う

```
func get_pyramid_list():
	# ピラミッドに含まれるカードのリストを返す
	var ret = []
	for c in cards:
		var card:Card = c
		if card.place == Card.ePlace.Pyramid:
			ret.append(card)
	
	# 昇順ソート
	ret.sort_custom(CardSorter, "sort_ascending")
	
	return ret
```

そのスクリプト内でしか使用しなければ、`self` を使っても良い

```
func sort_ascending(a:Card, b:Card) -> bool:
  # 昇順ソート
  if a.pos_index < b.pos_index:
    return true
  return false

# 呼び出し
var arr = []
arr.sort_custom(self, "sort_ascending")
```

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E6%8F%8F%E7%94%BB%E9%A0%86%E3%81%AE%E5%88%B6%E5%BE%A1)描画順の制御

`z_index` の値で描画順を制御できる。  
この値を大きくするほど手前に描画される

```
	# ピラミッドに追加
	for i in range(6):
		var card = deck.pop_back()
		card.set_next_position(get_pyramid_position(i))
		card.z_index = i # 下にあるカードが手前に描画する
		card.place = Card.ePlace.Pyramid
		card.pos_index = i

```

[![gd_pyramid__DEBUG_.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Facb2790b-b855-ac2c-9d02-71a06ef3857d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=560c80f499828090cf4bdd68c6ac724f)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Facb2790b-b855-ac2c-9d02-71a06ef3857d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=560c80f499828090cf4bdd68c6ac724f)

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#2021222-%E8%BF%BD%E8%A8%98)2021.2.22 追記

描画順の制御方法について、さらに調べてみました  
→[【Godot】2Dゲームの描画順の制御方法](https://qiita.com/2dgames_jp/items/f72896469a519115f308)

`CanvasLayer` を使うことで、描画順の制御が柔軟に行えるようになります。

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%81%A8%E3%82%8A%E3%81%82%E3%81%88%E3%81%9A%E3%83%80%E3%82%A4%E3%82%A2%E3%83%AD%E3%82%B0%E3%82%92%E8%A1%A8%E7%A4%BA%E3%81%97%E3%81%9F%E3%81%84)とりあえずダイアログを表示したい

デバッグ用にとりあえずダイアログを表示するには `OS.alert()` を使う

```
func _ready() -> void:
	OS.alert("hoge", "piyo")
```

[![gd_pyramid__DEBUG_.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F6ab91975-9e9f-d1d3-d212-ad18fed04694.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7c5ca3cf0e0cc410207649b55d920154)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F6ab91975-9e9f-d1d3-d212-ad18fed04694.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7c5ca3cf0e0cc410207649b55d920154)

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%83%9D%E3%83%BC%E3%82%BA%E3%83%A1%E3%83%8B%E3%83%A5%E3%83%BC%E3%82%92%E8%A1%A8%E7%A4%BA%E3%81%97%E3%81%9F%E3%81%84)ポーズメニューを表示したい

`get_tree().paused` に `true` を指定すると、ゲーム全体が停止します。  
使い方の詳細は以下のページにまとめました。

- [【Godot】ゲームを一時停止するポップアップウィンドウの実装方法](https://qiita.com/2dgames_jp/items/3df03851d8e7a4622e40)

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%82%A4%E3%83%BC%E3%82%B8%E3%83%B3%E3%82%B0%E9%96%A2%E6%95%B0%E3%82%92%E4%BD%BF%E3%81%84%E3%81%9F%E3%81%84)イージング関数を使いたい

`ease()` という関数が用意されていて単純な曲線であればこれで実装できます。  
ただ、パラメータの指定が独特なのでこのグラフを見てどの値を指定するのかを検討する必要があります。  
[![29590477-56c5b672-879a-11e7-9d25-67c562a66093.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F2edfdf41-bdef-3bcc-9eb1-38aa4f656d33.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bdb72a4dce3bce607fdf88fe9a0acccf)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F2edfdf41-bdef-3bcc-9eb1-38aa4f656d33.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bdb72a4dce3bce607fdf88fe9a0acccf)  
[https://github.com/godotengine/godot/issues/10572](https://github.com/godotengine/godot/issues/10572)

```
# cube in
var d = ease(t, 0.4)
# cube out
var d = ease(t, 4.6)
# expo in
var d = ease(t, 0.2)
# expo out
var d = ease(t, 4.8)
```

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E5%B8%B8%E3%81%AB%E3%82%B0%E3%83%AA%E3%83%83%E3%83%89%E3%81%8C%E8%A1%A8%E7%A4%BA%E3%81%95%E3%82%8C%E3%81%A6%E3%81%97%E3%81%BE%E3%81%A3%E3%81%A6%E3%82%A8%E3%83%87%E3%82%A3%E3%82%BF%E3%81%8C%E9%87%8D%E3%81%9F%E3%81%84)常にグリッドが表示されてしまってエディタが重たい

2Dエディタにグリッドを表示していると処理落ちが起きる場合は、`ビュー > 常にグリッドを表示` のチェックを外すと処理が軽くなることがあります

[![Godot_Engine_-gd_pyramid-Main_tscn___.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fc8c78216-6c29-35ee-a583-4294115c332b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3069c0fd8ae7c4246ba994f470078140)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fc8c78216-6c29-35ee-a583-4294115c332b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3069c0fd8ae7c4246ba994f470078140)

この設定を行うと、グリッドスナップが無効の場合にはグリッドが表示されなくなります  
[![Godot_Engine_-gd_pyramid-Main_tscn___.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Ff0bdca94-f6e0-e0eb-be72-9cd717fd9f67.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=dcbb522cff7c3f842acf73c4d836ddbd)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Ff0bdca94-f6e0-e0eb-be72-9cd717fd9f67.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=dcbb522cff7c3f842acf73c4d836ddbd)

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E5%8A%A0%E7%AE%97%E3%83%96%E3%83%AC%E3%83%B3%E3%83%89%E3%83%A2%E3%83%BC%E3%83%89%E3%81%A7sprite%E3%82%92%E6%8F%8F%E7%94%BB%E3%81%97%E3%81%9F%E3%81%84)加算ブレンドモードでSpriteを描画したい

CanvasItem > Material > 新規CanvasItemMaterial でMaterialを作成し、`Blend Mode` に `Add` を指定すると加算ブレンドモードで Sprite を描画できるようになります。  
[![Godot_Engine_-gd_pyramid-_Card_tscn.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Ff3bf15c8-ea55-32fd-8a6f-8774eb2e1bb4.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ef0989e0caa239efe69c7881761ee471)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Ff3bf15c8-ea55-32fd-8a6f-8774eb2e1bb4.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ef0989e0caa239efe69c7881761ee471)

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E5%87%BA%E5%8A%9B%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6%E3%81%AE%E5%88%B6%E5%BE%A1)出力ウィンドウの制御

主張の激しい「出力ウィンドウ」の制御方法について

ひとまずここをクリックすれば表示・非表示は切り替えられますが、初期設定ではデバッグ実行時に常に表示されてしまいます  
[![Godot_Engine_-gd_pyramid-Main_tscn_と「【Godot】GodotのTIPS」を編集_-_Qiita.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F28d22592-32e8-68ab-9687-5eaebe26b00d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=00fa2e12b1087a16d4cc11ffd32d0766)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F28d22592-32e8-68ab-9687-5eaebe26b00d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=00fa2e12b1087a16d4cc11ffd32d0766)

メニューから `エディタ > エディタ設定` を選択  
[![Godot_Engine_-gd_pyramid-_Main_tscn.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F75c48892-a9c6-c6a0-b817-138a41505d02.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=daf51f7a373102325ec80372718861e9)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F75c48892-a9c6-c6a0-b817-138a41505d02.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=daf51f7a373102325ec80372718861e9)

`一般 > Run > Output` から出力ウィンドウの設定を

[![Godot_Engine_-gd_pyramid-_Main_tscn.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F707fd67c-f374-3dde-9372-521b1ec213c8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=db6d160eebab06d4eee43418077deb5b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2F707fd67c-f374-3dde-9372-521b1ec213c8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=db6d160eebab06d4eee43418077deb5b)

- Always Open Output On Play: デバッグ起動時に出力ウィンドウを自動で表示する
- Always Close Output On Stop: デバッグ停止時に出力ウィンドウを自動で非表示にする

個人的には出力ウィンドウが常に必要ではなく、必要なときだけ表示したいので、`Always Open Output On Play` のチェックは外しています

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E4%B8%89%E8%A7%92%E5%BD%A2%E3%82%92%E5%8B%95%E7%9A%84%E3%81%AB%E6%8F%8F%E7%94%BB%E3%81%99%E3%82%8B)三角形を動的に描画する

`_draw()` をオーバーライドして、`draw_polygon()` で三角形が描画されます。

```
func _draw():
	var points = PoolVector2Array()
	var colors = PoolColorArray([Color(0, 1, 0, 0.5)])
	points.push_back(Vector2(50, 0))
	points.push_back(Vector2(0, 200))
	points.push_back(Vector2(200, 200))
	draw_polygon(points, colors)
```

ただ、この描画コマンドはキャッシュされる(開始時に1度しか呼び出されない)ため、位置を変更したい場合は、描画情報が変更されたタイミングで`update()` を呼び出して `draw()` を再び呼び出すようにします。

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E6%9C%AA%E4%BD%BF%E7%94%A8%E5%A4%89%E6%95%B0%E3%81%AE%E8%AD%A6%E5%91%8A%E3%82%92%E6%B6%88%E3%81%97%E3%81%9F%E3%81%84)未使用変数の警告を消したい

関数の引数で未使用変数の警告を消したい場合がたまにあります。  
例えば `_process(delta)` の `delta` が未使用である場合です

```
func _process(delta):
  pass
```

[![Godot_Engine_-TestAdv-_AdvMoveCursor_tscn.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fc3193e10-efc4-8c05-04a8-1b827ac50401.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=92c656b664c213f0f74dc5e5ec23e3e9)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fc3193e10-efc4-8c05-04a8-1b827ac50401.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=92c656b664c213f0f74dc5e5ec23e3e9)

こんな感じで警告が出てしまうのですが、引数の変数名の前に「_」をつけると未使用変数として警告を抑制することができます。(_deltaなど)

```
func _process(_delta):
  pass
```

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%82%B5%E3%82%A6%E3%83%B3%E3%83%89%E3%82%92%E5%86%8D%E7%94%9F%E3%81%97%E3%81%9F%E3%81%84)サウンドを再生したい

プロジェクトに *.wav または *.ogg を追加

再生したいシーンまたはノードに `AudioStreamPlayer2D` を追加  
[![Godot_Engine_-gd_pyramid-_Main_tscn.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fe67ee387-920e-4c93-dcde-dd3b57c4f115.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=577f2449eadbf2dcad37cbe441f23f8a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F43772%2Fe67ee387-920e-4c93-dcde-dd3b57c4f115.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=577f2449eadbf2dcad37cbe441f23f8a)

ノード名を 「AudioDrawCard」とした場合、スクリプトで以下のように記述する

```
# 再生
$AudioDrawCard.play()
```

1つの AudioStreamPlayer2D に対し、1つの再生のみとなる（再生中に play() を呼び出すと、再生中のサウンドは停止する）

再生中は再生しない、という処理は以下のように行う

```
if $AudioDrawCard.playing == false:
  # 再生していないときのみ再生する
  $AudioDrawCard.play()
```

## [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%82%B5%E3%82%A6%E3%83%B3%E3%83%89%E3%82%92%E5%8B%95%E7%9A%84%E3%81%AB%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%81%BF%E3%81%9F%E3%81%84)サウンドを動的に読み込みたい

`AudioStreamPlayer2D.stream` にサウンドリソースを指定すると、動的な読み込みができます。  
詳細は以下のページにまとめました。

- [【Godot】サウンドを動的にロードして再生する方法](https://qiita.com/2dgames_jp/items/76f9e69db318d4297eb9)

# [](https://qiita.com/2dgames_jp/items/d150aa1cc98f200292ab#%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%82%A6%E3%81%AE%E3%83%AA%E3%82%B5%E3%82%A4%E3%82%BA)ウィンドウのリサイズ

スクリプトから画面サイズを変更するには `OS.set_window_size()` を使用します

```
# ウィンドウを 480x320 に変更する
OS.set_window_size(Vector2(480, 320))
```
