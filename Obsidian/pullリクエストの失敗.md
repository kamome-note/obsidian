Android側で、ObsidianGitでpullリクエストでABENDするように。
原因調査や復帰するために色々調べたが、原因はmp3のような大きいサイズのファイルをアップしたため。
データをpullするときにエラーになっていた。

macのファイルをzip圧縮し、そのままAndroidにコピー。
Android上で解凍し、AndroidのObsidianで書庫ファイルを参照し直すことで復帰。

![[Pasted image 20250202201539.png]]

１ファイルずつmp3をコミットしたらpullに成功したので、
単純にファイルサイズが大きかった＝大量データのpullでエラーになっていたらしい。

ただ、mp3などの音声データはAndroidで聴いただけでpullの対象になった（メタデータが更新される？）ので
Gitの管理対象から外すのが妥当。

ignoreフォルダ以下に配置したファイルは除外するルールにする。
![[Pasted image 20250202214122.png]]

 .gitignoreを作成。
![[Pasted image 20250202213951.png]]

定義は以下の通り。
![[Pasted image 20250202214249.png]]

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



