# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The transformation appears to have completed successfully. No build errors were detected across any of the projects in the solution:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore Dependencies

Run a NuGet package restore to ensure all dependencies are resolved correctly before building:

```bash
dotnet restore
```

Review the output for any warnings about deprecated or incompatible packages and update them if necessary using:

```bash
dotnet list package --outdated
dotnet add package <PackageName>
```

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Ensure the build completes with zero errors and review any warnings that may indicate compatibility concerns.

---

## 3. Run Unit Tests

Execute the test project to verify that existing logic behaves as expected after the migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test output for:
- Any failing tests that may indicate behavioral regressions
- Any tests that were skipped or ignored that should be re-enabled
- Code coverage gaps in critical domain logic

---

## 4. Validate the Data Layer

Since `Bookstore.Data` handles data access, verify the following:

- **Database provider compatibility**: Confirm that the Entity Framework Core (or whichever ORM is in use) provider targets a cross-platform compatible version.
- **Connection strings**: Ensure connection strings in `appsettings.json` or environment variables are correctly configured for the target environment.
- **Migrations**: If using Entity Framework Core, verify existing migrations are intact and apply them against a test database:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

---

## 5. Validate the Web Application

Run the web application locally to confirm it starts and functions correctly:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Check the following:
- The application starts without runtime exceptions
- All routes and pages load as expected
- Static assets are served correctly
- Any authentication or session handling works as intended

---

## 6. Validate the CDK Project

If `Bookstore.Cdk` defines infrastructure, review the following:

- Confirm all CDK construct dependencies are compatible with the migrated .NET version
- Perform a CDK synthesis to validate the infrastructure definitions without deploying:

```bash
cdk synth
```

Review the synthesized output for any unexpected changes from the original infrastructure definition.

---

## 7. Review Target Framework Consistency

Confirm that all projects in the solution target the same .NET version to avoid interoperability issues. Open each `.csproj` file and verify the `<TargetFramework>` value is consistent, for example:

```xml
<TargetFramework>net8.0</TargetFramework>
```

---

## 8. Check for Removed or Changed APIs

Review the code for any use of APIs that were available in .NET Framework but have changed behavior or been removed in cross-platform .NET. Key areas to check include:

- `System.Web` references (not available in cross-platform .NET)
- `HttpContext` usage patterns
- Windows-specific APIs such as the registry or certain cryptography providers
- Configuration system changes from `Web.config` to `appsettings.json`

The [.NET Upgrade Assistant compatibility analyzer](https://learn.microsoft.com/en-us/dotnet/core/porting/upgrade-assistant-overview) can assist in identifying remaining compatibility issues.