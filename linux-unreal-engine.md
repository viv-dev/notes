# Unreal Linux + VSCode

## UE4 Installation

For my installation, although you can follow instructions on Epics documentation, I noticed that the version on the Arch User Repository had implemented some patches that fixes some known issues.

Comments reported that even though it hadn't been updated recently, manually modifying the `PKGBUILD` file to point to the version of the engine you want to build still works and applies all but one patch correctly, so I decided to try it out.

#### Clone and Modify Package Files

To modify and build the package manually, first clone the package files:

```bash
git clone https://aur.archlinux.org/unreal-engine.git
```

Then edit the `PKGBUILD` file with the `pkgver` of the unreal version you want to build:

```ini
pkgname=unreal-engine
pkgver=4.27.1 # Change this
pkgrel=6
...
```

> Note that you can also edit other properties, such as the default install location, if something else is more convenient for your setup

Additionally, make sure you also have the `base-devel` package group installed:

```bash
sudo pacman -S --needed base-devel
```

#### Read the Arch Wiki

It's useful to read the Arch linux wiki pages for building packages to better understand what all the cloned files are. Useful wiki pages include:

- [How to download and build packages](https://wiki.archlinux.org/title/Arch_User_Repository)
- [How to create packages](https://wiki.archlinux.org/title/Creating_packages)
- [`PKGBUILD` reference](https://wiki.archlinux.org/title/PKGBUILD)
- [`mkpkg` reference](https://wiki.archlinux.org/title/Makepkg)
- [How patches work](https://wiki.archlinux.org/title/Patching_packages)

#### Get Unreal Source Code Access

For the clone step of the `PKGBUILD` to work, you need to have a github account setup that is configured with your default ssh key and has access to the unreal source repository - you can do so by following the instructions [here](https://www.unrealengine.com/en-US/ue4-on-github?sessionInvalidated=true).

#### Build Package

Once you have the correct Github access and a ssh key added to your github account, you can then build and install the engine by running the following command:

```bash
makepkg -sic
```

This will take a while (approx 27 minutes with a AMD 5950x for reference - anything less that 16 cores might take closer to an hour), and within the first 10 minutes or so there will be a pop up asking about asset installation. The first time I tried this, it was somehow using a foreign language font... if this happens to you, trust it and click what would be the 'yes' option.

#### Maintenance

Even if the package maintainer doesn't update the package, I'll likely use this as a reference and try to make my own modifications to the `PKGBUILD` and supporting files if needed in future, since it's much easier to manage version installations using `pacman`.

## Setting up Dependencies

With the engine built and running, you now need to setup the depenencies for your development environment for building Unreal projects.

Linux Dependencies:

- cmake
- mono
- dotnet-sdk

On arch based distros, installation is:

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

## Building in VSCode

In the Unreal Editor preferences (at least for the current version as of this writing - `4.27.1`), you can specify VSCode as the default editor. When set, it will automatically create VSCode configuration files when you create a new project, and you can manually trigger it to update those files from the Files menu.

Once the files are generated, when opening the project in VSCode there will be a `launch.json` and `tasks.json` file in the `.vscode` folder.

There are two ways to build the project - one using the `vscode` tasks options, and the second is using `cmake`. Both define a set of targets, however the only ones supported on linux are:\

- `DebugGame`
- `Development`
- `Shipping`

This corresponds to the second table [here](https://docs.unrealengine.com/4.27/en-US/ProductionPipelines/DevelopmentSetup/BuildConfigurations/). As this link explains, those particular targets only worry about building your specfic game code and not that of the editor.

Additionally, there are `<project name>` and `<project name>Editor` versions of the targets - when actively working on your project you want to be building the `Editor` versions, as they are configured to work with the non-cooked assets as configured in the editor and will let you iteratively debug code changes.

The non-Editor version requires you to cook and package your content so that it is standalone before being able to run/debug.

### Build Using VSCode Tasks

To build using vscode tasks you can do so by going to `Terminal` > `Run task...` which will bring up a list of targets. Choose one of the target versions as described above and it should build. You can also set one of the tasks as the 'default' and it will automatically build when you press `ctrl` + `shift` + `B`.

### Buid Using Cmake

To build using `cmake` (which is a bit more convenient, since the cmake extension adds a useful bottom tool bar), you'll first have to configure the project first, and then the bottom bar will appear with target and build options. Make sure you have at least one compiler cmake can detect installed (it doesn't matter which you pick, as the `CMakeLists.txt` file overwrites the defualt c++ compile to point to a specific version of `clang` shipped with the version of the engine you're using). From there in the target window, select a build target as per the above, and then you can click the `Build` button and it should start the build process.

Unfortunately, the `cmake` run/debug capabilities don't work, as there's only a 'FakeTarget' defined. You'll have to revert to using the vscode debug settings defined in the `launch.json` file.

## Debugging

As mentioned above, in order to be able to debug while activley iterating and making changes you will need to be building the `Editor` target. You also need all of the vscode extensions mentioned in the first section.

From here, you can go to the debug tab on the left side (the icon looks like a 'play' triangle with a little bug in front of it). Next, you'll need to set your default debug target to be `<project name>Editor (Debug Game)` to match the version you're actually building. If you have the wrong version set, you'll likely see an error like the following:

```bash
LogShaderLibrary: Error: Failed to initialize ShaderCodeLibrary
```

The correct debug version will launch an instance of the editor, and from there you can press play to run the game, and trigger any breakpoints you have setup in your VSCode environment.

## Epic Game Store Content

Install reccommended wine dependencies from here: https://github.com/lutris/docs/blob/master/WineDependencies.md.

In addition, also ensure you install wine-mono (sadly missing from their installation guide):

```bash
sudo pacman -S wine-mono
```

Install Lutris and then search Lutris for 'Epic Games Store' - follow all the installation prompts to install.

Once installed, you can run the launcher.

To download assets, the only thing you need to do is, in the wine-prefix folder location for the epic-game-store is create a dummy `.uproject` in `<c_drive>/users/<your_user>/Unreal Projects/Dummy`. This uproject file should contain the version of the engine you want to download assets for:

```json
{
  "FileVersion": 3,
  "EngineAssociation": "4.27",
  "Category": "",
  "Description": ""
}
```

Restart the launcher, and it should appear as a recognised project. You can then install assets to it from the Vault, and then copy paste from the dummy project to your native linux project.

For plugins... it's a little more complicated. To do this, you need to install an entire version of the engine through the Epic Launcher - which means you'll end up with it taking up some hard drive space. Once downloaded though, you can then add plugins from the vault to the engine, and it will appear in the `<wine UE4 install location>/Engine/Plugins/Marketplace` directory.

If you're lucky, then the plugin might already be built with compatible linux binaries - in which case you can just copy the plugin to the equivalent location in your native linux installation and add it to projects via the Editor plugin menu when you create them.

For plugins that don't provide native linux binaries, you'll need to hope they bundle the source with the plugin (most do, especially free ones, since unreal encourages it... but some paid for plugins seem to find a way around this). If they do, then in your native linux enivironment create a blank project specifically for compiling plugins (e.g. call it `PluginBuilder`). Copy the plugin from the wine version of UE4 to the `Plugins` directory of this project. After copying the plugin, launch the project in the native linux UE4 editor and it will prompt to recompile the plugin for your current platform.

Once it compiles, you should have a `Linux` folder in the `Binaries` directory of the plugin folder now. You can then cut the entire plugin folder and move it to the `<native UE4 install location>/Engine/Plugins/Marketplace`.
