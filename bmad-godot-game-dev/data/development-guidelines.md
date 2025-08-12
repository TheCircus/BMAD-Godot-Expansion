# Game Development Guidelines (Godot & GDScript)

## Overview

This document establishes coding standards, architectural patterns, and development practices for game development using Godot Engine and GDScript. These guidelines ensure consistency, performance, and maintainability across all game development stories, whether creating 2D or 3D games.

## GDScript Standards

### Naming Conventions

**Classes and Scenes:**

- PascalCase for class names: `PlayerController`, `GameData`, `WeaponPickup`
- Scene files use PascalCase: `PlayerController.tscn`, `MainMenu.tscn`
- Script files match their scene or class name: `PlayerController.gd`

**Functions and Signals:**

- snake_case for functions and signals: `calculate_score()`, `player_died`, `weapon_pickup`
- Use descriptive verb phrases for functions: `activate_shield()` not `shield()`
- Use past tense for signals: `player_died`, `item_collected`, `level_completed`

**Variables and Properties:**

- snake_case for all variables: `player_health`, `movement_speed`, `is_alive`
- Boolean variables with descriptive prefixes: `is_alive`, `has_key`, `can_jump`
- Constants use ALL_CAPS: `MAX_HEALTH`, `GRAVITY_SCALE`, `GAME_VERSION`

**Node References:**

- Use descriptive names that indicate the node's purpose: `health_bar`, `jump_sound`, `collision_area`
- Group related nodes with prefixes: `ui_health_bar`, `sfx_jump_sound`

### Style and Formatting

- **Indentation**: Use tabs (Godot default)
- **Line Length**: Keep lines under 100 characters when possible
- **Spacing**: One space around operators, no space before colons in type hints
- **Comments**: Use `## ` for documentation comments, `# ` for inline comments

```gdscript
## This class handles player movement and input
class_name PlayerController
extends CharacterBody2D

const MAX_SPEED := 300.0
const JUMP_VELOCITY := -400.0

@onready var sprite: Sprite2D = $Sprite2D
@onready var animation_player: AnimationPlayer = $AnimationPlayer

var health: int = 100
var is_jumping: bool = false
```

## Godot Architecture Patterns

### Scene Structure and Node Organization

**Scene Lifecycle Management:**

```gdscript
# SceneManager.gd - Singleton for managing scene transitions
extends Node

signal scene_loaded(scene_name: String)

func change_scene_to(scene_path: String) -> void:
    # Use call_deferred to avoid issues with current frame processing
    call_deferred("_deferred_change_scene", scene_path)

func _deferred_change_scene(scene_path: String) -> void:
    var error = get_tree().change_scene_to_file(scene_path)
    if error != OK:
        push_error("Failed to change scene to: " + scene_path)
        return
    
    scene_loaded.emit(scene_path.get_file().get_basename())
```

### Node Lifecycle Management

**Understanding Core Node Events:**

```gdscript
# Example of standard Node lifecycle in Godot
# Use Node2D for 2D games, Node3D for 3D games, or Node for logic-only
extends Node

# _INIT: Constructor, called when the object is created
func _init():
    print("GameController initialized")

# _ENTER_TREE: Called when node enters scene tree (before _ready)
func _enter_tree():
    print("GameController entered tree")

# _READY: Called when node and children are ready
func _ready():
    print("GameController ready")
    # Best place for initial setup and connections

# _PROCESS: Called every frame
func _process(delta: float):
    # General game logic that needs to run every frame

# _PHYSICS_PROCESS: Called every physics frame (typically 60fps)
func _physics_process(delta: float):
    # Physics-related code like movement and collision

# _EXIT_TREE: Called when node is removed from scene tree
func _exit_tree():
    print("GameController exiting tree")
```

### Component-Based Architecture with Composition

**Scene Composition Pattern:**

