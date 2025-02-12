## Beginner dialogue tutorial for Godot 4
https://www.youtube.com/watch?v=UhPFk8FSbd8

## 試行

ダウンロードしたらプロジェクト設定 > プラグインから有効化。
![[Pasted image 20250212235139.png]]

有効化するとタブが表示される。
![[Pasted image 20250212235221.png]]

右上に新バージョンの通知があるときはダウンロードして最新化。
![[Pasted image 20250212235322.png]]

ダイアログファイルを作成。
![[Pasted image 20250212235559.png]]

```
~ start
Nathan: [[Hi|Hello|Howdy]], this is some dialogue.
Nathan: Here are some choices.
- First one
	Nathan: You picked the first one.
- Second one
	Nathan: You picked the second one.
- Start again => start
- End the conversation => END
Nathan: For more information see the online documentation.
=> END
```

```
~ start
モルにゃん: こんにちにゃもす。
=> END
```

テスト実行してみる。
![[Pasted image 20250212235942.png]]

playerにスクリプトを追加。

![[Pasted image 20250213000326.png]]
```python
func _unhandled_input(event: InputEvent) -> void:
	if Input.is_action_just_pressed("ui_accept"):
	DialogueManager.show_example_dialogue_balloon(load("res://dialogue/main.dialogue"), "start")
		return
```

スペースキーを押すとダイアログが表示された。
素晴らしい。

![[Pasted image 20250213000713.png]]

動画の2:50以降に、Collisionを検知してダイアログを表示する方法など他の説明もあり。
https://www.youtube.com/watch?v=UhPFk8FSbd8



---

## ダイアログの書式説明
https://github.com/nathanhoad/godot_dialogue_manager/blob/main/docs/Basic_Dialogue.md

最も基本的なダイアログは単なる文字列です:

```
This is some dialogue.
```

会話するキャラクターを追加する場合は、コロンの前に名前を入れ、その後にセリフを入れます。

```
Nathan: This is me talking.
```

BBCodeを使用すると、ダイアログにスパイスを加えることができます。Godot で利用できるすべての機能に加えて、`RichTextLabel`Dialogue Manager が提供するいくつかの追加機能も使用できます。

- `[wait=N]`ここで、N はダイアログの入力を一時停止する秒数です。
- `[speed=N]`ここで、N はデフォルトのタイピング速度に掛ける数値です。
- `[next=N]`ここで、N は、次の行のダイアログに自動的に進む前に待機する秒数です。 を使用する`[next=auto]`と、ラベルがテキストの長さに基づいて待機時間を決定するようにすることもできます。

会話の行が次々に書かれています:

```
Nathan: I'll say this first.
Nathan: Then I'll say this line.
```

ダイアログにインタラクティブ性を加えるには、応答を指定できます。応答は で始まる行です`-` :

```
- This is a response
- This is a different response
- And this is the last one
```

### 回答

応答後にダイアログを分岐させる方法の1つは、各応答の下にさらにダイアログをネストすることです。ネストされた応答ダイアログは、ブランチが追加されるにつれて無限にネストできます。

```
Nathan: How many projects have you started and not finished?
- Just a couple
	Nathan: That's not so bad.
- A lot
	Nathan: Maybe you should finish one before starting another one.
- I always finish my projects
	Nathan: That's great!
	Nathan: ...but how many is that?
	- A few
		Nathan: That's great!
	- I haven't actually started any
		Nathan: That's what I thought.
```

応答には、選択可能かどうかを決定する条件を含めることができます。応答に条件を含めるには、次のように角括弧で囲んだ条件式を追加します。

```
- This is a normal response
- This is a conditional response [if SomeGlobal.some_property == true]
```

### 会話の行をランダム化する

複数の行からランダムに 1 行を選択する場合は、`%`次のように行の先頭に を付けます。

```
Nathan: I will say this.
% Nathan: And then I might say this
% Nathan: Or maybe this
% Nathan: Or even this?
```

各行が発音される可能性は均等になります。

線に重みを付けるには、 a`%`の後に重み付けする数値を指定します。たとえば、 a は、`%2`その線が通常の線の 2 倍の確率で選択されることを意味します。

```
%3 Nathan: This line has a 60% chance of being picked
%2 Nathan: This line has a 40% chance of being picked
```

ランダムな行の複数のグループを区切るには、空行を使用します。

```
% Group 1
% Also group 1

% Group 2
% And this is also group 2
```

ブロック全体をランダムにすることもできます。

