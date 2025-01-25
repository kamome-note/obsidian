『両手いっぱいに芋の花を』制作者の記事。
超参考になる。

Godot Engineでつまづいたところ集 1
https://note.com/nwsnkn/n/nc6f21fd809d2
Godot Engineでつまづいたところ集 2
https://note.com/nwsnkn/n/nf56a2ee381ef
Godot Engineでつまづいたところ集 3
https://note.com/nwsnkn/n/n3fc346ddf25f

---

## 設定画面の見間違い  
エディター設定とプロジェクト設定は、設定画面がぱっと見似ている。探している項目が見当たらない時は、間違えて別の画面を開いている。  
あるある。

---

## count回ループするfor文  
for文でcount回ループするときは、`for i in range(0, count)`。  
`for i in [0, count]`は間違い。この場合、inの右の部分は0とcountの2要素からなる配列になってしまう。2回しかループしない。

---

## 配列の配列  
Arrayは中身を型指定できるが、型のネストはできない。例えば`Array[Array[MyClass]]`はNG。  
MyClassの2次元配列を型安全に使いたければ、X×Yサイズの`Array[MyClass]`で代替するしかなさそう。

---

## enumはクラスに属す  
enumは他クラスで定義していても、ちゃんと使用可能。例えば、`MyClass.MyEnum.ENUM_ITEM_0` のように書く。  
ちなみに、未保存でも構文解析して評価してくれるVSのインテリセンスと違って、`my_class.gd`側をちゃんとファイル保存していないと、「そんなenumは無い」とエディタに怒られる。これはenumに限らず、クラスや関数の定義でも同様。

---

## コードエディタの矩形選択  
GDScriptのエディタは、普通のテキストエディタのように、マウスによる矩形選択はできない。その代わり、Shift + Ctrl + カーソルキーで、矩形選択できる。ただし、操作は慣れるまで少し難しい。

---

## 「参照の検索」は無い  
GDScriptのエディタは、VSのような「参照の検索」機能が無い。なので文字列による検索を頼ることになる。コードもそれを意識した書き方をしたほうがよさそう。  
ちょっと冗長に書いたほうがいい。Cellクラスのメンバ変数`_cell_index`、これはC#なら`_index`に直したくなる。だが、"`index`"というワードはいろんなクラスや関数で使うだろう。なので、`_cell_index`のほうが、検索性が上がる。  
（関係無いけど、<u>indexはidxと略して書いたほうがGodot風</u>である）

---

## `_ready`と`_init` 
`_ready`はノードがシーンツリーに追加されたときに呼ばれる関数。UnityのStart関数のようなものであって、コンストラクタ的なものではない。Objectのnew時に呼ばれるのは`_init`。こっちがいわば、コンストラクタ。

慣れるまでは何度か間違える。非ノードクラスに`_ready`を定義してしまっても何もワーニング等出ないので、誤りに気付かないことも。

- `_init`: コンストラクタ  
- `_ready`: ノードの初回更新時に呼ばれる  
- `_process`: ノードの更新  
- `_update`: そんな関数は無い  
- `_proc`: こんな関数も無い

---

## あえてオーバーライド前の関数を呼ぶ  
親クラスの同関数をオーバーライド先のクラスから呼ぶには、キーワード"`super`"を使って"`super.my_func()`"と書く。  
ちなみにGodot3では、ドットだけを使って"`.my_func()`"のように呼ぶ書式だったが、Godot4でsuperに変わった。わかりやすくなった。

---

## 整数の割り算はワーニング対象  
整数同士の割り算をしようとすると、「余りは捨てられるけどいいの？」とワーニングが出る。奇妙に感じるメッセージだが、これはPythonユーザーを意識したワーニング。Pythonは整数同士の割り算で自動的にfloatになる仕様なので。  
プロジェクト設定から、このワーニングをオフにできる。もしくは、floatにキャストしてから割り算をして、intにキャストしなおすことでも回避できる。

---

## enumでループ  
C#のようにenumでforeachしたい場合、`for mode in Enemy.Mode:`とやると、名前:Stringでループしてしまう。`for mode in Enemy.Mode.values():`とすれば、enumの値:intでループされる。  
ちなみに、どうやらenumの値は暗黙にintのようで、とくにキャストとかせず、`my_array[mode]`などのように、配列の添え字に使用可能。

---

## Blenderのバージョンは古いとNG？  
blendファイルのインポートで`ERR_FILE_CANT_OPEN`エラーが出て上手くいかないときは、Godotが参照しているBlenderのexeのバージョンが、ファイルよりも古い。  
さらにどうやら、ファイルに対してBlenderのバージョンが「新しくても」エラーが出る（のかもしれない）。Blender3.2で保存したファイルを3.4のexe設定で開こうとしてもエラーが出た。

---

## テスト実行とシーンエディタ  
<u>エディタ上のゲーム実行（UEの用語で PIE = Play In Editor ）の話</u>。  
Godotはエディタ上で実行中に、<u>ライブなシーンの状態をビジュアル的に見ることができない</u>。UnityやUEのエディタは、PIE中にワールドの状態をエディタ上で可視化する。それがない。

確かに、ツリーのヒエラルキーや各ノードのパラメータは、シーンタブの「リモート」を選択することで見ることができる。しかし一方で、シーンエディタに映っている映像は、PIEの状況を視覚的に表したものではない。そもそも、この画面はシーン（tscnファイル）を編集するためのもので、デバッグする用途ではないのだ。

なので、<u>PIE中に編集画面上で何も動いていないのは、正常</u>である。これは多分Unityとかやってた人は、最初引っかかると思う。

結局、ゲーム画面で確認するしかない。一応救済機能がある。エディタには、「プロジェクトカメラのオーバーライド」というボタンが備わっている。カメラのアイコンだ。それをオンにすると、PIEのゲーム画面が強制的に、シーンエディタ上の視点で描画されるようになる。PIE中にエディタの視点を動かすと、ゲーム画面の視点にリアルタイムに反映される。これがデバッグ機能として想定されているようだ。

---

## コード生成メッシュのワイヤーフレームを見る方法  
通常、メッシュはシーンエディタ上でワイヤーフレーム表示することができる。しかし、ランタイムで動的に作成したArrayMeshは、通常はワイヤーフレームで見る手段が無い。なぜなら前述の通り、実行中の状態はエディタで見ることができないから。  
どうしてもエディタ上で形状を確認したければ、<u>@toolを先頭に付けたデバッグ専用のビューワーシーンを作成する</u>アイデアがある。ビューワーシーンの_ready内でメッシュを作成すればよい。

---

## デフォルトの背景色の落とし穴  
StandardMaterial3Dを作成して、<u>デフォルト値のまま使用すると</u>、ノーマルの無いメッシュなどは、<u>カメラの背景色のデフォルト値（暗い灰色）とまったく同じRGB値で描かれる</u>。<u>結果的に何も映ってないように見える</u>。これは、知ってないと「なぜオブジェクトが見えない？」となる。映ってないのではなく、背景と同化しているだけだ。

カメラの背景色のデフォルト値（暗い灰色）は、「アンビエント以外でライティングされていない白い物体の色」なのだ。そういう仕様にした経緯はなんとなく解るけど、慣れるまではデフォルト値は変えてしまったほうがいい。  
<u>プロジェクト設定の、「レンダリング＞環境＞デフォルトのクリア色」から変更可能</u>。

---

## エディタ実行コードとstatic関数  
@toolを先頭に付けたデバッグ用のシーンでは、class_name経由で他の自作クラスのstatic関数をいきなり呼び出すことができない。"Invalid call, Nonexistent function" のエラーになる。スクリプトをloadすればいけそう。

---

## play関数の引数  
AnimationPlayerノードのplay関数の引数となるのは、"ライブラリ名/アニメーションキー名"。例えば、"`MyAnimLib/Evade`"。"`Evade`"だけではだめ。  
AnimationLibraryの`get_animation_list()`で返ってくるのは、アニメーションキー名だけなので、これにライブラリ名とスラッシュを付ける必要がある。

---

## マテリアルのパラメータ、個別変更
Unityでは、特定のオブジェクトだけマテリアルのパラメータを変えたい場合、明示的にマテリアルのコピーを作るか、`MeshRenderer.material`の副作用を使って一時的なマテリアルを暗黙に作成していた。マテリアルの複製が必要なのは、Godotでも同様である。しかし、Godotでは、マテリアルはメッシュに紐づいている。ということは、たとえマテリアルをコピーしても、メッシュに直接セットしては、同じメッシュを使っているインスタンスノードはすべて見た目が変わってしまう。

メッシュに紐づいているマテリアルは、いわばメッシュの「デフォルトマテリアル」なのだ。だから動的にマテリアルを変更するには、これを変えるのではない。デフォルトを隠ぺいして上書きする手段が別途用意されている。それが、`GeometryInstance3D. set_surface_override_material()`だ。

これでノードにマテリアルをセットすることで、描画に使われるマテリアルが変更される。もちろん、マテリアルのコピーは自分で事前にやっておく必要がある。コピーしたマテリアルは自分のコードで保持しておいてもいいが、`get_surface_override_material()`でセット済みのものを再取得することもできる。（`get_surface_override_material()`は、ソースコード見る限り、簡単なエラーチェックの後にメンバ変数をreturnしているだけなので、パフォーマンスも問題無さそう。）

ちなみに、`GeometryInstance3D. material_overlay`というプロパティもある。名前が似ていてこちらにもマテリアルをセットできるので、つい混同してしまう。が、まったく別物。こちらは上塗り表現用のもの。バリアやサビ、濡れの表現などに使えるだろう。

---

## クラス内クラスはRefCounted  
`Inner Class`、つまりクラス内クラスは、外のクラスが何であれ、`RefCounted`派生クラスである。なので、`free()`不要。むしろ`free()`するとエラーになる。

---

## InputEventでトリガーは取れない  
`InputEvent`システムはキーやボタンが「今押された」かどうか（トリガー）を通知できない。そのような機能がそもそも無い。  
確かに、`InputEventKey`には`echo`というプロパティがある。これはキーがリピートして押されているかどうかを示すbool値だが、押された後も数フレームはfalseを返す。（OSが検知するキーの長押しを取得しているのではないか？）なので、`echo==false`でトリガーを検知することはできない。`InputEventKey`のボタン版、`InputEvenButton`に至っては、`echo`に相当するものも無い。  
ある程度の規模のゲームで`InputEvent`を入力の機構として直接使うのは現実的ではないだろう。結局なんらかのラッピングは必須だ。

---

## InputMapのデフォルト設定は盛沢山  
`InputMap`には、プロジェクトのデフォルト値が存在する。プロジェクト設定の`InputMap`画面で、「組み込みアクションを表示」をオンにすると、これらが表示される。"`ui_何々`"という名前のアクションがたくさん登録されている。このデフォルトアクションは、アクションに紐づいているイベント（操作）は削除できるが、アクション自体は削除できない。ちなみに、イベントを削除すると、削除した分だけ、プロジェクトファイル（`project.godot`）に、消した旨の記載がむしろ増えていく。  
<u>使うつもりのない設定がプロジェクトに含まれていると、つい消したくなってしまうが、消さずにそのままでいい</u>だろう。有っても邪魔にはならないし、消してもプロジェクトがそれほどクリーンになるわけではないので。

---

## Blenderの「無効」と「隠す」  
blendファイルをインポートする際に、Blender上で「ビューポートで無効」（モニタアイコン）になっているオブジェクトは、ミラーモディファイアが適用されない。  
「ビューポートで隠す」（目アイコン）になっているオブジェクトなら、モディファイアが期待通り適用される。

Blender3.0以降、特に意識せず「無効」ばかり使ってきたが、Blenderのコンセプトに従って、「無効」と「隠す」を意識して使い分けたほうがよさそう。カメラやライトのような、普段いじらないものは「無効」、編集のために一時的に見えなくしておきたいものは「隠す」を使う。こんな運用がいいと思う。

---

## スクリプトのアタッチ  
`Object.set_script`によるスクリプトのアタッチは、次の通り動作する。ドキュメントにも詳細が書かれてある。

- 「自作クラスのオブジェクト」に対するアタッチは、機能がまったく上書きされる。<u>新しいスクリプトに存在しない変数や関数は消える</u>。

- 自作クラスのサブクラスをアタッチする場合は、オーバーライドされる。  
    というよりも、そもそもサブクラスがアタッチしなおされる。なので、<u>親クラスから継承したメンバ変数は初期化</u>される。

- 組み込みクラスのオブジェクトに対するアタッチは、機能が共存する形になる。<u>名前が衝突する関数はオーバーライド</u>される。

- set_scriptを2回呼び出すと、新しいほうで上書きされる。<u>2つ以上アタッチすることはできない</u>ようだ。

