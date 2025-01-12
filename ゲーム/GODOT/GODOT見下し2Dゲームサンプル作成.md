Field Westさんの記事を参考にざっとサンプルゲームを作成。
![[Pasted image 20250113034446.png]]
## GODOT Game engineでゲームを作ってみよう！
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

```python
extends Area2D

@export var speed = 400 # How fast the player will move (pixels/sec).
var screen_size # Size of the game window.

# Called when the node enters the scene tree for the first time.
func _ready():
	hide()
	screen_size = get_viewport_rect().size


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta):
	var velocity = Vector2.ZERO # The player's movement vector.
	if Input.is_action_pressed("move_right"):
		velocity.x += 1
	if Input.is_action_pressed("move_left"):
		velocity.x -= 1
	if Input.is_action_pressed("move_down"):
		velocity.y += 1
	if Input.is_action_pressed("move_up"):
		velocity.y -= 1

	if velocity.y < 0:
		$AnimatedSprite2D.animation = "up"
	elif velocity.y > 0:
		$AnimatedSprite2D.animation = "down"
	elif velocity.x > 0:
		$AnimatedSprite2D.animation = "right"
	elif velocity.x < 0:
		$AnimatedSprite2D.animation = "left"

	if velocity.length() > 0:
		velocity = velocity.normalized() * speed
		$AnimatedSprite2D.play()
	else:
		$AnimatedSprite2D.stop()

	position += velocity * delta
	position = position.clamp(Vector2.ZERO, screen_size)
```

![[Pasted image 20250112021434.png]]
![[Pasted image 20250113020911.png]]
```python
extends Area2D

signal hit

@export var speed = 400 # How fast the player will move (pixels/sec).
var screen_size # Size of the game window.

# Called when the node enters the scene tree for the first time.
func _ready():
	screen_size = get_viewport_rect().size
	hide()

# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta):
	var velocity = Vector2.ZERO # The player's movement vector.
	if Input.is_action_pressed("move_right"):
		velocity.x += 1
	if Input.is_action_pressed("move_left"):
		velocity.x -= 1
	if Input.is_action_pressed("move_down"):
		velocity.y += 1
	if Input.is_action_pressed("move_up"):
		velocity.y -= 1

	if velocity.y < 0:
		$AnimatedSprite2D.animation = "up"
	elif velocity.y > 0:
		$AnimatedSprite2D.animation = "down"
	elif velocity.x > 0:
		$AnimatedSprite2D.animation = "right"
	elif velocity.x < 0:
		$AnimatedSprite2D.animation = "left"

	if velocity.length() > 0:
		velocity = velocity.normalized() * speed
		$AnimatedSprite2D.play()
	else:
		$AnimatedSprite2D.stop()

	position += velocity * delta
	position = position.clamp(Vector2.ZERO, screen_size)


func _on_body_entered(body: Node2D) -> void:
	hide() # Player disappears after being hit.
	hit.emit()
	# Must be deferred as we can't change physics properties on a physics callback.
	$CollisionShape2D.set_deferred("disabled", true)
	
func start(pos):
	position = pos
	show()
	$CollisionShape2D.disabled = false
```

![[Pasted image 20250113021251.png]]
![[Pasted image 20250113021403.png]]

## GODOT Game engineでゲームを作ってみよう！（その2）
https://note.com/fieldwest/n/n1db3f343fa3a

![[Pasted image 20250113025221.png]]
![[Pasted image 20250113025353.png]]
![[Pasted image 20250113025522.png]]
![[Pasted image 20250113025640.png]]
![[Pasted image 20250113025824.png]]
![[Pasted image 20250113030008.png]]
![[Pasted image 20250113030118.png]]
![[Pasted image 20250113030254.png]]
![[Pasted image 20250113030333.png]]
![[Pasted image 20250113030417.png]]
```python
extends RigidBody2D

# Called when the node enters the scene tree for the first time.
func _ready():
	var mob_types = $AnimatedSprite2D.sprite_frames.get_animation_names()
	$AnimatedSprite2D.play(mob_types[randi() % mob_types.size()])
	$AnimatedSprite2D.flip_h = true

# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta):
	pass

func _on_visible_on_screen_notifier_2d_screen_exited():
	queue_free()
```

![[Pasted image 20250113030611.png]]
![[Pasted image 20250113030632.png]]
![[Pasted image 20250113030729.png]]
![[Pasted image 20250113030845.png]]
![[Pasted image 20250113030922.png]]
![[Pasted image 20250113031004.png]]
![[Pasted image 20250113031045.png]]
![[Pasted image 20250113031521.png]]
![[Pasted image 20250113031612.png]]
![[Pasted image 20250113031751.png]]
![[Pasted image 20250113032104.png]]
```python
extends Node

@export var mob_scene: PackedScene
var score

func game_over():
	$ScoreTimer.stop()
	$MobTimer.stop()

func new_game():
	score = 0
	$Area2D.start($StartPosition.position)
	$StartTimer.start()

func _on_score_timer_timeout():
	score += 1

func _on_start_timer_timeout():
	$MobTimer.start()
	$ScoreTimer.start()

func _on_mob_timer_timeout():
	# Create a new instance of the Mob scene.
	var mob = mob_scene.instantiate()

	# Choose a random location on Path2D.
	var mob_spawn_location = get_node("MobPath/MobSpawnLocation")
	mob_spawn_location.progress_ratio = randf()

	# Set the mob's direction perpendicular to the path direction.
	var direction = mob_spawn_location.rotation + PI / 2

	# Set the mob's position to a random location.
	mob.position = mob_spawn_location.position

	# Add some randomness to the direction.
	direction += randf_range(-PI / 4, PI / 4)
	mob.rotation = direction

	# Choose the velocity for the mob.
	var velocity = Vector2(randf_range(150.0, 250.0), 0.0)
	mob.linear_velocity = velocity.rotated(direction)

	# Spawn the mob by adding it to the Main scene.
	add_child(mob)

func _ready():
	new_game()
```

![[Pasted image 20250113033605.png]]
![[Pasted image 20250113033644.png]]
![[Pasted image 20250113033749.png]]
![[Pasted image 20250113033838.png]]
![[Pasted image 20250113033951.png]]
![[Pasted image 20250113034040.png]]
![[Pasted image 20250113034326.png]]
![[Pasted image 20250113034356.png]]

できた。
![[Pasted image 20250113034446.png]]
大筋は理解できたが、
オブジェクトをもっと理解する必要がありそう。

## GODOT Game engineでゲームを作ってみよう！（その3）
https://note.com/fieldwest/n/ne7629e10093b





## GODOTで作成したアプリを配布しよう。～エクスポート方法（Windows実行アプリ）～
https://note.com/fieldwest/n/nb2254f8d900e



## GODOT Game engineでゲーム作成上の留意点など（雑記）
https://note.com/fieldwest/n/n40832bf2dbf1

Vulkanに対応していないGPUでないとエラーになるらしい。
以下、対応方法。
- 新規作成のとき：GODOTプロジェクトを作る際に、「レンダラー」を「互換性」にする
- 既存PJのとき：メモ帳でproject.godotファイルを書き換える

```ini
config/features=PackedStringArray("4.0", "Mobile")
↓↓↓
config/features=PackedStringArray("4.2", "GL Compatibility")

renderer/rendering_method="mobile"
↓↓↓
renderer/rendering_method="gl_compatibility"
renderer/rendering_method.mobile="gl_compatibility"
```








