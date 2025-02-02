ファイル連携に失敗するようになったが、原因はmp3などの大きめのファイルをpullしようとしたためだった。
１ファイルずつコミットすれば無事にpullできたが、曲を聴く度に更新扱いになることが分かり、更新の挙動が怪しいことが分かった。
（メタデータが更新される？）

gitignoreを定義することで更新を制御するように。

### １）mp3などファイルサイズが大きいものはignoreフォルダに格納することにする

![[Pasted image 20250202223831.png]]

### ２）gitignoreを定義してignoreフォルダ以下を無視する設定にする

![[Pasted image 20250202223915.png]]

![[Pasted image 20250202223950.png]]

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

## 参考サイト

Obsidian 結局行き着いた .gitignore
https://note.com/sho7650/n/nbbc6976103ff

上手くいかないな。
何故だ。