「自作クラスのオブジェクト」に対するアタッチは、あまり意味というか使いどころがない。オブジェクトを作り直すのと変わらないのだ。ポリモーフィズム的な使い方はあるかもしれないけど。あとは、パフォーマンスを出すための「オブジェクトプール」的な使い方とか…。  
やはり`set_script`の使用は、組み込みクラスに自作クラスの機能を足す用途が基本になるだろう。

---

## custom_blend  
`AnimationPlayer.play`の引数`custom_blend`は、なぜかドキュメントに説明がない。これは期待通り、今再生中のアニメーションと、新しく再生するアニメーションをクロスフェードするためのパラメータだ。単位は秒だ。

ちなみに、`AnimationPlayer.set_blend_time`は、`play`関数で`cunstom_blend`を指定しなかったときの、デフォルトの`cunstom_blend`値である。

---

## playの多重呼び出し  
`AnimationPlayer.play`に再生中のアニメーションを指定すると、始めから改めて再生とはならず、再生が継続される。この仕様自体は問題無いのだが、バグで毎フレーム呼んでいたとしても、そのバグに気付きにくい。

ちなみに、毎フレーム呼ぶと、`custom_blend`は意図しない動作をする。おそらく、`play`を呼んだ数だけ多重フェードする。音楽や映像でいうところの「トラック」が追加されるイメージだ。例えばwalk再生中にattackを4回`play`すると、walk 1 : attack 4 の割合くらいでattackにクロスフェードする動きになっているように見える。  
「なんかやたらフェードが速いな？」と思ったら、これを疑っていい。

---

## Godotにとっての「前」とは  
Godotでは**カメラもライトも-Zが前方**である。`Vector3.FRONT`は`(0, 0, -1)`で、`Vector3.BACK`は`(0, 0, 1)`だ。（ちなみにドキュメントによると **-Zが北** らしい。）だからGodotは、 **-Z Front** なのだ。  
しかし一方で、Blenderで慣習通りに-Y向きに作ったキャラクター（Blenderのおサルさんスザンヌなど）は、+Z向きでインポートされる。Godot公式のサンプルプロジェクト（`3d/IK`と`3d/platformer`）もまた、キャラクターの向きは+Zになっている。さらに、`StandardMaterial3D`の`Billboard`機能は、メッシュの+Z側をカメラを向かせるものだ。  
どうやらGodotにおいては、**モデルは後方（観測者＝あなた）を向いている**、という認識のようだ。これはもう、そういうものとして割り切るしかない。異文化においては物の見え方も常識も変わる。キャラクターや物体に、彼らにとって主体的な方向を向かせたければ、180度回転を加えて振り返らせる。違いを意識して取り扱うことが必要。  
（以下、追記）  
Godot 4.1では、Model Frontという概念が追加された。モデルは+Zが前だ。逆を言うと通常のFrontは-Zだ。向きに関して、Godotが明確に方針を示したことになる。もう迷わない。

---

## pause使用の注意点  
`AnimationPlayer.is_playing()`がfalseのとき、アニメーションが最後まで再生された結果falseなのか、`pause()`をかけた結果falseなのか、判断する手段がおそらく無い。見当たらない。

再生位置から推測することはできない。なぜなら、`pause`中も、通常の再生終了後も、「再生中」ではないので、`AnimationPlayer. current_animation_position`はエラーとなり取得できない。  
なので、`animation_finished`シグナルを受け取れるようにしておいて、事前判定するしかない。それかそもそも、`pause()`を使わないことだ。

こういった事情を踏まえて、筆者は`AnimationPlayer`を`playback_process_mode = MANUAL`で運用することにした。外部からクロックを与えて駆動させるモードだ。このほうが確実。

---

## initはアタッチでも呼ばれる  
`set_script()`によるスクリプトのアタッチでも、`_init()`は通常通り呼ばれる。`set_script()`したタイミングで呼ばれる。

---

## 継承クラス経由で親のenum呼ばない方がいい  
gdスクリプトをフォルダ移動すると、"`Class ○○ hides a global script class`" というエラーが出るようになった。海外のフォーラムによると4.0系のバグらしい。プロジェクトをリロードすると直るとの報告もあるが、自分の場合はそれだけでは駄目だった。

下記コードで、Flameが名前衝突する旨がエラーとして出ていたのだが、water_glare.gdのFlame. MatShareMode.～ をMiniature. MatShareMode.～に修正したら、エラーが出なくなった。修正前は別に文法的に間違っちゃいないのだが、意図としておかしな記述ではあったので、なんとなく気になっていた。「もしや」と思って修正したら直った。  
親クラス定義のenumを子クラス名経由で記述することが想定外なのだろう。

```python
# miniature.gd
class_name Miniature
enum MatShareMode {
	INSTANCE_MAT,
	SHARE_MAT, 
}
# flame.gd
class_name Flame
extends Miniature 
# water_glare.gd
class_name WaterGlare
extends Miniature
const _MAT_SHARE_MODE: Flame.MatShareMode = Flame.MatShareMode.INSTANCE_MAT
↓ 修正後
const _MAT_SHARE_MODE: Miniature.MatShareMode = Miniature.MatShareMode.INSTANCE_MAT
```


---

## BlenderとGodotのボーンの座標系の違い  
`BoneAttachment3D`によるボーンへの`Node3D`のアタッチメントは、Blender上で上向きに生やしたひねりゼロのボーンに対してアタッチしたときに、方向が（直観的な期待通りに）維持される。Godotはボーンの先端方向を「上」と捉えている。

これはBlender上で親をボーンに設定（ペアレントタイプがボーン）したオブジェクトの向きとは一致しない。  
おそらく、Blenderではボーンは+Y方向（奥）に延びるのを基準としており、そのときの上は+Zだからだ。Blenderにとってはボーンの側面が上だ。

- Godot: ボーンの生えてる先端方向が上  
- Blender: ボーンの側面が上

`BoneAttachment3D`によるアタッチと向きが一致するようにしたければ、Blender上ではボーン上のオブジェクトをローカルでX=-90回転させればよい。逆に、Godot側をBlender上の向きと合わせたければ、`BoneAttachment3D`に載せるNode3Dを正か負に90度回転させてやればいい。

---

## current_animation_position取得できる条件  
`AnimationPlayer`について。

`current_animation_position`の取得は、「`is_playing()`がfalseの時にエラーとなり失敗する」という認識だったのだが、違うようだ。「`current_animation`が""のときにエラーとなり失敗する」が有力なように思う。例えば、`pause()`した状態で、`advance()`等すると、`is_playing()`がfalseなのに、`current_animation`が取れる時がある。このときは`current_animation_position`も取れる。

一方で、`current_animation_length`の取得は、エラーメッセージこそ"`has no current animation`"なのだが、実際は「`assigned_animation`が""のときにエラーとなり失敗する」だと思う。`current animation`が""のときでも取得できることがあるのだ。

`current_animation_position`は `current_animation != ""`なら取得OK  
`current_animation_length`は `assigned_animation != ""`なら取得OK  
こんな違いがある。

でも、ソースコード見ると、エラー出す条件同じっぽいんだよなあ。なんでなんだろうなあ・・・。

```python
// AnimationPlayer::get_current_animation_position
ERR_FAIL_COND_V_MSG(!playback.current.from, 0, "AnimationPlayer has no current animation");

// AnimationPlayer::get_current_animation_length
ERR_FAIL_COND_V_MSG(!playback.current.from, 0, "AnimationPlayer has no current animation");
```

---

## アニメーション進行を自コードで制御  
`AnimationPlayer`について。アニメーションの更新をGodotのクロックによる自動更新ではなく、自分で制御したい場合。`playback_process_mode`に `ANIMATION_PROCESS_MANUAL`をセットすればいい。`advance`を呼べば、アニメーションが進行する。

別の方法として、`assigned_animation`に直接アニメーション名をセットして、`play()`をせず、`seek()`で時間を進めるという手段もある。ただし`seek`による更新では、シグナルやコールバックの類は発生せず、アニメーションのクロスフェードも（やり方があるかもしれないが）できない。時間管理が手の内にある安心感はあるが、マニアックなやり方。例えばプレイヤーが時間を巻き戻すゲームとかならいいかも。

---

**アニメーションのadvanceとspeed**  
AnimationPlayerのadvane()は、speed_scaleとcustom_speedの影響を受ける。どちらかがゼロだと当然進まない。

---

**RefCountedには_processも_readyも無い！**  
当たり前だが、RefCounted派生の自作クラスは、Node派生ではないので、_process()関数は呼ばれない。わかってても、やらかす。GDScriptはoverride宣言が不要なゆえにミスに気づかない。  
同様に、_init()の間違いで定義しちゃった_ready()も呼ばれない。

---

**BiDiとは**  
LabelノードのプロパティにBiDiという項目がある。  
BiDi = Bi-Direction Text、バイダイ。基本的に右横書きだが、アラビア数字などは左書きで書く、双方向の文字体系のこと。アラビア文字などはこの文化。

---

**デフォルトフォント**  
デフォルト（この場合、ユーザーが何も一切設定していない状態の意味）のフォントはソースコード中の<thirdparty / fonts>フォルダ内の各woff2ファイルが組み込まれて用いられているようだ。woff2はWeb用のフォントファイル形式。ソースコードを読む限り、ゲーム本体（Export Template）に組み込まれるのは、OpenSans_SemiBold.woff2のみで、46KB。たぶん英字数字と記号だけだろう。他の30個程度のwoff2ファイル、合計2.5MB程度はエディタに組み込まれるようだ。

Windows10と11では実行時の絵文字の見え方が明らかに違うので、OpenSans_SemiBoldに無い文字はOSのシステムフォントから取得しているっぽい。エディタもビルド成果物もサイズが小さいのに、仮名や漢字が問題無く表示されるのは、こういうことだろう。

ちなみに、プロジェクト設定にはCustom Fontという項目があり、これがLabelSettings.font未設定時のデフォルトのフォントになる。上記OpenSansは、いわばデフォルトのデフォルトである。  
LabelSettings.font -> Custom Font -> OpenSans  
このような優先順で適用される。

---

**Labelノードの「バグらせ」方**  
Labelノード。  
・new()後にサイズを設定していない初期状態  
・autowrap_modeをARBITRARYに設定  
・textをセットする  
この条件をすべて満たすと、その後サイズを設定しても、sizeプロパティの動作がおかしくなる。セットしたサイズよりも大きな値が返る。  
サイズセット→ update_minimum_size() → 再度サイズセット、という手順を踏むと正常に戻るが、そもそもこんな状態になること自体避けたほうがいい。

そもそも、幅がゼロの状態なのに「このテキストを適切に折り返せ」という無茶振りをするのが想定外なのだろう。Control系のノードにおいては、まずLayoutを設定してから、LabelやTextureRectの表示内容をセットするという運用にしたい。

---

**コールバック関数の引数の数**  
signalとconnectとbindの関係。  
コールバック関数の引数 = signalの引数 + bind引数

connect時にコールバック関数をbindすると、引数の後ろ側を呼ばれる側が決めることになる。決してデフォルト値とかではないので、呼ぶ側が上書きすることはできない。不足があっても、余剰があってもエラーとなる。型の不一致も当然エラーになる。

何個の引数をbindしたとか、コールバック関数の引数が何個あるとか、呼ぶ側は知らなくていい。自分のsignalの引数をemit_signal時に過不足無く指定するだけである。

逆に、呼ばれる側は、コールバック関数の引数の前方部分を、signalの引数と一致させなければならない。そして、自分のための付加情報を引数の後方に足す。それをbind時に指定する。

```
# 呼ばれる側 ----------------

func _on_sub_finished(
  arg0: int, arg1: String, arg2: Vector2,
  arg3: float, arg4: bool, arg5: Vector3
  ) -> void:

# arg3, 4, 5をbindする
signal_emit_node.finished.connect(
  _on_sub_finished.bind(123.0, false, Vector3(4.0, 5.0, 6.0))
  )

# 呼ぶ側 ----------------

signal finished(arg0: int, arg1: String, arg2: Vector3)

# arg0, 1, 2を指定してemitする
emit_signal("finished", 12, "34", Vector2(5.0, 6.0))
```

---

**.godotフォルダは消していい**  
GDScriptエディタのオートコンプリートというか、サジェストの機能が壊れた。一度実行したり、プロジェクトを開きなおしたりすると動くようになることもあるが、それでも直らない事態に陥った。"godot autocomplete not work"あたりでググっても、「型を指定しろ」的な初歩的な問答のログしか得られない。  
プロジェクトの.godotフォルダを一度消したら直った。

原因がなんにせよ、対処法はわかった。.godotフォルダは気軽に消していい。

---

**循環依存はオートコンプリートを邪魔する？**  
GDScriptエディタのオートコンプリートやUnsafe Lineの機能の動作が不安定なのは、クラスの循環依存・循環定義が原因な気がする。クラスAがクラスBを使っているのに、Bが間接的にでもAを使ってるような定義だ。newしていなくても、クラス名が登場するだけで依存性が発生する。  
確証はない。しかし、循環依存を無くしてからは、メソッドの候補は全部出るし、Ctrl+クリックで定義に飛べるし、書いたばかりの行のUnsafe判定は数秒待つと解除されるようになった。

