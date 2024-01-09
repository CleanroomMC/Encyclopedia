- Events are the backbone of Forge's modding ecosystem, there are two major event types.
	- `FMLEvent`
	- `Event`
	
- FMLEvents are events for different aspects of mod loading.
	1. `FMLFingerprintViolationEvent`: fires when the mod that is running has mismatched fingerprints.
	2. `FMLInterModComms$IMCEvent`: fires between `FMLInitializationEvent` and `FMLPostInitializationEvent`, for modders to receive their `InterModComms` messages.
	3. `FMLModDisabledEvent`: fires when your mod is disabled for any reason.
	4. `FMLModIdMappingEvent`: fires when ID mappings have changed when loading into a world.

- FMLStateEvents (extends FMLEvent) specifically depicts different phases of the FML lifecycle.

- Initial Loading Stages:
  1. `FMLConstructionEvent`: fires when Forge finishes constructing mods, annotations, mod lists are ready to be queried here.
  2. `FMLPreInitializationEvent`: fires when Forge is ready to start initializing mods, you can again query annotations once again, and see where different files related to your mod would situate (e.g. config files).
  3. `FMLInitializationEvent`: fires after registry events are fired, game objects largely is available in this event. Hence a lot of OreDictionary activity is done here.
  4. `FMLPostInitializationEvent`: fires after `FMLInitializationEvent` is dispatched to all mods, to consolidate any manipulations the mods have made.
  5. `FMLLoadCompleteEvent`: fires straight before the main menu shows up, mods like JustEnoughItem does all their calculations here, it is the last event in the loading FML lifecycle.

- Server Loading Stages:
  1. `FMLServerAboutToStartEvent`: fires after settings and properties are initialized.
  2. `FMLServerStartingEvent`: fires after worlds are loaded, custom commands and more can be done here.
  3. `FMLServerStartedEvent`: fires when the server is ready for players.
  
- Server Stopping Stages:
  1. `FMLServerStoppingEvent`: fires when shutdown is initiated.
  2. `FMLServerStoppedEvent`: fires before the last tick is ran, after this the shutdown will finish. On integrated servers the menu will be loaded after this.
     

#### Listening to Events

- Different event types have their own ways of being listened to and unique ways of being posted.

- FMLEvents are listened to by having the `@EventHandler` annotation on methods within `@Mod` annotated classes. These must be member methods. **These listeners are called reflectively**
??? abstract "Example"
	```java title="ExampleClass.java"
	@Mod(modid = "modid", name = "Mod Name", version = "1.0")
	public class ExampleClass {

		@EventHandler
		public void runOnPreInit(FMLPreInitializationEvent event) {
			// This block of code will run when FMLPreInitializationEvent is happening
		}

	}
	```
- Other types of events are more flexible in how they're being registered. **These listeners are called natively**
	1. Annotation Magic: `@EventBusSubscriber` class level annotation
		- These classes must withhold from being loaded before annotations are processed.
		- If it is annotated with `@Mod`, the `modid` argument isn't needed, otherwise it is needed for recognition sake.
		- Any methods in here that wants to listen to an event **must** be static.
		??? abstract "Example"
			```java title="ExampleClass.java"
			@EventBusSubscriber(modid = "modid")
			public class ExampleClass {

				@SubscribeEvent
				public static void thisIsAEventListener(Event event) {
					// This block of code will run when whichever Event is denoted in the argument
				}

			}
			```

	2. 	EVENT_BUS interaction:
		- Events are ran on different event buses, Forge originally wanted to differentiate events properly, then realised that EventBuses are really confusing.
		- All the EventBuses can be found in `MinecraftForge.class`, those being `EVENT_BUS`, `TERRAIN_GEN_BUS` and `ORE_GEN_BUS`.
		- Technically a mod can implement their own buses, but there doesn't seem to be any in the wild.
		- Call `register` on any EventBuses and pass through either a class or an object that you want the buses to fire events to. 
			- **Class = static methods accepted only.**
			??? abstract "Example"
				```java title="StaticExample.java"
				public class StaticExample {

					public static void register() {
						MinecraftForge.EVENT_BUS.register(EventListener.class);
					}

					public static class EventListener {

						@SubscribeEvent
						public static void thisListenerWillRun(Event event) {
							// This method is static
							// This block of code will run when whichever Event is denoted in the argument
						}

						@SubscribeEvent
						public void thisListenerWillNeverRun(Event event) {
							// This method is not static
						}

					}

				}
				```
			- **Object = member methods accepted only.**
			??? abstract "Example"
				```java title="MemberExample.java"
				public class MemberExample {

					public static void register() {
						MinecraftForge.EVENT_BUS.register(new EventListener());
					}

					public static class EventListener {

						@SubscribeEvent
						public void thisListenerWillRun(Event event) {
							// This method is not static
							// This block of code will run when whichever Event is denoted in the argument
						}

						@SubscribeEvent
						public static void thisListenerWillNeverRun(Event event) {
							// This method is static
						}

					}

				}
				```
