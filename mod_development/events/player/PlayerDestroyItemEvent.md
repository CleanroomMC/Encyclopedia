## PlayerDestroyItemEvent
- `PlayerDestroyItemEvent` is fired when a player destroys an item.

#### Hooks
1. `PlayerControllerMP#onPlayerDestroyBlock(BlockPos)`
2. `PlayerControllerMP#processRightClick(EntityPlayer, World, EnumHand)`
3. `PlayerControllerMP#processRightClickBlock(EntityPlayerSP, WorldClient, BlockPos, EnumFacing, Vec3d, EnumHand)`
4. `EntityPlayer#attackTargetEntityWithCurrentItem(Entity)`
5. `EntityPlayer#damageShield(float)`
6. `EntityPlayer#interactOn(Entity, EnumHand)`
7. `ForgeHooks#getContainerItem(ItemStack)`
8. `PlayerInteractionManager#processRightClick(EntityPlayer, World, ItemStack, EnumHand)`
9. `PlayerInteractionManager#processRightClickBlock(EntityPlayer, World, ItemStack, EnumHand, BlockPos, EnumFacing, float, float, float)`
10.`PlayerInteractionManager#tryHarvestBlock(BlockPos)`

#### Characteristics
  1. Event is not cancellable.
  2. Event does not have a result.

#### Behaviour
  1. Fired normally from ForgeEventFactory#onPlayerDestroyItem(EntityPlayer, ItemStack, EnumHand)
  2. Fired on MinecraftForge#EVENT_BUS

#### Oddities
  - This event is never fired correctly for the context of the 7th hook's listed above: `ForgeHooks#getContainerItem(ItemStack)`. Forge's logic when trying to determine if the retrieved container item is destroyed is all wrong. This bug was introduced in [MinecraftForge's PR#3388](https://github.com/MinecraftForge/MinecraftForge/pull/3388). Which meant that the event never fires for when the container item was actually destroyed. This was introduced when Forge was correcting all null-checks on `ItemStacks` to `ItemStack#isEmpty` calls instead. In most contexts, checking `ItemStack#isEmpty` would be enough, but in this particular context, the semantics was misunderstood. Any `ItemStack` that are destroyed will canonically be `ItemStack#isEmpty() == true && ItemStack != ItemStack.EMPTY`, meaning the logic of `if (!stack.isEmpty() && stack.isItemStackDamageable() && stack.getMetadata() > stack.getMaxDamage())` in `ForgeHooks#getContainerItem(ItemStack)` is indeed wrong and should have been `if (stack.isEmpty() && (stack.isItemStackDamageable() || stack.getDamage() >= stack.getMaxDamage())`.
  - To circumvent this oddity, one would have to make sure they handle all the logic they would have done in a `PlayerDestroyItemEvent` listener in their item's respective class's `getContainerItem` method instead.