Godotは3.xから4.xになるにあたって、クラスのCyclic Dependencyを許すようになったはずである。実際、ゲーム実行中の動作は問題無い。だが、エディタによるコード解析の面ではまだ発展途上なのではないか？

ストレスなくコードを書きたければ、依存性を解いていくしかない。そのためにはクラスを、抽象度や応用度、分野で分類して、関係を一方通行にするしかない。双方向の依存が要る場合は、適切にインターフェースクラスを挟んでいくことになる。

---

**ワーニング設定の厳格化**  
プロジェクト設定において、デフォルトでIgnoreになっている4つの警告について、Warnに設定して使い勝手を見てみた。必要なワーニングはできるだけ出してほしいので。

- Unsafe Property Access  
    $を使って、シーン中のノードのプロパティに直接アクセスしてると、このワーニングが出る。一度キャストして変数で受けてやれば出なくなる。回避可能である。  
    var shaft_y: Node3D = $ShaftY as Node3D  
    shaft_y.rotation = Vector3()
    
- Unsafe Method Access  
    Autoloadクラス内で、自分のクラス名（Worldなど）ではなく、自分のノード名（g_Worldなど）経由でstatic関数を呼ぶと、このワーニングが出る。すなおにクラス名経由で関数を呼べばいい。回避可能。  
    そもそも、存在しない関数を呼ぶと出るワーニングなのだが、それは適切にキャストしていないってことなので、本来出てほしいワーニング。  
    でも、キャストが必要ということはだいたい、サブクラスへのダウンキャストか、DictionaryのValueのキャストなので、今度はUnsafe Castのワーニングが出ることになる。
    
- Unsafe Cast  
    ダウンキャストをする限り必ず出る、ダウンキャストは組み込みクラスの関数や（Node. dupulicateなど）、Dictionaryを使う限り、必ず実施する。なので回避不可能なワーニングである。Dictionaryを型安全に記述するすべが無い。多分無いので、どうしようもない。  
    これをWarn設定にしていると、何もできない。Ignore一択である。
    
- Unsafe Call Argument  
    引数が型安全でない時にワーニングが出るらしいが、自分のコードでは該当箇所が無かった。これは常にONでいいと思う。回避可能。
    

結論：Unsafe Cast以外はお行儀よく書けば出ないワーニング。

Unsafe CastだけはIgnoreにして、残り3つ、Unsafe Method Access / Unsafe Property Access / Unsafe Call ArgumentはWarnにするのが、いい妥協点ではないだろうか。

Unsafe MethodとUnsafe Propertyに付いては、適切にキャストすることでUnsafe Castに「押し付ける」ことができるが、Unsafe CastはDictionaryで必ず出るのでどうしようもない。

ではこの運用で、安全でないキャスト(Unsafe Cast)を見逃してしまうだろうか？　それは大丈夫。  
GDScripのキャストはasを明示的に記述しない限り発生しない。人間が意識的にas書くんだ。Unsafe Castのワーニングが無くても十分清潔さはコントロール可能である。

---

**Godot製ゲームのライセンス表記**  
（間違ってるかもなので注意。）

Godotで作ったゲームには、エンジン本体とそれが使ってるライブラリ3つのライセンス表記が必ず必要。

【Godot本体】  
必要な表記は、Engine.get_license_text()で全文Stringで取得可能。

【FreeType】  
Godot公式ドキュメントによれば、URLを含む、"Portions of this software ～"で始まる短い一文だけで良さそうだ。条文を関数から取得可能なのだが、勝手な解釈はしないほうがよさそう。一文だけ。  
表記する年数を、Godotが用いているFreeTypeのバージョンに合わせる必要があり、それはGodotエディタのヘルプ＞サードパーティライセンスを参照せよ、とのこと。

【ENet】【mbed TLS】  
ライセンス条文はEngine.get_license_info()で取得できるが、その頭に付ける「Copyright 誰々」みたいなのは、手書きで用意する必要がある。公式ドキュメントを参考にしよう。条文はget_license_info()が返すDictionaryから、それぞれ、"Expat"と"Apache-2.0"をキーに取得可能。

---

**2D on 3Dレンダリングのセットアップ**  
3D空間上の2D画面（オフスクリーンレンダリングした2D画面）を、スクリプトで動的にセットアップする方法。  
吹き出しとか、ソウルライクの雑魚敵の体力バーとか、頭上のバフアイコンとか、ゲーセンの筐体に映る2Dゲームとか、宇宙船の操縦室の空間に浮かぶコンソール画面とか。

1. SubViewportを作成する。
2. SubViewportを適当なノード下に配置する。
3. SubViewportの解像度を設定する。
4. SubViewport下に画像や文字などの2Dノードを配置する。
5. SubViewportから、ViewportTextureを取得する。
6. マテリアルのカラーに、取得したViewportTextureに設定する。

2.～4.は6.の後でもいいし、前後してもいい。重要なのは、5.だ。ViewportTextureはランタイムでnewしてはいけない。

---

**ViewportTextureのnewはNG**  
上記の補足。

ViewportTextureはViewportへの参照を持っていないといけない。"Viewport Texture must be set to use it."のエラーメッセージは、この参照が無い時に出る。 ViewportTextureをスクリプトで直接newして作った場合、この参照をゲーム実行中に解決する手段がおそらく無い。ソースコードを荒く検索する限り見当たらない。  
（viewport_pathプロパティでViewportのパスを設定することができるが、そのパスが使われるのは、setup_local_to_scene関数内だけである。どう見たってエディタから呼ばれる関数だ。同プロパティは、シーン埋め込みのリソースを、エディタ上のスクリプト実行で作るためのものなのだろう。）

Viewportは作られた時点で、内部にデフォルトのViewportTextureを作って保持する。このViewportTextureは、作成者であるViewportが参照としてセットされているので、使用可能である。

なので、ViewportTextureを使いたければ、SubViewportから、get_textureで取得すればいい。むしろそれしかなさそう。

---

**カメラの座標変換は自作するしかない**  
カメラによる座標変換。グローバル座標からスクリーン座標への変換はCamera3D .unproject_position()が用意されているが、その中間の結果を得るにはユーザ側で処理が必要。カメラ座標空間だとどこかな？と知りたければ、自分のスクリプトで計算するしかない。

```
# カメラ座標空間
var trans_cam_inv: Transform3D = my_cam.get_camera_transform().inverse()
var pos_cam: Vector3 = trans_cam_inv * node.global_position

# 射影空間
var aspect: float = my_width / my_height
var proj_cam: Projection = Projection.create_perspective(
    my_cam.fov, aspect, my_cam.near, my_cam.far, false
    )
var pos_proj: Vector4 = proj_cam * Vector4(pos_cam.x, pos_cam.y, pos_cam.z, 1.0)
var x: flat = pos_proj.x / pos_proj.w
var y: flat = pos_proj.y / pos_proj.w
var z: flat = pos_proj.z / pos_proj.w

# スクリーン座標空間
var pos_screen: Vector2 = my_cam.unproject_position(node.global_position)
```

エンジンはシェーダバイナリに渡す変換行列をどこかで作ってるはずだが、それはC++のカメラやViewportクラス内ではやっていない。これをGD側から取得する関数はどうやら無さそう。  
（メモ：ソースコード RendererSceneCull::render_camera()が該当する処理の起点のはず。）

---

**Blender exeへのパスが消えた**  
エディタが急に、blendファイルを自動でインポートしなくなった。同時に、エディタのファイルシステムタブ（res://下のファイル一覧のある画面）に、blendファイルが表示されなくなった。  
エディター設定を開くと、「Blender 3 Path」がなぜか空欄になっていた。再設定しなおすと、正常にblendファイルを認識するようになった。何がきっかけでエディタ設定が壊れたのかは不明。

---

**Arrayでも+=は使える**  
Array[float]に対して、加算代入演算子（+=）は使用可能。  
array[idx] += 0.1　←これはOK。ちゃんと書き変わる。  
本来Arrayはなんでも入るコンテナだからこういうのはダメなのかな、と勝手に思ってた。なんとなく。

---

**mipmap設定方法**  
CanvasItem（Node2D派生とControl派生）で表示するテクスチャは、デフォルトではmipmap表示されない。mipmapを適用する方法。

1. テクスチャのインポート設定  
　「ミップマップ＞Generate」にチェックを入れる

2. CanvasItem.texture_filterに値をセット  
　- TEXTURE_FILTER_NEAREST_WITH_MIPMAPS  
　- TEXTURE_FILTER_LINEAR_WITH_MIPMAPS  
　- TEXTURE_FILTER_PARENT_NODE（GUI上では"Inherit"）※

※TEXTURE_FILTER_PARENT_NODEに設定した場合、  
　フォールバック先としてプロジェクト設定が適用される。  
　「プロジェクト設定＞レンダリング＞キャンバスのテクスチャ  
　＞デフォルトのテクスチャフィルタ」を次のどちらかに設定にする。  
　- Linear Mipmap  
　- Nearest Mipmap

---

**posmodの使い方**  
添え字に使うインデックスをループさせるときの簡潔な書き方。  
idx = posmod(idx + 1, COUNT)  
または  
idx = posmod(idx + 1, INDEX_MAX - 1)  
ゲームによく使う処理が組み込み関数に入ってるのはゲーム用言語のいいところ。

---

**_processは描画の前に呼ばれるか？**  
作成したノードの最初の_process関数は描画より先に呼ばれるとは限らない。条件は不明だが、描画後に初回の_processが呼ばれることがある。この場合おそらく、作成したフレームでは呼ばれておらず、その次のフレームまで呼び出しが持ち越されている。  
こういった懸念があるので、描画に関わる初期化処理は_initか_readyでやったほうがいい。ただし、initで初期化するときは注意が必要である。例えば自分のスクリプトでビルボード処理をしている場合、_initでglobal_positionを設定することはできない。まだ親が無いので。

---

**Subviewportの解像度は特別に高く取ろう**  
UIは「想定解像度」的なものを決めておいて、そのサイズでデザインして、実際の画面解像度に合わせてスケーリングする、という手法を採用している。Godotのドキュメントも推奨しているやり方だ。その場合、想定解像度を小さめの解像度、1280x720など、にすることもあるだろう。その方が16の倍数などで綺麗にレイアウトしやすいから。この解像度感で自分なりの規格を決めるのだ。アイコンは32pxなど。

しかしその小さい解像度でデザインしたUIを、そのままSubviewportに配置して、3D空間上のUIとして使ってはならない。落とし穴だ。Windowと違ってSubviewportの解像度は一定なので、想定解像度が実際の解像度になってしまう。これは低すぎる。さらに、3D空間に配置するということは、UIが斜めになったり拡縮したりするので、相応に高めの解像度が求められる。

なので、Subviewportの解像度とUIのControl.scaleを定数倍してあげるか、もしくは3D空間上のUIはHUD的なUIとは別のサイズ規格でデザインする、といった対応が必要になる。

長々と書いたが何が言いたいかというと、オフリーンレンダリングするときの解像度はちゃんと高めにしようね、だ。

---

**find_childの制限**  
Node.find_child()は、引数のパスが絶対か相対かに関わらず、シーンツリー上に無ければ機能しない。_initの中や、newしたばかりのノードには使えない。代わりにNode.get_node()を使うべし。  
node.find_child("./Sail") as Node3D # NG  
node.get_node("./Sail") as Node3D # OK

---

**Wheelと名付けたばっかりに**  
Blender上で名前の末尾に"_Wheel"を付けたオブジェクトは、インポート時にVehicleWheel3Dノードになってしまう。Import Hint機能によるものだ。水車小屋を作って水車部分に"Watermill_Wheel"と名付けたら、このオブジェクトが名前で取得できずバグった。

ドキュメントによると実際のキーワードは”_Wheel”ではなく"-wheel"だ。アンダースコアとハイフンの違いがあるし、大文字小文字の違いもあるし、いろいろと違うはずなのだが。よろしく解釈されしまうのだろう。  
結局、"Watermill_WheelObj"と名付けたら回避できた。

---

**レンダーターゲットのアルファチャンネル**  
SubViewportにUIなどをオフスクリーンレンダリングしたい場合は、SubViewportのtransparent_bgをtrueにする。こうすると、レンダーターゲットにアルファチャンネルが追加され、クリア時にアルファが0.0になる。falseの場合、たとえシェーダーでアルファに0.0を書き込もうとしてもアルファチャンネルに反映されない。  
ちなみに、このレンダーターゲットをテクスチャとして使う場合には、使用側のマテリアルのtransparencyを、ALPHAやSCISSORに設定するのを忘れないようにしないといけない。

---

