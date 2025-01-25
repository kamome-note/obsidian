『両手いっぱいに芋の花を』制作者の記事。
超参考になる。

Godotたちのシングルトン
https://note.com/nwsnkn/n/nf6bb3f997cdf

---

手段がGodotにせよ何にせよ、ゲーム作ってるとシングルトン要りますよね。ロードしたリソースをキープしたり、ゲームの進行状況を記録したり。  
この場合、「常に1個しか存在しない」という本来の機能はそこまで重要ではなくって、  
・参照を渡されなくてもどこからでもアクセスできる  
・シーン（広義）が切り替わっても保持され続ける  
というのがキーとなる要件だと思います。

んで、どうやってシングルトンを実装するかですけど、やり方が色々あるように思います。一長一短。  
というわけで、そのアイデアを書き連ねてみます。

## 案A. AutoLoad機能を使う

Godot公式が推奨（？）するやり方です。Godotの「自動読み込み / AutoLoad」機能を使います。Godotさんがシングルトンと言ったらこれのことです。  
プロジェクト設定から、自動読み込みリストにクラスを登録しておくと、ゲーム実行時に自動的にノードが作られます。ノードツリーのルート直下に置かれます。

```python
# クラス定義 ============

class_name MySingleton
extends Node

# 普通のクラスのように書く
var val_a: int = -1
var val_b: int = -1
var val_c: int = -1

func do_something() -> void:
	exec_something(val_a)

# 使う側 ============

# ノード名でインスタンスにアクセスできる。
# ノード名はクラス名と重複できないので、g（globalのg）を付けてます。
g_MySingleton.do_something()
```

**メリット**

- プロジェクト内のシングルトンの存在が、GUI上でリストされます。管理がしやすい。どんなシングルトンが在るか一目瞭然。
- 仕組みがちょっと大げさなので、使用に慎重になって（ほんとに？）、乱用を控えることになります。どこからでもアクセスできるデータは、使いすぎるとプログラムがわけわからんくなります。

**デメリット**

- どんなシーンを単独再生(F6キー)しても、プロジェクト内の全AutoLoadがインスタンス化されてしまいます。シーンの入れ子構造でゲームを作り上げていってねというのがGodotの理念のはずです。でも小さなシーン（扇風機とか、HPバーとか）を再生したときも、ゲーム全体を管理するようなシングルトンが出来上がってツリー下に配置されてると、気になっちゃって仕方がない。自動で駆動するようなシングルトンでもない限り、特に悪さするわけじゃないはずなので、気持ちの問題でしかないんですけどね。
- シングルトンを破棄できません。無駄なメモリを解放したり、リークを検知するために、データをバッサリ消したいことあると思うんです。そのときに、シングルトンノードごと消すということができない。とはいえ、別にノード自体を消さなくても、それが保持してるデータを消せれば十分ではあります。

```python
class_name MySingleton

# データだけDispose（破棄）できるようにした例

var dat: MySingletonDat = null # メンバ変数はこのクラスに集約する

func dispose() -> void:
	if dat != null:
		dat.dispose()
		dat = null
```

## 案B. 特定名のノードに保持させる

永続的なデータの置き場としてノードを使うのなら、別にAutoLoadでエンジンに作ってもらわなくても、自分で作ればいいんじゃない？って案です。  
この時カギになるのが、ノードの名前です。特定の名前（だいたいはクラス名）でツリー直下に置いておけば、存在の確認ができるし、取得できます。

```python
class_name MySingleton
extands Node

var val_a: int = -1

static func _get_singleton() -> MySingleton
	# 名前で取得する
	var my_singleton: MySingleton = get_node("/root/MySingleton") as MySingleton
	if my_singleton == null:
		# 無ければ作ってルートに配置する
		my_singleton = MySingleton.new()
		my_singleton.name = "MySingleton"
		var scene_tree: SceneTree = Engine.get_main_loop() as SceneTree
		var root: Window = scene_tree.root
		root.add_child(my_singleton)
	return my_singleton

static func do_something() -> void:
	MySingleton._get_singleton().do_something()

static func set_val_a(val: int) -> void:
	MySingleton._get_singleton().val_a = val
```

**メリット**

- static変数の無かった時代の、古いGodotでも使えます。

**デメリット**

