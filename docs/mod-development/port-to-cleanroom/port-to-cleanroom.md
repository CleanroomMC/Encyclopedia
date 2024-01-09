# How to port your mod to Cleanroom, or Cleanroom compatibility guide
## 1 Overview
Porting your mods to Cleanroom is unnecessary if it runs normally on Cleanroom, unless:
- You want to use language feature from Java 21
- You want to use LWJGL3
- Your mod is incompatible with Cleanroom in an un-patchable way
- You want to prompt Cleanroom by publish Cleanroom-only releases
- You want to use APIs only available in Cleanroom (There are no such API when I write down this guide)

The main problems need to be taken care can be concluded to three parts: 
- Newer Java changes
- New version of libraries
- Old versions of Scala and Kotlin can't compile under newer Java
### 2 Newer Java Changes
#### 2.1 URLClassLoader
A few mods cast AppClassLoader into URLClassLoader to get URLs, 
like this: `((URLClassLoader) Launch.classLoader.getClass().getClassLoader()).getURLs()`

This casting is no longer feasible and will throw errors in newer Java. We wrote a `URLClassLoaderTransformer` to patch such castings, but you will need to change it reflective way like [this](https://github.com/CleanroomMC/Cleanroom/blob/cf59ba1080dc2bf7eb3f60e4ae5cff82639cb042/src/main/java/net/minecraftforge/fml/relauncher/CoreModManager.java#L459).
#### 2.2 javax to jakarta
Java EE changed its namespace from `javax` to `jakarta` in Jakarta EE 9. This change affect javax packages:
- bind
- xml
- ws
- activation
- soap
- jws

Furthermore, in newer Java they aren't shipped by default in many JDK vendors.

We wrote a `JavaxTransformer` to remap every old `javax` reference to `jakarta`, and made Cleanroom ships those Jakarta EE dependencies by default. All you need to do is to search and replace your code similarly.
#### 2.3 ScriptEngineManager
Java removed the default Nashorn JS engine since Java 15, but we have included its standalone version back.

The only problem is calling `new ScriptEngineManager(null)` may yield errors since it will search script engine in a wrong classloader. Please replace it with `new CleanroomScriptEngineManager()` or `new ScriptEngineManager()`.

We also have a `ScriptEngineTransformer` to patch that.
#### 2.4 Malformed UUID
JDK 8 will consider some invalid UUID as valid, which fixed in newer Java. Old invalid UUIDs will crash the game when detected.

We wrote a `MalformedUUIDTransformer` to patch this, but please verify your UUID and use a valid one.
#### 2.5 `sun.reflect.Reflection`
This class is now moved to JDK internal, and we don't encourage filtering other mods' reflection call for any reason. If you want to get caller class, use `java.lang.StackWalker` instead.

Currently, we remap every reference of this class to a dummy class don't do anything except `getCallerClass()`.
#### 2.6 ASM API version
Many CoreMods have something like `super(Opcodes.ASM5, cv);` in their custom `ClassVisitor`, `MethodVisitor` or `FieldVisitor`.

Such visitors can't handle newer version of class. For this, we wrote a `ASMVersionUpper` in Bouncepad (our fork of launchwrapper).

You should update it to at least ASM9.
#### 2.7 Getting or setting field with reflection
Newer Java now has more strict access control around final field, even the access is made by reflection or any other tradition way.

`Unsafe` is the official way to set final fields now, but we are considering third-party libraries like [Mirror](https://github.com/Moderocky/Mirror), [Narcissus](https://github.com/toolfactory/narcissus) and [JVM-Driver](https://github.com/toolfactory/jvm-driver).

We also made a `ReflectionFieldTransformer` to redirect every `set()` or `get()` of fields to `Unsafe`, but this will be removed once the community is ready.
#### 2.8 `itf` of `MethodInsnNode`
Java 8 doesn't care if an interface status in method calling is true or not. Some older version of ASM5 doesn't set it correctly too. All these made some ASM-involved mods crashing on CLeanroom.

We have located and fixed two mods crash by this change (Lag Goggles and ZenScript), but you should check your ASM code when porting.
#### 2.9 `private` methods calls shouldn't use `invokespecial` now
It was a JDK change made for nest-based access control, [here](https://openjdk.org/jeps/181)'s the JEP link. 

Some mods' ASM code rely on counting amount of `invokespecial` or `invokevirtual`, they may encounter crash in Cleanroom. Currently only Advanced Rocketry is affect by this and has been patched.
#### 2.10 Mixin's `@Accessor` may crash your game
Since newer JVM restricted its access control, it will refuse to set `final` field even through Mixin accessor. Adding a `@Mutable` on same accessor method could remove target's `final` modifier and fix this.

Adding AT(Access Transformer) works too, but on vanilla fields. We have added some of them manually in *Fugue* to fix a few dead mods.
### 3 New Version of Libraries
#### 3.1 New Mixin and ModifyArgs
- You do not need to bootstrap Mixin anymore, and should not do this.
- Use `IEarlyMixinLoader` and `ILateMixinLoader` for better compatibility, don't add config manually unless you know what you are doing.
- `@MoodifyArgs` is broken in run time, but should be fixed in our Bouncepad refactor. If your mod have to use it, please open an issue in [Fugue](https://github.com/CleanroomMC/Fugue), we will patch it manually.
- Our Mixin is based on latest Fabric fork and have everything you want.
#### 3.2 LWJGL3
We use our fork of LWJGL with path `org.lwjgl3`. The lwjglx compat layer is in `org.lwjgl`. If you can't find a method in lwjglx, that's normal, and you should always find it in lwjgl3.

All mods calling LWJGL should switch to LWJGL3, we will switch to official version once the community is ready. 
#### 3.3 Guava
It's always latest(currently 33.0) now. Some mods will need to add `Runnable::run` in some `Futures` methods, some mods will find the method they are using now return an `Optional`. All cases we found have been patched in *Fugue*, but you have better update it yourself.
#### 3.4 Fastutil
Fastutil doesn't allow setting certain hash set's load factor to `1.0` anymore, always check the latest javadoc when crashed!
#### 3.5 ICU4J
We use upstream version of ICU4J for a working line breaking engine, mods should use `net.minecraft.client.gui.FontRenderer#BREAK_ITERATOR` too for better internationalization.
#### 3.6 OSHI
This library is updated too, some debug screen mod should update their way to get CPU info.
### 4 Scala and Kotlin
#### 4.1 Scala
Old Forge was shipped with Scala for some reason, but they never updated it. The problem is, Scala 2.11 is no longer compilable under Java 21, 2.12.18 is the minimum version to do this.

But if we ship Scala 2.12, none of current mods will launch. We are planning to strip Scala libraries to a standalone mod, make two mods shipping Scala 3 and 2.11, then try to port everything to Scala 3.

For now, Scala mods can only develop under Forge with Java 8.
#### 4.2 Kotlin
Kotlin is easier compare to Scala - it was solely shipped by Forgelin in whole 1.12 life era. We have made a Forgelin shipping 21-compatible Kotlin 1.9.21, but some older mods are having problem running on newer Kotlin.

Mod porting is not begin yet, so old Forgelin is still necessary for old modpacks.

