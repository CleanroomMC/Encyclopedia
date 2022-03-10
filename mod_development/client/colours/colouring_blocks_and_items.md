## Colouring Blocks and Items
- Colouring a block or item statically or dynamically
- When colouring a block `(IBlockColor)`, you have the following contexts:
  - `IBlockState`
  - `World` (Nullable)
  - `BlockPos` (Nullable)
  - `int tintIndex`
- When colouring an item `(IItemColor)`, you have the following contexts:
  - `ItemStack`
  - `int tintIndex`

#### Timing to Register
- Any time after specified `Block` or `Item` is registered is suitable.
- Recommend listening to `ColorHandlerEvent.Block` and `ColorHandlerEvent.Item` as that gives a general timing of where all the mods would be registering their colouring handlers.

#### Usage Explanation
- For Blocks, each `BlockPartFace` (seen as the elements of a `"facing"` `JsonArray`) can have an unique `tintIndex`. When handling and registering your `IBlockColor` implementation, you can return the valid colour depending on what `tintIndex` is in that specific call context.
- For Items, each `"layer"` can have an unique `tintIndex`. When handling and registering your `IItemColor` implementation, you can return the valid colour depending on what `tintIndex` is in that specific call context.
