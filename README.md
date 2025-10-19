# Vex 2.0

High-performance voxel destruction system for Roblox with greedy meshing and object pooling.

## What is this?

Vex converts Roblox parts into destructible voxel structures. Break buildings into pieces, apply explosion forces, and watch physics do its thing.

Originally created in 2022, Vex 2.0 is a complete rewrite focused on performance and usability.

## Features

- **Greedy meshing** - Combines adjacent voxels into larger parts (massive performance gain)
- **Object pooling** - Reuses parts instead of constantly creating/destroying them
- **Force application** - Apply physics forces to voxels for explosions
- **Auto-cleanup** - Set lifetime to automatically remove debris
- **Custom voxel sizes** - Not limited to 1x1x1 cubes
- **Material preservation** - Voxels inherit material and color from source

## Installation

### Option 1: Roblox Model
Get it from the Roblox library: [Vex 2.0](https://create.roblox.com/store/asset/8491559721/Vex)

1. Insert into your game
2. Move the `Vex` ModuleScript to `ReplicatedStorage`
3. Done

### Option 2: Manual Installation
1. Download the source files from this repo
2. Create a ModuleScript named `Vex` in ReplicatedStorage
3. Create 4 child ModuleScripts: `Config`, `VoxelGrid`, `GreedyMesher`, `VoxelPool`
4. Copy the corresponding `.lua` files into each module

## Quick Start

```lua
local Vex = require(game.ReplicatedStorage.Vex)

-- Basic destruction
local structure = Vex.new(workspace.Building)
structure:Destroy()

-- With configuration
local structure = Vex.new(workspace.Building, {
    voxelSize = 2,
    lifetime = 30,
    useGreedyMesh = true
})

structure:Destroy()

-- Apply explosion force
structure:ApplyForce(
    Vector3.new(0, 800, 0),
    explosionPosition
)
```

## Documentation

Full documentation available in [DOCUMENTATION.md](DOCUMENTATION.md)

Quick reference:
- `Vex.new(model, config)` - Create voxelized structure
- `structure:Destroy()` - Break into voxels
- `structure:ApplyForce(force, position)` - Apply physics force
- `structure:Cleanup()` - Remove all voxels
- `structure:GetVoxelCount()` - Get part count

## Performance Tips

1. Keep greedy meshing enabled (it's on by default)
2. Use larger voxel sizes for big structures (`voxelSize = 2` or `3`)
3. Set `maxVoxels` limits to prevent server crashes
4. Use `lifetime` parameter for auto-cleanup
5. Put debris in a separate collision group

## Examples

See [examples/](examples/) for complete usage examples.

## Known Limitations

- Only supports BaseParts (no MeshParts/Unions yet)
- Server-side only (client-side voxelization planned)
- Very large structures (50k+ studs³) may cause issues

## Contributing

Pull requests are welcome. For major changes, open an issue first to discuss what you'd like to change.

## License

[MIT](LICENSE)

## Credits

Original concept by Doglord120 (2019)

Vex 1.0 by EternalEthel/qrisquinn (2022)

Vex 2.0 rewrite by qrisquinn (EternalEthel account discontinued) (2025)
