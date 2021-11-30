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

If your project or plugin module depends on other modules, they must be added to your modules `.Build.cs` file.

## Unreal Macros

Unreal Engine C++ codebase uses a custom preprocessor, called the `Unreal Header Tool (UHT)` which generates custom Runtime Type Information (RTTI) from your C++ code. This is paired with a series of macros, which the UHT uses to generate special versions of the class that include additonal metadata that allows a form of 'reflection' to be performed at runtime.

Macros include (taken from (here)[https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/ProgrammingWithCPP/IntroductionToCPP/]):

- UCLASS() — Used to tell Unreal to generate reflection data for a class. The class must derive from UObject.
- USTRUCT() — Used to tell Unreal to generate reflection data for a struct.
- GENERATED_BODY() — UE4 replaces this with all the necessary boilerplate code that gets generated for the type.
- UPROPERTY() — Enables a member variable of a UCLASS or a USTRUCT to be used as a UPROPERTY. A UPROPERTY has many uses. It can allow the variable to be replicated, serialized, and accessed from Blueprints. They are also used by the garbage collector to keep track of how many references there are to a UObject.
- UFUNCTION() — Enables a class method of a UCLASS or a USTRUCT to be used as a UFUNCTION. A UFUNCTION can allow the class method to be called from Blueprints and used as RPCs, among other things.

## Unreal Char/Strings

Unreal defines it's own `TCHAR` type to handle different platform encodings for a char. When allocating a string literal to a `TCHAR *`, it should be done using the `TEXT()` macro in order to ensure it is specified in the correct encoding type for the underlying char type used by the platform. That is:

```c++
const TCHAR * some_string = TEXT("Some Text")
```

## Unreal HTTP

Unreal provides it's own HTTP client for being able to interact with web services. In order to use it, you must add the appropriate dependencies to your `.Build.cs` files:

```c#
    PrivateDependencyModuleNames.AddRange(
        new string[]
        {
            ... // Other project dependencies
            "Http",
            "Json",
            "JsonUtilities"
        }
    );
```

## Adding an Editor Settings Page

Reference (here)[https://nerivec.github.io/old-ue4-wiki/pages/customsettings.html].

To hide properties based on other settings, see (this)[https://benui.ca/unreal/uproperty-edit-condition-can-edit-change/].

To create enum drop downs that have a different name to the enum variable name, you can use the `UMETA` macro in addition to the `UENUM` macro:

```c++
UENUM()
enum class InfluxDBVersion {
	V1 UMETA(DisplayName = "v1.8"),
	V2 UMETA(DisplayName = "v2.0")
};
```

## Adding Custom Log Category

Reference (here)[https://blog.jamie.holdings/2020/04/21/unreal-engine-4-custom-log-categories/]