- static関数を呼ぶたびに、ツリーからノードを取得するので、動作が重いかもしれません。ノードの操作は重くて推奨されないイメージあります。Unityはそうでしたね。  1フレームに何度も処理をするような場合は、呼び出し側にノードを取らせて、非static関数を呼ばせるようにするといいかもしれません。

```python
# ツリー上のノードの検索がcount回される
for i in range(0, count):
	MySingleton.do_something()

# これならノードの検索が1度で済む（instはinstanceの略）
var my_singleton: MySingleton = MySingleton.get_inst()
for i in range(0, count):
	my_singleton.do_something()
```

## 案C. static変数を使う

Godot 4.1では、static変数が使えるようになりました。今まで無かったのが不思議です。もうこれでいいじゃん。

Godotは長い歴史の中で、class_nameが導入されて、自作クラスにアクセスできるようになったのは最近（Godot 3.1 2019年3月）の話です。  
多分ですけど、gdファイルがクラスとしてコンパイルされるようになったのはこのVer.以降で、それまでのGDScriptは名前通り、今よりもっともっと「スクリプト」的だったんじゃないかなと思います。事前にノードにアタッチしてシーン化するか、load()してアタッチして使うのが常で。だから仕組み的にstatic変数なんて作りようがなかったんだと思います。たぶん。

### C-1. static変数にデータを置く

欲望に忠実な素直なやり方です。

```python
class_name MySingleton
extands Node

# データを全部static変数にする
static var val_a: int = -1
static var val_b: int = -1
static var val_c: int = -1
static var node: MeshInstance3D = null

# それか別のクラスにまとめてしまう
static var dat: MySingletonData = null
```

**メリット**

- わかりやすい！

**デメリット**

- デバッグ中にインスペクタで内容が見れません。これはつらい。AutoLoadのノードはスタックトレースの変数一覧に表示されるんですけどね。static変数は対応してないようです。

### C-2. ノード化してstatic変数で保持する

案Bとほぼ同じアイデアですが、作ったノードはツリーに配置するだけでなく、static変数で保持しておきます。こうすることで、ツリー上から毎回ノードを検索させるコストを解消できます。

```python
# C-2-1
# 自動的にインスタンスが作成される案
# 使う側がインスタンスの存在を意識しなくていい

class_name MySingleton
extands Node

var _inst: MySingleton = null

# データは非static(dynamic)変数にする
var val_a: int = -1
var val_b: int = -1
var val_c: int = -1
var node: MeshInstance3D = null

static func _secure_inst() -> void:
	if MySingleton._inst == null:
		MySingleton._inst = MySingleton.new()
		var scene_tree: SceneTree = Engine.get_main_loop() as SceneTree
		var root: Window = scene_tree.root
		root.add_child.call_deferred(MySingleton._inst)

# 呼び出される関数の冒頭で必ず_secure_inst()を呼ぶ
static func do_something() -> void:
	MySingleton._secure_inst()
	MySingleton._inst.exec_something()
```

```python
# C-2-2
# 使う側が明示的に初期化する案
# データ管理の責任者をはっきりさせる＆解放が可能になる

class_name MySingleton
extands Node

var _inst: MySingleton = null

# データは非static(dynamic)変数にする
var val_a: int = -1
var val_b: int = -1
var val_c: int = -1
var node: MeshInstance3D = null

# 使う側に初期化させる
# 引数を指定できるメリットがある
# 親をルート以外に指定できる
static func secure_inst(parent_node: Node, arg: int) -> void:
	if MySingleton._inst == null:
		MySingleton._inst = MySingleton.new(arg)
		parent_node(MySingleton._inst)

# 破棄可能
static func abandon_inst() -> void:
	if MySingleton._inst != null:
		MySingleton._inst.free()
		MySingleton._inst != null

# 初期化せずに呼び出すと_instが無いのでエラーとなる
static func do_something() -> void:
	MySingleton._inst.exec_something()
```

**メリット**

- 関数内でメンバ変数/メンバ関数にアクセスする際には、必ず_inst.から始まるので、エディタのサジェスト機能がいい感じに働いてくれます。C#やC++で必ずthis付けるタイプの人に優しい。

**デメリット**

- 記述がちょっと面倒です。とはいえ、1つ作ってしまえばあとはコピペと置換でいけます。

