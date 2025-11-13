# Complete Roblox Studio & Luau Crash Course

<div align="center">
  <img src="./assets/roblox_studio_banner.png" alt="Project Banner" width="100%" style="max-width: 900px; border-radius: 8px;">
</div>

## Part 1: Game Development Basics (General Concepts)

### What is a Game Engine?

**A game engine handles:**

1. **Rendering** - Drawing graphics to screen (handled by Roblox)
2. **Physics** - Calculating movement, collisions, gravity (handled by Roblox)
3. **Input** - Detecting keyboard/mouse/touch (UserInputService)
4. **Audio** - Playing sounds (SoundService)
5. **Game Loop** - Running code every frame (RunService)

**In Roblox, the engine handles 1 & 2 automatically. You script 3, 4, & 5.**

### The Game Loop Concept

**Every game has a loop:**

```lua
-- Pseudo-code (not actual Roblox)
while game_is_running do
    handle_input()      -- Check what player pressed
    update_game_state() -- Move characters, check collisions
    render_graphics()   -- Draw frame (Roblox does this)
    wait_for_next_frame()
end
```

**In Roblox, you hook into this loop:**

```lua
local RunService = game:GetService("RunService")

-- This runs EVERY FRAME (60 times per second)
RunService.Heartbeat:Connect(function(deltaTime)
    -- deltaTime = time since last frame (usually ~0.016s for 60 FPS)
    updatePlayers(deltaTime)
    checkCollisions()
end)
```

### Client vs Server Architecture

**Roblox is multiplayer by default. Code runs in two places:**

| Server                                | Client                         |
| ------------------------------------- | ------------------------------ |
| One instance for entire game          | One instance per player        |
| Authoritative (what it says is truth) | Renders visuals, handles input |
| Handles game logic, damage, physics   | Predicts movement, shows VFX   |
| **Scripts** run here                  | **LocalScripts** run here      |

**Think of it like web dev:**

- **Server** = Node.js backend (authoritative, handles data)
- **Client** = Browser (renders UI, sends requests)

**Communication:**

- Client → Server: `RemoteEvent:FireServer()`
- Server → Client: `RemoteEvent:FireClient(player)`
- Server → All Clients: `RemoteEvent:FireAllClients()`

## Part 2: Roblox Studio Hierarchy & Objects

### The Game Tree (Like DOM in Web Dev)

```
game (DataModel)
├── Workspace (3D world players see)
│   ├── Baseplate (Part)
│   ├── Camera (Camera)
│   └── Terrain (Terrain)
├── Players (Container for all players)
│   └── PlayerName (Player)
│       └── Character (Model)
│           ├── HumanoidRootPart (Part)
│           ├── Humanoid (Humanoid)
│           └── Head, Torso, Arms, Legs (Parts)
├── ReplicatedStorage (Shared between server & client)
│   ├── Modules (Folder)
│   └── RemoteEvents (Folder)
├── ReplicatedFirst (Loads before anything else)
├── ServerScriptService (Server-only scripts)
│   └── MainScript (Script)
├── ServerStorage (Server-only assets, not replicated)
├── StarterGui (UI templates, copied to PlayerGui)
│   └── ScreenGui
│       └── Frame, TextLabel, etc.
├── StarterPlayer (Player templates)
│   ├── StarterCharacterScripts (Scripts in every character)
│   └── StarterPlayerScripts (Scripts in every player)
├── StarterPack (Tools given to players on spawn)
├── Lighting (Visual settings: skybox, time, fog)
├── SoundService (Global audio settings)
├── Teams (Team definitions)
└── TweenService, RunService, etc. (Services)
```

### Key Containers Explained

#### **Workspace**

- The 3D world players see and interact with
- Contains all physical objects (Parts, Models, NPCs)
- Client AND server can see it (replicated)

```lua
local part = Instance.new("Part")
part.Position = Vector3.new(0, 10, 0)
part.Parent = workspace  -- Now visible in game
```

#### **ReplicatedStorage**

- Shared assets accessible by both server and client
- Use for: Modules, RemoteEvents, shared data
- Does NOT automatically appear in Workspace (just storage)

```lua
-- Server script
local module = require(game.ReplicatedStorage.CombatSystem)

-- Client script (same module, works on both)
local module = require(game.ReplicatedStorage.CombatSystem)
```

**Why use it:** Avoid duplicating code. One module, usable everywhere.

#### **ServerScriptService**

