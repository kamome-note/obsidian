『両手いっぱいに芋の花を』制作者の記事。
超参考になる。

Godotマイこだわり集
https://note.com/nwsnkn/n/n294d3151a002

---

## enum

Godotでは自作enumは必ずクラスに属しています。ユーザーの用意するスクリプトは全部クラスということにすることで、いろいろシンプルにする狙いがあるんじゃないかと思ってます。  
とはいえ、C系の言語の要領で考えると、どこに定義していいかわからなくなることがあります。特定のクラスとの結びつきがよっぽど強いenumは、素直にそのクラスの中に作ればいいと思います。しかしそうでないものもあります。

ひとまず、そのenumが存在するための専用のクラスを作る。という方針にすると迷いが無くなります。これはstaticクラス的な扱いで、インスタンスは使用禁止です。

```python
class_name CombatResult
extends Node # インスタンス作らないけど、ひとまずNodeやObject

# "Type", "State", "Category", "Phase"などをenum名にする
enum Type
{
  ELIMINATED,
  GOT_WIPED,
  RAN_AWAY,
}
```


そして、このenumの便利関数（ヘルパ）的なものは同じクラスに足していきます。

```python
# static関数として関数を追加する

# enumの意味付け
static func CanAcquireReward(combat_result_type: CombatResult.Type) -> bool:
  if combat_result_type == CombatResult.Type.ELIMINATED:
    return true
  return false

# 最終的には、だいたいこういうのが必要になる
static func GetTextID(combat_result_type: CombatResult.Type) -> TextID:
  if combat_result_type == CombatResult.Type.ELIMINATED:
    return TextID.COMBAT_RESULT_ELIMINATED # 勝利
  if combat_result_type == CombatResult.Type.GET_WIPED:
    return TextID.COMBAT_RESULT_GET_WIPED # 全滅
  if combat_result_type == CombatResult.Type.RAN_AWAY:
    return TextID.COMBAT_RESULT_RAN_AWAY # 逃走
return TextID.ERROR

# 制作序盤はこれでいい
static func GetName(combat_result_type: CombatResult.Type) -> StringName:
  if combat_result_type == CombatResult.Type.ELIMINATED:
    return &"勝利"
  if combat_result_type == CombatResult.Type.GET_WIPED:
    return &"全滅"
  if combat_result_type == CombatResult.Type.RAN_AWAY:
    return &"逃走"
return "エラー"
```


この方法のいいところは、後々になって、このクラスをそのまま正式なクラスに昇格（？）して使えるところです。  
その場合、クラスのインスタンスは、クラス名を体現する複合データです。enumはそのクラスのサブカテゴリ（サブ型・サブ分類）に意味を変え、クラスのメンバ変数として使われます。そしてその他のメンバ変数で、enumを補う情報を付加します。

```python
class_name CombatResult
extends RefCounted # RefCountedやNodeにする

enum Type
{
  ELIMINATED,
  GOT_WIPED,
  RAN_AWAY,
}

# enumがメンバの1つになる
# このクラスインスタンスの、分類・区分として使用する
var type: CombatResult.Type = CombatResult.Type.ELIMINATED

# 付加情報
# 経過ターン数と、スペシャル攻撃による決着かどうか
var total_turn_count: int = 0
var is_sp_atk_finish: bool = 0
```


使う側はこんな感じに変わります。この運用なら、変数名やクラス名を変えなくても変じゃないはずです。

```python
# enumだけのクラスとして使う時
var combat_result: CombatResult.Type = CombatResult.Type.ELIMINATED

↓

# インスタンス化できるクラスに「成った」時
var combat_result: CombatResult = CombatResult.new()
combat_result.type = CombatResult.Type.ELIMINATED
combat_result.total_turn_count = turn_count
combat_result.is_sp_atk_finish = false
```


## newの代わり

スクリプトエディタ、自作クラスのnewの引数をサジェスト（？）してくれないことがありますよね。筆者の場合はときどきあります。newはnewであって_initじゃないから、いろいろ都合があって解析しにくいんだろうなあと思ってます。

そこで、よく使うクラスはstatic関数で作れるようにしておきます。これなら候補がすぐ出てくれる。

