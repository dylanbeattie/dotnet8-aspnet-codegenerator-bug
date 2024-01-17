# dotnet8-aspnet-codegenerator-bug
Reproduction code for a bug in `Microsoft.VisualStudio.Web.CodeGeneration.Design` 8.0.0

#### Symptoms:

The bug occurs when trying to run `dotnet aspnet-codegenerator` and specify a `DbContext`:

```
dotnet aspnet-codegenerator controller -name ArtistsController -m Artist -dc MyDbContext
```

If the application's `Program.Main` method calls any extension methods, the tool fails with:

```
Could not get the reflection type for DbContext : MyDbContext
  at Microsoft.VisualStudio.Web.CodeGeneration.ActionInvoker.<BuildCommandLine>b__6_0()
  at Microsoft.Extensions.CommandLineUtils.CommandLineApplication.Execute(String[] args)
  at Microsoft.VisualStudio.Web.CodeGeneration.ActionInvoker.Execute(String[] args)
  at Microsoft.VisualStudio.Web.CodeGeneration.CodeGenCommand.Execute(String[] args)
```

This happens with projects that reference `Microsoft.VisualStudio.Web.CodeGeneration.Design` v8.0.0. The same code works fine with `Microsoft.VisualStudio.Web.CodeGeneration.Design` v7.0.1, running under both .NET 7 and under .NET 8.

Two example projects are provided:

[dotnet7](dotnet7) references EF Core 7.x and Microsoft.VisualStudio.Web.CodeGeneration.Design 7.0.1

[dotnet8](dotnet8) references EF Core 8.x and Microsoft.VisualStudio.Web.CodeGeneration.Design 8.0.0

To reproduce the problem, check out the repo, open the `dotnet8` project:

```
dotnet build
dotnet aspnet-codegenerator controller -name ArtistsController -m Artist -dc MyDbContext
```

To verify the cause, comment out  `Program.cs` line 18:

```csharp
// builder.DoNothing();
```

and re-run the `aspnet-codegenerator` command; it should produce the expected output:

```
Building project ...
Finding the generator 'controller'...
Running the generator 'controller'...

Minimal hosting scenario!
Attempting to compile the application in memory.
Attempting to figure out the EntityFramework metadata for the model and DbContext: 'Artist'

Using database provider 'Microsoft.EntityFrameworkCore.Sqlite'!

Added Controller : '\ArtistsController.cs'.
Added View : \Views\Artists\Create.cshtml
Added View : \Views\Artists\Edit.cshtml
Added View : \Views\Artists\Details.cshtml
Added View : \Views\Artists\Delete.cshtml
Added View : \Views\Artists\Index.cshtml
RunTime 00:00:07.56
```