- Server-only scripts (clients can't see or access)
- Use for: Game logic, admin commands, anti-cheat

```lua
-- This runs on SERVER only
-- Clients cannot see this code (security)
local Players = game:GetService("Players")

Players.PlayerAdded:Connect(function(player)
    print(player.Name .. " joined")
    -- Give player starting items, load data, etc.
end)
```

#### **ServerStorage**

- Server-only assets (models, tools, etc.)
- Clients can't access (unlike ReplicatedStorage)
- Use for: Admin tools, rare items, things you don't want players seeing

#### **StarterGui**

- UI templates copied to each player's `PlayerGui` on join
- Use for: Health bars, hotbars, menus

```lua
-- StarterGui structure:
StarterGui
└── ScreenGui (container)
    ├── Frame (rectangle UI element)
    │   └── TextLabel (text display)
    └── TextButton (clickable button)
```

**When player joins:** StarterGui contents → Copied to `player.PlayerGui`

#### **StarterPlayer**

- Templates for player characters and scripts

**StarterCharacterScripts:**

- Scripts placed here run inside every character
- Use for: Custom movement, camera effects, footstep sounds

**StarterPlayerScripts:**

- Scripts placed here run for every player (not per character)
- Use for: Input handling, UI controllers

### Script Types

| Script Type      | Runs On              | Where to Place                       | Use For                         |
| ---------------- | -------------------- | ------------------------------------ | ------------------------------- |
| **Script**       | Server               | ServerScriptService, Workspace       | Game logic, damage, spawning    |
| **LocalScript**  | Client               | StarterPlayer, StarterGui, character | Input, UI, VFX, camera          |
| **ModuleScript** | Both (when required) | ReplicatedStorage                    | Shared code, classes, utilities |

**Key difference:**

- `Script` = Server-side (authoritative, secure)
- `LocalScript` = Client-side (fast, responsive, per-player)
- `ModuleScript` = Reusable library (doesn't run on its own)

### Instance Hierarchy (Parents & Children)

**Everything in Roblox is an Instance (like DOM nodes).**

```lua
-- Create object
local part = Instance.new("Part")
part.Name = "MyPart"
part.Size = Vector3.new(10, 1, 10)

-- Set parent (makes it exist in game)
part.Parent = workspace

-- Access children
for _, child in pairs(workspace:GetChildren()) do
    print(child.Name)
end

-- Find specific child
local myPart = workspace:FindFirstChild("MyPart")

-- Find descendant (searches recursively)
local hrp = workspace:FindFirstChild("Player").HumanoidRootPart

-- Check if object exists
if workspace:FindFirstChild("MyPart") then
    print("Part exists!")
end
```

**Parent/Child relationships:**

- Setting `.Parent` makes object appear in game
- Removing parent (`part.Parent = nil`) removes from game
- `GetChildren()` = direct children only
- `GetDescendants()` = all children recursively

## Part 3: Lighting & Visual Services

### Lighting Service

**Controls global visuals: time of day, fog, skybox, color correction.**

```lua
local Lighting = game:GetService("Lighting")

-- Time of day (0-24 hour format)
Lighting.ClockTime = 14  -- 2 PM (afternoon sun)
Lighting.TimeOfDay = "06:30:00"  -- 6:30 AM (sunrise)

-- Fog (distance-based visibility)
Lighting.FogEnd = 500  -- Fog fully opaque at 500 studs
Lighting.FogStart = 100  -- Fog starts at 100 studs
Lighting.FogColor = Color3.fromRGB(200, 200, 200)  -- Gray fog

-- Ambient (global light color)
Lighting.Ambient = Color3.fromRGB(100, 100, 100)  -- Slight gray tint
Lighting.OutdoorAmbient = Color3.fromRGB(150, 150, 150)  -- Outdoor light

-- Brightness
Lighting.Brightness = 2  -- Global brightness multiplier

-- Skybox (custom sky texture)
local sky = Instance.new("Sky")
sky.SkyboxBk = "rbxassetid://12345"  -- Back texture
sky.SkyboxFt = "rbxassetid://12346"  -- Front texture
-- ... (6 sides total)
sky.Parent = Lighting
```

### When to Manipulate Lighting

**Use cases:**

1. **Day/Night Cycle**

```lua
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")

RunService.Heartbeat:Connect(function(deltaTime)
    -- Increment time by 1 minute every second
    Lighting.ClockTime = Lighting.ClockTime + (deltaTime / 60)

    if Lighting.ClockTime >= 24 then
        Lighting.ClockTime = 0  -- Reset to midnight
    end
end)
```

2. **Ability Effects (Dark Screen)**

```lua
-- When ability activates, darken screen
local ColorCorrection = Instance.new("ColorCorrectionEffect")
ColorCorrection.Brightness = -0.5  -- Darken
ColorCorrection.Saturation = -0.5  -- Desaturate
ColorCorrection.Parent = Lighting

task.wait(2)  -- Effect lasts 2 seconds
ColorCorrection:Destroy()
```

3. **Atmosphere Changes**

```lua
-- Underground cave: dark, foggy
Lighting.ClockTime = 0  -- Night
Lighting.FogEnd = 50  -- Thick fog
Lighting.Ambient = Color3.fromRGB(50, 50, 70)  -- Blue tint

-- Back to surface
Lighting.ClockTime = 12  -- Noon
Lighting.FogEnd = 1000  -- Clear
Lighting.Ambient = Color3.fromRGB(128, 128, 128)  -- Neutral
```

### Post-Processing Effects (Inside Lighting)

**These are effects added to Lighting as children:**

```lua
-- Blur (depth of field)
local Blur = Instance.new("BlurEffect")
Blur.Size = 10  -- Blur intensity
Blur.Parent = Lighting

-- Color Correction (adjust colors globally)
local CC = Instance.new("ColorCorrectionEffect")
CC.Brightness = 0.1  -- Slightly brighter
CC.Contrast = 0.2  -- More contrast
CC.Saturation = -0.1  -- Slightly desaturated
CC.TintColor = Color3.fromRGB(255, 200, 200)  -- Red tint
CC.Parent = Lighting

-- Bloom (glow effect)
local Bloom = Instance.new("BloomEffect")
Bloom.Intensity = 0.5  -- Glow strength
Bloom.Size = 24  -- Glow radius
Bloom.Threshold = 0.8  -- Only bright areas glow
Bloom.Parent = Lighting

-- Sunrays (god rays)
local Sunrays = Instance.new("SunRaysEffect")
Sunrays.Intensity = 0.2  -- Ray visibility
Sunrays.Spread = 0.1  -- Ray spread angle
Sunrays.Parent = Lighting
```

## Part 4: Luau-Specific Features

### What is Luau?

**Luau = Roblox's fork of Lua 5.1 with:**

- Type annotations (optional, like TypeScript)
- Performance improvements (~2x faster)
- New syntax features

**You don't NEED to use Luau-specific features, but they're helpful.**

### Type Annotations (Optional but Recommended)

```lua
-- Basic types
local name: string = "Adi"
local age: number = 25
local isActive: boolean = true

-- Function types
local function add(a: number, b: number): number
    return a + b
end

-- Table types
type Player = {
    name: string,
    health: number,
    isAlive: boolean
}

local player: Player = {
    name = "Adi",
    health = 100,
    isAlive = true
}

-- Function parameter types
local function healPlayer(player: Player, amount: number): ()
    player.health = player.health + amount
end
```

**Why use types:**

- Catches bugs early (type mismatches)
- Better autocomplete in Studio
- Self-documenting code

### Generics (Advanced)

```lua
-- Generic function (works with any type)
local function first<T>(array: {T}): T
    return array[1]
end

local numbers = {1, 2, 3}
local firstNum = first(numbers)  -- Type is 'number'

local names = {"Adi", "Bob"}
local firstName = first(names)  -- Type is 'string'
```

### String Interpolation (Luau-specific)

```lua
-- Old way (Lua)
local message = "Hello, " .. name .. "! You have " .. tostring(score) .. " points."

-- New way (Luau)
local message = `Hello, {name}! You have {score} points.`

-- Works with expressions
local message = `Your health: {player.health}/{player.maxHealth}`
```

## Part 5: Vector Mathematics (Game Dev Essential)

### What Are Vectors (Practical Definition)

**Forget "quantities with direction." Here's what vectors ACTUALLY are:**

**A Vector is a set of coordinates (X, Y, Z) that represents:**

1. **Position** - Where something is in 3D space
2. **Direction** - Which way something is pointing
3. **Velocity** - How fast and in what direction something is moving
4. **Force** - A push or pull in a direction

### Vector3 (3D Coordinates)

```lua
-- Create vector
local position = Vector3.new(10, 5, 20)  -- X=10, Y=5, Z=20

-- Access components
print(position.X)  -- 10
print(position.Y)  -- 5
print(position.Z)  -- 20

-- Common vectors
local zero = Vector3.zero  -- (0, 0, 0)
local up = Vector3.new(0, 1, 0)  -- Points upward
local forward = Vector3.new(0, 0, -1)  -- Points forward in Roblox
local right = Vector3.new(1, 0, 0)  -- Points right
```

### Vector Math (The Useful Stuff)

#### 1. Addition (Moving Objects)

```lua
-- Current position + offset = new position
local currentPos = Vector3.new(10, 5, 0)
local moveRight = Vector3.new(5, 0, 0)
local newPos = currentPos + moveRight  -- (15, 5, 0)

part.Position = part.Position + Vector3.new(0, 10, 0)  -- Move up 10 studs
```

**Use case:** Moving objects by offset

#### 2. Subtraction (Getting Direction Between Points)

```lua
local playerPos = Vector3.new(10, 0, 10)
local targetPos = Vector3.new(20, 0, 30)

-- Direction FROM player TO target
local direction = targetPos - playerPos  -- (10, 0, 20)
```

**Use case:** Aiming projectiles, making NPCs face player

#### 3. Multiplication (Scaling)

```lua
local velocity = Vector3.new(1, 0, 0)  -- 1 stud/second to the right
local fastVelocity = velocity * 10  -- 10 studs/second

part.AssemblyLinearVelocity = Vector3.new(0, 50, 0)  -- Launch upward
```

**Use case:** Adjusting speed, force strength

#### 4. Magnitude (Distance/Length)

```lua
local vector = Vector3.new(3, 4, 0)
local length = vector.Magnitude  -- sqrt(3² + 4²) = 5

-- Distance between two points
local distance = (playerPos - targetPos).Magnitude
print("Target is " .. distance .. " studs away")
```

**Use case:** Checking if player is in range, measuring distances

#### 5. Unit (Direction Without Length)

```lua
local direction = Vector3.new(10, 0, 20)  -- Has length
local unitDirection = direction.Unit  -- Same direction, length = 1

-- Now you can scale it to any speed
local speed = 50
local velocity = unitDirection * speed  -- Move 50 studs/sec in that direction
```

**Use case:** Projectile direction, movement direction

#### 6. Dot Product (How "Aligned" Two Directions Are)

```lua
local forward = Vector3.new(0, 0, -1)
local playerFacing = character.HumanoidRootPart.CFrame.LookVector

local dot = forward:Dot(playerFacing)
-- dot = 1: Facing same direction
-- dot = 0: Perpendicular
-- dot = -1: Opposite directions

if dot > 0.5 then
    print("Player is facing forward")
end
```

**Use case:** Checking if player is facing target, cone-based detection

#### 7. Cross Product (Perpendicular Direction)

```lua
local up = Vector3.new(0, 1, 0)
local forward = Vector3.new(0, 0, -1)
local right = forward:Cross(up)  -- (1, 0, 0)

-- Get direction to the right of player
local playerRight = character.HumanoidRootPart.CFrame.RightVector
```

**Use case:** Side-stepping, orbiting around objects

### Vector2 (2D Coordinates)

**Used for:** UI positions, screen space, 2D math

```lua
local screenPos = Vector2.new(100, 200)  -- X=100 pixels, Y=200 pixels

-- UI positioning
local frame = Instance.new("Frame")
frame.Position = UDim2.new(0, 100, 0, 200)  -- (Scale, Offset, Scale, Offset)
-- UDim2 = 2D position with scale + pixel offset
```

### CFrame (Coordinate Frame = Position + Rotation)

**CFrame = Vector3 (position) + rotation**

```lua
-- Position only
local cf = CFrame.new(10, 5, 0)  -- At position (10, 5, 0), no rotation

-- Position + look direction
local cf = CFrame.new(position, targetPosition)  -- Face toward target

-- Rotation
local cf = CFrame.Angles(math.rad(45), 0, 0)  -- Rotate 45° around X axis

-- Get components
local position = cf.Position  -- Vector3
local lookVector = cf.LookVector  -- Where it's facing (Vector3)
local rightVector = cf.RightVector
local upVector = cf.UpVector
```

### Practical Vector Examples

#### Example 1: Launch Player Upward

```lua
local character = player.Character
local hrp = character.HumanoidRootPart

-- Apply upward force
hrp.AssemblyLinearVelocity = Vector3.new(0, 50, 0)  -- 50 studs/sec up
```

#### Example 2: Move Toward Target

```lua
local playerPos = character.HumanoidRootPart.Position
local targetPos = workspace.Target.Position

-- Get direction (unit vector)
local direction = (targetPos - playerPos).Unit

-- Move at 20 studs/second toward target
local speed = 20
character.HumanoidRootPart.AssemblyLinearVelocity = direction * speed
```

#### Example 3: Check if Player is in Range

```lua
local enemyPos = workspace.Enemy.Position
local playerPos = character.HumanoidRootPart.Position

local distance = (enemyPos - playerPos).Magnitude

if distance < 10 then
    print("Enemy is within 10 studs!")
    attack(enemy)
end
```

#### Example 4: Dash Forward

```lua
local character = player.Character
local hrp = character.HumanoidRootPart

-- Get direction player is facing
local forwardDirection = hrp.CFrame.LookVector

-- Dash 50 studs forward
local dashDistance = 50
hrp.CFrame = hrp.CFrame + (forwardDirection * dashDistance)
```

#### Example 5: Knockback

```lua
-- When player gets hit
local attackerPos = attacker.HumanoidRootPart.Position
local victimPos = victim.HumanoidRootPart.Position

-- Direction from attacker to victim
local knockbackDir = (victimPos - attackerPos).Unit

-- Apply force
local knockbackStrength = 30
victim.HumanoidRootPart.AssemblyLinearVelocity = knockbackDir * knockbackStrength + Vector3.new(0, 10, 0)  -- Up + away
```

## Part 6: Common Services & Their Uses

### RunService (Game Loop)

```lua
local RunService = game:GetService("RunService")

-- Runs every frame (60 FPS)
RunService.Heartbeat:Connect(function(deltaTime)
    -- deltaTime = seconds since last frame (~0.016 for 60 FPS)
    updateGame(deltaTime)
end)

-- Server-only loop
if RunService:IsServer() then
    RunService.Heartbeat:Connect(function()
        -- Server logic
    end)
end

-- Client-only loop
if RunService:IsClient() then
    RunService.RenderStepped:Connect(function()
        -- Client-side (runs before rendering, good for camera)
    end)
end
```

### TweenService (Smooth Animations)

```lua
local TweenService = game:GetService("TweenService")

local part = workspace.Part
local goal = {Position = Vector3.new(0, 10, 0), Transparency = 0.5}
local info = TweenInfo.new(
    2,  -- Duration (seconds)
    Enum.EasingStyle.Bounce,  -- Easing style
    Enum.EasingDirection.Out  -- Easing direction
)

local tween = TweenService:Create(part, info, goal)
tween:Play()

tween.Completed:Connect(function()
    print("Tween finished!")
end)
```

**Easing styles:** Linear, Sine, Quad, Cubic, Bounce, Elastic, etc.

### UserInputService (Input Handling)

```lua
local UIS = game:GetService("UserInputService")

-- Detect key press
UIS.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end  -- Ignore if typing in chat

    if input.KeyCode == Enum.KeyCode.Space then
        print("Space pressed!")
        jump()
    end

    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        print("Left click!")
        attack()
    end
end)

-- Detect key release
UIS.InputEnded:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.W then
        print("Stopped moving forward")
    end
end)

-- Mouse position
local mousePos = UIS:GetMouseLocation()  -- Vector2
```

### Players Service

```lua
local Players = game:GetService("Players")

-- When player joins
Players.PlayerAdded:Connect(function(player)
    print(player.Name .. " joined")

    -- When their character spawns
    player.CharacterAdded:Connect(function(character)
        print(player.Name .. "'s character spawned")

        local humanoid = character:WaitForChild("Humanoid")
        humanoid.Died:Connect(function()
            print(player.Name .. " died")
        end)
    end)
end)

-- When player leaves
Players.PlayerRemoving:Connect(function(player)
    print(player.Name .. " left")
    savePlayerData(player)
end)

-- Get all players
for _, player in pairs(Players:GetPlayers()) do
    print(player.Name)
end
```

### Debris Service (Auto-Cleanup)

```lua
local Debris = game:GetService("Debris")

-- Create temporary part
local explosion = Instance.new("Part")
explosion.Position = Vector3.new(0, 10, 0)
explosion.Parent = workspace

-- Auto-delete after 5 seconds
Debris:AddItem(explosion, 5)
```

**Use case:** VFX particles, temporary objects, projectiles

## Part 7: Practical Patterns

### Remote Events (Client-Server Communication)

```lua
-- In ReplicatedStorage, create RemoteEvent named "DamageEvent"

-- SERVER SCRIPT (ServerScriptService)
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local damageEvent = ReplicatedStorage:WaitForChild("DamageEvent")

damageEvent.OnServerEvent:Connect(function(player, target, damage)
    -- Validate (prevent exploits)
    if not target or not target:FindFirstChild("Humanoid") then return end

    local distance = (player.Character.HumanoidRootPart.Position - target.HumanoidRootPart.Position).Magnitude
    if distance > 10 then return end  -- Too far

    -- Apply damage
    target.Humanoid.Health -= damage
end)

-- CLIENT SCRIPT (LocalScript)
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local damageEvent = ReplicatedStorage:WaitForChild("DamageEvent")

-- When player presses button
damageEvent:FireServer(targetPlayer, 20)  -- Request server to deal 20 damage
```

### Module Pattern (Reusable Code)

```lua
-- ReplicatedStorage/CombatSystem (ModuleScript)
local CombatSystem = {}

function CombatSystem:dealDamage(target, amount)
    if not target or not target:FindFirstChild("Humanoid") then return end
    target.Humanoid.Health -= amount
end

function CombatSystem:heal(target, amount)
    if not target or not target:FindFirstChild("Humanoid") then return end
    target.Humanoid.Health = math.min(target.Humanoid.Health + amount, target.Humanoid.MaxHealth)
end

return CombatSystem

-- Usage in any script
local CombatSystem = require(game.ReplicatedStorage.CombatSystem)
CombatSystem:dealDamage(player.Character, 10)
```

### Raycasting (Line-of-Sight Detection)

```lua
local function checkLineOfSight(origin, target)
    local direction = (target - origin).Unit * 100  -- Ray length 100 studs

    local rayParams = RaycastParams.new()
    rayParams.FilterType = Enum.RaycastFilterType.Exclude
    rayParams.FilterDescendantsInstances = {workspace.Camera}  -- Ignore camera

    local result = workspace:Raycast(origin, direction, rayParams)

    if result then
        print("Hit:", result.Instance.Name)
        print("Position:", result.Position)
        return result.Instance
    else
        print("Hit nothing")
        return nil
    end
end

-- Usage: Check if player can see target
local playerPos = player.Character.HumanoidRootPart.Position
local targetPos = workspace.Target.Position
local hitObject = checkLineOfSight(playerPos, targetPos)
```

## Part 8: Quick Reference

### Common Properties

````lua
-- Part properties
part.Position = Vector3.new(0, 10, 0)
part.Size = Vector3.new(10, 1, 10)
part.Transparency = 0.5  -- 0 = opaque, 1 = invisible
part.CanCollide = false
part.Anchored = true  -- Fixed in place
part.Material = Enum.Material.Neon
part.BrickColor = BrickColor.new("Bright red")
part.Color = Color3.fromRGB(255, 0, 0)

-- Humanoid properties
humanoid.Health = 100
humanoid.MaxHealth = 100
humanoid.WalkSpeed = 16  -- Studs per second
humanoid.JumpPower = 50  -- Or JumpHeight depending on mode
```lua
humanoid.Died:Connect(function()
    print("Character died")
end)

-- Character properties
local character = player.Character
local hrp = character:WaitForChild("HumanoidRootPart")
hrp.CFrame = CFrame.new(0, 10, 0)  -- Teleport
hrp.AssemblyLinearVelocity = Vector3.new(0, 50, 0)  -- Set velocity

-- UI properties
frame.Size = UDim2.new(0.5, 0, 0.5, 0)  -- 50% width, 50% height
frame.Position = UDim2.new(0.25, 0, 0.25, 0)  -- Centered
frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
frame.BackgroundTransparency = 0  -- 0 = opaque, 1 = invisible
frame.Visible = true
````

### Common Methods

```lua
-- Instance methods
Instance.new("Part")  -- Create new object
object:Clone()  -- Duplicate object
object:Destroy()  -- Delete object
object:FindFirstChild("Name")  -- Find direct child
object:FindFirstChildOfClass("Part")  -- Find by class
object:GetChildren()  -- Array of direct children
object:GetDescendants()  -- Array of all descendants (recursive)
object:IsA("Part")  -- Check if object is of type
object:WaitForChild("Name", timeout)  -- Wait for child to exist

-- Math functions
math.random(1, 10)  -- Random integer 1-10
math.random()  -- Random float 0-1
math.floor(3.7)  -- 3
math.ceil(3.2)  -- 4
math.clamp(value, min, max)  -- Constrain value
math.rad(degrees)  -- Convert degrees to radians
math.deg(radians)  -- Convert radians to degrees

-- String methods
string.upper("hello")  -- "HELLO"
string.lower("HELLO")  -- "hello"
string.sub("hello", 2, 4)  -- "ell" (1-indexed)
string.find("hello world", "world")  -- 7, 11 (start, end)
string.gsub("hello", "l", "x")  -- "hexxo" (replace all)
string.format("Hello %s, you are %d years old", name, age)

-- Table methods
table.insert(array, value)  -- Add to end
table.insert(array, index, value)  -- Insert at index
table.remove(array, index)  -- Remove at index
table.sort(array)  -- Sort in place
table.concat(array, ", ")  -- Join into string
table.find(array, value)  -- Find index of value (Luau-specific)
table.clear(array)  -- Remove all elements (Luau-specific)
```

## Part 9: Advanced Concepts

### Attributes (Custom Properties)

**Attributes = custom metadata you can attach to any Instance**

```lua
-- Set attribute
part:SetAttribute("Health", 100)
part:SetAttribute("IsDestructible", true)
part:SetAttribute("Owner", "Adi")

-- Get attribute
local health = part:GetAttribute("Health")  -- 100
local isDestructible = part:GetAttribute("IsDestructible")  -- true

-- Listen for attribute changes
part:GetAttributeChangedSignal("Health"):Connect(function()
    local newHealth = part:GetAttribute("Health")
    print("Health changed to:", newHealth)
end)

-- Use case: Tag destructible objects
for _, obj in pairs(workspace:GetDescendants()) do
    if obj:IsA("Part") and obj:GetAttribute("Destructible") then
        makeDestructible(obj)
    end
end
```

**Why use attributes:**

- No need to add IntValue/StringValue children
- Faster and cleaner than using child objects
- Can be set in Studio properties panel

### Tags (CollectionService)

**Tags = label objects for easy grouping/filtering**

```lua
local CollectionService = game:GetService("CollectionService")

-- Add tag to object
CollectionService:AddTag(part, "Breakable")
CollectionService:AddTag(part, "Explosive")

-- Get all objects with tag
local breakables = CollectionService:GetTagged("Breakable")
for _, obj in pairs(breakables) do
    print(obj.Name .. " is breakable")
end

-- Listen for new tagged objects
CollectionService:GetInstanceAddedSignal("Breakable"):Connect(function(obj)
    print("New breakable object:", obj.Name)
    setupBreakable(obj)
end)

-- Check if object has tag
if CollectionService:HasTag(part, "Explosive") then
    print("This part explodes!")
end

-- Remove tag
CollectionService:RemoveTag(part, "Breakable")
```

**Use case:** Mark all destructible buildings, enemies, collectibles, etc.

### BindableEvents (Internal Communication)

**BindableEvents = events between scripts on SAME side (server-to-server or client-to-client)**

```lua
-- Create BindableEvent in ReplicatedStorage
local event = Instance.new("BindableEvent")
event.Name = "OnPlayerDamaged"
event.Parent = game.ReplicatedStorage

-- Script A: Fire event
event:Fire({player = player, damage = 20, attacker = attacker})

-- Script B: Listen for event
event.Event:Connect(function(data)
    print(data.player.Name .. " took " .. data.damage .. " damage")
    updateHealthBar(data.player)
end)
```

**Difference from RemoteEvent:**

- RemoteEvent = Client ↔ Server
- BindableEvent = Server ↔ Server OR Client ↔ Client (same side only)

### Context Action Service (Input Binding)

**Better than UserInputService for binding actions to multiple inputs**

```lua
local CAS = game:GetService("ContextActionService")

-- Bind action to multiple keys
local function onDash(actionName, inputState, inputObject)
    if inputState == Enum.UserInputState.Begin then
        print("Dash!")
        performDash()
    end
end

CAS:BindAction(
    "Dash",  -- Action name
    onDash,  -- Function
    true,  -- Create mobile button?
    Enum.KeyCode.Q,  -- Keyboard
    Enum.KeyCode.ButtonX  -- Gamepad
)

-- Unbind action
CAS:UnbindAction("Dash")

-- Use case: Mobile + PC support with one code
```

### Datastore (Save Player Data)

```lua
local DataStoreService = game:GetService("DataStoreService")
local playerData = DataStoreService:GetDataStore("PlayerData")

-- Save data
local function saveData(player)
    local success, err = pcall(function()
        local data = {
            level = player.leaderstats.Level.Value,
            coins = player.leaderstats.Coins.Value
        }
        playerData:SetAsync(player.UserId, data)
    end)

    if success then
        print("Data saved for", player.Name)
    else
        warn("Failed to save data:", err)
    end
end

-- Load data
local function loadData(player)
    local success, data = pcall(function()
        return playerData:GetAsync(player.UserId)
    end)

    if success and data then
        player.leaderstats.Level.Value = data.level
        player.leaderstats.Coins.Value = data.coins
    else
        -- Default values for new player
        player.leaderstats.Level.Value = 1
        player.leaderstats.Coins.Value = 0
    end
end

-- Use on player join/leave
Players.PlayerAdded:Connect(loadData)
Players.PlayerRemoving:Connect(saveData)
```

**Important:**

- Always use `pcall()` (protected call) for DataStore operations
- Use `player.UserId` as key (not Name, names can change)
- DataStores have rate limits (60 requests/minute per player)

### Sound Management

```lua
-- Create sound
local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://123456789"
sound.Volume = 0.5  -- 0-1 (or higher for louder)
sound.PlaybackSpeed = 1  -- 1 = normal, 2 = double speed
sound.Looped = false
sound.Parent = workspace  -- Or character part for 3D positional audio

-- Play sound
sound:Play()

-- Stop sound
sound:Stop()

-- Pause/Resume
sound:Pause()
sound:Resume()

-- Wait for sound to finish
sound.Ended:Connect(function()
    print("Sound finished")
    sound:Destroy()
end)

-- 3D positional audio (gets quieter with distance)
sound.Parent = character.HumanoidRootPart  -- Attach to character
sound.RollOffMaxDistance = 100  -- Max hearing distance (studs)
sound.RollOffMinDistance = 10  -- Min distance (full volume)

-- 2D audio (same volume everywhere)
sound.Parent = game.SoundService
```

### Particle Emitters (VFX)

```lua
-- Create particle emitter
local particles = Instance.new("ParticleEmitter")
particles.Parent = part

-- Appearance
particles.Texture = "rbxassetid://123456"  -- Particle image
particles.Color = ColorSequence.new(Color3.fromRGB(255, 0, 0))  -- Red
particles.Size = NumberSequence.new(1)  -- Size over lifetime
particles.Transparency = NumberSequence.new({
    NumberSequenceKeypoint.new(0, 0),  -- Start: opaque
    NumberSequenceKeypoint.new(1, 1)   -- End: invisible
})

-- Emission
particles.Rate = 20  -- Particles per second
particles.Lifetime = NumberRange.new(1, 2)  -- Live 1-2 seconds
particles.Speed = NumberRange.new(5, 10)  -- Initial speed

-- Physics
particles.Acceleration = Vector3.new(0, -10, 0)  -- Gravity
particles.Drag = 1  -- Air resistance
particles.VelocityInheritance = 0  -- Inherit parent velocity?

-- Spread
particles.SpreadAngle = Vector2.new(45, 45)  -- Cone angle

-- Enable/Disable
particles.Enabled = true
particles.Enabled = false

-- Burst (one-time emission)
particles:Emit(50)  -- Emit 50 particles at once
```

**Common particle effects:**

```lua
-- Explosion
local explosion = Instance.new("ParticleEmitter")
explosion.Texture = "rbxasset://textures/particles/smoke_main.dds"
explosion.Rate = 0  -- Don't constantly emit
explosion.Lifetime = NumberRange.new(0.5, 1)
explosion.Speed = NumberRange.new(20, 40)
explosion.Parent = part
explosion:Emit(100)  -- Single burst
task.wait(2)
explosion:Destroy()

-- Trail effect (behind moving object)
local trail = Instance.new("Trail")
trail.Attachment0 = attachment0  -- Front of object
trail.Attachment1 = attachment1  -- Back of object
trail.Lifetime = 0.5
trail.Color = ColorSequence.new(Color3.fromRGB(255, 255, 0))
trail.Parent = part
```

### Beams (Laser Effects)

```lua
-- Create attachments (beam connects two points)
local attach0 = Instance.new("Attachment")
attach0.Parent = part1

local attach1 = Instance.new("Attachment")
attach1.Parent = part2

-- Create beam
local beam = Instance.new("Beam")
beam.Attachment0 = attach0
beam.Attachment1 = attach1
beam.Color = ColorSequence.new(Color3.fromRGB(0, 255, 0))  -- Green
beam.Transparency = NumberSequence.new(0.5)  -- Semi-transparent
beam.Width0 = 2  -- Width at start
beam.Width1 = 2  -- Width at end
beam.Texture = "rbxasset://textures/beam.png"
beam.TextureSpeed = 1  -- Animated texture
beam.Parent = part1

-- Use case: Laser beams, electricity, ability connections
```

## Part 10: Performance & Best Practices

### Do's and Don'ts

**✅ DO:**

```lua
-- Use local variables (faster lookup)
local Players = game:GetService("Players")
local workspace = game:GetService("Workspace")

-- Cache frequently accessed objects
local hrp = character:WaitForChild("HumanoidRootPart")
for i = 1, 100 do
    hrp.Position = hrp.Position + Vector3.new(0, 0.1, 0)
end

-- Use task.wait() instead of wait()
task.wait(1)

-- Disconnect events when done
local connection = part.Touched:Connect(function() end)
connection:Disconnect()

-- Use Debris for auto-cleanup
game:GetService("Debris"):AddItem(part, 5)
```

**❌ DON'T:**

```lua
-- Don't use global variables (slow)
myGlobal = 5  -- BAD

-- Don't create parts every frame
RunService.Heartbeat:Connect(function()
    local part = Instance.new("Part")  -- BAD: Creates 60 parts/second
    part.Parent = workspace
end)

-- Don't use wait() in loops without task.wait()
for i = 1, 100 do
    wait(0.1)  -- BAD: Old, less accurate
end

-- Don't forget to disconnect events
part.Touched:Connect(function()
    -- If part gets deleted, this event still exists (memory leak)
end)

-- Don't do heavy calculations every frame
RunService.Heartbeat:Connect(function()
    -- Find all parts in workspace every frame? NO!
    for _, part in pairs(workspace:GetDescendants()) do
        -- This is VERY expensive
    end
end)
```

### Object Pooling (Reuse Objects)

**Instead of creating/destroying objects constantly, reuse them:**

```lua
local BulletPool = {}
BulletPool.Active = {}
BulletPool.Inactive = {}

function BulletPool:Get()
    local bullet
    if #self.Inactive > 0 then
        -- Reuse existing bullet
        bullet = table.remove(self.Inactive)
    else
        -- Create new bullet
        bullet = Instance.new("Part")
        bullet.Size = Vector3.new(1, 1, 2)
        bullet.Material = Enum.Material.Neon
    end

    table.insert(self.Active, bullet)
    bullet.Parent = workspace
    return bullet
end

function BulletPool:Return(bullet)
    -- Remove from active
    local index = table.find(self.Active, bullet)
    if index then
        table.remove(self.Active, index)
    end

    -- Add to inactive (for reuse)
    bullet.Parent = nil
    table.insert(self.Inactive, bullet)
end

-- Usage
local bullet = BulletPool:Get()
bullet.Position = gun.Position
-- ... bullet logic ...
task.wait(5)
BulletPool:Return(bullet)  -- Return to pool instead of destroying
```

### Reduce Network Traffic

**Only replicate what's necessary:**

```lua
-- BAD: Sending position every frame (client to server)
RunService.Heartbeat:Connect(function()
    remoteEvent:FireServer(character.HumanoidRootPart.Position)
    -- Sends 60 updates/second = network overload
end)

-- GOOD: Send only on action
UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.E then
        remoteEvent:FireServer("UseAbility")
        -- Sends once per button press
    end
end)
```

### Use Region3 for Spatial Queries (Deprecated, use new method)

**Old way (deprecated):**

```lua
local region = Region3.new(min, max)
```

**New way (use this):**

```lua
local OverlapParams = OverlapParams.new()
OverlapParams.FilterType = Enum.RaycastFilterType.Exclude
OverlapParams.FilterDescendantsInstances = {character}

-- Get all parts in sphere
local position = Vector3.new(0, 10, 0)
local radius = 20
local parts = workspace:GetPartBoundsInRadius(position, radius, OverlapParams)

for _, part in pairs(parts) do
    print("Found part:", part.Name)
end

-- Get all parts in box
local cframe = CFrame.new(0, 10, 0)
local size = Vector3.new(20, 20, 20)
local parts = workspace:GetPartBoundsInBox(cframe, size, OverlapParams)
```

**Use case:** AOE ability detection, finding nearby players

## Part 11: Debugging Tools

### Print Debugging

```lua
-- Basic print
print("Hello")  -- White text in Output

-- Warn (yellow text)
warn("Warning: Player health is low")

-- Error (red text, but doesn't stop script)
error("Critical error!", 0)  -- 0 = don't show stack trace

-- Pretty print tables
local data = {name = "Adi", level = 5}
print(data)  -- Just prints table address (useless)

-- Better: Iterate and print
for key, value in pairs(data) do
    print(key, "=", value)
end

-- Or use string interpolation (Luau)
print(`Player: {data.name}, Level: {data.level}`)
```

### Breakpoints & Debugging

**In Roblox Studio:**

1. Click left margin of script to add breakpoint (red dot)
2. Run game
3. When code hits breakpoint, game pauses
4. Inspect variables in "Watch" window
5. Step through code line-by-line

### Output Window Tricks

```lua
-- Log with timestamp
local function log(message)
    print(os.date("%H:%M:%S") .. " - " .. message)
end

log("Player joined")  -- "14:32:15 - Player joined"

-- Conditional logging
local DEBUG = true

local function debug(message)
    if DEBUG then
        print("[DEBUG]", message)
    end
end

debug("Testing ability cooldown")  -- Only prints if DEBUG = true
```

### Inspect Objects

```lua
-- Get object path
print(part:GetFullName())  -- "Workspace.Map.Building.Wall"

-- Check object type
print(part.ClassName)  -- "Part"
print(part:IsA("BasePart"))  -- true

-- List all properties
for _, property in pairs(part:GetPropertyChangedSignal("Position")) do
    print(property)
end

-- Get all children recursively
for _, child in pairs(workspace:GetDescendants()) do
    if child:IsA("Part") and child.Name == "Breakable" then
        print("Found breakable part at:", child:GetFullName())
    end
end
```

## Part 12: Common Gotchas

### 1. Yielding (Blocking Code)

**Functions that WAIT/PAUSE your script:**

```lua
-- These YIELD (pause script until complete):
task.wait()
object:WaitForChild()
Players.PlayerAdded:Wait()
tween.Completed:Wait()

-- Problem:
local player = Players:WaitForChild("Adi")  -- BLOCKS until "Adi" joins
print("This line waits for player")

-- Solution: Use events instead
Players.PlayerAdded:Connect(function(player)
    if player.Name == "Adi" then
        print("Adi joined!")
    end
end)
print("This line runs immediately")
```

### 2. Index vs. Pairs/IPairs

```lua
local array = {10, 20, 30, 40, 50}

-- ipairs: Stops at first nil
array[3] = nil  -- Remove middle element
for i, v in ipairs(array) do
    print(i, v)  -- Prints 1=10, 2=20, then STOPS (doesn't see 40, 50)
end

-- pairs: Iterates all (but unordered)
for i, v in pairs(array) do
    print(i, v)  -- Prints all, but order not guaranteed
end

-- Numeric for: Explicit iteration
for i = 1, #array do
    print(i, array[i])  -- Prints all including nils
end
```

### 3. CFrame vs. Position

```lua
-- Setting Position can cause physics issues
part.Position = Vector3.new(0, 10, 0)  -- Can clip through walls

-- Setting CFrame is instant teleport (no physics)
part.CFrame = CFrame.new(0, 10, 0)  -- Preferred for teleporting

-- Moving smoothly
part.CFrame = part.CFrame + Vector3.new(0, 1, 0)  -- Move up 1 stud
```

### 4. Anchored vs. Physics

```lua
-- Anchored parts don't move (no physics)
part.Anchored = true
part.AssemblyLinearVelocity = Vector3.new(0, 50, 0)  -- Does nothing!

-- Unanchored parts use physics
part.Anchored = false
part.AssemblyLinearVelocity = Vector3.new(0, 50, 0)  -- Launches upward

-- If you want physics but fixed position:
part.Anchored = false
local bodyPosition = Instance.new("BodyPosition")
bodyPosition.Position = Vector3.new(0, 10, 0)
bodyPosition.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
bodyPosition.Parent = part
```

### 5. Client vs. Server Replication

```lua
-- CLIENT (LocalScript) creates part
local part = Instance.new("Part")
part.Parent = workspace
-- Only THIS CLIENT sees the part
-- Server and other clients DON'T see it

-- SERVER (Script) creates part
local part = Instance.new("Part")
part.Parent = workspace
-- EVERYONE sees the part (automatically replicated)
```

**Rule:** Game logic = server. Visuals/UI = client.

## Part 13: Example Mini-Project (Complete)

### Simple Combat System

**Structure:**

```
ReplicatedStorage
├── CombatModule (ModuleScript)
└── DamageEvent (RemoteEvent)

ServerScriptService
└── CombatServer (Script)

StarterPlayer
└── StarterCharacterScripts
    └── CombatClient (LocalScript)
```

**CombatModule.lua (ReplicatedStorage):**

```lua
local Combat = {}
Combat.PUNCH_DAMAGE = 10
Combat.PUNCH_RANGE = 10
Combat.PUNCH_COOLDOWN = 0.5

function Combat:isInRange(attacker, target)
    local distance = (attacker.Position - target.Position).Magnitude
    return distance <= self.PUNCH_RANGE
end

function Combat:dealDamage(target, amount)
    local humanoid = target.Parent:FindFirstChild("Humanoid")
    if humanoid and humanoid.Health > 0 then
        humanoid.Health = humanoid.Health - amount
        return true
    end
    return false
end

return Combat
```

**CombatServer.lua (ServerScriptService):**

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Combat = require(ReplicatedStorage.CombatModule)
local damageEvent = ReplicatedStorage:WaitForChild("DamageEvent")

-- Track cooldowns
local cooldowns = {}

damageEvent.OnServerEvent:Connect(function(player, targetPlayer)
    -- Validate
    if not player.Character or not targetPlayer.Character then return end

    -- Check cooldown
    local now = tick()
    if cooldowns[player.UserId] and now - cooldowns[player.UserId] < Combat.PUNCH_COOLDOWN then
        return  -- Still on cooldown
    end

    local attackerHRP = player.Character.HumanoidRootPart
    local targetHRP = targetPlayer.Character.HumanoidRootPart

    -- Check range
    if not Combat:isInRange(attackerHRP, targetHRP) then
        return  -- Too far
    end

    -- Deal damage
    if Combat:dealDamage(targetPlayer.Character, Combat.PUNCH_DAMAGE) then
        cooldowns[player.UserId] = now
        print(player.Name .. " punched " .. targetPlayer.Name)
    end
end)
```

**CombatClient.lua (StarterCharacterScripts - LocalScript):**

```lua
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local player = Players.LocalPlayer
local damageEvent = ReplicatedStorage:WaitForChild("DamageEvent")

-- Find closest player
local function getClosestPlayer()
    local character = player.Character
    if not character then return nil end

    local hrp = character.HumanoidRootPart
    local closest = nil
    local closestDist = math.huge

    for _, otherPlayer in pairs(Players:GetPlayers()) do
        if otherPlayer ~= player and otherPlayer.Character then
            local otherHRP = otherPlayer.Character.HumanoidRootPart
            local dist = (hrp.Position - otherHRP.Position).Magnitude

            if dist < closestDist then
                closestDist = dist
                closest = otherPlayer
            end
        end
    end

    return closest
end

-- Punch on left click
UIS.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end

    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        local target = getClosestPlayer()
        if target then
            damageEvent:FireServer(target)
        end
    end
end)
```

## Summary: Key Takeaways

**1. Use `:` for methods, `.` for static functions**

**2. Server = authoritative, Client = visual/input**

**3. Vectors are coordinates + math (position, direction, velocity, force)**

**4. Common services:**

- RunService (game loop)
- TweenService (animations)
- UserInputService (input)
- Players (player management)

**5. Hierarchy matters:**

- Workspace = visible 3D world
- ReplicatedStorage = shared assets
- ServerScriptService = server logic
- StarterGui = UI templates

**6. Performance:**

- Pool objects instead of create/destroy
- Cache frequently accessed objects
- Use task.wait() not wait()
- Disconnect unused events

**7. Debugging:**

- Use print, warn, error
- Set breakpoints in Studio
- Inspect object paths with :GetFullName()
