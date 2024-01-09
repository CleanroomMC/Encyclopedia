- There are numerous of ways to play a sound within Minecraft.
- The methods of doing so frequently get mixed up as the overload parameters are very alike.
- They also differ depending on which logical side you are on, and how you want the sound to be perceived.

#### Server-Side
1. `World#playSound(EntityPlayer, double, double, double, SoundEvent, SoundCategory, float, float)`
  - This is the most commonly used method of playing a sound.
  - It plays locational sound to anyone in-range (base 16 blocks radius, see: `ServerWorldEventHandler#playSoundToAllNearExcept(EntityPlayer, SoundEvent, SoundCategory, double, double, double, float, float)`). This sound can be amplified to further than 16 blocks radius by passing a `float volume` of anything larger than 1.0F. `(formula: 16 * volume when volume > 1.0F)`
  - `null` is normally passed to the `EntityPlayer` parameter. This isn't clear from the method itself nor are there any documentation. But this is basically the "except" argument, any one `EntityPlayer` passed through the argument will ***NOT*** hear the sound.
2. `World#playSound(EntityPlayer, BlockPos, SoundEvent, SoundCategory, float volume, float pitch)`
  - Just calls the first method with `BlockPos#getX`, `BlockPos#getY`, `BlockPos#getZ` as the overloaded double parameters.
3. `Entity#playSound(SoundEvent, float, float)`
  - This method checks if the entity `isSilent` before calling the first method ***with*** the `EntityPlayer` being `null`.
4. `EntityPlayer#playSound(SoundEvent, float, float)`
  - Overrides the 3rd method which is in `EntityPlayer`'s superclass `Entity`.
  - It also calls the first method but ***with*** the `EntityPlayer` callee. This means the callee will ***NOT*** hear any sounds, but everyone else in the range. Strange? Well... it'll clear up when we mention the client-side options.

#### Client-Side
1. `WorldClient#playSound(double, double, double, SoundEvent, SoundCategory, float, float, boolean)`
  - Main method in `WorldClient`, sets up a `PositionedSoundRecord` on the client to make sure sounds played are positional.
2. `WorldClient#playSound(BlockPos, SoundEvent, SoundCategory, float, float, boolean)`
  - Calls the first method.
3. `WorldClient#playSound(EntityPlayer, SoundEvent, SoundCategory, float, float)`
  - Checks if `EntityPlayer` == `this.mc.player` to avoid any issues on LAN before calling first method.
4. `EntityPlayer#playSound(SoundEvent, float, float)`
  - Simply calls the first method.

`(COMING SOON: SoundHandler methods)`

The 4th option for both server and client-side does the complete opposite. Server-side's plays sounds to everyone else ***but*** the `EntityPlayer` themselves. While the client-side's version plays it ***only*** to the `EntityPlayer` themselves.

All-in-all, that method is basically a cheat for anyone that doesn't check which logical side they are on. If the context they're in gets called on both sides, the sound could play normally without the dev potentially knowing this caveat.
