## Gemini Code Assistantを導入
https://note.com/lush_clover5338/n/nc7b5356bf0b6
## VS Code × Gemini Code AssistantでGodotをプログラム
https://note.com/lush_clover5338/n/n043b2e9009ba

---
## Visual Studio Codeをインストール
インストール済。

## VS Code Copilotを導入
スキップ。

## Gemini Code Assistをインストール

Gemini Code Assist
![[Pasted image 20250324064820.png]]

## Google Cloud Codeもインストール

Google Cloud Code
![[Pasted image 20250324065037.png]]

## Pythonをインストール
テスト用にPythonをインストール

Python
![[Pasted image 20250324070145.png]]

## VSCodeにgodot-toolsをインストール

godot-tools
![[Pasted image 20250324065254.png]]

## Gemini Code Assistでログイン

以下アカウントでログイン
kamome.no.micro@gmail.com

![[Pasted image 20250324070354.png]]

## 試しにコードを生成：Hello World

依頼
```
PythonでHello Worldを表示するプログラムを書いてください
```

![[Pasted image 20250324070912.png]]

```python
print("Hello World")
```

## 試しにコードを生成：Hit＆Blow

```
PythonでHit＆Blowのゲームをプログラムを書いて下さい。以下、仕様です。

どのように作れるかの概要：秘密の数字の生成:
まず、コンピューターが秘密の数字（例：4桁の重複しない数字）をランダムに生成します。
random モジュールを使って、ランダムな数字を生成することができます。

プレイヤーの入力:
プレイヤーに数字を入力してもらいます。
input() 関数を使って、プレイヤーからの入力を受け取ります。

ヒット＆ブローの判定:
プレイヤーの入力と秘密の数字を比較して、ヒット数とブロー数を計算します。
ループや条件分岐（if, elif, else）を使って、比較と計算を行います。

ヒントの表示:
ヒット数とブロー数をプレイヤーに表示します。
print() 関数を使って、結果を表示します。

ゲームのループ:
プレイヤーが正解するまで、入力、判定、ヒント表示を繰り返します。
while ループを使って、ゲームを繰り返します。

ゲーム終了:
プレイヤーが正解したら、ゲームを終了し、結果を表示します。
```

![[Pasted image 20250324072037.png]]

```python
import random

def generate_secret_number(digits):
    """重複のない指定桁数の秘密の数字を生成する"""
    numbers = list(range(10))
    random.shuffle(numbers)
    return "".join(map(str, numbers[:digits]))

def count_hits_and_blows(secret, guess):
    """ヒット数とブロー数を計算する"""
    hits = 0
    blows = 0
    for i in range(len(secret)):
        if secret[i] == guess[i]:
            hits += 1
        elif guess[i] in secret:
            blows += 1
    return hits, blows

def is_valid_guess(guess, digits):
    """入力された数字が有効かどうかをチェックする"""
    if not guess.isdigit() or len(guess) != digits:
        return False
    if len(set(guess)) != digits:
        return False
    return True

def play_hit_and_blow(digits=4):
    """Hit & Blow ゲームをプレイする"""
    secret_number = generate_secret_number(digits)
    print(f"秘密の数字（{digits}桁）を生成しました。当ててみてください！")
    attempts = 0

    while True:
        guess = input(f"{digits}桁の数字を入力してください: ")
        attempts += 1

        if not is_valid_guess(guess, digits):
            print(f"無効な入力です。{digits}桁の重複のない数字を入力してください。")
            continue
        
        hits, blows = count_hits_and_blows(secret_number, guess)
        print(f"ヒット: {hits}, ブロー: {blows}")

        if hits == digits:
            print(f"おめでとうございます！{attempts}回で正解しました！")
            break

if __name__ == "__main__":
    play_hit_and_blow()

```

![[Pasted image 20250324072505.png]]

**コードの解説:**
1. **generate_secret_number(digits)関数:**
    - `digits`: 秘密の数字の桁数を受け取ります（デフォルトは4）。
    - `numbers = list(range(10))`: 0から9までの数字をリストに格納します。
    - `random.shuffle(numbers)`: リストの要素をランダムにシャッフルします。
    - `"".join(map(str, numbers[:digits]))`: シャッフルされたリストから`digits`個の要素を取り出し、文字列に変換して連結し、秘密の数字として返します。
