音源の音源MIDI化の手順は以下が参考になる。

REAPERで音声をMIDIに変換 (1) ドラムループサンプルのMIDI化
https://peloreaper.blog.jp/archives/57070224.html
REAPERで音声をMIDIに変換 (2) ピッチ検出でNote生成
https://peloreaper.blog.jp/archives/57073180.html

これを使えばサンプル音源のピッチを変えることが可能になる。

１．音源設定
![[音源MIDI化-1748160242635.png|593x51]]

２．ReaTune
FXからReaTuneを設定。
Send MIDI events when pitch changesを選択することでMIDI化できる。
![[音源MIDI化-1748160286514.png|593x347]]

３．MIDIをバウンスしてMIDI Item化
Item右クリックで Apply track/take FX to items as new take (MIDI output) を実行すればOK。
![[音源MIDI化-1748160524862.png|593x95]]

ただし、かなり精度が低いのでまともに使えない…。
![[音源MIDI化-1748163088911.png|593x110]]