```python
# createとかmakeとかそんな名前で統一する
static func create(cube_count: int, new_scale: float) -> IceCubes:
  # newしてreturnするだけ
  return IceCubes.new(cube_count, new_scale)

func _init(cube_count: int, new_scale: float) -> void:
  _cube_count = cube_count
  _base_scale = new_scale
  _setup_nodes()

# 作る側
# 引数がサジェストされます
var ice_cubes: IceCubes = IceCubes.create(3, 0.1)
```


これの副次的ないいところは、create系関数を後から増やせるところです。インスタンスの作り方を複数持てます。関数名を変えられるので、C#/C++で作れる引数違いのコンストラクタ（オーバーロード）よりも、意図が明示的です。

```python
# 引数の型がかぶってもOK
static func create_by_index(idx: int) -> SoundTrig:
static func create_by_id(id: int) -> SoundTrig:

# enumで作る
static func create_by_category(ladder_cat: Ladder.Category) -> Ladder:

# コピーを作る（よく要る）
static func create_copy(src: DamageParam) -> DamageParam:
```


## アセットリソースのファイル名

Godotの公式ドキュメントでは、プロジェクト下のファイルはスネークケースで命名することが推奨されています。kouiu_kanji。

筆者は長年ファイル名をパスカルケースで書く派です。KouiuNamae。でもGodotでは、郷に入らば郷に従えの精神で、スクリプトやシーンのファイル名はスネークケースにしてます。

でもね、外からインポートさせるデータ系ファイルに関しては、パスカルケースで通してます。PNGファイルとかblendファイルとか。

理由

- リソースファイルは、Godotだけで使うわけではない。Godotに使うためにファイル名を変えていては、管理が混乱してしまう。
- フォントファイルや音源ファイルなど、自分由来のファイルじゃない場合、ファイル名を変えずにそのまま使いたいこともある。管理のためでもあるけど、作者の名づけを尊重したい。
- Windows一筋なのでむしろ、大文字小文字の扱いに注意が必要なのは、十分慣れてる。

## フォルダ構成

Godotではシーンに近いアセットのファイルをグループ化して、同じフォルダにまとめましょう、ということになってます。

こんな感じです。

```python
/ballista
  ballista.tscn
  ballista_volt.tscn
  ballista.gd
  ballista_volt.gd
  ballista.blend
  ballista.png
  ballista_volt_shot.wav
  ballista_volt_hit.wav
```

チームで作っている場合、こういうのは必須だと思うんですよ。大人数でおっきなゲーム作るならなおさら。

筆者は最初はこれに倣おうとしました。でもそれによるうま味を感じなくって、結局はデータとスクリプトを分けることにしました。

```python
/Data
  /3D
    /Scenery
      Ballista.blend
      Ballista.png
  /SFX
    ballista_volt_shot.wav
    ballista_volt_hit.wav
/Low #小規模データの意味
  /Scenery #シーンの意味
    ballista.gd
    ballista_volt.gd
# シーンは使わない
```


理由

- 一人で作っていて、さらに、スクリプトやシーンを含めたGodot用「アセット」を外部から導入することもないので、上記のballistaのようなフォルダを、着脱したり更新をかけたりする必要が無い。
    
- データファイルはスクリプトにくらべてファイルサイズが大きいので、分かれていたほうがいろいろと都合がいい。
    
- 作るのが非アクションゲームなので、スクリプトと見た目と音は、疎な繋がりにできるし、したほうが（自分的には）やりやすい。
    
- データは分かれていたほうが気持ちがいい。迷いが無いのは大事。
    

## ブロックを作る

ローカル変数が衝突しないように、スコープを切りたいときは、if文でブロックを作ってます。

```python
# こういうのが同じ関数でいくつも続いたり、
# 別の場所であっても頻出する場合、
# なるべく変数名と書式を揃えて、一覧性、視認性を上げたい

if true:
  var pos_x: float = 16.0
  var pos_y: float = 16.0 * float(count + 0)
  var item: MenuItem = MenuItemBox.create()
  _add_box(item, pos_x, pos_y)

if true:
  var pos_x: float = 32.0
  var pos_y: float = 16.0 * float(count + 1)
  var item: MenuItem = MenuItemFrame.create()
  _add_frame(item, pos_x, pos_y)

if true:
  var pos_x: float = _calc_pos_x()
  var pos_y: float = 16.0 * float(count + 2)
  var item: MenuItem = _get_next_item()
  _add_item(item, pos_x, pos_y)
```

