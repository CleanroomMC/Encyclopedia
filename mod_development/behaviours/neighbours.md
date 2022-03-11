# Neighbours
- A "neighbour" is a `IBlockState` in-world that is directly adjacent to another "neighbour".
- It ***MUST*** be able to be queried via the 6 `EnumFacing` directions when calling `offset` on its `BlockPos`.

#### Flags
- Flags are used when `setBlockState` is called, certain flags to certain things, and its good to know them. (This will be in its own page soon.)
  - Flag 1: Notify neighbours
  - Flag 2: Send to Clients
  - Flag 4: No need to update render
  - Flag 8: Mark for immediate render update (ignored if flag 4 is present)
  - Flag 16: Disable Observers from seeing this block update

- Forge has marked `1 | 2` as the "default" flag combination when `setBlockState`.
- Forge has marked `1 | 2 | 8` as the "default" flag combination when `setBlockState` that needs to update the main render thread.
