
【Godot】ブルームを適用する方法
https://qiita.com/2dgames_jp/items/459e5902cdd36b0438b6

【Godot】ガウスブラーの実装方法
[【Godot】ガウスブラーの実装方法 #Godot - Qiita](https://qiita.com/2dgames_jp/items/bbf302201a56c1d6609d)

画面をリッチにするために使用される技術
- アンチエイリアス - Anti-Aliasing
- 被写界深度 - Depth of Field
- ブルーム - Bloom
- アンビエントオクルージョン - Ambient Occlusion
- モーションブラー - Motion Blur

Godot Engine 4.3の日本語のドキュメント
[機能一覧 — Godot Engine (4.x)の日本語のドキュメント](https://docs.godotengine.org/ja/4.x/about/list_of_features.html#id1)

### ポストプロセッシング：
- トーンマッピング (Linear、Reinhard、Filmic、ACES)。
- ビューポートの明るさに基づく自動露出調節(および手動での露出オーバーライド)。
- 調節可能なボケのシミュレーション (箱形、六角形、円形) 付きの、近景と遠景の被写界深度。
- 半分または完全解像度によるスクリーン空間アンビエントオクルージョン (SSAO)。
- オプションのバイキュービック アップスケーリング付きのグロー / ブルームと、複数のブレンドモード: スクリーン、ソフトライト、追加、置換、ミックス。
- グローには色付きの汚れマップテクスチャを適用でき、レンズの汚れ効果として使えます。
- グローは [スクリーン スペースのブラー効果](https://docs.godotengine.org/ja/4.x/tutorials/3d/environment_and_post_processing.html#doc-environment-and-post-processing-using-glow-to-blur-the-screen) として使用できます。
- 1次元の諧調か3D LUTテクスチャによる色補正。
- スペキュラ・エイリアシングの影響を低減するためのラフネス・リミッタ。
- 明るさ、コントラスト、彩度の調整。

