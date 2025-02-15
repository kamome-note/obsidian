## 3D RTS カメラ コントローラー GODOT 4
https://gist.github.com/artokun/81260d266945786da05b436b17d2a5f3

- マウスが画面の端にあるときに動的スクロールします (画面の端に近づくほど高速になります)
- マウスポイントにズーム
- WASDカメラ移動
- 平面の交点を右クリックしてドラッグ
- イージングによるスクロールズーム
- カメラの境界（設定可能）
- マウスの中ボタンでマウスポイントを回転（高度による軌道制御）
- ズームと回転の固定値
- すべての定数は実行時に調整可能
- ウェブおよびデスクトップのエクスポートに対応

![[Pasted image 20250215193640.png]]

World (Node3D)
└RTSController (Node3D)
　└Elevation (Node3D)
　　└MainCamera (Camera3D)

```python
extends Node3D

#####################
# EXPORT PARAMS
#####################
# movement params
@export_range(0,1000) var movement_speed: float = 15
@export_range(0,1000) var camera_bounds_margin: int = 100
# rotation params
@export_range(0,90) var min_elevation_angle: int = 10
@export_range(0,90) var max_elevation_angle: int = 90
@export_range(0,1000, 0.1) var rotation_speed: float = 5
# zoom
@export_range(0,1000) var min_zoom: int = 5
@export_range(0,1000) var max_zoom: int = 20
@export_range(0,1000, 0.1) var zoom_speed: float = 50
@export_range(0,1000, 0.1) var zoom_speed_damp: float = 0.5
#edge
@export_range(0,1000) var edge_margin: float = 50
@export_range(0,10, 0.5) var edge_speed: float = 3
#pan
@export_range(0,10, 0.01) var pan_speed: float = 2
# flags
@export var allow_rotation: bool = true
@export var inverted_y: bool = false
@export var zoom_to_cursor: bool = true
@export var allow_pan: bool = true

#####################
# PARAMS
#####################
# movement
var _last_mouse_position = Vector2()
var _is_rotating = false
@onready var Elevation: Node3D = $Elevation
# zoom
var _zoom_direction = 0
@onready var Camera: Camera3D = $Elevation/Camera3D
const GROUND_PLANE = Plane(Vector3.UP, 0)
const RAY_LENGTH = 1000
# pan
var _is_panning = false
var _last_drag_point = Vector3()

#####################
# OVERRIDE FUNCTIONS
#####################

func _process(delta) -> void:
	_edge_move(delta)
	_move(delta)
	_rotate(delta)
	_zoom(delta)
	_pan(delta)
	
func _unhandled_input(event: InputEvent) -> void:
	# test if we are rotating
	if event.is_action_pressed("camera_rotate"):
		_is_rotating = true
		_last_mouse_position = get_viewport().get_mouse_position()
	if event.is_action_released("camera_rotate"): 
		_is_rotating = false
	
	# test if we are zooming
	if event.is_action_pressed("camera_zoom_in"):
		_zoom_direction = -1
	if event.is_action_pressed("camera_zoom_out"):
		_zoom_direction = 1
		
	# test if we are rotating
	if event.is_action_pressed("camera_pan"):
		_is_panning = true
		_last_mouse_position = get_viewport().get_mouse_position()
		_last_drag_point = _get_ground_click_location()
	if event.is_action_released("camera_pan"): 
		_is_panning = false
		_last_drag_point = position

#####################
# MOVEMENT FUNCTIONS
#####################
func _move(delta: float) -> void:
	# initialize a velocity vector
	var velocity = Vector3()

	# populate it
	if Input.is_action_pressed("camera_forward"):
		velocity -= transform.basis.z
	if Input.is_action_pressed("camera_backward"):
		velocity += transform.basis.z
	if Input.is_action_pressed("camera_left"):
		velocity -= transform.basis.x
	if Input.is_action_pressed("camera_right"):
		velocity += transform.basis.x
	# normalize and clamp speed
	velocity = velocity.normalized()
	# translate
	global_translate(velocity * delta * movement_speed)
	
	position = position.clamp(
		Vector3(float(camera_bounds_margin),float(max_zoom),float(camera_bounds_margin)) * -1, 
		Vector3(float(camera_bounds_margin),float(max_zoom),float(camera_bounds_margin))
		)

func _rotate(delta: float) -> void:
	if !_is_rotating || !allow_rotation:
		return 
	# calculate mouse movement
	var displacement = _get_mouse_displacement()
	# use horizontal displacement to rotate
	_rotate_left_right(delta, displacement.x)
	# use the vertical displacement to elevate
	_elevate(delta, displacement.y)
	
func _edge_move(delta: float) -> void:
	# initialize a velocity vector
	var velocity = Vector3()
	var viewport = get_viewport()
	var visible_rect = viewport.get_visible_rect()
	# get mouse position
	var m_pos = viewport.get_mouse_position()
	# populate it
	if m_pos.x < edge_margin:
		velocity.x = lerp(
			velocity.x,
			velocity.x - abs(m_pos.x - edge_margin)/edge_margin * edge_speed, 
			edge_speed * delta 
		)
	elif m_pos.x > visible_rect.size.x - edge_margin:
		velocity.x = lerp(
			velocity.x,
			velocity.x + abs(m_pos.x - visible_rect.size.x + edge_margin)/edge_margin * edge_speed, 
			edge_speed * delta 
		)
	if m_pos.y < edge_margin:
		velocity.z = lerp(
			velocity.z,
			velocity.z - abs(m_pos.y - edge_margin)/edge_margin * edge_speed, 
			edge_speed * delta 
		)
	elif m_pos.y > visible_rect.size.y - edge_margin:
		velocity.z = lerp(
			velocity.z,
			velocity.z + abs(m_pos.y - visible_rect.size.y + edge_margin)/edge_margin * edge_speed, 
			edge_speed * delta 
		)
	global_translate(velocity.rotated(Vector3(0,1,0), rotation.y))

func _pan(delta: float) -> void:
	if !_is_panning || !allow_pan:
		return
	# get the mouse displacement
	var new_position = position + _last_drag_point - _get_ground_click_location()
	# we transform the displacement into velocity
	position = position.lerp(new_position,0.5)

#####################
# ZOOM FUNCTIONS
#####################
func _zoom(delta: float) -> void:
	# calculate the new zoom and clamp zoom between min and max
	var new_zoom = clamp(
		Camera.position.z + zoom_speed * delta * _zoom_direction,
		min_zoom,
		max_zoom
		)
	# save 3d position
	var pointing_at = _get_ground_click_location()
	# zoom
	Camera.position.z = new_zoom
	# move the camera such that we are pointing at the same location
	if zoom_to_cursor && pointing_at != null:
		_realign_camera(pointing_at)
	# stop scrolling
	_zoom_direction *= zoom_speed_damp
	if abs(_zoom_direction) <= 0.0001:
		_zoom_direction = 0;

#####################
# HELPER FUNCTIONS
#####################
func _get_mouse_displacement() -> Vector2:
	var current_mouse_position = get_viewport().get_mouse_position()
	var displacement = current_mouse_position - _last_mouse_position
	_last_mouse_position = current_mouse_position
	return displacement

func _rotate_left_right(delta: float, val: float) -> void:
	rotation.y += deg_to_rad(val * delta * rotation_speed) * -1

func _elevate(delta: float, val: float) -> void:
	# calculate new elevation
	var new_elevation = rad_to_deg(Elevation.rotation.x)
	if inverted_y:
		new_elevation += val * delta * rotation_speed
	else:
		new_elevation -= val * delta * rotation_speed
	# clamp the new elevation
	new_elevation = clamp(
		new_elevation, 
		-max_elevation_angle, 
		-min_elevation_angle
		)
	Elevation.rotation.x = deg_to_rad(new_elevation)
	# set the new elevation based on the clamped value
	
func _get_ground_click_location():
	var mouse_pos = get_viewport().get_mouse_position()
	var ray_from = Camera.project_ray_origin(mouse_pos)
	var ray_to = ray_from + Camera.project_ray_normal(mouse_pos) * RAY_LENGTH
	return GROUND_PLANE.intersects_ray(ray_from, ray_to)

func _realign_camera(location: Vector3) -> void:
	# calculate where we need to move
	var new_location = _get_ground_click_location()
	var displacement = location - new_location
	# move the camera based on that calculation
	position += displacement
```

