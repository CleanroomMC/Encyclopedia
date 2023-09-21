A typical mixin configuration file looks like this:

```json
--8<--
https://raw.githubusercontent.com/CleanroomMC/MixinBooter/main/src/main/resources/mixin.mixinbooter.init.json
--8<--
```

???+ abstract
    Taken from MixinBooter's [`mixin.mixinbooter.init.json`](https://github.com/CleanroomMC/MixinBooter/blob/main/src/main/resources/mixin.mixinbooter.init.json)

All the mixin configurations and the purposes of them is-as follows:

- `parent`: Whether this configuration inherits from another. `[Optional: String]`
- `package`: The root package of where the mixins resides. `[Required: String]`
- `required`: Whether the mixins need to hard crash the game if it isn't applied properly. `[Optional: Boolean]`
    - Default value: `false`
- `refmap`: The file name of the refmap. `[Required: String]`
- `plugin`: Path to an implementation of `IMixinConfigPlugin` to do various mixin-related tasks before, during and after mixin applications
- `target`: Target environment. `[Optional: String]`
    - Default value: `@env(DEFAULT)`
    - Acceptable values: `@env(DEFAULT)` | `@env(PREINIT)` | `@env(INIT)`
??? warning
    `target` being set to `@env(DEFAULT)` and allowing MixinBooter to handle the target environments would result in the least problems.
- `minVersion`: Minimum compatible version of Mixin. `[Optional: String]`
??? tip
    `minVersion` set at `0.8.5` allows you to use all of MixinBooter's features and annotations without worry. However, setting it at `0.8` will allow for those without MixinBooter and running Mixin through other methods to have less chances of crashing.
- `compatibilityLevel`: Compatible Java version. `[Optional: String]`
    - Default value: `JAVA_6`
    - Acceptable values: any java version above 6, prefix the value with a `JAVA_` string
- `mixins`: Mixins to be applied, path is relative to the `package` property `[Optional: String Array]`
- `client`: Mixins to be only applied on the [Physical Client-side](../../../sidedness), path is relative to the `package` property `[Optional: String Array]`
- `server`: Mixins to be only applied on the [Physical Server-side](../../../sidedness), path is relative to the `package` property `[Optional: String Array]`
- `priority`: Default priority this config. `[Optional: Integer]`
    - Default value: `0`
    - Acceptable values: From `0` to `MAX_INTEGER`
- `mixinPriority`: Default priority of all mixins. `[Optional: Integer]`
    - Default value: `1000`
    - Acceptable values: From `0` to `MAX_INTEGER`
- `setSourceFile`: Override the classes that are mixin'd into to have the mixins' source file metadata instead. `[Optional: Boolean]`
    - Default value: `false`
- `refmapWrapper`: Path to an implementation of `IReferenceMapper` to provide fine-grained controls when remapping. `[Optional: String]`
- `verbose`: Verbose logging. `[Optional: Boolean]`
    - Default value: `false`
- `injectors`: Injector Options. `[Optional: Json Object]`
    - `defaultRequire`: Amount of times attempted at injecting. `[Optional: Integer]`
        - Default value: `0`
        - Acceptable values: From `0` to `MAX_INTEGER`
    - `maxShiftBy`: Amount of times an Injection can shift through opcodes. `[Optional: Integer]`
        - Default value: `0`
        - Acceptable values: From `0` to `5`
    - `defaultGroup`, `namespace`, `injectionPoints`, `dynamicSelectors` all are properties to do with the `InjectionPoint` and `ITargetSelectorDynamic` implementations. Their javadoc explains it the most in-depth.
- `overwrites`
    - `conformVisibility`: Whether the overwrite conforms the original method's visibility. `[Optional: Boolean]`
        - Default value: `false`
    - `requireAnnotations`: Allows overwrites to be performed without the `@Overwrite` annotation labelling the method. `[Optional: Boolean]`
        - Default value: `false`