```gdscript
# Player.gd - Main player scene controller
# Use CharacterBody2D for 2D, CharacterBody3D for 3D
extends CharacterBody3D

# Component references
@onready var health_component: HealthComponent = $HealthComponent
@onready var movement_component: MovementComponent = $MovementComponent
@onready var input_component: InputComponent = $InputComponent

func _ready():
    # Connect component signals
    health_component.health_depleted.connect(_on_health_depleted)
    input_component.jump_pressed.connect(_on_jump_pressed)

func _physics_process(delta: float):
    # Let components handle their own logic
    var input_vector = input_component.get_movement_input()
    movement_component.move(input_vector, delta)

func _on_health_depleted():
    # Handle player death
    queue_free()

# HealthComponent.gd - Reusable health system
extends Node
class_name HealthComponent

signal health_changed(new_health: int, max_health: int)
signal health_depleted

@export var max_health: int = 100
var current_health: int

func _ready():
    current_health = max_health

func take_damage(amount: int):
    current_health = max(0, current_health - amount)
    health_changed.emit(current_health, max_health)
    
    if current_health <= 0:
        health_depleted.emit()
```

### Data-Driven Design with Resources

**Custom Resource Classes:**

```gdscript
# WeaponData.gd - ScriptableObject equivalent
extends Resource
class_name WeaponData

@export var weapon_name: String
@export var damage: int
@export var fire_rate: float
@export var sprite: Texture2D
@export var sound: AudioStream

# Weapon.gd - Uses WeaponData resource
# Use Node2D/Sprite2D for 2D, Node3D/MeshInstance3D for 3D
extends Node3D

@export var weapon_data: WeaponData
@onready var mesh_instance: MeshInstance3D = $MeshInstance3D
@onready var audio_player: AudioStreamPlayer3D = $AudioStreamPlayer3D

func _ready():
    if weapon_data:
        setup_weapon()

func setup_weapon():
    # For 2D: sprite.texture = weapon_data.sprite
    # For 3D: mesh_instance.material_override.albedo_texture = weapon_data.sprite
    audio_player.stream = weapon_data.sound

func fire():
    if weapon_data:
        # Deal weapon_data.damage
        audio_player.play()
```

### System Management with Singletons

**Singleton Pattern (AutoLoad):**

```gdscript
# GameManager.gd - AutoLoad singleton
extends Node

signal score_changed(new_score: int)
signal game_over

var score: int = 0
var is_game_active: bool = false

func _ready():
    # Initialize game state
    process_mode = Node.PROCESS_MODE_ALWAYS

func add_score(points: int):
    score += points
    score_changed.emit(score)

func start_game():
    score = 0
    is_game_active = true

func end_game():
    is_game_active = false
    game_over.emit()
```

## Performance Optimization

### Object Pooling Pattern

**Required for High-Frequency Objects:**

```gdscript
# ObjectPool.gd - Generic pooling system
extends Node
class_name ObjectPool

@export var prefab_scene: PackedScene
@export var initial_pool_size: int = 20

var available_objects: Array[Node] = []
var active_objects: Array[Node] = []

func _ready():
    # Pre-populate the pool
    for i in initial_pool_size:
        var obj = prefab_scene.instantiate()
        obj.set_process(false)
        obj.visible = false
        available_objects.push_back(obj)
        add_child(obj)

func get_pooled_object() -> Node:
    var obj: Node
    
    if available_objects.size() > 0:
        obj = available_objects.pop_back()
    else:
        # Pool exhausted, create new object
        obj = prefab_scene.instantiate()
        add_child(obj)
    
    active_objects.push_back(obj)
    obj.set_process(true)
    obj.visible = true
    return obj

func return_to_pool(obj: Node):
    if obj in active_objects:
        active_objects.erase(obj)
        available_objects.push_back(obj)
        obj.set_process(false)
        obj.visible = false
```

### Frame Rate Optimization

**Process Loop Optimization:**

- Cache node references in `_ready()` using `@onready`
- Use `_physics_process()` for movement and physics
- Use `_process()` only when necessary, prefer signals for events
- Implement custom update timers for non-critical systems