## カメラ制御スクリプト GODOT 3
https://github.com/JaccomoLorenz/godot-camera-control

GODOT 3
デモ用のシーンも同梱されているので動作確認が容易。

```python
# Licensed under the MIT License.
# Copyright (c) 2018-2020 Jaccomo Lorenz (Maujoe)

extends Spatial

# User settings:
# General settings
export var enabled = true setget set_enabled

# See https://docs.godotengine.org/en/latest/classes/class_input.html?highlight=Input#enumerations
export(int, "Visible", "Hidden", "Captured, Confined") var mouse_mode = Input.MOUSE_MODE_CAPTURED

enum Freelook_Modes {MOUSE, INPUT_ACTION, MOUSE_AND_INPUT_ACTION}

# Freelook settings
export var freelook = true
export (Freelook_Modes) var freelook_mode = 2
export (float, 0.0, 1.0) var sensitivity = 0.5
export (float, 0.0, 0.999, 0.001) var smoothness = 0.5 setget set_smoothness
export (int, 0, 360) var yaw_limit = 360
export (int, 0, 360) var pitch_limit = 360

# Pivot Settings
export(NodePath) var privot setget set_privot
export var distance = 5.0 setget set_distance
export var rotate_privot = false
export var collisions = true setget set_collisions

# Movement settings
export var movement = true
export (float, 0.0, 1.0) var acceleration = 1.0
export (float, 0.0, 0.0, 1.0) var deceleration = 0.1
export var max_speed = Vector3(1.0, 1.0, 1.0)
export var local = true

# Input Actions
export var rotate_left_action = ""
export var rotate_right_action = ""
export var rotate_up_action = ""
export var rotate_down_action = ""
export var forward_action = "ui_up"
export var backward_action = "ui_down"
export var left_action = "ui_left"
export var right_action = "ui_right"
export var up_action = "ui_page_up"
export var down_action = "ui_page_down"
export var trigger_action = ""

# Gui settings
export var use_gui = true
export var gui_action = "ui_cancel"

# Intern variables.
var _mouse_offset = Vector2()
var _rotation_offset = Vector2()
var _yaw = 0.0
var _pitch = 0.0
var _total_yaw = 0.0
var _total_pitch = 0.0

var _direction = Vector3(0.0, 0.0, 0.0)
var _speed = Vector3(0.0, 0.0, 0.0)
var _gui

var _triggered=false

const ROTATION_MULTIPLIER = 500

func _ready():
	_check_actions([
		forward_action,
		backward_action,
		left_action,
		right_action,
		gui_action,
		up_action,
		down_action,
		rotate_left_action,
		rotate_right_action,
		rotate_up_action,
		rotate_down_action
	])

	if privot:
		privot = get_node(privot)
	else:
		privot = null

	set_enabled(enabled)

	if use_gui:
		_gui = preload("camera_control_gui.gd")
		_gui = _gui.new(self, gui_action)
		add_child(_gui)

func _input(event):
		if len(trigger_action)!=0:
			if event.is_action_pressed(trigger_action):
				_triggered=true
			elif event.is_action_released(trigger_action):
				_triggered=false
		else:
			_triggered=true
		if freelook and _triggered:
			if event is InputEventMouseMotion:
				_mouse_offset = event.relative
				
			_rotation_offset.x = Input.get_action_strength(rotate_right_action) - Input.get_action_strength(rotate_left_action)
			_rotation_offset.y = Input.get_action_strength(rotate_down_action) - Input.get_action_strength(rotate_up_action)
	
		if movement and _triggered:
			_direction.x = Input.get_action_strength(right_action) - Input.get_action_strength(left_action)
			_direction.y = Input.get_action_strength(up_action) - Input.get_action_strength(down_action)
			_direction.z = Input.get_action_strength(backward_action) - Input.get_action_strength(forward_action)

func _process(delta):
	if _triggered:
		_update_views(delta)

func _update_views(delta):
	if privot:
		_update_distance()
	if freelook:
		_update_rotation(delta)
	if movement:
		_update_movement(delta)

func _physics_process(delta):
	if _triggered:
		_update_views_physics(delta)

func _update_views_physics(delta):
	# Called when collision are enabled
	_update_distance()
	if freelook:
		_update_rotation(delta)

	var space_state = get_world().get_direct_space_state()
	var obstacle = space_state.intersect_ray(privot.get_translation(),  get_translation())
	if not obstacle.empty():
		set_translation(obstacle.position)

func _update_movement(delta):
	var offset = max_speed * acceleration * _direction

	_speed.x = clamp(_speed.x + offset.x, -max_speed.x, max_speed.x)
	_speed.y = clamp(_speed.y + offset.y, -max_speed.y, max_speed.y)
	_speed.z = clamp(_speed.z + offset.z, -max_speed.z, max_speed.z)

	# Apply deceleration if no input
	if _direction.x == 0:
		_speed.x *= (1.0 - deceleration)
	if _direction.y == 0:
		_speed.y *= (1.0 - deceleration)
	if _direction.z == 0:
		_speed.z *= (1.0 - deceleration)

	if local:
		translate(_speed * delta)
	else:
		global_translate(_speed * delta)

func _update_rotation(delta):
	var offset = Vector2();
	
	if not freelook_mode == Freelook_Modes.INPUT_ACTION:
		offset += _mouse_offset * sensitivity
	if not freelook_mode == Freelook_Modes.MOUSE: 
		offset += _rotation_offset * sensitivity * ROTATION_MULTIPLIER * delta
	
	_mouse_offset = Vector2()

	_yaw = _yaw * smoothness + offset.x * (1.0 - smoothness)
	_pitch = _pitch * smoothness + offset.y * (1.0 - smoothness)

	if yaw_limit < 360:
		_yaw = clamp(_yaw, -yaw_limit - _total_yaw, yaw_limit - _total_yaw)
	if pitch_limit < 360:
		_pitch = clamp(_pitch, -pitch_limit - _total_pitch, pitch_limit - _total_pitch)

	_total_yaw += _yaw
	_total_pitch += _pitch

	if privot:
		var target = privot.get_translation()
		var dist = get_translation().distance_to(target)

		set_translation(target)
		rotate_y(deg2rad(-_yaw))
		rotate_object_local(Vector3(1,0,0), deg2rad(-_pitch))
		translate(Vector3(0.0, 0.0, dist))

		if rotate_privot:
			privot.rotate_y(deg2rad(-_yaw))
	else:
		rotate_y(deg2rad(-_yaw))
		rotate_object_local(Vector3(1,0,0), deg2rad(-_pitch))

func _update_distance():
	var t = privot.get_translation()
	t.z -= distance
	set_translation(t)

func _update_process_func():
	# Use physics process if collision are enabled
	if collisions and privot:
		set_physics_process(true)
		set_process(false)
	else:
		set_physics_process(false)
		set_process(true)

func _check_actions(actions=[]):
	if OS.is_debug_build():
		for action in actions:
			if not InputMap.has_action(action):
				print('WARNING: No action "' + action + '"')

func set_privot(value):
	privot = value
	_update_process_func()
	if len(trigger_action)!=0:
		_update_views(0)

func set_collisions(value):
	collisions = value
	_update_process_func()

func set_enabled(value):
	enabled = value
	if enabled:
		Input.set_mouse_mode(mouse_mode)
		set_process_input(true)
		_update_process_func()
	else:
		set_process(false)
		set_process_input(false)
		set_physics_process(false)

func set_smoothness(value):
	smoothness = clamp(value, 0.001, 0.999)

func set_distance(value):
	distance = max(0, value)
```

