# Sisk Specification generator

To build markdown specification files, you must compile Sisk in the Release configuration. Make sure you have this snippet in your `Sisk.Core.csproj`:

```xml
<PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Release|AnyCPU'">
	<DebugType>embedded</DebugType>
</PropertyGroup>
```

After that you can compile GenerateMdDoc and run it with the generated `Sisk.Core.xml` from the Sisk build:

```powershell
PS .\GenerateMdDoc.exe \path\to\Sisk.Core.xml
```

> It's necessary to create an directory called `Output` next to the `GenerateMdDoc` binary before running it.