**レンダーターゲットを好きな色でクリアする**  
レンダーターゲットのクリアカラー  
【Camera3Dノードがある場合】  
Environmentリソースのbackground_modeとbackground_colorが適用される。適用されるEnvironmentには優先順がある（詳細は省略）。フォールバック先として、プロジェクト設定の「デフォルトのクリア―色」が適用される。  
【Camera3Dノードが無い＆ViewportがWindowの場合】  
プロジェクト設定の「デフォルトのクリアー色」が適用される。  
【Camera3Dノードが無い＆ViewportがSubviewportの場合】  
RGB = オール0.0でクリアされる

上記のようになっている。このため、2Dノードだけを置いたSubviewportは、強制的に黒をバックに描画される。これの何が問題かというと、UIである。UIをSubviewportに配置して、3D空間上に配置するような場合だ。UIなので矩形以外の形をしていることが多い。Labelノードで文字を描画することもあるだろう。このときに、アルファ=0.0の境界がフィルタの関係で黒を拾ってしまい、強制的に暗くなる。これではデザインの自由度が下がってしまう。

回避策1  
描画対象が2Dしかないけど、あえて3Dのカメラを置く。  
ダミーのCamera3DノードをSubViewport下に置く。その上で、EnvironmentがViewportに適用されるようにセットアップする。例えばWorldEnvironmentノードを配置する、など。  
スマートな実装ではないし、パフォーマンス的にも悪いかもしれないが、簡単。

回避策2  
ビューポート全体を覆うようにTextureRectを配置する。この矩形は、シェーダーマテリアルで描画する。シェーダーは次の通り。  
shader_type canvas_item;  
render_mode blend_disabled; // アルファ含めてそのまま出力  
void fragment() { // 白でクリアしたい場合  
  COLOR.r = 1.0;  
  COLOR.g = 1.0;  
  COLOR.b = 1.0;  
  COLOR.a = 0.0;  
}

回避策3  
下記フォーラムによると、RenderingServer.set_default_clear_colorを使うといいらしい。が、上手くいかない。なんでだろう？

[**How to set the background color of a Viewport in 2D? - Godot Engine - Q&A**_Just trying to do that, but I find no method for this in the__ask.godotengine.org_](https://ask.godotengine.org/9092/how-to-set-the-background-color-of-a-viewport-in-2d)[](https://ask.godotengine.org/9092/how-to-set-the-background-color-of-a-viewport-in-2d)

2019年の投稿だが、SubViewportのクリア色に関してはこのころから問題視されていたようだ。

> it appears individual viewport clear colors is a relatively noticeable oversight in Godot

---

**_init内でも誰かの子になれる**  
ノードの_init関数内で、自分自身を他のオブジェクトの子にするのは問題ない。

```
func _int(new_parent: Node) ->void:
  new_parent.add_child(self) # OK!
```

「オブジェクト作成途中だからまだダメ」とかそういう制約ありそうだな、と思ったけど、大丈夫。

---

**AnimationLibraryとSkeleton3Dのインポート条件**  
blendファイルインポート時の、AnimationLibraryやSkeleton3Dが作られる条件

- ボーンアニメーションじゃなくても、オブジェクトのトランスフォームが移動するなどの、何らかのアクションがあればAnimationLibraryが作られる。
- アクションが存在しなくても、アーマチュアが存在すればSkeleton3Dノードが作られる。
- アーマチュアの子が存在せず、ウェイトを付けたメッシュさえも存在しなくても、アーマチュアが存在すればSkeleton3Dノードが作られる。

---

**ルートノードはグローバル関数から取得できない（※できる）  
**~~static関数からシーンツリーやルートノードを取る手段が無い。~~  
get_tree関数はNodeの非static関数であり、しかもおそらくツリーに追加されていない状態では使えない。  
グローバル関数的にアクセスできなくすることで、堅牢性を高める意図があるのだろう。

Godot 4.1で追加されたstatic変数を使って、AutoLoadクラスをC#風のシングルトン実装に改めようとしたのだが、上記の理由で、簡単ではないようだ。Nodeは自力で自分をツリーに配置できないから。

```
# static変数にインスタンスを保持すればAutoLoad要らない！？

class_name MySingleton
extends Node

static var _inst: MySingleton = null

static func _secure_inst() -> void:
  if _inst == null:
    _inst = MySingleton.new()
    var root: Node = something() # これができない
    root.add_child(_inst)

static func do_work() -> void:
  _secure_inst()
  _inst.work()
```

なので、なんらかの親ノードを渡す初期化関数を呼び出す必要がある。

```
# secure関数をpublic化して外部から呼び出す
# parent_nodeは別にルートノードでなくてもいい

static func secure_inst(parent_node: Node) -> void:
  if _inst == null
    _inst = MySingleton.new()
    parent_node.add_child(_inst)

static func do_work() -> void:
  _inst.work()
```

※  
2023/10/10  
コメントでご助言いただきました。ありがとうございます！  
Engine.get_main_loop()を使うことで、次のようにしてルートを取得することができるようです。  
記事書いといてよかった～

```
# SceneTree.rootを取得してその子になる
static func _secure_inst() -> void:
  if _inst == null:
    _inst = MySingleton.new()
    var scene_tree: SceneTree = Engine.get_main_loop() as SceneTree
    var root: Window = scene_tree.root
    root.add_child(_inst)

ただしこの場合、シーン初期配置ノードの_readyで上記関数が呼ばれないように気を付ける。
ノードの_readyが終わるまでは、scene_tree.rootにadd_childはできない。

# call_deferredを使うことで、_readyから呼ばれても大丈夫
    root.add_child.call_deferred(_inst)
```

---

**enum Keyを走査するには**  
例えばひとまず全キー（@GlobalScope.Key)の入力状態を知りたいときに、キーのリストを取得する手段が通常無い。クラス下の普通のenumと違って、グローバルenumであるKeyは  
for key in Key.values():  
のような書き方はなぜかできない。

enum Keyは、値の順に並べかえると次のようになっている。

```

# 無し
KEY_NONE = 0

# 主要な文字キー
KEY_SPACE = 32
～KEY_QUOTELEFT = 96

# {, |, }, ~ の4つ
KEY_BRACELEFT = 123
～KEY_ASCIITILDE = 126

# 円マーク
KEY_YEN = 165

# セクションマーク（§）
KEY_SECTION = 167

# 4194304=0x400000で、これでマスクされるキーは文字キーではなく表示不可
KEY_SPECIAL = 4194304

# 主要な非文字キー
KEY_ESCAPE = 4194305
～KEY_F35 = 4194366

# 00年代的な機能キー
KEY_MENU = 4194370
～KEY_LAUNCHF = 4194415

# MacやiPadの言語系キー（地球儀マークとか）
KEY_GLOBE = 4194416
～KEY_JIS_KANA = 4194419

# テンキー
KEY_KP_MULTIPLY = 4194433
～KEY_KP_9 = 4194447

KEY_UNKNOWN = 8388607
```

for文で走査するときはひとまずこの2つの組み合わせでループを回せば漏れはなさそう。  
range(KEY_SPACE, KEY_SECTION + 1) = 32～167  
range(KEY_ESCAPE, KEY_KP_9 + 1) = 4194305～4194447

---

**Inputで取得できないキー**  
・半角/全角  
・無変換  
・変換  
・カタカナ/ひらがな  
・ノートPCの「Fn」キー  
まだあるかもしれないけど、少なくともこの5つはInput. is_key_pressed関数では取得できないっぽい。  
ただし、「半角全角」に関しては、is_physical_key_pressed関数ならばKEY_QUOTELEFT(96)として取得可能。

---

**マルチパスレンダリングはnext_passを使う**  
複数のパスを持つシェーダーを実現したい場合、マテリアルのMaterial. next_passに2パス目の別のシェーダーのマテリアルをセットする。3パス目が欲しければ2パス目のマテリアルのnext_passに3パス目のシェーダーのマテリアルをセットする。数珠繋ぎ。  
マテリアルはStandardMaterial3DかShaderMaterialでさえあればよく、混合して繋げても問題ないようだ。

ちなみに、法線方向に膨らませる方式の輪郭表現は、2パス目にGrowを設定したStandardMaterial3Dをセットするだけで実現できる。「たいていの表現はStandardMaterial3Dだけで済ませてみせるぞ」というGodotの意志を感じる。

---

**SubViewportとレイヤーの関係**  
カメラは最も近い先祖のViewportに視点を提供する。逆を言うと、Viewportは、「直轄」のカメラを使って描画される。

ただ、描画される対象とその対象を照らすライトは、Viewportのドメインに縛られない。Unity同様、レイヤーで区別する必要がある。  
・Camera3D .cull_mask  
・VisualInstance3D .layers  
・Light3D .light_cull_mask

SubViewportで描画対象が決まるわけではないのだが、つい勘違いしそうになるのは、SubViewport自体がレイヤー＝世界を取りまとめる、入れ物のオブジェクトとしての使い方も期待されてる側面があるからだろう。  
実際、SubViewport下はすべてレイヤーを統一する、というのがよくある使い方になると思う。

---

**Environmentの適用優先順  
**Environmentの適用条件と、その優先順は、今のところ下記の理解。  
1. カメラにセットされたEnvironment  
2. ViewportにセットされたWorld3DのEnvironment  
3. （Viewport外の）WorldEnvironmentノードにセットされたEnvironment  
4. プロジェクト設定のDefault Environment

デバフとかアイテム（暗視ゴーグルとか）による一時的な変化はカメラ、そのマップにおける常態的なコンディションはWorldEnvironment、というのがいい感じの使い分けになると思う。

3でわざわざ「Viewport外」と注釈を付けたのは、SubViewportの中に入れるとSubViewportのEnvironmentが乗っ取られてしまうのだ。  
どういうこっちゃ？　次の項目参照。

---

**SubviewportのWorld3Dが上書きされる**  
エディタのGUI上の操作だけども、  
・WorldEnvironmentノードをSubviewportの子にすると、  
・子Subviewportに元々セットされていたWorld3DのEnvironmentが、  
・親WorldEnvironmentノードと同一のEnvironmentに置き換わる。  
（ややこしい）

これ自体が仕様なのかバグなのかわからないけど、仕様でも全然普通だと思う。

でも、そこからCtrl+ZでWorldEnvironmentの親子付けをアンドゥすると、SubviewportのEnvironmentが空になってしまう。元に戻らない。  
これはさすがに、仕様ではなくない・・・？

なんか、これに限らず、SubviewportのWorld3D周りの挙動、ちょっと怪しい感じがする。

---

**clampしたのに+=**  
ついついやっちゃうバグ  
誤: count += clampi(count + sub_count, COUNT_MIN, COUNT_MAX)  
正: count = clampi(count + sub_count, COUNT_MIN, COUNT_MAX)

---

**intからStringのフォーマット変換  
**"%d" % countのような、書式指定を使った文字列への変換。  
これ系はいつまで経っても慣れない。ドキュメント見ても毎回忘れる。

10進数(d)の場合はまだ割とわかりやすい。

```
          0     5    -5    12345   -12345
   %d:   <0>   <5>  <-5>  <12345> <-12345>
  %+d:  <+0>  <+5>  <-5> <+12345> <-12345>
  %3d: <  0> <  5> < -5>  <12345> <-12345>
 %+3d: < +0> < +5> < -5> <+12345> <-12345>
 %03d: <000> <005> <-05>  <12345> <-12345>
%+03d: <+00> <+05> <-05> <+12345> <-12345> 
 %-3d: <0  > <5  > <-5 >  <12345> <-12345> 
%-03d: <0  > <5  > <-5 >  <12345> <-12345>
```

- "d"の前の数字が、変換後の文字列の最小幅。  
    通常はスペースでパディングされる。  
    "%03d"のように、最初に"0"を付けるとゼロでパディングされる。  
    パディング前の幅は、符号があれば符号付きで評価される。
    
- "+"を付けると、ゼロや正であっても符号が付く。
    
- "-"を付けると、左詰めされる。スペースでパディングされる。  
    （ゼロでパディングはされない。123→"123000"じゃ、わけわからないもんね。）
    

---

**floatからStringのフォーマット変換  
**"%f" % ratioのような、書式指定を使った文字列への変換。小数の場合。