## シンプルなフリールックカメラ GODOT 4
https://godotengine.org/asset-library/asset/701

WとS を使って前進または後進します。
AとD を使って左右に移動します。
QとE を使って上下に移動します。
スクロールホイールを回すと移動速度が速くなったり遅くなったりします。
マウスの右ボタンを押したままにするとカメラが回転します。エディターにはマウスの感度を制御するスライダーがあります。

```python
class_name FreeLookCamera extends Camera3D

# Modifier keys' speed multiplier
const SHIFT_MULTIPLIER = 2.5
const ALT_MULTIPLIER = 1.0 / SHIFT_MULTIPLIER


@export_range(0.0, 1.0) var sensitivity: float = 0.25

# Mouse state
var _mouse_position = Vector2(0.0, 0.0)
var _total_pitch = 0.0

# Movement state
var _direction = Vector3(0.0, 0.0, 0.0)
var _velocity = Vector3(0.0, 0.0, 0.0)
var _acceleration = 30
var _deceleration = -10
var _vel_multiplier = 4

# Keyboard state
var _w = false
var _s = false
var _a = false
var _d = false
var _q = false
var _e = false
var _shift = false
var _alt = false

func _input(event):
	# Receives mouse motion
	if event is InputEventMouseMotion:
		_mouse_position = event.relative
	
	# Receives mouse button input
	if event is InputEventMouseButton:
		match event.button_index:
			MOUSE_BUTTON_RIGHT: # Only allows rotation if right click down
				Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED if event.pressed else Input.MOUSE_MODE_VISIBLE)
			MOUSE_BUTTON_WHEEL_UP: # Increases max velocity
				_vel_multiplier = clamp(_vel_multiplier * 1.1, 0.2, 20)
			MOUSE_BUTTON_WHEEL_DOWN: # Decereases max velocity
				_vel_multiplier = clamp(_vel_multiplier / 1.1, 0.2, 20)

	# Receives key input
	if event is InputEventKey:
		match event.keycode:
			KEY_W:
				_w = event.pressed
			KEY_S:
				_s = event.pressed
			KEY_A:
				_a = event.pressed
			KEY_D:
				_d = event.pressed
			KEY_Q:
				_q = event.pressed
			KEY_E:
				_e = event.pressed
			KEY_SHIFT:
				_shift = event.pressed
			KEY_ALT:
				_alt = event.pressed

# Updates mouselook and movement every frame
func _process(delta):
	_update_mouselook()
	_update_movement(delta)

# Updates camera movement
func _update_movement(delta):
	# Computes desired direction from key states
	_direction = Vector3(
		(_d as float) - (_a as float), 
		(_e as float) - (_q as float),
		(_s as float) - (_w as float)
	)
	
	# Computes the change in velocity due to desired direction and "drag"
	# The "drag" is a constant acceleration on the camera to bring it's velocity to 0
	var offset = _direction.normalized() * _acceleration * _vel_multiplier * delta \
		+ _velocity.normalized() * _deceleration * _vel_multiplier * delta
	
	# Compute modifiers' speed multiplier
	var speed_multi = 1
	if _shift: speed_multi *= SHIFT_MULTIPLIER
	if _alt: speed_multi *= ALT_MULTIPLIER
	
	# Checks if we should bother translating the camera
	if _direction == Vector3.ZERO and offset.length_squared() > _velocity.length_squared():
		# Sets the velocity to 0 to prevent jittering due to imperfect deceleration
		_velocity = Vector3.ZERO
	else:
		# Clamps speed to stay within maximum value (_vel_multiplier)
		_velocity.x = clamp(_velocity.x + offset.x, -_vel_multiplier, _vel_multiplier)
		_velocity.y = clamp(_velocity.y + offset.y, -_vel_multiplier, _vel_multiplier)
		_velocity.z = clamp(_velocity.z + offset.z, -_vel_multiplier, _vel_multiplier)
	
		translate(_velocity * delta * speed_multi)

# Updates mouse look 
func _update_mouselook():
	# Only rotates mouse if the mouse is captured
	if Input.get_mouse_mode() == Input.MOUSE_MODE_CAPTURED:
		_mouse_position *= sensitivity
		var yaw = _mouse_position.x
		var pitch = _mouse_position.y
		_mouse_position = Vector2(0, 0)
		
		# Prevents looking up/down too far
		pitch = clamp(pitch, -90 - _total_pitch, 90 - _total_pitch)
		_total_pitch += pitch
	
		rotate_y(deg_to_rad(-yaw))
		rotate_object_local(Vector3(1,0,0), deg_to_rad(-pitch))
```