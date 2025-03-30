# 0から学ぶ GDScript

公式教材。
https://gdquest.github.io/learn-gdscript/?ref=godot-docs

![[Pasted image 20250303002119.png]]

演習形式で実際にプログラムして動作確認するという教材になっている。

![[Pasted image 20250303002528.png]]

基礎的な知識を体系的に学べるので良さそう。

![[Pasted image 20250303002854.png]]


## 1.コードとは

PythonやJavascriptと通じる言語となっている。

```python
func _ready():
	print("Welcome!")
```

## 2.最初のエラー

```python
# retrunがないとエラーになる
func this_code_is_wrong():
	return
```

## 3.巨人の肩に乗っかる

関数の呼び出しと引数の指定。
関数内のコードの記述ルール。（Tabインデント）

```python
#表示、非表示
func run():
	show()
	hide()
```

```python
#30度回転
func run():
	rotate(0.5)
```

## 4.四角形を描く

```python
#直進→90度回転→直進
func draw_corner():
	move_forward(200)
	turn_right(90)
	move_forward(200)
```

```python
#横200、縦120の四角形
func draw_rectangle():
	move_forward(200)
	turn_right(90)
	move_forward(120)
	turn_right(90)
	move_forward(200)
	turn_right(90)
	move_forward(120)
```

```python
#横220、縦260の四角形
func draw_rectangle():
	move_forward(220)
	turn_right(90)
	move_forward(260)
	turn_right(90)
	move_forward(220)
	turn_right(90)
	move_forward(260)
```

## 5.最初の関数をコーディングする

関数の定義
	`func name():`
関数内の命令
	Tabインデント
関数名の命名ルール
	大文字小文字の区別なし（大文字を使用しても良い）
	アンダースコア`_`で繋ぐ
	数字から開始する名称は無効

```python
#辺の長さ200の正方形
func draw_square():
	move_forward(200)
	turn_right(90)
	move_forward(200)
	turn_right(90)
	move_forward(200)
	turn_right(90)
	move_forward(200)
```


```python
#辺の長さ200の正方形を3つ描画
func draw_square():
	move_forward(200)
	turn_right(90)
	move_forward(200)
	turn_right(90)
	move_forward(200)
	turn_right(90)
	move_forward(200)
	turn_right(90)

func draw_three_squares():
	draw_square()
	jump(300, 300)
	draw_square()
	jump(300, 300)
	draw_square()
```

## 6.最初の関数パラメータ

関数の定義
	`func name(parameter_1, parameter_2, ...):`






# Introduction to Programming!

英語の教材。（2024年12月公開）
https://www.patreon.com/collection/922491?view=expanded
