Godot4 Navigationを使った経路探索
https://zenn.dev/lettucefry/articles/a22004b9a5a1ac

## Regionの作成

まずはノードを作成。
NavigationRegion3Dを作成し、床や障害物をその配下に置く。

![[Pasted image 20250218003328.png]]

NavigationRegion3DはCellsとAgentsの項目を設定すればよい。
- Cellsの項目はNaviMeshの計算を行うときの基準となる大きさ
- AgentsはNPCの大きさ
- Max Clibは乗り越えられる段差の高さ
- Max Slopeは登れる坂の最大の角度

![[Pasted image 20250218003453.png]]

NavigationLink3Dで段差の上と下を繋ぐ。

![[Pasted image 20250218004212.png]]

- Start Position と End Positionでつなぐ2点を設定
- Costを増やすとこの経路を取りづらくなる
- BidirectionalをオンにするとEndからStartへの経路も有効になる

## NavigationMeshのベイク

NavigationRegion3Dを選択すると画面上部に焼き込むボタンが出てくるのでそれを押す。
成功すると半透明のNaviMeshが表示される。
薄いピンクの円と線で表示されているのがNavigationLink3D。

![[Pasted image 20250218004337.png]]

床や障害物の配置を変えるたびにベイクをする必要がある。
ゲーム中にベイクすることもできるが重い処理のため複雑な地形ではやらない方が良い。













