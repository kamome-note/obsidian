GDScriptリファレンス
https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html

---

## GDScriptの例：
```python
# Everything after "#" is a comment.
# A file is a class!

# (optional) icon to show in the editor dialogs:
@icon("res://path/to/optional/icon.svg")

# (optional) class definition:
class_name MyClass

# Inheritance:
extends BaseClass

# Member variables.
var a = 5
var s = "Hello"
var arr = [1, 2, 3]
var dict = {"key": "value", 2: 3}
var other_dict = {key = "value", other_key = 2}
var typed_var: int
var inferred_type := "String"

# Constants.
const ANSWER = 42
const THE_NAME = "Charly"

# Enums.
enum {UNIT_NEUTRAL, UNIT_ENEMY, UNIT_ALLY}
enum Named {THING_1, THING_2, ANOTHER_THING = -1}

# Built-in vector types.
var v2 = Vector2(1, 2)
var v3 = Vector3(1, 2, 3)

# Functions.
func some_function(param1, param2, param3):
	const local_const = 5

	if param1 < local_const:
		print(param1)
	elif param2 > 5:
		print(param2)
	else:
		print("Fail!")

	for i in range(20):
		print(i)

	while param2 != 0:
		param2 -= 1

	match param3:
		3:
			print("param3 is 3!")
		_:
			print("param3 is not 3!")

	var local_var = param1 + 3
	return local_var

# Functions override functions with the same name on the base/super class.
# If you still want to call them, use "super":
func something(p1, p2):
	super(p1, p2)

# It's also possible to call another function in the super class:
func other_something(p1, p2):
	super.something(p1, p2)

# Inner class
class Something:
	var a = 10

# Constructor
func _init():
	print("Constructed!")
	var lv = Something.new()
	print(lv.a)
```

## 言語：
GDScriptの概要を説明していきます。配列やその他のオブジェクトで使用できるメソッドなどの詳細については、リンクされたクラスの説明を参照してください。

## 識別子：
アルファベット文字( `a` から `z` および `A` から `Z` )、数字( `0` から `9` )、 `_` が識別子として修飾される文字列です。また、識別子は数字で始まってはいけません。識別子では大文字と小文字が区別されます( `foo` が `FOO` と異なる)。

## キーワード：
言語でサポートされているキーワードのリストを次に示します。キーワードは予約語(トークン)であるため、識別子として使用することはできません。次のセクションに示す演算子( `in` 、 `not` 、 `and` 、 `or` など)と組み込み型の名前も予約されています。

