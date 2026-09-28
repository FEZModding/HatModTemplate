# HAT mod template

A starting point for [HAT](https://github.com/FEZModding/HAT) code mods, asset mods, or both.

> [!NOTE]
> The sample component logs the current FEZ level when it changes.

## Code mod

1. Use this directory as a new repository. Rename `HatModTemplate.csproj`, `HatModTemplate` in `ModComponent.cs`, and the matching values in `Metadata.xml`. The metadata `Entrypoint` must be the full namespace and class name of a public `GameComponent` with a public `Game` constructor.
2. Copy `UserProperties.xml.template` to `UserProperties.xml`. Set `FezDir` to the FEZ installation directory that contains `HAT.dll`, `FezEngine.dll`, and `FNA.dll`. This file is ignored by Git so local paths stay private.
3. Install the .NET 10 SDK and run `dotnet build -c Release`.
4. The build puts `HatModTemplate.dll`, its `.deps.json`, `Metadata.xml`, and any assets in `out/`. Copy the **contents** of `out/` to a folder under FEZ's `Mods` directory, or set `ModOutputDir` in `UserProperties.xml` to build there directly. Restart FEZ to load the mod.

`Metadata.xml` names the library and the single component HAT should load. To add more components, create them from `ModComponent` through `ServiceHelper.AddComponent` or omit the `Entrypoint` element to let HAT load all public `GameComponent` classes.

Game and HAT assemblies are resolved from the local FEZ installation and are not committed to this repository. Add more references with `<Private>false</Private>` when your mod needs them.

## Assets with or without code

The separate `Assets.targets` file defines both asset packaging targets and is imported by the C# project.

Put loose assets under `Assets/`, keeping their game asset paths relative to that directory. For example, an asset named `background planes/gomez_house_a` belongs at `Assets/background planes/gomez_house_a.xnb` (or a supported source format such as `.png`). Music OGG files belong under `Assets/Music/`, such as `Assets/Music/villageville/bed.ogg`. You can also place a packed `Assets.pak` at the repository root. HAT loads loose assets and packed assets from the same mod.

For a **code and asset mod**, run the normal `dotnet build -c Release`. The build copies `Assets/` recursively and `Assets.pak` beside the DLL and metadata in `out/`.

For an **asset-only mod**, add at least one asset, then run `dotnet msbuild -t:PackageAssetsOnly`. This creates `out-assets/` with `Metadata.xml` and your assets; it does not need a .NET compile or `UserProperties.xml`. The target clears the code-only `LibraryName` and `Entrypoint` fields in its output metadata. Copy the **contents** of `out-assets/` to a folder under FEZ's `Mods` directory, or zip those contents with `Metadata.xml` at the ZIP root.

The build copies files but does not remove assets you deleted from source. Clear old output files before packaging an updated mod.