```gdscript
# Optimized enemy AI with custom update timer
# Use CharacterBody2D for 2D, CharacterBody3D for 3D
extends CharacterBody3D

@onready var player_detector: Area3D = $PlayerDetector
@onready var ai_timer: Timer = $AITimer

var target_player: Node3D
var ai_update_interval: float = 0.1  # Update AI 10 times per second instead of 60

func _ready():
    ai_timer.wait_time = ai_update_interval
    ai_timer.timeout.connect(_update_ai)
    ai_timer.start()

func _update_ai():
    # AI logic runs only 10 times per second
    if target_player:
        # Calculate path to player
        pass

func _physics_process(delta: float):
    # Movement runs every physics frame for smooth motion
    move_and_slide()
```

## Input Handling

### Input Map and Actions

**Input Action Configuration:**

Create input actions in Project Settings > Input Map:
- `move_left`, `move_right`, `move_up`, `move_down` (2D) or `move_forward`, `move_back`, `move_left`, `move_right` (3D)
- `jump`, `attack`, `interact`
- `ui_accept`, `ui_cancel`, `pause`
- For 3D: `look_up`, `look_down`, `look_left`, `look_right` (camera control)

**Input Handling Patterns:**

```gdscript
# InputHandler.gd - Centralized input management
extends Node
class_name InputHandler

signal move_input_changed(direction: Vector2)
signal jump_pressed
signal attack_pressed

func _input(event: InputEvent):
    # Handle discrete actions
    if event.is_action_pressed("jump"):
        jump_pressed.emit()
    
    if event.is_action_pressed("attack"):
        attack_pressed.emit()

func _process(_delta: float):
    # Handle continuous input - adapt based on 2D or 3D
    # For 2D games:
    var move_direction_2d = Vector2(
        Input.get_axis("move_left", "move_right"),
        Input.get_axis("move_up", "move_down")
    )
    
    # For 3D games:
    var move_direction_3d = Vector3(
        Input.get_axis("move_left", "move_right"),
        0,
        Input.get_axis("move_forward", "move_back")
    )
    
    # Emit appropriate signal based on game type
    if move_direction_2d != Vector2.ZERO:
        move_input_changed.emit(move_direction_2d)
```

## Error Handling and Debugging

### Graceful Error Management

**Resource Loading with Fallbacks:**

```gdscript
func load_sprite_with_fallback(sprite_path: String) -> Texture2D:
    var sprite = load(sprite_path) as Texture2D
    
    if not sprite:
        push_warning("Failed to load sprite: " + sprite_path)
        # Load default sprite
        sprite = load("res://art/default_sprite.png") as Texture2D
        
        if not sprite:
            push_error("Critical: Default sprite missing!")
            return null
    
    return sprite
```

### Assertions and Validation

**Runtime Validation:**

```gdscript
func setup_weapon(weapon_resource: WeaponData):
    assert(weapon_resource != null, "WeaponData cannot be null")
    assert(weapon_resource.damage > 0, "Weapon damage must be positive")
    
    if not weapon_resource.sprite:
        push_warning("Weapon sprite is missing for: " + weapon_resource.weapon_name)
```

## Testing Standards

### Unit Testing with GUT

**Test Scene Setup:**

```gdscript
# test_player_health.gd - Example test using GUT framework
extends GutTest

var player_scene = preload("res://scenes/Player.tscn")
var player: Node

func before_each():
    player = player_scene.instantiate()
    add_child_autofree(player)

func test_player_takes_damage():
    # Arrange
    var initial_health = player.health_component.current_health
    
    # Act
    player.health_component.take_damage(20)
    
    # Assert
    assert_eq(player.health_component.current_health, initial_health - 20)

func test_player_dies_at_zero_health():
    # Arrange
    var health_component = player.health_component
    
    # Act
    health_component.take_damage(health_component.max_health)
    
    # Assert
    assert_signal_emitted(health_component, "health_depleted")
```

### Integration Testing

**Scene Testing:**

