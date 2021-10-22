# Unreal

## Folder structure

For game projects:

- Binaries: compiled game binaries & debug databases
- Config: Default configuration files
- Content: Content asset packages
- Intermediate: Temporary files - okay to delete these as they will be re-generated
- Saved: Runtime configuration files & log files
- Source: C++ Source code

The project is described by a `.uproject` file.

For plugin projects:

- Binaries: compiled plugin binaries & debug databases
- Content: Content asset packages
- Intermediate: Temporary files - okay to delete these as they will be re-generated
- Resources: by default has the icon for your plugin, but is designed to hold any other files used by the plugin
- Source: C++ Source code

The plugin is described by a `.uplugin` file.

## Modules

A module is a folder that contains a `Build.cs` file that describes the module, and contains the source that implements the functionality of the module.

A new Unreal project will contain in the `Source` folder a default module with the same name as the project. Source files for modules are the standard C++ `.h` and `.cpp` files. Occasionally `.inl` files may appear, which stands for `inline header`, which as it's name implies, contains some inline implementations.

The `Plugins` folder will also contain the source for plugins being developed in a module. Plugins, since they are intended to be exported and re-used in other projects, additionally splits source into two directories:

- `Public`: This contains any header files that will expose functionality to other modules.
- `Private`: This will contain the `.cpp` files as well as any `.h`

You can also see the modules provided as part of Unreal under the `Source` folder of the `UE4` project inside the `Engine` folder. These modules are split into the following types:

- Development: For any appliction, bu used during development only
- Editor: For use in Unreal Editor only
- Runtime: For any application at run time
- ThirdParty: Code and libraries from external third parties

Note: The UE4 EULA prohibits inclusion of Editor modules in shipping games and apps