```
横に長～～～～～～～～～～～～～～～～～～～い
                      0.0              1.2             -1.2          0.12345         -0.12345          0.98765         -0.98765           1234.5           1234.5         0.000123        -0.000123    
      %f:       <0.000000>       <1.200000>      <-1.200000>       <0.123450>      <-0.123450>       <0.987650>      <-0.987650>    <1234.500000>   <-1234.500000>       <0.000123>      <-0.000123> 
     %+f:      <+0.000000>      <+1.200000>      <-1.200000>      <+0.123450>      <-0.123450>      <+0.987650>      <-0.987650>   <+1234.500000>   <-1234.500000>      <+0.000123>      <-0.000123> 
    %+3f:      <+0.000000>      <+1.200000>      <-1.200000>      <+0.123450>      <-0.123450>      <+0.987650>      <-0.987650>   <+1234.500000>   <-1234.500000>      <+0.000123>      <-0.000123> 
  %+0.3f:         <+0.000>         <+1.200>         <-1.200>         <+0.123>         <-0.123>         <+0.988>         <-0.988>      <+1234.500>      <-1234.500>         <+0.000>         <-0.000> 
  %+3.1f:           <+0.0>           <+1.2>           <-1.2>           <+0.1>           <-0.1>           <+1.0>           <-1.0>        <+1234.5>        <-1234.5>           <+0.0>           <-0.0> 
   %6.3f:         < 0.000>         < 1.200>         <-1.200>         < 0.123>         <-0.123>         < 0.988>         <-0.988>       <1234.500>      <-1234.500>         < 0.000>         <-0.000> 
  %+6.3f:         <+0.000>         <+1.200>         <-1.200>         <+0.123>         <-0.123>         <+0.988>         <-0.988>      <+1234.500>      <-1234.500>         <+0.000>         <-0.000> 
  %-6.3f:         <0.000 >         <1.200 >         <-1.200>         <0.123 >         <-0.123>         <0.988 >         <-0.988>       <1234.500>      <-1234.500>         <0.000 >         <-0.000> 
    %.3f:          <0.000>          <1.200>         <-1.200>          <0.123>         <-0.123>          <0.988>         <-0.988>       <1234.500>      <-1234.500>          <0.000>         <-0.000> 
    %6.f:         <     0>         <     1>         <    -1>         <     0>         <    -0>         <     1>         <    -1>         <  1235>         < -1235>         <     0>         <    -0> 
 %+12.3f:   <      +0.000>   <      +1.200>   <      -1.200>   <      +0.123>   <      -0.123>   <      +0.988>   <      -0.988>   <   +1234.500>   <   -1234.500>   <      +0.000>   <      -0.000> 
 %+12.8f:   < +0.00000000>   < +1.20000000>   < -1.20000000>   < +0.12345000>   < -0.12345000>   < +0.98765000>   < -0.98765000> <+1234.50000000> <-1234.50000000>   < +0.00012345>   < -0.00012345> 
%+012.8f:   <+00.00000000>   <+01.20000000>   <-01.20000000>   <+00.12345000>   <-00.12345000>   <+00.98765000>   <-00.98765000> <+1234.50000000> <-1234.50000000>   <+00.00012345>   <-00.00012345> 
```

- -0.0は0.0扱いされる。（もしくはGDScriptに-0.0は存在しない。）
    
- デフォルトでは小数点以下6桁が、値によらず必ず表示される。  
    （例: 12.0→"12.000000"）
    
- "+3f"のように、ピリオドを伴わずにfの前に数字を書いても何も効果が無い。
    
- "+"を付けると、ゼロや正の場合であっても符号が付く。  
    テキストの幅はこの符号を含めて評価される。
    
- 小数点の前の数字は、変換後の文字列の最小幅。  
    これより小さいならば、0かスペースでパディングされる。  
    値自体が大きかったり（120000000.1とか）、後述の主数点以下の表示桁数が大きい（"%2.8f"とか）場合は、より大きな幅が必要になり、この最小幅を超えて変換される。  
    パディング前の幅は、符号があれば符号付きで評価される。
    
- 小数点の後ろの数字は、表示させる小数点以下の桁数＝文字数。  
    キリのいい数字でも0でパディングされる。（例: 1.2→"1.2000"）  
    最小桁より1つ小さい桁の数字は四捨五入され、最小桁の数字を左右する。正負によらず文字通りの四捨五入。（例: -5.5555→"-5.56"）  
    この設定は最優先で堅持され、値や条件にかかわらずこれが反故にされることは多分ない。
    
- "-"を付けると、左詰めされる。スペースでパディングされる。
    
- "%.3f"のように、小数点の前に数字を付けない書き方も許容される。  
    この場合、特にパディングされない。
    
- "%8.f"のように、小数点の後ろに数字を付けない書き方も許容される。  
    この場合、デフォルトの6桁精度になる・・・とかではなく、整数として表示される。小数点も無い。小数点が無いので、その分パディングされるスペースの数は増える。（例: -1234.567 % "8.f" → "□□□-1235"）  
    一応四捨五入される。
    

例:  
0.0～1024.0の範囲で振れる値を、小数点以下2桁まで、文字数固定で表示したい。  
"%7.2f" （7 = 4桁 + 2桁 + 小数点）

例:  
0.0～9999.999…の範囲で振れる値を、小数点以下2桁まで、文字数固定で表示したい。  
"%7.2f" （7 = 4桁 + 2桁 + 小数点）  
※ただしこれは、8文字になる可能性がある。四捨五入されるので。

例:  
-100.0～+100.0の範囲で振れる値を、小数点以下1桁まで、文字数固定で表示したい。ただし、正の場合は符号は要らない。  
"%6.1f" （6 = 3桁 + 1桁 + 小数点 + マイナス符号）  
符号が要る場合でも"6"は変わらない。  
"%+6.1f" （6 = 3桁 + 1桁 + 小数点 + 正or負符号）

---

**関数内でnewしたものを引数経由で返すには**  
C#のout修飾子の付いた引数のように、最初はnullなんだけど関数内でnewして作ってくれる、そんな引数は作れそうで作れない。

```
var environment: Environment = null
_create_environment(environment)
# この時点でenvironmentは依然null

func _create_sky_environment(dst_environment: Environment) -> void:
	dst_environment = Environment.new()
	dst_environment.background_mode = Environment.BG_SKY
```

思い返せばこの場合のdst_environmentは、元の参照のコピーだからだ。呼んだ側の参照とは別物だ。だから新しくnewしたオブジェクトをコピー品に差させても、呼んだ側の参照（environment）には影響しない。

なので、関数に何か特定のものを作ってもらいたくて、それを戻り値ではなく、引数でやり取りしたければ、C#のref引数のように呼び出し側でnewするしかない。

```
var environment: Environment = Environment.new()
_create_environment(environment)
# OK

func _create_sky_environment(environment_ref: Environment) -> void:
	environment_ref.background_mode = Environment.BG_SKY
```

とはいえ、どうしても呼び出し側でnewしたくないときもある。この場合は「参照の参照」を渡すアイデアがある。Arrayとか。

```
func create_boss(dst_enemy: Array[Enemy]) -> void:
	# Enemy.newの引数が重要なので、外側に任せられない
	var enemy: Enemy = Enemy.new(_get_param(), _get_main_data(), _get_option())
	dst_enemy.append(enemy)
```

---

**コンテナも循環参照注意**  
RefCounted派生クラス同様、コンテナであるArrayとDictionaryも循環参照に気を付けないといけない。循環参照があると自動解放されないようだ。きっとこれも参照カウンタで管理されている。

```
# この関数を呼ぶと、ローカルなはずのarray_aとarray_bが解放されない。
# デバッグ実行中に「モニター＞Object」を見るとリークが確認できる。
func _leak_function() -> void:
	var array_a: Array = []
	var array_b: Array = []
	array_a.append(array_b)
	array_b.append(array_a) # ここをコメントアウトすると結果が変わる

	# オブジェクトを大量に入れておくとわかりやすい
	var objs = []
	for i in range(0, 16 * 1024):
		objs.append(Texture2D.new())
	array_a.append(objs)

# Dictionaryでも同様
	var dict_a: Dictionary = {}
	var dict_b: Dictionary = {}
	dict_a[0] = dict_b
	dict_b[0] = dict_a # ここをコメントアウトすると結果が変わる
```

---

**マルチキャレットを同じ行に持ってくる**  
コードエディタのカーソルを複数にする機能。  
おもしろテクニックを見つけた。

```
Shift + Ctrl + カーソルキー上下でカーソルを分身させて、
こんな感じで3文字目に持ってきて、
12|34
12|34
12|34
12|34
ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

```
カーソルキー右でカーソルを右にずっと動かすと、
同一行内の等間隔のカーソルになる
1234
1234
1234
1234
ABC|DEFGH|IJKLM|NOPQR|STUVWXYZ
```

それだけ。

---

**複数カーソルモードの終わらせ方**  
マルチキャレットモードの終了のさせ方。マウスでクリックするのが確実なんだけど、キーボードだけで済ませるやり方。  
これまで「なんとなく」で手グセの勘でカチャカチャ操作して、終了させてきた。  
改めてじっくりいじってみると、ちょっと法則とコツが見えてきた気がする。

Ctrl + Shift + 右（または左）を繰り返し押しまくる。そうするとカーソルが1つに統合され、モードが終了する。そうなる理由も、やってるとなんとなくわかる。

---

**DLLは依存関係が解決しないと「見つからない」**  
GDExtensionのために作ったdllをプロジェクトに追加すると、このようなエラーが出た。

```
Can't open dynamic library:
H:/MyGodotProj/bin/libgdexample.windows.template_debug.x86_64.dll.
Error: Error 126: 指定されたモジュールが見つかりません。
```

copy

そこにファイル在るはずなのになぜ！？  
答えはDLL間の依存関係。（そういうのあったね・・・）  
Godotが内部で呼んでるWin32 API LoadLibraryExWがエラーを返すのは、単に引数として渡したdllがファイルとして存在しないときだけではない。そのファイルの依存関係がすべて解決できないときもエラーを返す。  
このマイDLLは、MinGWでコンパイルしたのでMinGW製の標準ライブラリのdllを使っている。<mingw64/bin>下にあるlibstdc++-6.dll等7つのdllを、<C:\Windows\System32>にコピペしたら、エラーが出なくなった。

できればSystem32ではなく、ゲーム本体のフォルダに含める方法も試したいけど、やり方がわからなかった。  
- 呼び出し元のdllと同じフォルダ（この場合bin）に置く  
- Godotのプロジェクトフォルダに置く  
これはどちらもNGだった。

---

**DLLの依存関係の調べ方**  
今はこのツールがいいらしい。GitHubからダウンロードできる。  
lucasg/Dependencies  
12MB程度でインストール不要。dllをドラッグ＆ドロップするだけで簡単。

---

**blendからメッシュを複製させずにインポートする条件**  
blendファイル内でオブジェクトがメッシュを共有している場合、Godotインポート後のMeshInstance3Dもまた同一のメッシュがセットされている。メッシュの複製はされない。ただしこれは、オブジェクトに割り当たっている（場合によっては複数の）マテリアルが同一の場合。  
マテリアル（場合によってはその組み合わせ）が違うオブジェクトがあれば、インポート後はその数だけメッシュが複製される。なぜなら、Godotではメッシュにマテリアルが紐づくため、セットされるマテリアルを変えないといけないから。

---

**static変数はスタックトレースで見れない？**  
Godotエディタでstatic関数内でブレーク状態の時に、「デバッガ」＞「スタックトレース」を開いても、static変数の値は確認できない。AutoLoadクラスをstatic変数を使ったシングルトンに置き換えてきたのだけど、これはこの方法のデメリットのように思う。  
AutoLoadの内容なら表示されるんだけどね。

---

**ArrayはArray[T]にキャストできない**  
中身の型指定のないArrayを、Array[T]にキャストして代入しようとすると、実行時にエラーとなる。エラーとなる箇所は、エディタには型安全と判定されて、行番号がグレーアウトしない。だけどエラーになる。  
エラーメッセージ  
Trying to assign an array of type "Array" to a variable of type "Array[T]"  
asはキャストに失敗して、単なるArrayを返しているように思う。

Array.assign()関数を使えば問題ない。  
おそらく、単なるArrayと型指定のあるArrayはオブジェクトとして別物で、使う側（変数）の対応を変えるだけではダメで、使われる側（オブジェクト）を作り変える必要があるのだろう。

```
# これは実行時にエラーが出る（エディタには型安全判定されている）
var array: Array = [0, 1, 2]
var array_int: Array[int] = array as Array[int]

# これなら問題ない
# 3行目も型安全OKとされる
var array: Array = [0, 1, 2]
var array_int: Array[int] = []
array_int.assign(array)
```

このため、enum.keys()はStringの入った素のArrayを返すのだが、Array[String]にイコールで代入しようとするとエラーとなる。

---

**enumをStringにする方法  
**デバッグやセーブデータのために、enumを文字列化したい場合、次のように、keys()を使えばいい。

```
text += Combat.Phase.keys()[phase]
```

ただし上記のような書き方だと、型安全でないとして、行番号がグレーアウトしてしまう。グレーアウトさせたくない場合は下記の通りにするといい。

```
# asで一度キャストしてしまえばいい
# ただ、実際問題これはコードエディタを騙しているだけで、
# 実行時にはキャストに失敗しているのではないか
text += (Combat.Phase.keys() as Array[String])[phase]

# 冗長に見えても、こちらのほうが誠実かもしれない
var array_phase_str: Array[String] = []
array_phase_str.assign(Combat.Phase.keys())
text += array_phase_str[phase]

