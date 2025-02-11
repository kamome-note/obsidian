
【Godot】print関数まとめ
https://2dgames.jp/godot-print/

---

## print関数

print関数とは、主にデバッグ用として使う機能。例えば print() を呼び出すとエディタの下部に print() の内容が出力される。
もし表示されない場合は「出力」をクリックすることで表示することができる。

![[Pasted image 20250211163317.png]]

### 「出力」ウィンドウの動作設定

出力ウィンドウの動作は、メニューの「エディタ > エディタ設定」から「一般 > 出力」から設定できる。
![[Pasted image 20250211163420.png]]
### 引数には何でも渡せる

print関数には基本的に文字列や数値を渡すことになるが、リストやオブジェクトを渡すこともできる。引数は複数渡すことも可能。

```python
func _ready() -> void:
	var s = "文字列"
	var i = 123
	var list = [1, 2, 3]
	print(s, i, list)
```

ただオブジェクトはリファレンス番号となってしまうので、`_to_string()` を実装することで文字列表現にすることができる。

```python
class Obj:
	var a = 1
	var b = 2
	var c = "abc"

	# この実装をすることで print() で文字列表現となる.
	func _to_string() -> String:
		return "a:%d b:%d c:%s"%[a, b, c]

func _ready() -> void:
	var obj = Obj.new()
	print(obj)
```

## print_stack(): スタック情報を出力する

print_stack() はコールスタック情報を出力する。
これは `_ready()` → `_func1()` → `_func2()` を呼び出して、`print_stack()` を呼び出す例。

```python
func _ready() -> void:
	_func1()

func _func1():
	_func2()

func _func2():
	print_stack()
```

出力結果。

```
Frame 0 - res://Main.gd:9 in function '_func2'
Frame 1 - res://Main.gd:7 in function '_func1'
Frame 2 - res://Main.gd:4 in function '_ready'
```

ただこの出力処理が何回も呼び出される場合は「出力」ウィンドウのログが大量のコールスタック情報で埋まってしまうので、 push_error() / push_warning() を使う方が良いかも。

## print_debug(): デバッグのみ出力する

print_debug() は、デバッグ実行時のみ出力する関数。
配布するために実行ファイルしたときには出力されなくなる。
print() は実行ファイルにしても出力される。

## printerr(): エラー情報として出力する

printer() は、エラー情報を出力する関数。
例えば関数呼び出しの引数に「無効な値」が渡されたなどの場合、これでエラー出力しておくと良い。
printerr() が呼び出されると「出力」が赤色となり、エラーが発生したことがわかりやすく表示される。

## printraw(): 改行なしでprintする

printraw() は他のprint関数と異なり、改行せずに出力する。

```python
func _ready() -> void:
	printraw("A")
	printraw("B")
	printraw("C")
```

以下のように出力される。

```
ABC
```

## push_error() / push_warning(): デバッガにスタック情報を登録する

push_error() / push_warning() はデバッガにスタック情報を登録する。
呼び出されたときのコールスタックを知りたいときに便利。

```python
func _ready() -> void:
	push_error("エラースタック")
	_func1()

func _func1():
	push_warning("警告スタック")
	_func2()

func _func2():
	pass
```

エディタの「デバッガー」に情報が追加される。
ここを開くと、登録したスタック情報が表示される。

![[Pasted image 20250211165813.png]]

## エラーハンドリングについて

Godot は、何か問題が発生してもゲームを実行し続けるという哲学に従っており、API 全体はユーザーが処理できるようにエラーコードを返すことを中心に構築されている。

Errorを返すメソッドは、エラーが発生しなかった場合はOKを返す。
OK の値は 0 、他のすべてのエラー定数は正の整数なので、ブールチェックにも使用できる。

```python
var error = method_that_returns_error()
if error != OK:
	printerr("Failure!")

# Or, alternatively:
if error:
	printerr("Still failing!")
```
