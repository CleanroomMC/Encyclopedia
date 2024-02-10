## Overview
Normally, porting your mods to Cleanroom is unnecessary if it runs normally already, unless you want to:

- Use language feature from Java 21
- Use features of newer libraries packed with Cleanroom
- Depend solely on Cleanroom and not Forge because of its API or other reasons

The main problems when migrating from Forge to Cleanroom are as follows: 

- Java Changes
- Library Changes
- Older Scala and Kotlin compiled mods not able to work with newer Java

## Java Changes

### URLClassLoader
A few mods cast AppClassLoader into URLClassLoader to get URLs, 
like this: `((URLClassLoader) Launch.classLoader.getClass().getClassLoader()).getURLs()`

This casting is no longer feasible and will throw errors since Java 9. We wrote a `URLClassLoaderTransformer` to patch such castings, but you will need to change it reflective way like [this](https://github.com/CleanroomMC/Cleanroom/blob/cf59ba1080dc2bf7eb3f60e4ae5cff82639cb042/src/main/java/net/minecraftforge/fml/relauncher/CoreModManager.java#L459).

### javax to jakarta
Java EE changed most of its namespace from `javax` to `jakarta` in Jakarta EE 9. javax packages affect by this change includes:

- bind
- xml
- ws
- activation
- soap
- jws

Furthermore, in newer Java they aren't shipped by default in many JDK vendors.

We wrote a `JavaxTransformer` to remap some `javax` references to `jakarta`, and shipped newer Jakarta EE dependencies by default.

### ScriptEngineManager
Java removed the default Nashorn JS engine since Java 15, but we have included its standalone version back.

The only problem is calling `new ScriptEngineManager(null)` may yield errors since it will search script engine in a wrong classloader. Please replace it with `new CleanroomScriptEngineManager()`.

We also have a `ScriptEngineTransformer` to patch that.

### Malformed UUID
Java 8 considers some invalid UUID as valid, which was fixed in newer Java versions. Now, those previous valid UUIDs will crash the game.

We wrote a `MalformedUUIDTransformer` to patch this, but please verify your UUID and make sure to use a valid one.

### `sun.reflect.Reflection`
This class is now moved to JDK internal, and we don't encourage filtering other mods' reflection call for any reason. If you want to get caller class, use the new Java 9 `StackWalker` instead.

Currently, we remap every reference of `sun.reflect.Reflection` to a dummy class that NO-OP's most calls in here except `getCallerClass()` and `getCallerClass(int)`.

### ASM API version
Many coremods that use ASM's visitor API may extend `ClassVisitor`, `MethodVisitor` or `FieldVisitor` and include ASM5 as the API version.

Such visitors can't handle newer class versions, and crashes explicitly when visiting a nest class. For this, we wrote a `ASMVersionUpper` in Bouncepad (our fork of launchwrapper).

You should update it to at least ASM9 to read most if not all classes.

### Getting or setting field with reflection
Newer Java now has more strict access control around final field, even the access is made by reflection or any other tradition way.

`Unsafe` is the official way to set final fields now, but is very volatile.

We also made a `ReflectionFieldTransformer` to redirect every `set()` or `get()` of fields to `Unsafe`, but this may be removed once the community is ready.

### `itf` of `MethodInsnNode`
Java 8 doesn't care if an interface status in method calling is true or not. Some older version of ASM5 doesn't set it correctly too. All these made some ASM-involved mods crashing on CLeanroom.

We have located and fixed two mods crash by this change (Lag Goggles and ZenScript), but you should check your ASM code when porting.

### `private` methods calls shouldn't use `INVOKESPECIAL` now
It was a [JDK change made for nest-based access control](https://openjdk.org/jeps/181).

Some mods' ASM code rely on counting amount of `INVOKESPECIAL` or `INVOKEVIRTUAL`, and they may encounter crash in Cleanroom. Currently only Advanced Rocketry is affected by this and has been patched.

### Mixin's `@Accessor` may crash your game
Since newer JVM restricted its access control, it will refuse to set `final` field even through Mixin accessor. Adding a `@Mutable` on same accessor method could remove target's `final` modifier and fix this.

Adding AT(Access Transformer) works too, but on vanilla fields. We have added some of them manually in *Fugue* to fix a few dead mods.

## New Version of Libraries

### New Mixin and ModifyArgs
- You do not need to bootstrap Mixin anymore, and should not do this.
- Use `IEarlyMixinLoader` and `ILateMixinLoader` for better compatibility, don't add config manually unless you know what you are doing.
- `@ModifyArgs` is broken in run time, but should be fixed in our Bouncepad refactor. If your mod have to use it, please open an issue in [Fugue](https://github.com/CleanroomMC/Fugue), we will patch it manually.
- Our Mixin is based on latest Fabric fork and have everything you want.

### LWJGL3
We use our fork of LWJGL with path `org.lwjgl3`. The lwjglx compat layer is in `org.lwjgl`. If you can't find a method in lwjglx, that's normal, and you should always find it in lwjgl3.

All mods calling LWJGL should switch to LWJGL3, we will switch to official version once the community is ready. 

### Guava
It's always latest(currently 33.0) now. Some mods will need to add `Runnable::run` in some `Futures` methods, some mods will find the method they are using now return an `Optional`. All cases we found have been patched in *Fugue*, but you have better update it yourself.

### Fastutil
Fastutil doesn't allow setting certain hash set's load factor to `1.0` anymore, always check the latest javadoc when crashed!

### ICU4J
We use upstream version of ICU4J for a working line breaking engine, mods should use `net.minecraft.client.gui.FontRenderer#BREAK_ITERATOR` too for better internationalization.

### OSHI
This library is updated too, some debug screen mod should update their way to get CPU info.

## Scala and Kotlin

### Scala
Old Forge was shipped with Scala for some reason, but they never updated it. The problem is, Scala 2.11 is no longer compilable under Java 21, 2.12.18 is the minimum version to do this.

But if we ship Scala 2.12, none of current mods will launch. We are planning to strip Scala libraries to a standalone mod, make two mods shipping Scala 3 and 2.11, then try to port everything to Scala 3.

For now, Scala mods can only develop under Forge with Java 8.

### Kotlin
Kotlin is easier compare to Scala - it was solely shipped by Forgelin in whole 1.12 life era. We have made a Forgelin shipping 21-compatible Kotlin 1.9.21, but some older mods are having problem running on newer Kotlin.

Mod porting is not begin yet, so old Forgelin is still necessary for old modpacks.
