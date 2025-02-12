「ゲーム開発少年の30年が30分に凝縮」レトロゲームと生成AIの素敵な関係
https://note.com/aicu/n/ne9cdfd210a33

【公式】レトロゲームエンジンPyxelを使わない理由が見つからない
https://qiita.com/kitao/items/eae53dd47c663b497352

## 「Pxyelが何なのかわからないし、調べるのも億劫です」

はい、ここまで読んでいただきありがとうございます。
Pyxelはこんな感じのものが簡単につくれるゲーム制作ツールです。

![[Pasted image 20250213022858.png]]

https://kitao.github.io/pyxel/wasm/examples/10_platformer.html
https://kitao.github.io/pyxel/wasm/examples/megaball.html

「音がないとわからない」「ゲームは遊んでみないとわからない」という方は、お手数ですが上の画像をクリックしてみてください。実際にプレイできます。
こんなことをできるのがPyxelです。

## 「そもそもゲームを遊んだり、作ることに興味がない」

でも、何か動く絵を作りたくなったり、仕事の息抜きに、変わったことをやりたくなったりしませんか？
例えば、真面目なセキュリティツールをPyxelでこんな感じに可視化されている方がいます。
あなたがPythonで、最先端のAIプログラムを作っていたとして、pip install pyxelして、コードの先頭にimport pyxelと書き添えるだけで、ドット絵のお絵描きができるようになります。

## 「ゲーム作りに興味はあるけど、自分のスキルだと無理そう」

Pyxelは16色、同時発音数4音と非常にシンプルな仕様で、数個の命令（初期化命令と図形描画命令をいくつか）を覚えるだけで、ゲーム作りを始められます。例えば、冒頭に挙げた横スクロールゲームも、コードは300行ありません。
Pyxelで使うプログラミング言語は、初学者向きかつ、業務用システムやAI開発でも使われているPythonで、さらにグラフィックスやサウンドの作成ツールも同梱されています。
よければ、下の画像をクリックしてPyxelのツールを試してみてください。

![[Pasted image 20250213023100.png]]

https://kitao.github.io/pyxel/wasm/examples/image_editor.html
https://kitao.github.io/pyxel/wasm/examples/sound_editor.html

また、非常に丁寧な解説をしてくださっている、[こんなサイト](https://cpp-learning.com/pyxel_day1/)や、[あんなサイト](https://kinutani.hateblo.jp/entry/2022/10/24/161404)もありますので、かなり始めやすいのではないかと思います。

## 「でも、何を作ったらいいのか…」

何を作るか迷った時は、他の人がどんな作品を作っているのかを見てみると参考になるのではないかと思います。
Pyxelのユーザー作品集コーナーには、100以上のゲームが登録されており、ほとんどの作品のソースコードが公開されています。
中には、生成AIでカードを自動作成する実験的な作品なんかもあります。
他のライブラリと気軽に組み合わせて使えることも、Pyxelの醍醐味ですね。

## 「趣味でレトロゲームを作るのではなく、きちんとしたゲームを作って儲けたい」

お金は大事ですよね。
でも、「レトロゲーム＝売れない」なのでしょうか。むしろ、「レトロゲーム＝キャラが立っていて売れる」だったりしないでしょうか。
2024年10月にSteamで販売されたレトロゲーム風の見た目のゲーム、Dungeon Antiquaは、現在大ヒット中です。ゲーム実況動画もたくさん上がっています。

![[Pasted image 20250213023341.png]]

https://www.youtube.com/watch?v=Hwpyzd8jLI0

このゲームの例を見ても、ビジネスとしてのレトロゲームの可能性を感じることができます。
ここで、重要な追加情報を２つ。
こちらのDungeon Antiqua、実は「Pyxel製のゲーム」です。そして、Pyxelは無料ですので、私にロイヤリティを支払う必要はなく、全てを自分の儲けにできます。

## 「違う色が使いたいし、16 色じゃ全然足りない」

ファミコンですら52色使えますものね。
Pyxelはパレットの色、数共に変更可能です。プログラムから直接変更もできますし、.pyxpal形式の別ファイルを用意して、そこに色を列挙することでまとめて変更することもできます。
一番簡単な方法は、画像を読み込むload関数にincl_colorsオプションを指定することでしょうか。
例えば`pyxel.images[0].load(0, 0, "test.png", incl_colors=True)`のように画像を読み込むと、Pyxelのパレットがその画像で使われている色に置き換わります。
もはやPyxelとは全くわからない、[こちらのサンプル](https://github.com/kitao/pyxel/blob/main/python/pyxel/examples/15_tiled_map_file.py)でも実際の使い方が確認できます。

https://kitao.github.io/pyxel/wasm/examples/15_tiled_map_file.html

## 「サウンドの同時発音数も足りないです」

サウンドも拡張可能です。実際のコードは[こちら](https://github.com/kitao/pyxel/blob/main/python/pyxel/examples/14_synthesizer.py)をご参照ください。
せっかくですので、画像をクリックすると実際に音が聞こえるようにしておきます。
リアルタイムに波形編集できますので、マウスでグリグリいじってみてください。

## 「ドット絵もマップも、もっと本格的な使いやすいツールで作成したい」

Pyxelにはグラフィックスとマップ（タイルマップ）の制作ツールが同梱されていますが、お手軽さに振り切っているため、確かに大量の画像や大規模なマップを作るのは難しいかもしれません。
そんな場合は、扱い慣れた他のツールを使っていただくと良いと思います。
Pyxelは、各種画像ファイルや、Tiled Map File (TMX)の直接読み込みにも対応しています。マップ制作ツールの[Tiled](https://www.mapeditor.org/)で動作確認も行なっています。

「色々インストールしたりとか、始めるための準備が面倒そう」

こちら、Pyxelで最も心を砕いているところになります。
Pythonをお使いの方であれば、別ライブラリの事前インストール等なしに、`pip install pyxel`でPyxelが使えるようになります。

ブラウザさえ動けば、HTMLファイルにこう書くだけでPyxelが動いて、この絵が出ます。Pythonのインストールすら必要ありません。

```python
<script src="https://cdn.jsdelivr.net/gh/kitao/pyxel/wasm/pyxel.js"></script>
<pyxel-run script="

# ここにPyxel (Python)のコードを書いてください
# 例えばこんな感じに…
import pyxel
pyxel.init(200, 150)
pyxel.cls(8)
pyxel.line(20, 20, 180, 130, 7)
pyxel.show()

"></pyxel-run>
```