```gdscript
# test_game_scene_integration.gd
extends GutTest

var game_scene = preload("res://scenes/GameScene.tscn")
var game: Node

func before_each():
    game = game_scene.instantiate()
    add_child_autofree(game)

func test_player_enemy_collision():
    var player = game.get_node("Player")
    var enemy = game.get_node("Enemy")
    
    # Position enemy on player
    enemy.global_position = player.global_position
    
    # Wait for physics frame
    await get_tree().physics_frame
    
    # Assert collision detected
    assert_true(player.is_taking_damage)
```

## File Organization

### Project Structure

```
project/
├── scenes/
│   ├── main/
│   │   ├── MainMenu.tscn
│   │   └── MainMenu.gd
│   ├── game/
│   │   ├── GameScene.tscn
│   │   └── GameScene.gd
│   └── ui/
│       ├── HealthBar.tscn
│       └── HealthBar.gd
├── scripts/
│   ├── components/
│   │   ├── HealthComponent.gd
│   │   └── MovementComponent.gd
│   ├── managers/
│   │   ├── GameManager.gd
│   │   └── AudioManager.gd
│   └── data/
│       ├── WeaponData.gd
│       └── EnemyData.gd
├── art/
│   ├── 2d/              # For 2D games: sprites, textures, animations
│   │   ├── sprites/
│   │   └── textures/
│   ├── 3d/              # For 3D games: models, materials, textures
│   │   ├── models/
│   │   ├── materials/
│   │   └── textures/
│   └── shared/          # Shared assets (UI textures, etc.)
├── audio/
│   ├── music/
│   └── sfx/
├── data/
│   └── resources/
│       ├── weapons/
│       └── enemies/
└── tests/
    ├── unit/
    └── integration/
```

## Development Workflow

### Story Implementation Process

1. **Read Story Requirements:**
   - Understand acceptance criteria
   - Identify scene and node requirements
   - Review performance constraints

2. **Plan Implementation:**
   - Identify scenes to create/modify
   - Consider Godot's node-based architecture
   - Plan signal connections and data flow

3. **Implement Feature:**
   - Write clean GDScript following guidelines
   - Use established architectural patterns
   - Maintain stable frame rate performance

4. **Test Implementation:**
   - Write unit tests for game logic using GUT
   - Test scene interactions and signal flow
   - Validate performance on target devices

5. **Update Documentation:**
   - Mark story checkboxes complete
   - Document any architectural changes
   - Update node relationships if modified

### Code Review Checklist

- [ ] GDScript code runs without errors or warnings
- [ ] All tests pass using GUT framework
- [ ] Code follows naming conventions and patterns
- [ ] No expensive operations in `_process()` loops
- [ ] Signals are properly connected and disconnected
- [ ] Nodes are organized logically in scene tree

## Performance Targets

### Frame Rate Requirements

- **Desktop**: Maintain stable 60 FPS at 1080p
- **Mobile**: Maintain 60 FPS on mid-range devices, minimum 30 FPS on low-end
- **Web**: Maintain 30-60 FPS depending on browser performance

### Memory Management

- **Total Memory**: Keep projects under platform limits (mobile ~200MB for simple games)
- **Garbage Collection**: Minimize object creation in loops, use object pooling for frequent spawns
- **Texture Memory**: Use appropriate texture sizes and formats for target platforms

### Loading Performance

- **Scene Loading**: Under 3 seconds for scene transitions
- **Asset Streaming**: Use ResourcePreloader for critical assets
- **Background Loading**: Use ResourceLoader.load_threaded_request() for large assets

## Godot-Specific Best Practices

### Scene Organization

- Keep scenes focused on single responsibilities
- Use scene instancing for reusable components
- Organize nodes logically in scene tree hierarchy
- Use groups for categorizing similar nodes across scenes

### Signal Usage

- Prefer signals over direct references for decoupling
- Connect signals in `_ready()` and disconnect in `_exit_tree()`
- Use signal parameters to pass necessary data
- Document signal purposes with comments

### Resource Management

- Use custom Resource classes for data containers
- Preload critical resources in scenes
- Use ResourceLoader for dynamic loading
- Clean up resources properly in `_exit_tree()`

These guidelines ensure consistent, high-quality game development that meets performance targets and maintains code quality across all Godot implementation stories.