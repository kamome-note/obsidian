Godot4 Navigationを使った経路探索
https://zenn.dev/lettucefry/articles/a22004b9a5a1ac

## Regionの作成

まずはノードを作成します
NavigationRegion3Dを作成し、床や障害物をその配下に置きます
NavigationLink3Dで段差の上と下をつなぎます



NavigationRegion3DはCellsとAgentsの項目を設定すればよいです  
Cellsの項目はNaviMeshの計算を行うときの基準となる大きさです  
AgentsはNPCの大きさです  
Max Clibは乗り越えられる段差の高さ  
Max Slopeは登れる坂の最大の角度です










