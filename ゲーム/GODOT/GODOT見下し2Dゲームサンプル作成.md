
GODOT Game engineでゲームを作ってみよう！
https://note.com/fieldwest/n/n362af4d24a1e

![[Pasted image 20250112012509.png]]
![[Pasted image 20250112012638.png]]
![[Pasted image 20250112012725.png]]
![[Pasted image 20250112012913.png]]
![[Pasted image 20250112013337.png]]
![[Pasted image 20250112013533.png]]
![[Pasted image 20250112013901.png]]
![[Pasted image 20250112014052.png]]
![[Pasted image 20250112014220.png]]
![[Pasted image 20250112015258.png]]
```python
extends Area2D

# Called when the node enters the scene tree for the first time.
func _ready() -> void:
	pass # Replace with function body.

# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta: float) -> void:
	pass
```
↓のように変更。
```python
extends Area2D

@export var speed = 400 # How fast the player will move (pixels/sec).
var screen_size # Size of the game window.

# Called when the node enters the scene tree for the first time.
func _ready():
	screen_size = get_viewport_rect().size


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta):
	pass
```

![[Pasted image 20250112015749.png]]
![[Pasted image 20250112020122.png]]