# 頻繁に使うenumなら、関数化してしまうか
text += Combat.PhaseToStr(phase)
```

---

**複数行のラムダ式の書き方**  
公式ドキュメント等ではラムダ式は1文＝1行だけしか書かれてないことが多い。複数文、複数行書くには次のようにする。

```
# ローカル変数にするとき
var print_sign: Callable = func(val: int) -> void:
	if val >= 0:
		print("pos")
	else:
		print("neg")

# 関数のCallable引数に直接入れるとき
# 最後の閉じ括弧")"はこの段のインデントでないと受け付けない
array.sort_custom(
	func(a: int, b: int) -> bool:
		if a > b:
			return true
		else:
			return false
)
```

---

**マウスカーソルの位置の取得**  
マウスカーソルの位置はInputクラスではなく、Viewportから取得する。左上が0, 0の模様。

```
var main_loop: MainLoop = Engine.get_main_loop()
var window: Window = main_loop.get_root() # <- これはなぜか型安全判定されない
var pos_x: float = window.get_mouse_position().x
var pos_y: float = window.get_mouse_position().y
```

---

**デバッグのモニターの取得**  
デバッグ実行中にエディタの「モニター」タブに表示される項目は、スクリプトではPerformanceクラスから取得可能。Performance.get_monitor(Performance.OBJECT_COUNT)

---

**マウスカーソルで3Dオブジェクトを指す**  
マウスカーソルで3Dオブジェクトに当たり判定を取りたいときは、次のようにノードを組み立てる。物理とか使わない、一番簡易で（多分）処理が軽いやり方。

```
親Node
	Area3D
		CollisionShape3D
```

CollisionShape3Dには、Shapde3D派生リソースを割り当てるが、これにはBoxShape3DやSpahreShape3Dを使えばいい。

mose_enteredとmouse_exitedシグナルにコールバック関数をつなぐ。判定は、前後関係が考慮されるようで、一番手前にコリジョンがあるArea3Dだけがシグナルを発する。

```
area_3d.mouse_entered.connect(_on_area_3d_mouse_entered)
area_3d.mouse_exited.connect(_on_area_3d_mouse_exited)

func _on_area_3d_mouse_entered() -> void:
	処理

func _on_area_3d_mouse_exited() -> void:
	処理
```

---

**ローカル変数とメンバ関数の名前衝突**  
メンバ変数名とローカル変数名が一致していると、Ctrl + クリックで関数の定義に飛べなくなる。そもそも名前が衝突すること自体がアウトな気もするので避けた方がよさそう。

```
# でもこういうの書いちゃう
var is_idx_valid: bool = is_idx_valid(idx_x, idx_z)
```

---

**列挙体を返す関数をfor文のinで受け取ってしまう  
**for文のinの後は、配列ではなく単体の整数も受け付けるようだ。for i in Nの場合、N-1回ループすることになる。列挙体を返す関数をうっかりfor文に使ってしまった場合、文法のエラーにならない。バグの原因に気づかない。

```
# rand_dir4()は東西南北enumをランダムに返す関数

# 間違えてこう書いても文法エラーとはならず通ってしまう
# この場合、0回～3回ランダムに処理が走る
for dir4 in rand_dir4():
    do_something(dir4)

# 本当はこう書きたかった
var dir4: Dir.Dir4 = rand_dir4()
do_something(dir4)
```

---

**Playbackの意味**  
関数名とか公式ドキュメントの解説で出てくる"playback"という単語。単に「再生」という意味らしい。意味の広い"play"を、もうちょっと具体的に言い換えた表現で、録画・録音したものを再現するからbackが付いてるっぽい。  
日本人的には特定の歌の歌詞を連想して、どうしても「巻き戻す機能」という意味に捉えてしまう。

---

**TODOコメント強調表示**  
Godot 4.2ではGDScriptのコメントに"TODO"と書くとそこだけ色が変わる。これは、エディター設定の「テキストエディター」＞「テーマ」で設定されたキーワードに反応している。  
他には、"FIXME"や”CRITICAL”や"NOTE"などが、デフォルトのキーワードとして設定されている。

---

**選択範囲を評価**  
GDScriptのエディタで範囲選択をして、右クリックすると、コンテクストメニューの中に「選択範囲を評価」という項目がある。これは、この選択範囲のコードをその場で実行する機能のようだ。  
"print("abc")"を「評価」すると、ログに"abc"が表示され、選択範囲が"void"に置き換わる。"3+3"を「評価」すると選択範囲が"6"に置き換わる。  
何に使えるのかなー。

---

**Rect2iの活用**  
グリッドやセルで区切られたゲームを作る場合、インデックスの範囲を表すメンバ変数や引数として、4つintを並べることがある。  
idx_min_x: int  
idx_min_z: int  
idx_max_x: int  
idx_max_z: int  
こういう状況では、これらの代わりにRect2iを使うことを検討してもいいかもしれない。

```
idx_range: Rect2iで置き換えたとき
idx_min_x == idx_range.position.x
idx_min_z == idx_range.position.y
idx_max_x == idx_range.end.x - 1
idx_max_z == idx_range.end.y - 1
idx_range.size.x == idx_max_x - idx_min_x + 1
idx_range.size.y == idx_max_z - idx_min_z + 1
```

int 2つを他の組み込み変数（Vector2i）に置き換えるのは、ちょっと迷いがあるところだけど、4つなら十分メリット大きい。気安く使える。

---

**最後の引き数の後に点があってもいい**  
関数の定義や呼び出しで、引数が多いときは複数行に分けたりすることがある。このとき、最後の引数の後にカンマ(,)が入っていても許される。enumの定義の書き方と同様。  
優しい仕様。

```
# 定義 OK
func do_something(
	arg0,
	arg1,
	arg2,
	) -> void:

# 呼び出し OK
my_obj.do_something(
	get_arg0(),
	get_arg1(),
	get_arg2(),
	)

# 定義 一応一行のときでもOK
func do_something(arg0, arg1, arg2, ) -> void:

# 呼び出し 一応一行のときでもOK
my_obj.do_something(get_arg0(), get_arg1(), get_arg2(), )
```

---

**影を落とすのはノード単位**  
影を受ける・受けない（disable_receive_shadows）は、マテリアルの設定値。しかし、影を落とすかどうかは、GeometryInstance3Dの設定値（cast_shadow）。ノード単位で設定する。この差がちょっと厄介な感じがする。

ほとんどのケースにおいて、影を落とすかどうかは、マテリアルに依存する。不透明なものは落とす。半透明なものやシンボル的なオブジェは落とさない。さらに、不透明なものでも、草や髪の毛のようなアルファ抜き（カットオフ/カットアウト）の場合は、面の裏表によらず影を落とす設定にする。  
でもGodotは1ノード1メッシュではあるが、1メッシュ1マテリアルではない。不透明・半透明が混在するメッシュの場合、いい感じに影を設定できない。

マテリアルとcast_shadowを紐づけるか、それとも、cast_shadowはマテリアルとは別に意識的に管理するか、運用方法は決めがたい。でもひとつだけ確実なことは、影を落としたいポリゴンと落としたくないポリゴンは、Blenderの時点で別メッシュにする必要がある。特に、影が出るとおかしいくらいに大きなもの。ガラス窓とか。

もし、マテリアルとcast_shadowを紐づけて運用するのであれば、surfaceインデックス=0のマテリルに応じてcast_shadowの値を決めるようにするのが、いい塩梅かもしれない。普通は1つ目のマテリアルが、そのオブジェクトの代表素材なので。

---

**配列はshuffle()でシャッフル可能**  
配列は、Array.shuffle()関数で文字通りシャッフルできる。  
複数のオブジェクトの中から、条件を満たすものをランダムに選ぶときは、これを使うといいかも入れない。

```
# idxをシャッフルする方法
var idxs: Array[int] = range(0, objs.size()) as Array[int]
idxs.shuffle()
for idx in idxs:
	if objs[idx].is_valid() == true:
		処理
		break

# 有効なidxを事前に収集する方法
# 候補が多くて、当たりが少ないときに有効
var idxs: Array[int] = []
for idx in range(0, objs.size()):
	if objs[idx].is_valid() == true:
		idxs.append(idx)
if idxs.size() > 0:
	var idx_idx: int = randi_range(0, idxs.size() - 1)
	var obj: MyObject = objs[idxs[idx_idx]]
	処理

# 当たるまでひたすら繰り返す方法
for i in range(0, 999):
	var idx: int = randi_range(0, objs.size() - 1)
	if objs[idx].is_valid() == true:
		処理
		break
```

---

**同じrangeでも意味が違う**  
range()が返す配列は、第2引数を含まない。  
randi_range()が返すint値は、第2引数を含む。  
何度書いても「どっちだったっけ・・・」となるし、たびたびヘルプを見ることになる。交互に書いていると混乱する。  
ちなみに、randf_range()の返すfloat値は、第2引数を含む。含まなければrandf_range(-1.0, +1.0)みたいなのが書きにくくなるから、当然の仕様に思う。これと動作を統一したとすれば、randi_range()は「含む」方だと思い出すことができる。

---

**UIデバッグ用のアタリ枠**  
Controlの配置状況、特にLabelノードのサイズ感をデバッグするために、カラフルな枠テクスチャをこれまで使っていた。デバッグモードの特定の設定のときには、このテクスチャをLabelの子として親にフルストレッチするように設定して、Labelのレイアウトを可視化する。前作をUnityで作っていた時にもこういう仕組みを用意していたし、制作終盤に何度も助けられた。  
でも実はGodotには、こういう用途のためのノードが組み込みクラスとして存在している。ReferenceRectノードだ。これを使うとスマートかもしれない。

```
if デバッグ枠を表示するなら:
	# ReferenceRectを使う場合
	var reference_rect: ReferenceRect = ReferenceRect.new()
	reference_rect.border_width = 2.0
	reference_rect.editor_only = false # 実行中に見れないと困る
	reference_rect.set_anchors_preset(Control.PRESET_FULL_RECT)
	add_child(reference_rect)

	# 自前のテクスチャの場合
	# 色を辺ごとに描き分けられるのでこっちも捨てがたい
	var ui_debug_frame: NinePatchRect = NinePatchRect.new()
	add_child(ui_debug_frame)
	ui_debug_frame.texture = load("res://UIDebugFrame.png") as Texture2D
	ui_debug_frame.patch_margin_left = 2
	ui_debug_frame.patch_margin_right = 2
	ui_debug_frame.patch_margin_top = 2
	ui_debug_frame.patch_margin_bottom = 2
	ui_debug_frame.set_anchors_preset(Control.PRESET_FULL_RECT)
```

---

**global_rotationへのセットはscaleをリセットする  
**Node3Dで、global_rotationプロパティにVector3をセットすると、scaleプロパティがオール1になってしまう。globalではないただのrotationならば、リセットされない。

```
_node_3d.scale = Vector3(0.2, 0.2, 0.2)
print(_node_3d.scale.x) # この時点では0.2

_node_3d.global_rotation = Vector3(0.0, 0.0, 0.0)
print(_node_3d.scale.x) # 1.0になっている
```

ソースコードを見ると、Node3D:: set_global_rotationは、特に現在のスケール値を考慮せず、transformを上書きしている。  
スケールを維持したければ、global_rotationのセットの前に、一度現在のスケール値を一時的な変数に記録して、セット後にスケールもセットしなおすという操作が必要。

```
# Y軸だけビルボードするような処理
var current_scale: Vector3 = scale
global_rotation_degrees = Vector3(0.0, rot_y_deg, 0.0)
scale = current_scale
```

---

**asによるキャストは複製が発生することも  
**型指定の無いオブジェクトを型指定した変数で受け取るときに、よかれと思ってasを書くだけで別物になってしまうことがある。  
そもそもasは例外なくぜーんぶ複製を作るのだろうか？

```
var pack: Variant = PackedVector3Array()

pack.append(Vector3(1.0, 1.0, 1.0))
var pack_a: PackedVector3Array = pack
var pack_b: PackedVector3Array = pack as PackedVector3Array
pack.append(Vector3(2.0, 2.0, 2.0))
pack.append(Vector3(3.0, 3.0, 3.0))

print(pack.size()) # -> 3
print(pack_a.size()) # -> 3
print(pack_b.size()) # -> 1 キャストした方は増えていない
```

---

**ArrayMeshのTangentは自動で作られる**  
ArrayMesh.add_surface_from_arraysでメッシュにサーフェスを追加した場合、元のarraysにTangentが含まれていなくても、メッシュにはTangentが追加される。おそらくNoramlから計算＆生成されている。  
つまりコードでメッシュを作るときには、Tangentは不要で、Normalさえあればいい。

---

**複数アーマチュアのblendファイル**  
アーマチュアが複数あるblendファイルをインポートすると、シーン直下にアーマチュアノード(Node3D)が複数ぶら下がったシーンになる。シーンに含まれるAnimationPlayerは1つだけで、それの保持するAnimationLibraryも1つ。  
各Animationは、Blender上のアクションと一対一で対応している。ただし、Blenderのアクションのチャンネルには含まれていない他アーマチュアのボーンも、トラックとして追加されている。（こうして追加されたトラックは時間0にキー値0.0があるだけっぽい？）おそらく、どのAnimationも、全アクションの全チャンネルをとりあえず含めるような方針で生成される。

```
■Animationリソースのイメージ
アーマチュアArmaAが動くアニメAnimXと、
アーマチュアArmaBが動くアニメAnimY、AnimY2
の3つがあった場合

