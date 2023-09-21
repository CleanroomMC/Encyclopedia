Before registering mixins via MixinBooter, you have to consider how your mixins affect the game.

If your mixin affects **Vanilla Minecraft** or **Forge** classes, you will need a **coremod**.

### ^^Vanilla Minecraft/Forge Mixins^^
- Make a Coremod
- In your `IFMLLoadingPlugin` implementation, also implement `IEarlyMixinLoader`

### ^^Mod Mixins^^
- Create a class in your mod, anywhere (preferably under your mod's packages).
- In your class, implement `ILateMixinLoader`

Both `IEarlyMixinLoader` and `ILateMixinLoader` have identical methods:

```java
/**
 * @return mixin configurations to be queued and sent to Mixin library.
 */
List<String> getMixinConfigs();

/**
 * Runs when a mixin config is successfully queued and sent to Mixin library.
 *
 * @param mixinConfig mixin config name, queried via {@link getMixinConfigs()}.
 * @return true if the mixinConfig should be queued, false if it should not.
 */
default boolean shouldMixinConfigQueue(String mixinConfig) {
    return true;
}

/**
 * Runs when a mixin config is successfully queued and sent to Mixin library.
 * @param mixinConfig mixin config name, queried via {@link getMixinConfigs()}.
*/
default void onMixinConfigQueued(String mixinConfig) { }
```