# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Repository overview

This repository currently contains only Git metadata and no source files or .NET projects yet. It is intended for C#/.NET development.

Future instances of Warp should first detect the concrete structure before making changes:
- Look for solution files (`*.sln`) and project files (`*.csproj`).
- Infer the main application entrypoint from `Program.cs` (or equivalent) in the primary project.
- Identify test projects (commonly named `*.Tests.csproj` or located under a `tests/` or `test/` directory).

All commands below assume you are in the repository root or a solution/project directory and that the .NET SDK (`dotnet` CLI) is installed.

## Common commands

### Discovering projects and solutions

Use `find` to locate solutions and projects (adjust paths/filters as needed):

```bash
find . -maxdepth 6 \( -name "*.sln" -o -name "*.csproj" \)
```

Once a solution exists, list its projects:

```bash
dotnet sln path/to/YourSolution.sln list
```

### Restore, build, and run

From the solution directory (preferred) or a project directory:

- Restore NuGet packages:

```bash
dotnet restore
```

- Build all projects in the current solution or project:

```bash
dotnet build
```

If there are multiple solutions, specify which one to build:

```bash
dotnet build path/to/YourSolution.sln
```

To run an executable project (console app, web app, etc.):

```bash
dotnet run --project path/to/YourProject.csproj
```

### Tests

Run all tests for the solution (from the solution directory):

```bash
dotnet test
```

Run tests for a specific test project:

```bash
dotnet test path/to/YourTests.csproj
```

Run tests for a single test class or method using filters (adjust the filter to the actual namespace/class/method):

```bash
# By fully-qualified name (namespace + class + method)
dotnet test path/to/YourTests.csproj \
  --filter "FullyQualifiedName~YourNamespace.YourTestClass.YourTestMethod"

# By test display name (exact match)
dotnet test path/to/YourTests.csproj --filter "Name=YourTestMethod"
```

Run tests continuously on file changes (where supported):

```bash
dotnet watch test
```

### Formatting and analysis

If the repository enables formatting or analyzers via the .NET SDK, you can usually run:

```bash
dotnet format
```

This will respect any `.editorconfig` and analyzer configuration present in the solution or projects.

## Architecture and structure guidance for future agents

Because there is no application code checked in yet, the precise architecture cannot be described. Future Warp agents should:

1. **Identify top-level layout**
   - Check for common directory conventions such as `src/`, `app/`, `tests/` or `test/`.
   - Map each `.csproj` in `src/` (or equivalent) to its corresponding test project in `tests/` or `test/`.

2. **Classify project roles**
   - Web/API projects: look for ASP.NET Core hosting patterns (e.g., `Program.cs` with `WebApplication.CreateBuilder`, `Controllers/`, `Endpoints/`).
   - Libraries: projects referenced by apps but not directly executable.
   - Test projects: projects that reference `xunit`, `nunit`, `MSTest`, or similar testing frameworks.

3. **Summarize architecture at a high level**
   - Describe how the main app(s) depend on internal libraries.
   - Note where cross-cutting concerns live (e.g., shared `Infrastructure`, `Domain`, or `Common` projects) if such projects exist.
   - Highlight any major patterns once they are detectable (e.g., clean architecture layers, modular monolith, microservices).

When editing or generating code, prefer to follow the existing structure and conventions discovered from the actual projects and solutions in this repository.