AnimX
  ArmaA.BoneA0 *----*-*--*
  ArmaA.BoneA1 *----*-*--*
  ArmaB.BoneB0 *           ←Blenderには無いトラック
  ArmaB.BoneB1 *           ←Blenderには無いトラック
AnimY
  ArmaA.BoneA0 *           ←Blenderには無いトラック
  ArmaA.BoneA1 *           ←Blenderには無いトラック
  ArmaB.BoneB0 *-*-----*
  ArmaB.BoneB1 *-*-----*
AnimY2
  ArmaA.BoneA0 *           ←Blenderには無いトラック
  ArmaA.BoneA1 *           ←Blenderには無いトラック
  ArmaB.BoneB0 *----**---*---*
  ArmaB.BoneB1 *----**---*---*

アニメーションしないアーマチュアArmaCや、
ボーンArmaA.BoneA2があっても、
これらはトラックには追加されない
```

---

**コールバック関数はemit_signal関数内で呼ばれる**  
コールバック関数は、emit_signal関数の中で、即時呼ばれる。関数から帰ってくるまでの間に呼ばれる。ラグがあるわけではない。

```
# コールバック側
func _on_sub_node_finished() -> void:
    print("1")

# エミット側
emit_signal("finished")
print("2") # 1 -> 2の順に表示される
```

これは、コールバック先でさらにエミットしてる場合でも同じで、折り返し電話してたり、他のオブジェクトにエミットしていても、その場で解決される。  
なので、なんらかの実行要求シグナルに対して成否を即答したり、参照の要求に対してすぐ参照を渡してやったりというのは、可能ではある。  
ただ、そうして得られる返答は、emitした関数内のローカル変数に直接取ることはできない。どうしても、コールバック関数内で完結して使い切るか、一度メンバ関数に格納するなどが必要。

```
# Swordmanクラスの実装
# Blacksmithクラスに依存したくない

# 第三者が事前にコネクトしておく
swordman.request_weapon.connect(blocksmith.on_someone_reqested_weapon.bind())

# シグナルでリクエストして取得
emit_signal("request_weapon")
var weapon: Weapon = _received_weapon # emit後、メンバ変数に格納されている
_received_weapon = null

# ----------------------------
# シグナルの代わりにインターフェイスクラスを使う例
# 事前に第三者がBlocksmith参照を渡す
swordman.set_blacksmith(blacksmith)
# IBlocksmithインターフェース越しに取得
var weapon: Weapon = i_blacksmith.get_weapon()
# ----------------------------
```

そもそもエミット側が反応を期待してる時点で、シグナルに向いてない処理のように思う。

---

**メッシュ未適用マテリアルはサーフェイスにならない**  
Blender上でマテリアルのスロットが複数あって、それにマテリアルが割り当てられていても、メッシュに割り当てられていないマテリアルは、サーフェイスにならない。よくもわるくも取り除かれる。  
これは、サーフェスが存在することを期待してマテリアルにアクセスしようとするときに、エラーの原因となる。

---

**アーマチュア下のオブジェはオフセット無しでインポートされる**

Blender上で、アーマチュア下のスキンメッシュオブジェクトが、トランスフォームにオフセットを持っていた場合。

```
アーマチュア
　メッシュオブジェ（XYZ = (0.0, 0.0, 0.0)）
　メッシュオブジェ（XYZ = (3.0, 0.0, 0.0)）
　メッシュオブジェ（XYZ = (2.0, 2.0, 2.0)）
```

インポートするとこうなる。

```
Skeleton3D
　MeshInstance3D（XYZ = (0.0, 0.0, 0.0)）
　MeshInstance3D（XYZ = (0.0, 0.0, 0.0)）
　　※メッシュの頂点に、X=-3.0のオフセットが入ってる
　MeshInstance3D（XYZ = (0.0, 0.0, 0.0)）
　　※メッシュの頂点に、各XZY=-2.0のオフセットが入ってる
```

オブジェクト自体のオフセットが消えて、メッシュで差が吸収される。

---

**emit_signalは古い  
**Object.emit_signal関数はもう古い。Godot 4以降はSignal.emit関数が使える。

```
# リテラルでシグナル名を書きたくない
emit_signal("my_signal")
# 長い
emit_signal(my_signal.get_name())
# 簡素
my_signal.emit()
```

---

**3Dにかぶせて別カメラで3Dを表示**  
別カメラ別ライティングで描画される別レイヤーのオブジェクトを、通常の画面の上に描画したい。  
例えばFalloutや時のオカリナのインベントリのように、3Dオブジェクトを画面を覆うメニューとして使うとか、JRPG風の会話シーンで立ち絵的にキャラクターが表示されるとか。  
この場合、SubviewportのViewportTextureに一度描いて、画面に貼り付ける必要がある。こうするしかないっぽい。1つのレンダリングターゲットで実現できると、シンプルだしパフォーマンス的にも嬉しい。どうにかできないかなあ。  
（もう忘れたけどUnityではCameraのtargetTextureをnullにすることで画面に直接描画できたっぽい。）

```
【方法1】

・レイヤー1ノード
・レイヤー1カメラ
・SubViewport （TransparentBG = true、Size = 画面サイズ）
　・レイヤー2ノード
　・レイヤー2カメラ
・TextureRect（ViewportTexture、Expand Mode = Ignore Size、レイアウトを画面全体）

# 自分のスクリプトで、画面サイズ変更に合わせてsizeを都度更新する
# window.size_changed.connect(_on_window_size_changed) # コールバックを繋いでおく
# my_sub_viewport.size = window.size # シグナルを受け取ったら修正

【方法2】

・レイヤー1ノード
・レイヤー1カメラ
・SubViewportContainer（Stretch = true、AnchorPreset = Rect全体）
　・SubViewport （TransparentBG = true）
　　・レイヤー2ノード
　　・レイヤー2カメラ

# SubViewportContainerの機能で、
# 画面サイズに合わせて自動で動的にテクスチャサイズが変わる
```

---

**size_2d_overrideはテクスチャのサイズを変えない**  
SubViewportのsize_2d_overrideをいくら書き換えてもテクスチャの解像度が変わったように思えない。size_2d_overrideは、ViewportTextureの実サイズに関係しないようだ。

```
ソースコード
RenderingServer上のViewport実体のサイズを変えてると思しき箇所2つ。
どちらも、size_2d_overrideの値を取っていない。

1. Viewport::_set_size(...)
RS::get_singleton()->viewport_set_size(viewport, size.width, size.height);

2. SubViewport::SubViewport()
RS::get_singleton()->viewport_set_size(
    get_viewport_rid(), get_size().width, get_size().height
    );
```

ではsize_2d_overrideはなんだろう？  
[https://github.com/godotengine/godot-proposals/issues/6221](https://github.com/godotengine/godot-proposals/issues/6221)  
上記フォーラムでは「size_of_display_areaという名前のほうが妥当では」と提案されている。2D系のノードが、テクスチャの一部をトリミングして表示するための機能、のような感じだろうか？

---

**get_window().sizeは描画領域のサイズ**  
Node.get_window().sizeで取得できるサイズは、実際にゲームで使われる描画領域のサイズ。ウィンドウのフチは含まれていないので心配いらない。  
Node.get_window()は、そのノードが含まれているWindowを返す関数らしい。（たぶん自作Windowを使ってない限りは、）get_tree().get_root()と同じとのこと。

---

**シグナルでウィンドウのサイズ変更を検知できる**  
ウィンドウのサイズを監視して、サイズ変更があったらSubViewportのテクスチャサイズも変えたい。しかし毎回Windowを取得しては、多分、パフォーマンスに影響が出る。そんなときは、Viewport.size_changedシグナルを受け取るようにすればいい。

```
var scene_tree: SceneTree = Engine.get_main_loop() as SceneTree
var window: Window = scene_tree.root as Window
window.size_changed.connect(_on_window_size_changed)
```

Node.NOTIFICATION_WM_SIZE_CHANGED (1008)を使ってもよさそう。

---

**SubViewportの内容を見るにはTexture2D.get_image**  
デバッグ目的でSubViewportの中身を見たい。そんな時は、Texture2D. get_image()でImageが生成されるので、これをsave_png関数で保存する。

```
var texture: ViewportTexture = _sub_viewport.get_texture()
var image: Image = texture.get_image() # get_imageはパフォーマンス影響大
image.save_png("user://sub_viewport.png")

保存先
C:\Users\ユーザ名\AppData\Roaming\Godot\app_userdata\プロジェクト名\sub_viewport.png
```

---

**組み込みクラスは_initも_readyも_processもvirtual**  
Camera3Dクラスの継承クラスを作ったからと言って、_init内でsuper._init()を呼ぶ必要は無い。そもそも呼べない。なぜならvirtualなので関数自体が無くなる。_readyも_prcessも同様。  
おそらく継承元では何もやってないんでしょう。だから気にする必要は無い。

---

**Arrayは==で比較可能**  
Array同士は==演算子で比較できる。ソースコード見る限り、  
1. ポインタが一致するならtrue  
2. サイズが一致しないならfalse  
3. 中身が1つでも一致しないならfalse  
　intやVector3等の場合は値で比較  
4. 1～3に該当しなければtrue  
という処理をしている。普通に使う分には問題なさそう。

---

**set_layer_maskは追加設定用**  
VisualInstance3D. set_layer_mask()関数は、layer_maskのビットの1つを書き換えるだけ。さらに、layer_maskは初期値が1になっている。なので、レイヤー2だけで描画されるようにしたければ、  
set_layer_mask(1, false)  
set_layer_mask(2, true)  
という2段階の処理が必要。他のレイヤーのビットがどうなってるかわからなければ、一度全部falseにするしかない。  
ビット演算になじみがなくても、layersプロパティに値を直で設定するのが一番確実なように思う。

```
# ビット演算が苦手なのでstatic関数にしておく
static func derive_layer_mask_single(layer_number: int) -> int:
  return 1 << (layer_number - 1)

layers = MyUtilClass.derive_layer_mask_single(layer_number)
```

---

**posmodの第2引数は0だとエラー**  
posmodの第2引数は0だとエラーが発生してプログラムが止まってしまう。配列などのインデックスをループさせるときに、要素数を入れて使うことがあるが、if文でチェックしたほうがいい。  
if slots.size() > 0:  
　idx = posmod(idx + sub_idx, slots.size())

---

**tmpファイルができることがある**  
いつのまにかプロジェクト内に拡張子tmpのファイルが作成されることがある。GDファイルの名前の頭3文字+ランダムっぽい16進数数字+.tmp、という感じで名前が付いてる気がする。

```
【事例】
・main_play.gd → mai75E.tmp
・seq_tester.gd → seq3394.tmp
・seq_tester.gd → seqFFB2.tmp
この2つのgdファイルの共通項としては、それぞれ同名のシーン
main_play.tscnとseq_tester.tscnにアタッチしているということ。
それから割とレイヤー最上位の「使う側」のクラスであるということ。

【seq3394.tmpの中身】
[gd_scene load_steps=2 format=3 uid="uid://ck2tkea48pafo"]

[ext_resource type="Script" path="res://Sequence/Tester/seq_tester.gd" id="1_gy513"]

