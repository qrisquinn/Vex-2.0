# Changelog

All notable changes to Vex will be documented in this file.

## [2.0.0] - 2025-10-19

### Added
- Complete rewrite of the entire codebase
- Greedy meshing algorithm for performance optimization
- Object pooling system for part reuse
- Force application API for explosion effects
- Auto-cleanup with lifetime parameter
- Custom voxel size support (not limited to 1x1x1)
- Material and color preservation from source parts
- Collision group support
- Input validation and error handling
- Comprehensive documentation
- Model support (not just individual parts)
- Safety limits with maxVoxels parameter
- Modular architecture (5 separate modules)

### Changed
- API completely redesigned (not backward compatible with 1.x)
- Now requires explicit :Destroy() call instead of immediate destruction
- Configuration passed as table instead of positional arguments
- Welding is now optional via weldAdjacent parameter

### Removed
- Old DestructionWrapper API
- Automatic voxelization on creation (now requires :Destroy() call)

### Performance
- Greedy meshing reduces part count by 80-95% for solid structures
- Object pooling eliminates garbage collection spikes
- Sparse grid storage reduces memory usage

### Known Issues
- Only BaseParts supported (MeshParts/Unions planned)
- Rotated parts may have slight alignment issues
- Very large structures (50k+ studs³) may cause performance issues
- Server-side only (client-side voxelization planned)

---

## [1.5.0] - 2022-01-09

### Added
- Support for parts of any size (not just 1-stud increments)

### Fixed
- Anchored parameter now works correctly
- Y-axis gaps on odd-sized parts (mostly fixed, some edge cases remain)

---

## [1.0.0] - 2022-01-08

### Added
- Initial release
- Basic voxelization of parts and models
- 1x1x1 voxel size
- Simple welding system
- Anchored parameter

### Known Issues
- Not optimized for large-scale destruction
- Only supports 1x1x1 voxel size
- Only parts supported (no models)
- Performance issues with many voxels
