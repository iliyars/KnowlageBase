#### Self contained

[.Net Rid catalog](https://learn.microsoft.com/ru-ru/dotnet/core/rid-catalog)
В файле проекта .csproj добавить тэг `<RuntimeIdentifier>` 
``` .csproj
  <PropertyGroup>
    <RuntimeIdentifier>win-x64</RuntimeIdentifier>
  </PropertyGroup>
```
В developer console написать команду
``` console
dotnet publish -c Release --self-contained
```

