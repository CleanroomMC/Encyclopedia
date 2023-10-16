# Getting Started

## Adding ModularUI to your mod

Add the maven to the repositories (if not already present)

````groovy
maven {
    name 'CurseMaven'
    url 'https://cursemaven.com'
    content {
        includeGroup 'curse.maven'
    }
}
````

and the dependency declaration to dependencies

````groovy
implementation 'curse.maven:modularui-624243:4786372-sources-4786373'
````

- `624243` is the Curseforge project id of ModularUI
- `4779301` is the `dev` file id (for version 2.2.3)
- `4779302` is the `sources` file id (for version 2.2.3)

By including the sources in your dependency declaration you'll get access to the real uncompiled code with javadocs.

Make sure to use the latest version!

## Development Tools

I highly recommend using IntelliJ in combination with the Single Hotswap plugin.

## Other documentation

As always, the code itself is the best documentation. Most API classes have lots of useful javadoc information.

## Creating a GUI

First you need to decide if you want a client only screen or a client-server synced gui.

### Client only GUI

Client only GUIs are easier to work with, but they can't communicate with the server.
You can open one by calling `ClientGUI.open(ModularScreen, JeiSettings)`.
`JeiSettings` can be omitted by an overloaded method. Client only GUIs don't display jei on the side by default.
You can change that by creating your own `JeiSettings` instance. The `ModularScreen` should be a new instance.

Go [here](client_gui_tutorial.md) to get started on creating a client GUI. Even if you are looking into making a synced 
GUI, I still recommend
checking it out as it contains some information which is useful for both cases.

### Synced GUI

Synced GUIs are much more complicated to open. The most important thing is that you must only open synced GUIs
on server side. ModularUI (via Forge) automatically syncs to client. This means you need to supply enough information on
server side to find the GUI on client side. For example if the GUI is bound to a `TileEntity`, then you need to supply
at least a world instance and a block position. `GuiInfo`s take care of that information.
Not that Forge only syncs `EntityPlayer`, `World` and a `BlockPos`. For more specific data you need multiple `GuiInfo`s.
For example if a GUI is opened on right click with an item. The `GuiInfo` does not know with wich hand the GUI was
opened.
That's why ModularUI provides two `GuiInfo`s for that. One for main hand and one for the off hand.
ModularUI provides some default `GuiInfo`s at `GuiInfos`. If you want to create your own `GuiInfo`, take a look at
`GuiInfo.builder()`.
Now that you have your `GuiInfo`, simply call `open()` with your needed parameters.

Go [here](synced_gui_tutorial.md) to get started on creating a synced GUI.