2. **count_hits_and_blows(secret, guess)関数:**
    - `secret`: 秘密の数字（文字列）を受け取ります。
    - `guess`: プレイヤーの入力（文字列）を受け取ります。
    - `hits`: ヒット数をカウントします。
    - `blows`: ブロー数をカウントします。
    - `for`ループで、`secret`と`guess`の各桁を比較します。
    - `secret[i] == guess[i]`：同じ位置に同じ数字があればヒットをインクリメント。
    - `guess[i] in secret`: 異なる位置に同じ数字があればブローをインクリメント。
    - ヒット数とブロー数を返します。
3. **is_valid_guess(guess, digits)関数:**
    - `guess`: プレイヤーの入力（文字列）を受け取ります。
    - `digits`: 秘密の数字の桁数
    - `guess.isdigit()`: 全て数字で構成されているか
    - `len(guess) != digits`:桁数が正しいか
    - `len(set(guess)) != digits`: 重複した数字があるか
    - 以上の条件をすべて満たしていればTrue, それ以外はFalseを返します。
4. **play_hit_and_blow(digits=4)関数:**
    - `digits`: 秘密の数字の桁数を受け取ります（デフォルトは4）。
    - `secret_number = generate_secret_number(digits)`: 秘密の数字を生成します。
    - `print(f"秘密の数字（{digits}桁）を生成しました。当ててみてください！")`: 開始メッセージを表示します。
    - `attempts`: 試行回数をカウントします。
    - `while True`: プレイヤーが正解するまでループを繰り返します。
    - `guess = input(...)`: プレイヤーに数字を入力させます。
    - `attempts += 1`:試行回数を1増やす。
    - `is_valid_guess`: 入力された数字が正しいか確認する。
    - `hits, blows = count_hits_and_blows(secret_number, guess)`: ヒットとブローを計算します。
    - `print(f"ヒット: {hits}, ブロー: {blows}")`: ヒット数とブロー数を表示します。
    - `if hits == digits`: 全てがヒットの場合。
        - `print(f"おめでとうございます！{attempts}回で正解しました！")`: 正解メッセージを表示します。
        - `break`: ループを終了します。
5. **if __name__ == "__main__":ブロック:**
    - このスクリプトが直接実行された場合にのみ、`play_hit_and_blow()` 関数を呼び出します。

**ゲームの遊び方:**
1. プログラムを実行すると、「秘密の数字（4桁）を生成しました。当ててみてください！」というメッセージが表示されます。
2. 4桁の重複しない数字を入力して、エンターキーを押します。
3. 「ヒット: X, ブロー: Y」という結果が表示されます。
    - ヒットは、数字と位置が両方合っている数です。
    - ブローは、数字は合っているが位置が違う数です。
4. 正解するまで2と3を繰り返します。
5. 正解したら、おめでとうメッセージと試行回数表示されます。

**改善点**
- **桁数の指定:** `play_hit_and_blow(digits=4)` のように引数に桁数を渡すことで、桁数を変更可能にした。
- **入力チェック**: `is_valid_guess`関数を作り入力された値が数字で重複していないかのチェックを行いエラー処理を加えました。
- **コメントの追加**: 行っている処理がわかるようにコメントを追加しました。
- **関数の分割**: 各処理を関数に分割し、見やすくしました。
- **ゲーム終了メッセージ:** 試行回数も表示するように改善しました。
- **エラー処理**: 不正な入力(数字以外, 桁数不足、重複している数字)に対してのエラー処理を追加しました。

```
apple@kamome-MacBook-Pro ~ % /usr/local/bin/python3.11 /Users/apple/Downloads/print.py
秘密の数字（4桁）を生成しました。当ててみてください！
4桁の数字を入力してください: 1222
無効な入力です。4桁の重複のない数字を入力してください。
4桁の数字を入力してください: 1982
ヒット: 3, ブロー: 0
4桁の数字を入力してください: 1984
ヒット: 2, ブロー: 0
4桁の数字を入力してください: 0982
ヒット: 2, ブロー: 0
4桁の数字を入力してください: 1972
ヒット: 3, ブロー: 0
4桁の数字を入力してください: 1942
ヒット: 3, ブロー: 0
4桁の数字を入力してください: 1952
ヒット: 3, ブロー: 0
4桁の数字を入力してください: 1962
ヒット: 4, ブロー: 0
おめでとうございます！7回で正解しました！
```

えええ、すご！
すごくない！？

![[Pasted image 20250324073358.png]]