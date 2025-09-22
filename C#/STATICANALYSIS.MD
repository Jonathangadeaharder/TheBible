# C# Static Analysis Tools

This document outlines the best static analysis tools for C# that can be used via MCP or CLI.

## 1. SonarScanner for .NET

**Category**: Multi-category analysis (bugs, vulnerabilities, code smells)
**Integration**: CLI, CI/CD
**MCP Support**: Via command execution

### Installation
```bash
dotnet tool install --global dotnet-sonarscanner
```

### Usage
```bash
dotnet sonarscanner begin /k:"project-key" /d:sonar.login="token"
dotnet build
dotnet sonarscanner end /d:sonar.login="token"
```

### Key Features
- Detects bugs, vulnerabilities, and code smells
- Security hotspot identification
- Code coverage integration
- Technical debt metrics
- Supported languages: C#, VB.NET, C, C++, JavaScript, TypeScript

## 2. ReSharper Command Line Tools (JetBrains)

**Category**: Code quality, refactoring suggestions
**Integration**: CLI
**MCP Support**: Via command execution

### Installation
Download from JetBrains website or use:
```bash
# Via NuGet
nuget install JetBrains.ReSharper.CommandLineTools
```

### Usage
```bash
# Inspect code
jb inspectcode YourSolution.sln --output=inspection.xml

# Cleanup code
jb cleanupcode YourSolution.sln
```

### Key Features
- Code quality analysis
- Code style enforcement
- Dead code detection
- Redundancy identification
- Solution-wide analysis

## 3. Microsoft Code Analysis (FxCopAnalyzers)

**Category**: Code quality, design guidelines
**Integration**: NuGet package, MSBuild
**MCP Support**: Via build execution

### Installation
```xml
<!-- In .csproj file -->
<PackageReference Include="Microsoft.CodeAnalysis.NetAnalyzers" Version="7.0.0">
  <PrivateAssets>all</PrivateAssets>
  <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
</PackageReference>
```

### Usage
```bash
# Automatically runs during build
dotnet build

# Run standalone
dotnet build /p:RunAnalyzers=true
```

### Key Features
- .NET design guidelines enforcement
- Performance optimization suggestions
- Security vulnerability detection
- Interoperability best practices
- Portability analysis

## 4. StyleCop Analyzers

**Category**: Style and consistency
**Integration**: NuGet package, MSBuild
**MCP Support**: Via build execution

### Installation
```xml
<PackageReference Include="StyleCop.Analyzers" Version="1.2.0-beta.435">
  <PrivateAssets>all</PrivateAssets>
  <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
</PackageReference>
```

### Usage
```bash
# Configure rules in stylecop.json
dotnet build
```

### Key Features
- Style consistency enforcement
- Documentation requirements
- Layout and spacing rules
- Readability improvements
- Maintainability focus

## 5. Security Code Scan

**Category**: Security vulnerability detection
**Integration**: NuGet package, MSBuild
**MCP Support**: Via build execution

### Installation
```xml
<PackageReference Include="SecurityCodeScan.VS2019" Version="5.6.7">
  <PrivateAssets>all</PrivateAssets>
  <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
</PackageReference>
```

### Usage
```bash
dotnet build
```

### Key Features
- OWASP Top 10 vulnerability detection
- Cryptographic weakness identification
- Input validation issues
- Injection flaw detection
- Configuration security checks

## 6. Roslynator

**Category**: Code analysis and refactoring
**Integration**: CLI, Visual Studio extension
**MCP Support**: Via command execution

### Installation
```bash
dotnet tool install --global roslynator.dotnet.cli
```

### Usage
```bash
# Analyze solution
roslynator analyze YourSolution.sln

# Fix issues
roslynator fix YourSolution.sln
```

### Key Features
- 500+ analyzers and refactorings
- Performance improvements
- Code simplification suggestions
- Redundancy elimination
- Modern C# feature adoption

## 7. NDepend

**Category**: Architecture and complexity analysis
**Integration**: CLI, Visual Studio extension
**MCP Support**: Via command execution

### Installation
Download from NDepend website and install.

### Usage
```bash
# Run analysis
ndepend.console.exe NDependProject.ndproj
```

### Key Features
- Architecture quality metrics
- Dependency cycle detection
- Code complexity analysis
- Technical debt calculation
- Quality gates enforcement

## Recommended Tool Combination

For comprehensive C# static analysis, we recommend using the following combination:

1. **Microsoft Code Analysis** - Built-in .NET analyzer for design guidelines
2. **SonarScanner** - Multi-category analysis with security focus
3. **ReSharper CLI Tools** - Code quality and redundancy detection
4. **Security Code Scan** - Specialized security vulnerability detection

## CI/CD Integration Example

```yaml
# GitHub Actions example
- name: Setup .NET
  uses: actions/setup-dotnet@v3
  with:
    dotnet-version: '6.0.x'

- name: Restore dependencies
  run: dotnet restore

- name: Build with code analysis
  run: dotnet build --no-restore

- name: Run SonarScanner
  run: |
    dotnet sonarscanner begin /k:"your-project-key" /d:sonar.login="${{ secrets.SONAR_TOKEN }}"
    dotnet build
    dotnet sonarscanner end /d:sonar.login="${{ secrets.SONAR_TOKEN }}"

- name: Run ReSharper InspectCode
  run: jb inspectcode YourSolution.sln --output=results.xml
```

## MCP Integration Approach

For MCP integration, you can execute these tools via command line and parse their output:

```csharp
// Example MCP command execution
var process = new Process
{
    StartInfo = new ProcessStartInfo
    {
        FileName = "dotnet",
        Arguments = "build /p:RunAnalyzers=true",
        RedirectStandardOutput = true,
        RedirectStandardError = true,
        UseShellExecute = false
    }
};

process.Start();
var output = process.StandardOutput.ReadToEnd();
process.WaitForExit();
```

## Configuration Tips

1. **Rule Sets**: Customize rules in .ruleset files for Microsoft analyzers
2. **Suppression**: Use #pragma warning disable for legitimate exceptions
3. **Baseline**: Create suppression files for existing issues in legacy code
4. **Performance**: Limit analysis scope for large solutions during development
5. **Integration**: Configure different rule severities for build vs. IDE analysis