|キーワード|説明|
|---|---|
|if|[if/else/elif](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#if-else-elif) を参照して下さい。|
|elif|[if/else/elif](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#if-else-elif) を参照して下さい。|
|else|[if/else/elif](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#if-else-elif) を参照して下さい。|
|for|[for](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#for)を参照して下さい。|
|while|[while](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#while)を参照して下さい。|
|match|[match](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#match)を参照して下さい。|
|break|現在の`for`または`while`ループの実行を終了します。|
|continue|`for` または `while` ループの次の反復に直ちにスキップします。|
|pass|ステートメントが構文的には必要だが、コードの実行が望ましくない場合 (空の関数など) に使用されます。|
|return|関数から値を返します。|
|class|内部クラスを定義します。 [Inner classes](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#inner-classes) を参照してください。|
|class_name|スクリプトを名前を持つグローバルにアクセス可能なクラスとして定義します。 [Registering named classes](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#registering-named-classes) を参照。|
|extends|現在のクラスで拡張するクラスを定義します。|
|is|変数が特定のクラスを拡張するのか、それとも特定の組み込み型のものかをテストします。|
|in|値が文字列、配列、範囲、辞書、またはノード内に存在するかどうかをテストします。 `for` と一緒に使用すると、それらを反復処理をします。|
|as|可能であれば、指定した型に値をキャストします。|
|self|現在のクラスインスタンスを参照します。|
|super|親メソッドのスコープを解決します。 [Inheritance](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#inheritance) を参照。|
|signal|シグナルを定義します。|
|func|関数を定義します。|
|static|静的関数または静的メンバー変数を定義します。|
|const|定数を定義します。|
|enum|列挙型を定義します。|
|var|変数を定義します。|
|breakpoint|デバッガブレークポイントのエディタヘルパー。ガター内をクリックして作成されたブレークポイントとは異なり、 `breakpoint` はスクリプト自体に保存されます。これによりバージョン管理を使用する際、異なるマシン間で永続的なブレークポイントになります。|
|preload|クラスまたは変数をプリロードします。[Classes as resources](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#classes-as-resources) を参照してください。|
|await|シグナルまたはコルーチンが終了するのを待ちます。 [Awaiting signals or coroutines](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#awaiting-signals-or-coroutines) を参照。|
|yield|以前はコルーチンに使用されていました。トランジションのキーワードとして保持します。|
|assert|条件を表明し、失敗時にエラーをログに記録します。 デバッグ以外のビルドでは無視されます。[Assert keyword](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#assert-keyword) を参照してください。|
|void|関数が値を返さないことを表すために使用されます。|
|PI|PI定数。|
|TAU|TAU定数。|
|INF|Infinity定数。比較や計算の結果として使用されます。|
|NAN|NAN (not a number)定数。計算上の不可能な結果として使用されます。|
## オペレーター：
以下はサポートされている演算子とその優先順位のリストです。 `**` 演算子を含め、すべての二項演算子は左結合です。これは `2 ** 2 ** 3` は `(2 ** 2) ** 3` と等しいことを意味します。必要な優先順位を明示的に指定するには、括弧を使用します (例: `2 ** (2 ** 3)`)。三項演算子 (`if/else`) は右結合です。

| **演算子**                                                                                                                                                                                     | **説明**                                                                                                                                                                                                                                                                             |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `(` `)`                                                                                                                                                                                     | グループ化 (最優先)<br><br>括弧は実際には演算子ではありませんが、演算の優先順位を明示的に指定できます。                                                                                                                                                                                                                          |
| `x[index]`                                                                                                                                                                                  | 添字指定                                                                                                                                                                                                                                                                               |
| `x.attribute`                                                                                                                                                                               | 要素参照                                                                                                                                                                                                                                                                               |
| `foo()`                                                                                                                                                                                     | 関数呼び出し                                                                                                                                                                                                                                                                             |
| `await x`                                                                                                                                                                                   | [シグナルまたはコルーチンを待つ](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#awaiting-signals-or-coroutines)                                                                                                                                            |
| `x is Node`<br><br>`x is not Node`                                                                                                                                                          | 型チェック<br>[is_instance_of()](https://docs.godotengine.org/ja/4.x/classes/class_%40gdscript.html#class-gdscript-method-is-instance-of) 関数も参照してください。                                                                                                                                  |
| `x ** y`                                                                                                                                                                                    | 累乗<br><br>[pow()](https://docs.godotengine.org/ja/4.x/classes/class_%40globalscope.html#class-globalscope-method-pow) 関数を呼び出すのと同様に、 `x` を `y` 乗します。                                                                                                                                |
| `~x`                                                                                                                                                                                        | ビット単位のNOT                                                                                                                                                                                                                                                                          |
| `+x`<br><br>`-x`                                                                                                                                                                            | 正数/負数                                                                                                                                                                                                                                                                              |
| `x * y`<br><br>`x / y`<br><br>`x % y`                                                                                                                                                       | 乗算/除算/剰余<br><br>`%` 演算子は [フォーマット文字列](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_format_string.html#doc-gdscript-printf) でも使用されます。<br><br>**注意:** これらの演算子は C++ と同じ動作をしますが、Python、JavaScript などを使用しているユーザーにとっては予期しない動作である可能性があります。表の後の詳細な注意を参照してください。 |
| `x + y`<br><br>`x - y`                                                                                                                                                                      | 加算 (もしくは配列の連結) / 減算                                                                                                                                                                                                                                                                |
| `x << y`<br><br>`x >> y`                                                                                                                                                                    | ビットシフト                                                                                                                                                                                                                                                                             |
| `x & y`                                                                                                                                                                                     | ビットAND                                                                                                                                                                                                                                                                             |
| `x ^ y`                                                                                                                                                                                     | ビットXOR                                                                                                                                                                                                                                                                             |
| `x \| y`                                                                                                                                                                                    | ビットOR                                                                                                                                                                                                                                                                              |
| `x == y`<br><br>`x != y`<br><br>`x < y`<br><br>`x > y`<br><br>`x <= y`<br><br>`x >= y`                                                                                                      | 比較<br><br>表の後の詳細注記を参照してください。                                                                                                                                                                                                                                                       |
| `x in y`<br><br>`x not in y`                                                                                                                                                                | 包含チェック<br><br>`in` は、構文の一部として [for](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#for) キーワードとともに使用されます。                                                                                                                                    |
| `not x`<br><br>`!x`                                                                                                                                                                         | ブール演算NOT とその [非推奨のエイリアス](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_styleguide.html#boolean-operators)                                                                                                                                              |
| `x and y`<br><br>`x && y`                                                                                                                                                                   | ブール演算ANDとその [非推奨のエイリアス](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_styleguide.html#boolean-operators)                                                                                                                                               |
| `x or y`<br><br>`x \| y`                                                                                                                                                                    | ブール演算ORとその [非推奨のエイリアス](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_styleguide.html#boolean-operators)                                                                                                                                                |
| `true_expr if cond else false_expr`                                                                                                                                                         | 参考演算子の if/else                                                                                                                                                                                                                                                                     |
| `x as Node`                                                                                                                                                                                 | [型変換とキャスト](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#casting)                                                                                                                                                                          |
| `x = y`<br><br>`x += y`<br><br>`x -= y`<br><br>`x *= y`<br><br>`x /= y`<br><br>`x **= y`<br><br>`x %= y`<br><br>`x &= y`<br><br>`x \|= y`<br><br>`x ^= y`<br><br>`x <<= y`<br><br>`x >>= y` | 代入 (優先度は最低)<br><br>式の中で代入演算子を使用することはできません。                                                                                                                                                                                                                                         |

## リテラル：

| 例                           | 説明                                                                                               |
| --------------------------- | ------------------------------------------------------------------------------------------------ |
| `null`                      | null値                                                                                            |
| `false`, `true`             | ブール値                                                                                             |
| `45`                        | 10進整数                                                                                            |
| `0x8f51`                    | 16進整数                                                                                            |
| `0b101010`                  | 2進整数                                                                                             |
| `3.14`, `58.1e-10`          | 浮動小数点数(実数)                                                                                       |
| `"Hello"`, `'Hi'`           | 通常の文字列                                                                                           |
| `"""Hello"""`, `'''Hi'''`   | 三重引用符で囲まれた通常の文字列                                                                                 |
| `r"Hello"`, `r'Hi'`         | raw文字列                                                                                           |
| `r"""Hello"""`, `r'''Hi'''` | 三重引用符で囲まれたraw文字列                                                                                 |
| `&"name"`                   | [StringName](https://docs.godotengine.org/ja/4.x/classes/class_stringname.html#class-stringname) |
| `^"Node/Label"`             | [NodePath](https://docs.godotengine.org/ja/4.x/classes/class_nodepath.html#class-nodepath)       |

リテラルのように見えるが、実際にはそうではない2つの短縮構文もあります。

| 例             | 説明                             |
| ------------- | ------------------------------ |
| `$NodePath`   | `get_node( "NodePath")` の短縮形   |
| `%UniqueNode` | `get_node("%UniqueNode")` の短縮形 |

Integerとfloatの数値は可読性のために `_` で分割することができます。

```python
12_345_678  # Equal to 12345678.
3.141_592_7  # Equal to 3.1415927.
0x8080_0000_ffff  # Equal to 0x80800000ffff.
0b11_00_11_00  # Equal to 0b11001100.
```

通常の文字列リテラルには、次のエスケープシーケンスを含めることができます。

| エスケープシーケンス | 展開                                                |
| ---------- | ------------------------------------------------- |
| `\n`       | 改行 (line feed)                                    |
| `\t`       | 水平タブ文字                                            |
| `\r`       | リターン                                              |
| `\a`       | アラート (ビープ／ベル)                                     |
| `\b`       | バックスペース                                           |
| `\f`       | フォームフィードの改ページ                                     |
| `\v`       | 垂直タブ文字                                            |
| `\"`       | 二重引用符                                             |
| `\'`       | 一重引用符                                             |
| `\\`       | バックスラッシュ                                          |
| `\uXXXX`   | UTF-16 Unicodeコードポイント `XXXX` (16進数、大文字小文字の区別なし)   |
| `\UXXXXXX` | UTF-32 Unicodeコードポイント `XXXXXX` (16進数、大文字小文字の区別なし) |
## アノテーション：

アノテーションはスクリプトまたはそのコードに対する修飾子として機能する、GDScriptの特別なトークンでありGodotエンジンまたはエディタによるスクリプトの処理方法に影響を与える可能性があります。
全てのアノテーションは `@` 文字で始まり、名前によって指定されます。

例えば、これを使用して値をエディタにエクスポートできます。
```python
@export_range(1, 100, 1, "or_greater")
var ranged_var: int = 50
```

必要な引数の型と互換性のある任意の定数式を、アノテーションの引数として渡すことができます。
```python
const MAX_SPEED = 120.0

@export_range(0.0, 0.5 * MAX_SPEED)
var initial_speed: float = 0.25 * MAX_SPEED
```

アノテーションは1行に1つ指定することも、同じ行にすべて指定することもできます。これらはアノテーションではない次のステートメントに影響します。アノテーションには括弧で囲み、カンマで区切って指定する引数を含めることができます。
これらはどちらも同じです。

```python
@annotation_a
@annotation_b
var variable

@annotation_a @annotation_b var variable
```

---

以降はリファレンスページ参照。
