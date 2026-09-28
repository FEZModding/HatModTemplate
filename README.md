# HAT mod template

A small C# code mod for [HAT](https://github.com/FEZModding/HAT).

> [!NOTE]
> The sample component logs the current FEZ level when it changes.

## Start a mod

1. Use this directory as a new repository. Rename `HatModTemplate.csproj`, `HatModTemplate` in `ModComponent.cs`, and the matching values in `Metadata.xml`. The metadata `Entrypoint` must be the full namespace and class name of a public `GameComponent` with a public `Game` constructor.
2. Copy `UserProperties.xml.template` to `UserProperties.xml`. Set `FezDir` to the FEZ installation directory that contains `HAT.dll`, `FezEngine.dll`, and `FNA.dll`. This file is ignored by Git so local paths stay private.
3. Install the .NET 10 SDK and run `dotnet build -c Release`.
4. The build puts `HatModTemplate.dll`, its `.deps.json`, and `Metadata.xml` in `out/`. Copy these files to a folder under FEZ's `Mods` directory, or set `ModOutputDir` in `UserProperties.xml` to build there directly. Restart FEZ to load the mod.

`Metadata.xml` names the library and the single component HAT should load. To add more components, create them from `ModComponent` through `ServiceHelper.AddComponent` or omit the `Entrypoint` element to let HAT load all public `GameComponent` classes.

Game and HAT assemblies are resolved from the local FEZ installation and are not committed to this repository. Add more references with `<Private>false</Private>` when your mod needs them.
