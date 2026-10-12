# HAT mod template

Template for FEZ mods loaded by [HAT](https://github.com/FEZModding/HAT). Supports code mods, asset mods, and combined code and asset mods.

The sample `ModComponent` logs the current FEZ level name when it changes.

## Code mod configuration

Code mods require the .NET 10 SDK and a FEZ installation with HAT 3.0.

1. Rename `HatModTemplate.csproj` and the `HatModTemplate` namespace in `ModComponent.cs` for the mod.
2. Update the mod name, description, author, version, `LibraryName`, and `Entrypoint` in `Metadata.xml`.
3. Copy `UserProperties.xml.template` to `UserProperties.xml` and configure the properties below. `UserProperties.xml` is ignored by Git.

| Property       | Description                                                                                                                                                    |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `FezDir`       | FEZ installation directory containing `HAT.dll`, `FezEngine.dll`, and `FNA.dll`.                                                                               |
| `MonoModDir`   | MonoMod assembly directory. The properties template sets this to `$(FezDir)/HATDependencies/MonoMod`.                                                          |
| `ModOutputDir` | Mod package output directory. The properties template sets this to `$(FezDir)/Mods/HatModTemplate`; the project defaults to `out/` when the property is unset. |

`LibraryName` specifies the mod DLL filename. `Entrypoint` specifies the fully qualified name of a public `GameComponent` class with a public constructor that accepts a `Game` instance. The sample metadata requires HAT version 3.0 or later.

Additional components can be registered from `ModComponent` through `ServiceHelper.AddComponent`. If `Entrypoint` is omitted, HAT loads all public `GameComponent` classes.

Game, HAT, and MonoMod assemblies are referenced from local directories and are not included in the repository. Additional game assembly references should specify `<Private>false</Private>` to exclude them from build output.

## Asset layout

Loose assets are stored under `Assets/`, with paths relative to that directory matching game asset paths. Examples:

- `Assets/background planes/gomez_house_a.xnb` for the asset `background planes/gomez_house_a`. Supported source formats, such as `.png`, can also be used.
- `Assets/Music/villageville/bed.ogg` for a music OGG file.

A packed `Assets.pak` can be placed at the repository root. HAT supports loose and packed assets in the same mod.

Asset packaging targets are defined in `Assets.targets`, which is imported by the C# project.

## Build and packaging

Run commands from the repository root.

### Code and combined mods

Debug build:

```sh
dotnet build -c Debug
```

Release publish:

```sh
dotnet publish -c Release -p:ContinuousIntegrationBuild=true
```

Both commands copy the mod DLL, `.deps.json`, and `Metadata.xml` to `ModOutputDir`. Any files under `Assets/` are copied recursively to the output `Assets/` directory, and `Assets.pak` is copied to the output root if present.

### Asset-only mods

At least one file under `Assets/` or a root-level `Assets.pak` is required.

Asset-only mods can be packaged manually without the .NET 10 SDK. Place `Metadata.xml` and the `Assets/` directory, `Assets.pak`, or both in a package directory. Update the mod metadata and remove the `LibraryName` and `Entrypoint` elements or leave their values empty. Preserve the asset paths described in [Asset layout](#asset-layout).

Alternatively, use the .NET 10 SDK to run the packaging target:

```sh
dotnet msbuild -t:PackageAssetsOnly
```

This target packages metadata and assets without compiling code or requiring `UserProperties.xml`. Output defaults to `out-assets/` and can be changed through the `AssetOnlyOutputDir` property. The output metadata has empty `LibraryName` and `Entrypoint` fields.

Packaging copies files without removing output assets that were deleted from source. Remove obsolete output files before packaging an updated mod.

## Installation and distribution

Copy the contents of the package output directory to a mod folder under the FEZ installation's `Mods` directory. If `ModOutputDir` already points to that folder, the build writes the package there directly. Restart FEZ to load the mod.

For ZIP distribution, archive the package contents with `Metadata.xml` at the ZIP root.
