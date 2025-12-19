# Purpose

This repo shows a vulnerability with .NET 10 where the command 'dotnet list package --vulnerable --include-transitive' does not show vulnerable transitive packages when it should. 

This issue is reported for .NET, but also appears to adversely affect Visual Studio 2026. See issue report here: https://github.com/dotnet/sdk/issues/52245

The following vulnerable transitive packages are contained in the project via GraphQL.AspNetCore v1.1.4

- Moderate -> Microsoft.AspNetCore.Server.IIS 2.2.0
- Critical -> Microsoft.AspNetCore.Server.Kestrel.Core 2.2.0
- Moderate -> System.Security.Cryptography.Xml 4.5.0

## Summary

- run the command 'dotnet list package --vulnerable --include-transitive' in the WebApp dir, the report shows no vulnerable transitive packages
- run the command 'dotnet list package --vulnerable --include-transitive' in the root dir, the report only vulnerable transitive packages for the WebApp.Tests project
- remove the WebApp.Tests project and run the command 'dotnet list package --vulnerable --include-transitive' in the root dir, the report shows no vulnerable transitive packages

It's incredibly odd that the vulnerabilities would get picked up with a test project reference, but not on the project containing the top-level package that contains the vulnerabilities!

On branch net8, a version of this project in .NET 8 is included, where if you run the similar commands from .NET 8 SDK the command works as expected. 

## Impact

'dotnet list package --vulnerable --include-transitive' is the command recommended for security auditing per official documentation. https://learn.microsoft.com/en-us/nuget/concepts/auditing-packages

When this command does not function properly, it can cause build pipelines to pass when they should fail, which can result in unwittingly shipping Critical vulnerabilities to Production.