[node name="SeqTester" type="Node"]
script = ExtResource("1_gy513")
```

[https://github.com/godotengine/godot/issues/956  
https://github.com/godotengine/godot/issues/82270](https://github.com/godotengine/godot/issues/956%EF%BF%BChttps://github.com/godotengine/godot/issues/82270%EF%BF%BC%E2%86%91%E3%83%95%E3%82%A9%E3%83%BC%E3%83%A9%E3%83%A0%E3%81%AE%E3%82%84%E3%82%8A%E5%8F%96%E3%82%8A%E3%80%82Windows%E3%81%AE%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E3%81%AB%E7%94%B1%E6%9D%A5%E3%81%99%E3%82%8B%E3%83%90%E3%82%B0%E3%81%A3%E3%81%BD%E3%81%84%EF%BC%9F)

↑フォーラムのやり取り。Windowsだけで起こるバグっぽい？　10年前からあるが、それが4.1で復活した？  
なんにせよ、消してよさそう。

---

**コールバック関数内でfreeするとエラー**  
signalを用いたコールバック関数内で、自分自身をfreeしようとすると、次のようなエラーが出る。  
Object is locked and can't be freed.  
色々試したけれども、コールバックされる側と、signalをemitする側の、ノードの親子関係は、エラーと関係ない。他人でも出る。  
また、シグナルを接続した関係であっても、emit側がsignalを使わずに、process内でコールバックされる側をfreeするのは問題ない。  
他のノードは関係無しに、「コールバックされてる最中に自分をfreeしてはならない」ということっぽい。  
これはfree()の代わりにqueue_free()関数を使えば回避できる。

---

**queue_free()はいつfreeするのか  
**queue_free()をしたオブジェクトはいつ解放されるのか？　それがわからないので、queue_free()食わず嫌いでいる。  
ソースコードを荒く読んだ感じ、Godot製ゲームのメインループは、だいたい大まかにはこのような順番で処理をしている。・入力取得  
・物理  
・組み込みクラスの更新（NOTIFICATION_INTERNAL_PROCESS）  
・_process()呼び出し（NOTIFICATION_PROCESS）  
・queue_free()されたオブジェの削除  
・描画  
・音再生  
自作クラスの_process()内でqueue_free()すると、すべての_processが終わった直後に、オブジェクトが解放される。

queue_free()を使うときの懸念としては、次のようなものがある。  
・描画されてしまわないか？  
　→描画前にfreeされるので描画されないはず。  
・他のクラスが使ってしまわないか？  
　→組み込みクラスの処理はすべて終わっているので、  
　　自作クラスのやらかしだけ心配すればいい。  
　→_process内で、ローカル変数に一時的に参照を得るのは、  
　　問題ない。  
　→メンバ変数に参照を保持する場合、注意が必要。  
　　一蓮托生で自分も一緒に消える、  
　　つまり参照が自分の親ノードであったり、  
　　同時にqueue_freeされることが約束されているオブジェクトなら、  
　　運用として許容できそう。

前述の通り、コールバック関数内で自分をfree()するとエラーとなるのだが、ドキュメントに従ってqueue_free()に置き換えても、特に心配することは無さそう。ただ、queue_free()したあとに、消える予定のオブジェの_processが回ってくる可能性（エンジンが気を利かせてブロックしてるかもしれないけど）があることだけは留意したほうがよさそう。

---

**Animationのvalueとは**  
Animationクラスの関数。"Value"という言葉が違う意味で2つ出てくる、という話。  
キーフレームの時間を知りたい場合は、track_get_key_time()関数で取得できる。キーフレームの値を取り出したい場合は、track_get_key_value()関数で取得できる。戻り値がVariant型なのでVector3やQuaternionで受け取る。この場合の"value"は、連想配列的な意味でのvalueだろう。key引きのvalueだ。

```
var time: float = animation.track_get_key_time(track_idx, key_idx)
var key_pos: Vector3 = animation.track_get_key_value(track_idx, key_idx)
var key_rot: Quaternion = animation.track_get_key_value(track_idx, key_idx)
var key_scale: Vector3 = animation.track_get_key_value(track_idx, key_idx)
```

一方、キーフレームではなく、補間値を知りたい場合。value_track_interpolate()関数がある。これを使えばいいのか、というと違う。この関数の"value"は、先ほどのkey_time vs key_valueの"value"とは意味が違う。  
Animationに収めるキー値の種類には、Position・Rotation・Scale以外にもいろいろあって、その1つがValueだ。これはPosition・Rotation・Scale以外の「汎用的に使う補間可能な値」のようだ。value_track_interpolate()関数の返すのはこのValue値で、普通のアニメーションでは使ってないトラックのようだ。

```
# 普通はこの3つの関数を使えばいい
var interp_pos: Vector3 = animation.position_track_interpolate(track_idx, time)
var interp_rot: Quaternion = animation.rotation_track_interpolate(track_idx, time)
var interp_scale: Vector3 = animation.scale_track_interpolate(track_idx, time)

# Valueはボーンの姿勢以外の用途
# 色の変化とかそういうのを格納できるのかな？
var track_type: Animation.TrackType = animation.track_get_type(track_idx)
if track_type == Animation.TYPE_VALUE:
  var interp_val: Variant = animation.value_track_interpolate(track_idx, time)
```

---

**ReferenceRectについて  
**ReferenceRectというNodeクラスがある。これは、文字通り枠線を表示するだけのControlで、制作サポート目的のものだ。  
これに、set_anchors_presetでPRESET_FULL_RECTをセットして使うと、親Controlが画面上のどこに矩形を展開してるか、可視化することができる。ただし、editor_onlyプロパティがデフォルトでtrueになっていて、これだとエディタ上の実行時に見えなくなってしまう（editor_onlyは、いわばscene_edit_onlyということ）ので、falseにする必要がある。  
NinePatch派生クラスとしてデバッグ用アタリ矩形を自作してたけど、ReferenceRect派生で作ってもよさそう。

---

**消せないフォルダ**  
Godot 4.3 dev6で、フォルダの名前を変えると、元のフォルダが「ファイルシステム」に残ってしまい、消そうとしてもエラーで消せなくなった。.godotフォルダを一度消してから起動すると、改名前のフォルダが無事に消えた。  
Godot 4.3 dev6はこの他にも、フォルダ作成時に名前の重複がある旨のエラーが出たりして、フォルダ関係が不安定っぽい。正式版までには直りそう。

---

**SHADOWED_VARIABLEはstatic関数でも出る**  
引き数名にメンバ変数名と同じものを使ってると、ワーニングが出る。  
実はこれ、static関数でも出る。static関数内ではこれがメンバ変数でないことは明確だけど、出る。

```
class_name MyObj
var val_a: int = 0

# 非staticではワーニングが出る
func set_val_a(val_a: int) -> void:
    val_a = val_a

# staticでも出る
static func create(val_a: int) -> MyObj:
    var obj = MyObj.new()
    obj.val_a = val_a
    return obj
```

---

**配列のインデックスは負でもいい**  
配列の添え字は、0～size()-1だけでなく、-size()～-1でもアクセスできる。  
なので最後の要素へのアクセスはa[s.size()-1]と書く代わりに、a[-1]と書いてもいい。

---

**簡易的にキーのトリガー取得**  
デバッグ目的でキーボードのキーのトリガーを取りたい。でもInputから取得できるのは現在の押下状態だけだし、自分で作った入力管理クラスも（クラス間の関係を明確にしたい理由で）使いたくない。組み込み機能だけで済ませたい。  
そんなときは、ひとまずこんな風に書くと、コードを極力汚さず少ない記述で判定できる。

```
var trig_key: Key = KEY_NONE
var are_all_key_released: bool = true
var keys_a: Array = range(KEY_SPACE, KEY_SECTION + 1)
var keys_b: Array = range(KEY_ESCAPE, KEY_KP_9 + 1)
for key: Key in (keys_a + keys_b):
	if Input.is_key_pressed(key) == true:
		if _were_all_keys_released == true:
			trig_key = key
		are_all_key_released = false
_were_all_keys_released = are_all_key_released
```

---

**Shiftキーの左右判定**  
最近のGodotの変更（4.3あたり）で、InputEvent経由のキー取得で、ShiftキーやAltキーの左右どちらのキーが押されたか判定できるようになった。でもInputクラス経由のキー取得ではまだ判定できないっぽい。

---

**選択中の文字列と一致する文字の色**  
GDScriptのエディターで、選択中の文字列と同じ文字列の背景には色が付く。この色がデフォルトだと微妙に薄いので設定で変更したい。  
これは、「エディター設定＞テキストエディター＞テーマ＞ハイライトされた単語の色」で変えられる。これのアルファの値を上げればハイライトが見つけやすくなる。でも上げすぎてもいけない。なぜなら、選択中の文字列と見分けがつかなくなる。選択中の文字列は、「選択色」の上から「ハイライトされた単語の色」を重ね塗りして表示されるようだ。なので、ハイライトのアルファが255だと「選択色」を完璧に上書きしてしまう。

---

**Labelの文字幅検知**  
Labelで表示する文字列の幅を知りたい。なぜなら、自分のスクリプトでフォントサイズを自動調整したいから。下記のようにすれば取得できるらしい。  
でも引数の意味がよくわからない。

```
var font: Font = label.get_theme_font("font") # なぜ"font"？

var width: float = -1 # 詳細不明
var font_size: int = 16
var size: Vector2 = font.get_string_size(
        text,
        HORIZONTAL_ALIGNMENT_LEFT,
        width,
        font_size,
        ... # 引数が他にもある
        )
```

TextServer.shaped_text_get_sizeでも取得できそう。こちらも詳細不明。

---

**enumのsize()**  
enumの値の種類数を知りたい場合、MyClass.MyEnum.size()で取得できる。知らなった。以前からあった・・・？  
これがあれば下記のような書き方をしなくて済む。

```
enum Something
{
  XX_XXXXX,
  YYYY,
  ZZZ_Z_ZZZ,
  ...
  COUNT, # 数を取得するために存在する、使わない値
}
```

---

**3D in Control と Control in 3D  
**2D UI（Control）の中に3Dオブジェクトを映す場合も、3D空間上に2DのUIを浮かべる場合も、SubViewportを使う。この2つは手段的にあまり区別が無い。

A. SubViewport on Control  
　TextureRectノードを使う。  
　SubViewportのテクスチャを指定する。  
B. SubViewport in 3D空間  
　マテリアルのカラー、アルベドなどに、  
　SubViewportのテクスチャを指定する。

X. Control → SubViewportに描画  
　SubViewportの子にControlを配置する。  
Y. 3Dオブジェクト → SubViewportに描画　SubViewportの子にCamera3Dを配置する。  
　カメラのCullMaskとレイヤーを一致させたMeshInstance3D等を  
　シーンツリーのどこかに配置する。  
　この3DオブジェクトはSubViewportの子でなくてもいい。

---

**BaseMaterial3Dでビルボードするとスケールが1になる**  
BaseMaterial3D.billboard_modeをビルボード設定にすると、ノードのスケールが描画時に無視される。BaseMaterial3D.billboard_keep_scale = trueにすれば、ビルボードでもスケールが適用される。（ちなみに、スケールが無視されるときは、親ノードによるスケーリングも効かない。）  
billboard_keep_scaleがデフォルトでfalseになっているということは、このほうがパフォーマンスはいいのだろう。いかほど違いがあるかは不明。

---

**時計回りが表**  
Godotでは、3Dメッシュにおいて、時計回りを面の表としている。これは、ArrayMeshクラスのヘルプに明記されている。

---

**Control上に動的にポリゴンを描く**  
CanvasItem._draw関数を定義して、その中でdraw_colored_polygon関数を呼ぶと、Controlにテクスチャ付の図形を描画できる。しかしこれはn角形しか作れない。へこんでる角があってもいいみたいだけど、トポロジー的に穴をあけられない。  
draw_colored_polygonの代わりにRenderingServer. canvas_item_add_triangle_arrayを使うともっとメッシュ的なものを描画できる。

```
RenderingServer.canvas_item_add_triangle_array(
		get_canvas_item(), # 描画するControlのRID
		indices,
		points,
		colors,
		uvs,
		PackedInt32Array(),
		PackedFloat32Array(),
		_texture.get_rid() # テクスチャは_draw()内でロードしてはいかないので注意
		)
```

---

**_drawは毎フレーム呼ばれるわけではない**  
_drawを定義してもそれは毎フレーム呼ばれるわけではない。少なくともControlの場合は必要なときしか呼ばれない。  
CanvasItem.queue_redraw()を呼べば、そのフレームでNOTIFICATION_DRAWが発行され、_drawが呼ばれる。

---

**Texture2DArrayとしてインポートしたPNG**  
エディタのインポートタブで、『Texture2DArray』としてインポートしたpngファイルは、CompressedTextureLayeredとしてloadされる。CompressedTextureLayeredは、Texture2DArrayクラスの派生クラスではない。この辺りはほんとにややこしい。

---

**半透明同士の描画順**  
no_depath_testをtrueにした半透明オブジェクトが、位置によらず、他の半透明オブジェクトよりも手前に描画されるようにしたい。  
この場合、Material. render_priorityを0から1にするといい。  
【Godotの描画順の優先度】  
1. render_priority  
2. 透明/不透明  
3. カメラからの距離  
局所的にはこうなってると理解した。もっと要素あると思うけど。

---

**いつのまにかLOD**  
扇状に三角形を配置した十二角形のメッシュが、BlenderからGodotへのインポート後に、中心の頂点が消されてしまう。テクスチャの貼り付けが壊れてしまうので、これは困る。どうやらこれはLODメッシュのようだ、インポート設定の「LODを生成」のチェックを外すと、本来のメッシュが使用されるようになった。  
LODメッシュが生成されるだけでなく、いつの間にか使われているのは意外だった。そもそも、ゲーム全体でLODが使われないようにしたい。  
プロジェクト設定の「LOD Change>Threshold Pixels」を0にすると、プロジェクト全体で無効になるようだ。動的に有効/無効を設定するには、Viewportのプロパティを変えればいいらしい。