```
%
	Nathan: This is the first block.
	Nathan: Still the first block.
% Nathan: This is the possible outcome.
```

最初のランダム項目が選択されると、ネストされた両方の行が再生されます。

### 会話における変数

会話の行内でゲームの状態の値を示すには、それを二重の波括弧で囲みます。

```
Nathan: The value of some property is {{SomeGlobal.some_property}}.
```

同様に、キャラクターの名前が変数に基づいている場合は、二重中括弧で指定することもできます。

```
{{SomeGlobal.some_character_name}}: My name was provided by the player.
```

### タグ

`[#`行にタグを付ける必要がある場合は、タグを とで囲み`]`、カンマで区切ります。つまり、行に「happy」タグと「surprised」タグを指定するには、次のようにします。

```
Nathan: [#happy, #surprised] Oh, Hello!
```

実行時に、`DialogueLine`の`tags`プロパティには が含まれます`["happy", "surprised"]`。

`get_tag_value`また、メソッドを使用してアクセスできるタグ値を に指定することもできます`DialogueLine`。

```
Nathan: [#mood=happy] Oh, Hello!
```

このセリフの場合、`tags`配列は となり`["mood=happy"]`、 が`line.get_tag_value("mood")`返されます`"happy"`。

### 同時対話

複数のキャラクターに同時に話してもらいたい場合は、同時行構文を使用できます。通常のダイアログ行の後に、同時に話される行の前に「|」を付けることができます。

```
Nathan: This is a regular line of dialogue.
| Coco: And I'll say this line at the same time!
| Lilly: And I'll say this too!
```

同時実行ラインを使用するには、`concurrent_lines`の プロパティにアクセスします`DialogueLine`。

_注: シンプルさを保つために、組み込みの例のバルーンには同時実行ラインの実装は含まれていません。_

### タイトルとジャンプ

タイトルは、ダイアログ内で開始したりジャンプしたりできるマーカーです。通常、ゲームでは、タイトルを指定してダイアログを開始します (デフォルトのタイトルは です`start`が、ダイアログに書き込んだタイトルにすることもできます)。

タイトルは a で始まり`~` 、次のように命名されます (スペースなし):

```
~ this_is_a_title
```

ダイアログ内のどこかからタイトルにジャンプするには、ジャンプ/移動行を使用できます。ジャンプ行には a というプレフィックスが付き`=>` 、その後にジャンプ先のタイトルを指定します。

```
=> this_is_a_title
```

ダイアログ ランタイムがジャンプに遭遇すると、フローはそのタイトル マーカーに誘導され、そこから続行されます。

ダイアログ内からフローを終了したい場合は、次の場所にジャンプできます`END`。

```
=> END
```

これにより、現在の対話の流れは終了します。

また、ダイアログの流れをリダイレクトして、ジャンプ元の場所に戻る「ジャンプして戻る」タイプのジャンプを使用することもできます。これらの行には がプレフィックスとして付けられ`=><` 、ジャンプ先のタイトルが指定されます。フローが`END`（またはファイルの終わり）に達すると、フローはジャンプ元の場所に戻り、そこから続行されます。

連鎖する「ジャンプとリターン」に関係なく会話を強制的に終了したい場合は、行を使用できます`=> END!`。

ジャンプは応答のインラインでも使用できます。

```
~ start
Nathan: Well?
- First one
- Another one => another_title
- Start again => start
=> END

~ another_title
Nathan: Another one?
=> END
```

### 表現ジャンプ

式をジャンプ ディレクティブとして使用できます。式は既知のタイトル名に解決される必要があります。そうでない場合、予期しない結果になります。

ダイアログ コンパイラはコンパイル時に式の値がタイトルと一致するかどうかを確認できないため、**これらは注意して使用してください。**

表現ジャンプは次のようになります。

`=> {{SomeGlobal.some_property}}`

### ダイアログを他のダイアログにインポートする

複数の他のファイルで使用したい共通のダイアログを含むダイアログ ファイルがある場合は、それ`import`をそれらのファイルに組み込むことができます。

たとえば、次のような`snippets.dialogue`ファイルがあるとします。

```
~ banter
Nathan: Blah blah blah.
=> END
```

これを別のダイアログ ファイルにインポートし、`banter`スニペット ファイルからタイトルにジャンプすることができます (`=><`ジャンプしたダイアログが終了した後にこの行に戻ることを示す構文に注意してください)。

```
import "res://snippets.dialogue" as snippets

~ start
Nathan: The next line will be from the snippets file:
=>< snippets/banter
Nathan: That was some banter!
=> END
```

