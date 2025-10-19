# Vex 2.0 Documentation

Complete API reference and usage guide.

## Table of Contents

- [Installation](#installation)
- [Basic Usage](#basic-usage)
- [Configuration](#configuration)
- [API Reference](#api-reference)
- [Performance Guide](#performance-guide)
- [Examples](#examples)
- [Troubleshooting](#troubleshooting)

---

## Installation

### Roblox Model
1. Get the model: [Vex 2.0](https://create.roblox.com/store/asset/8491559721/Vex)
2. Insert into your game
3. Move `Vex` ModuleScript to `ReplicatedStorage`

### Manual Setup
1. Create ModuleScript `Vex` in `ReplicatedStorage`
2. Create 4 child ModuleScripts:
   - `Config`
   - `VoxelGrid`
   - `GreedyMesher`
   - `VoxelPool`
3. Copy source code from `/src` directory into each module

### Optional: Collision Group Setup
1. Open Collision Groups in Studio
2. Create group named `Debris`
3. Set it to not collide with itself

This dramatically improves physics performance.

---

## Basic Usage

### Simple Destruction

```lua
local Vex = require(game.ReplicatedStorage.Vex)

local structure = Vex.new(workspace.Building)
structure:Destroy()
```

### With Configuration

```lua
local structure = Vex.new(workspace.Building, {
    voxelSize = 2,
    lifetime = 30,
    useGreedyMesh = true
})

structure:Destroy()
```

### Applying Forces

```lua
-- First destroy the structure
structure:Destroy()

-- Then apply force (like an explosion)
structure:ApplyForce(
    Vector3.new(0, 500, 0),    -- Force vector
    explosionPosition           -- Origin point
)
```

---

## Configuration

All configuration is optional. Here are the available options:

### voxelSize
- **Type:** `number`
- **Default:** `1`
- **Range:** `0.1` to `10`

Size of individual voxels in studs. Larger voxels = better performance but less detail.

```lua
voxelSize = 2  -- Each voxel is 2x2x2 studs
```

### useGreedyMesh
- **Type:** `boolean`
- **Default:** `true`

Enables greedy meshing algorithm. Combines adjacent voxels into larger parts for massive performance improvement.

**Keep this enabled unless you have a specific reason not to.**

```lua
useGreedyMesh = true
```

### maxVoxels
- **Type:** `number`
- **Default:** `10000`
- **Range:** `1` to `50000`

Maximum number of voxels to generate. Prevents crashes from oversized structures.

```lua
maxVoxels = 5000
```

### lifetime
- **Type:** `number` or `nil`
- **Default:** `nil` (never cleanup)
- **Minimum:** `1` second

Auto-cleanup debris after X seconds. Nil means voxels persist forever.

```lua
lifetime = 30  -- Remove after 30 seconds
```

### material
- **Type:** `Enum.Material` or `nil`
- **Default:** `nil` (inherit from source)

Override material for all voxels. Nil means inherit from source parts.

```lua
material = Enum.Material.Concrete
```

### collisionGroup
- **Type:** `string`
- **Default:** `"Debris"`

Collision group for voxel parts. Create this group in Studio for best results.

```lua
collisionGroup = "Debris"
```

### anchored
- **Type:** `boolean`
- **Default:** `false`

Whether voxels should be anchored (static) or affected by physics.

```lua
anchored = false  -- Let physics affect them
```

### weldAdjacent
- **Type:** `boolean`
- **Default:** `true`

Welds adjacent voxel clusters together. Creates more cohesive destruction.

```lua
weldAdjacent = true
```

---

## API Reference

### Vex.new()

```lua
Vex.new(model: Model | BasePart, config: table?) -> VoxelStructure
```

Creates a new voxelized structure. Does not destroy the source yet - call `:Destroy()` when ready.

**Parameters:**
- `model` - Model or BasePart to voxelize
- `config` - Optional configuration table

**Returns:** VoxelStructure object

**Example:**
```lua
local structure = Vex.new(workspace.Building, {
    voxelSize = 2,
    lifetime = 30
})
```

---

### VoxelStructure:Destroy()

```lua
structure:Destroy() -> void
```

Breaks the structure into voxels. The source model/part is destroyed.

**Example:**
```lua
structure:Destroy()
```

---

### VoxelStructure:ApplyForce()

```lua
structure:ApplyForce(force: Vector3, position: Vector3) -> void
```

Applies physics force to voxels near a position. Only works after calling `:Destroy()`.

Force is applied with falloff based on distance (default radius: 20 studs).

**Parameters:**
- `force` - Force vector (direction and magnitude)
- `position` - Origin point (center of explosion)

**Example:**
```lua
structure:ApplyForce(
    Vector3.new(0, 800, 0),
    workspace.Bomb.Position
)
```

---

### VoxelStructure:Cleanup()

```lua
structure:Cleanup() -> void
```

Immediately removes all voxels and returns them to the object pool.

**Example:**
```lua
structure:Cleanup()
```

---

### VoxelStructure:GetVoxelCount()

```lua
structure:GetVoxelCount() -> number
```

Returns the current number of voxel parts in the structure.

**Returns:** Number of parts

**Example:**
```lua
local count = structure:GetVoxelCount()
print("Structure has", count, "parts")
```

---

### Vex.clearPool()

```lua
Vex.clearPool() -> void
```

Clears the entire object pool, destroying all pooled parts.

**Example:**
```lua
Vex.clearPool()
```

---

### Vex.getPoolStats()

```lua
Vex.getPoolStats() -> table
```

Returns statistics about the object pool.

**Returns:** Table with keys:
- `active` - Parts currently in use
- `pooled` - Parts waiting to be reused
- `total` - Active + Pooled

**Example:**
```lua
local stats = Vex.getPoolStats()
print(string.format("Active: %d, Pooled: %d", stats.active, stats.pooled))
```

---

## Performance Guide

### Optimization Checklist

1. **Enable greedy meshing** (default: on)
   - Reduces part count by 80-95% for solid structures
   - Keep this on unless you have a very specific reason not to

2. **Use appropriate voxel sizes**
   - Small structures: `voxelSize = 1`
   - Medium structures: `voxelSize = 2`
   - Large structures: `voxelSize = 3`

3. **Set maxVoxels limits**
   - Prevents destroying huge structures from crashing server
   - Recommended: `5000` to `10000` for most games

4. **Use lifetime parameter**
   - Auto-cleanup prevents debris accumulation
   - Recommended: `30` to `60` seconds

5. **Configure collision groups**
   - Create "Debris" collision group
   - Set it to not collide with itself
   - Reduces physics overhead significantly

6. **Limit concurrent destructions**
   - Don't destroy 50 buildings at once
   - Stagger destruction events

### Performance Comparison

Greedy meshing makes a huge difference:

**10x10x10 solid cube (1000 voxels):**
- Without greedy mesh: 1,000 parts
- With greedy mesh: 6 parts
- **99.4% reduction**

**Complex hollow structure:**
- Without greedy mesh: ~50,000 parts
- With greedy mesh: ~5,000 parts  
- **90% reduction**

### When Performance Still Sucks

If you're still having issues:

1. **Increase voxel size** - Try doubling it
2. **Reduce maxVoxels** - Lower the limit
3. **Disable weldAdjacent** - Welding has overhead
4. **Check your collision groups** - Make sure debris doesn't collide with itself
5. **Limit destruction frequency** - Don't spam it

---

## Examples

### Example 1: Destructible Wall

```lua
local Vex = require(game.ReplicatedStorage.Vex)

local wall = workspace.Wall
local structure = Vex.new(wall, {
    voxelSize = 2,
    lifetime = 15,
    material = Enum.Material.Brick
})

-- Destroy on projectile impact
workspace.Projectile.Touched:Connect(function(hit)
    if hit == wall then
        structure:Destroy()
        structure:ApplyForce(
            workspace.Projectile.AssemblyLinearVelocity * 10,
            workspace.Projectile.Position
        )
    end
end)
```

### Example 2: Explosion Destruction

```lua
local Vex = require(game.ReplicatedStorage.Vex)

local building = workspace.Building
local structure = Vex.new(building, {
    voxelSize = 2,
    lifetime = 30
})

-- Wait for explosion
workspace.Bomb.Touched:Connect(function()
    structure:Destroy()
    
    -- Apply upward force from base
    structure:ApplyForce(
        Vector3.new(0, 1000, 0),
        workspace.Bomb.Position
    )
end)
```

### Example 3: Pre-Voxelized Structures

```lua
local Vex = require(game.ReplicatedStorage.Vex)

-- Pre-voxelize multiple buildings
local structures = {}

for _, building in ipairs(workspace.Buildings:GetChildren()) do
    structures[building] = Vex.new(building, {
        voxelSize = 2,
        lifetime = 45
    })
end

-- Destroy them on demand
game.ReplicatedStorage.DestroyBuilding.OnServerEvent:Connect(function(player, buildingName)
    local building = workspace.Buildings:FindFirstChild(buildingName)
    if building and structures[building] then
        structures[building]:Destroy()
    end
end)
```

### Example 4: Controlled Demolition

```lua
local Vex = require(game.ReplicatedStorage.Vex)

local function demolishBuilding(building)
    -- Countdown
    for i = 3, 1, -1 do
        print(i)
        task.wait(1)
    end
    
    print("BOOM!")
    
    local structure = Vex.new(building, {
        voxelSize = 2,
        lifetime = 30,
        weldAdjacent = false  -- Independent pieces
    })
    
    structure:Destroy()
    
    -- Multiple explosion points at base
    local base = building.Position - Vector3.new(0, building.Size.Y / 2, 0)
    
    for i = 1, 5 do
        local offset = Vector3.new(
            math.random(-10, 10),
            0,
            math.random(-10, 10)
        )
        
        structure:ApplyForce(
            Vector3.new(0, 1500, 0),
            base + offset
        )
        
        task.wait(0.1)
    end
end

demolishBuilding(workspace.Building)
```

---

## Troubleshooting

### "No parts found in model"
Your model doesn't contain any BasePart descendants. Make sure there are actual parts inside the model.

### "Reached maxVoxels limit"
The structure is too large. Either:
- Increase `maxVoxels` in config
- Use a larger `voxelSize`
- Break the structure into smaller pieces

### Parts fall through the floor
Either:
- Make sure the floor has collision enabled
- Set `anchored = true` in config (if you want static debris)

### Performance is terrible
- Make sure `useGreedyMesh = true` (it's the default)
- Increase `voxelSize` (try `2` or `3`)
- Lower `maxVoxels` limit
- Set up collision groups properly
- Use `lifetime` for auto-cleanup

### Parts aren't welding together
- Check that `weldAdjacent = true` (it's the default)
- Make sure `voxelSize` isn't too large (welds detect adjacency)

### Forces aren't working
You need to call `:Destroy()` first before calling `:ApplyForce()`.

```lua
structure:Destroy()  -- Do this first
structure:ApplyForce(force, position)  -- Then this
```

### Memory leaks / debris not cleaning up
- Set a `lifetime` parameter in config
- Manually call `:Cleanup()` when done
- Use `Vex.clearPool()` occasionally to clear the pool

---

## Advanced Usage

### Custom Force Radius

The force radius is hardcoded to 20 studs by default. If you need to change it, you'll need to modify the `ApplyForce` function in `Vex.lua`:

```lua
local FORCE_RADIUS = 20  -- Change this value
```

### Manual Pool Management

```lua
-- Clear the entire pool (destroys all pooled parts)
Vex.clearPool()

-- Check pool statistics
local stats = Vex.getPoolStats()
print("Active parts:", stats.active)
print("Pooled parts:", stats.pooled)
```

### Multiple Destruction Passes

You can destroy the same location multiple times by keeping the structure reference:

```lua
local structure = Vex.new(workspace.Building, {
    voxelSize = 2
})

structure:Destroy()

-- Later, apply more damage
structure:ApplyForce(Vector3.new(0, 500, 0), newPosition)
```

---

## FAQ

**Q: Can I use this in my game commercially?**  
A: Yes, it's MIT licensed. Use it however you want.

**Q: Does this work with MeshParts?**  
A: Not yet. Only BaseParts are supported. MeshPart support is planned.

**Q: Can I change voxel size after creating the structure?**  
A: No, voxel size is set at creation time.

**Q: How do I make destruction client-side?**  
A: Vex 2.0 is server-side only for now. Client-side voxelization is planned for a future update.

**Q: Can I save/load destroyed structures?**  
A: Not built-in, but you could serialize the voxel data yourself. Might add this in the future.

**Q: Why aren't my rotated parts working properly?**  
A: Rotated parts are supported but might have slight alignment issues with complex angles. This is on the list to improve.
