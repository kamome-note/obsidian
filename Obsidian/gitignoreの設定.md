ファイル連携に失敗するようになったが、原因はmp3などの大きめのファイルをpullしようとしたためだった。
１ファイルずつコミットすれば無事にpullできたが、曲を聴く度に更新扱いになることが分かり、更新の挙動が怪しいことが分かった。
（メタデータが更新される？）

gitignoreを定義することで更新を制御するように。

## 設定手順
### １）mp3などファイルサイズが大きいものはignoreフォルダに格納することにする

ディレクトリのどこだとしても`ignoreフォルダ`以下のファイルは全て対象外に。
`ignoreフォルダ`を複数定義すれば、それぞれが無視されるイメージ。

![[Pasted image 20250202223831.png]]

### ２）gitignoreを定義してignoreフォルダ以下を無視する設定にする

`kamome-note/obsidian/.gitignore`に配置すること。
（誤って最初`kamome-note/.gitignore`に配置していた）

![[Pasted image 20250203003727.png]]

![[Pasted image 20250202223950.png]]

### ３）gitignoreをaddして有効化する

最初、これを実施していなかったせいで上手くいかなかった。

![[Pasted image 20250203010154.png]]
- 有効化
	`git add .gitignore`
- 有効化を無効化
	`git rm --cached -r .gitignore`

## .gitignoreの定義

```python
# to exclude Obsidian's settings (including plugin and hotkey configurations)
# .obsidian/

# OR only to exclude workspace cache
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/*-plugins.json
.obsidian/plugins/recent-files-obsidian/data.json
.obsidian/plugins/obsidian-git/data.json
.obsidian/appearance.json

# exclude business notes
ignore/

# Add below lines to exclude OS settings and caches
.trash/
.DS_Store
```

## 運用

macとAndroidそれぞれ個別に手動でignoreフォルダを作成し、
その下に連携されたくないファイルを手動で配置する。

## 参考サイト

Obsidian 結局行き着いた .gitignore
https://note.com/sho7650/n/nbbc6976103ff

Git .gitignoreの仕様詳解
https://qiita.com/anqooqie/items/110957797b3d5280c44f
