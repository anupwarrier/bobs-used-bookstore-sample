# Next Steps

## Validation and Testing

### 1. Verify Project Configuration

- **Confirm Target Framework**: Ensure all projects are targeting a compatible .NET version (e.g., `net6.0`, `net7.0`, or `net8.0`). Check each `.csproj` file for consistent `<TargetFramework>` values.
- **Review Package References**: Verify that all NuGet packages have been updated to versions compatible with cross-platform .NET. Pay special attention to:
  - Entity Framework (should be `Microsoft.EntityFrameworkCore` instead of legacy EF)
  - AWS CDK packages (ensure they support .NET Core/5+)
  - Any web framework dependencies (ASP.NET Core packages)

### 2. Build Verification

- **Clean and Rebuild**: Execute a clean build to ensure no artifacts from the legacy project remain:
  ```bash
  dotnet clean
  dotnet build
  ```
- **Check for Warnings**: Review build warnings that may indicate deprecated APIs or potential runtime issues, even though the build succeeded.

### 3. Unit and Integration Testing

- **Run Existing Tests**: Execute the test suite in `Bookstore.Domain.Tests`:
  ```bash
  dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj
  ```
- **Verify Test Results**: Ensure all tests pass. Investigate any failures, as they may indicate:
  - Behavioral differences between .NET Framework and cross-platform .NET
  - Issues with test dependencies or mocking frameworks
  - Database connection or data access layer problems

### 4. Database and Data Layer Validation

- **Test Database Connectivity**: Verify that `Bookstore.Data` can successfully connect to your database:
  - Check connection strings are correctly configured
  - Ensure database provider packages are compatible (e.g., SQL Server, PostgreSQL)
  - Test basic CRUD operations
- **Validate Migrations**: If using Entity Framework migrations, ensure they run correctly:
  ```bash
  dotnet ef database update --project app/Bookstore.Data
  ```

### 5. Web Application Testing

- **Local Execution**: Run the web application locally:
  ```bash
  dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj
  ```
- **Functional Testing**: Verify core functionality:
  - Test all major user workflows
  - Verify authentication and authorization mechanisms
  - Check API endpoints (if applicable)
  - Test static file serving and routing
- **Configuration Review**: Ensure `appsettings.json` and environment-specific configurations are properly set up for cross-platform deployment.

### 6. CDK Infrastructure Validation

- **Synthesize CDK Stack**: Verify the CDK project generates valid CloudFormation templates:
  ```bash
  cd app/Bookstore.Cdk
  cdk synth
  ```
- **Review Generated Templates**: Examine the output for any issues or unexpected changes from the legacy version.
- **Diff Against Existing Infrastructure**: If you have existing deployed infrastructure, compare changes:
  ```bash
  cdk diff
  ```

### 7. Cross-Platform Compatibility Testing

- **Test on Target Platforms**: Run the application on all intended deployment platforms:
  - Windows
  - Linux
  - macOS (if applicable)
- **Verify File Path Handling**: Ensure file paths use `Path.Combine()` and are not hardcoded with Windows-specific separators.
- **Check Environment Variables**: Confirm environment variable access works consistently across platforms.

### 8. Performance and Compatibility Assessment

- **Benchmark Critical Paths**: Compare performance of key operations between the legacy and migrated versions.
- **Memory Profiling**: Check for memory leaks or excessive allocations, as garbage collection behavior differs between .NET Framework and modern .NET.
- **Third-Party Dependencies**: Verify all third-party libraries function correctly in the new runtime.

## Deployment Preparation

### 9. Publish the Application

- **Create Publish Profiles**: Generate deployment packages for your target environment:
  ```bash
  dotnet publish app/Bookstore.Web/Bookstore.Web.csproj -c Release -o ./publish
  ```
- **Verify Published Output**: Ensure all necessary files, dependencies, and configurations are included in the publish directory.

### 10. Deploy CDK Infrastructure

- **Deploy to AWS**: If CDK validation was successful, deploy the infrastructure:
  ```bash
  cd app/Bookstore.Cdk
  cdk deploy
  ```
- **Monitor Deployment**: Watch for any errors during stack creation or updates.

### 11. Deploy Web Application

- **Deploy to Target Environment**: Use your standard deployment process to deploy the published application to:
  - AWS Elastic Beanstalk
  - EC2 instances
  - App Service
  - Or your chosen hosting platform
- **Verify Deployment**: After deployment, perform smoke tests to ensure the application starts and responds correctly.

### 12. Post-Deployment Validation

- **Monitor Application Logs**: Check for runtime errors or warnings that didn't appear during local testing.
- **Verify Database Operations**: Ensure data access works correctly in the production environment.
- **Test End-to-End Workflows**: Execute critical business processes to confirm full functionality.
- **Performance Monitoring**: Observe application performance metrics and compare with baseline expectations.

## Documentation Updates

### 13. Update Project Documentation

- **README Updates**: Revise build and deployment instructions to reflect .NET cross-platform requirements.
- **Dependency Documentation**: Document any changes to third-party libraries or their versions.
- **Configuration Guide**: Update environment setup instructions for developers and operators.
- **Known Issues**: Document any behavioral differences or limitations discovered during migration.