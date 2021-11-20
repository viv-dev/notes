# unreal-linux.md

Dependencies:

- cmake
- mono
- dotnet-sdk

```bash
sudo pacman -s cmake mono dotnet-sdk
```

Add the following extensions to vscode:

- C/C++
- C#
- CMake
- CMake Tools
- CodeLLDB
- Mono Debug
- Unreal Engine 4 Snippets
- Clang-Format

## Epic Game Store Content

Install reccommended wine dependencies from here: https://github.com/lutris/docs/blob/master/WineDependencies.md.

In addition, also ensure you install wine-mono (sadly missing from their installation guideo).

```bash
sudo pacman -S wine-mono
```

Install Lutris and then search Lutris for 'Epic Games Store' - follow all the installation prompts to install.

Once installed, you can run the launcher.

To download assets, the only thing you need to do is, in the wine-prefix folder location for the epic-game-store is create a dummy .uproject in `<c_drive>/users/<your_user>/Unreal Projects/Dummy`. This uproject file should contain the version of the engine you want to download assets for.

Restart the launcher, and it should appear as a recognised project. You can then install assets to it from the Vault, and then copy paste from the dummy project to your native linux project.

For plugins... it's a little more complicated. To do this, you need to install an entire version of the engine through the Epic Launcher - which means you'll end up with it taking up some hard drive space. Once downloaded though, you can then add plugins from the vault to the engine, and it will appear in the `<UE4 install location>/Engine/Plugins/Marketplace` directory.

If you're lucky, then the plugin might already be built with compatible linux binaries - in which case you can just copy the plugin to the equivalent location in your native linux installation and add it to projects via the plugin menu when you create them.

For plugins that don't provide native linux binaries, you'll need to hope they bundle the source with the plugin. If they do, then in your native linux enivironment create a blank project specifically for compiling plugins. Copy the plugin from the wine version of UE4 to the `Plugins` directory of this project. After copying the plugin, launch the project in the native linux UE4 editor and it will prompt to recompile the plugin for the target platform.

Once it compiles, you should have a `Linux` folder in the `Binaries` directory of the plugin. You can then cut this entire folder and move it to the Engine Marketplace location in order to be able to just 'enable' it from the plugins menu in future projects.
