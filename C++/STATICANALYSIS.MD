# C++ Static Analysis Tools

This document outlines the best static analysis tools for C++ that can be used via MCP or CLI.

## 1. Clang Static Analyzer

**Category**: Bug detection, memory issues
**Integration**: CLI, LLVM/Clang toolchain
**MCP Support**: Via command execution

### Installation
```bash
# Ubuntu/Debian
sudo apt-get install clang-tools

# macOS
brew install llvm

# Windows
# Download from LLVM website
```

### Usage
```bash
# Basic analysis
clang++ --analyze myfile.cpp

# With specific checker
clang++ --analyze -Xanalyzer -analyzer-checker=core.CallAndMessage myfile.cpp

# Generate HTML report
clang++ --analyze -Xanalyzer -analyzer-output=html myfile.cpp
```

### Key Features
- Memory leak detection
- Null pointer dereference detection
- Buffer overflow identification
- Logic error detection
- Cross-platform support

## 2. Cppcheck

**Category**: Bug detection, style issues
**Integration**: CLI, GUI
**MCP Support**: Via command execution

### Installation
```bash
# Ubuntu/Debian
sudo apt-get install cppcheck

# macOS
brew install cppcheck

# Windows
# Download from cppcheck.sourceforge.io
```

### Usage
```bash
# Basic analysis
cppcheck myfile.cpp

# Check all configurations
cppcheck --enable=all myfile.cpp

# Generate XML report
cppcheck --xml myfile.cpp 2> report.xml

# Check specific directories
cppcheck --enable=all --inconclusive src/
```

### Key Features
- Memory leak detection
- Array out of bounds checking
- Resource leak identification
- Style and performance suggestions
- Misra C/C++ compliance checking

## 3. PVS-Studio

**Category**: Multi-category analysis (bugs, security, performance)
**Integration**: CLI, IDE plugins
**MCP Support**: Via command execution

### Installation
Download from PVS-Studio website (commercial tool with free version for open source).

### Usage
```bash
# Analyze CMake project
pvs-studio-analyzer analyze -o project.log

# Convert to readable format
plog-converter -t tasklist -o project.tasks project.log

# Analyze specific files
pvs-studio-analyzer analyze --source-files *.cpp
```

### Key Features
- 400+ diagnostic rules
- OWASP ASVS compliance
- CERT secure coding standard
- CWE classification
- MISRA standards support

## 4. PC-lint/PC-lint Plus (FlexeLint)

**Category**: Code quality, compliance
**Integration**: CLI, IDE plugins
**MCP Support**: Via command execution

### Installation
Commercial tool from Gimpel Software. Download and install from their website.

### Usage
```bash
# Basic analysis
pclp64 co-gcc.lnt myfile.cpp

# Generate HTML report
pclp64 -hs myfile.cpp

# Analyze with specific configuration
pclp64 std.lnt au-misra3.lnt myfile.cpp
```

### Key Features
- MISRA C/C++ compliance
- Customizable warning levels
- False positive suppression
- Cross-reference information
- Flow analysis

## 5. Coverity Static Analysis

**Category**: Security, quality, compliance
**Integration**: CLI, cloud service
**MCP Support**: Via command execution

### Installation
Commercial tool from Synopsys. Available as on-premises or cloud service.

### Usage
```bash
# Configure project
cov-configure --comptype gcc --compiler gcc

# Build and analyze
cov-build --dir cov-int make

# Upload to cloud for analysis
tar czvf myproject.tgz cov-int
# Upload via web interface or API
```

### Key Features
- Deep path exploration
- Security vulnerability detection
- Standards compliance (MISRA, CERT)
- Integration with CI/CD
- Cloud-based analysis

## 6. Infer

**Category**: Bug detection
**Integration**: CLI
**MCP Support**: Via command execution

### Installation
```bash
# Ubuntu/Debian
wget -O infer.tar.xz https://github.com/facebook/infer/releases/latest/download/infer-linux64.tar.xz
tar xf infer.tar.xz
sudo ln -s "$PWD/infer-linux64/bin/infer" /usr/local/bin/infer

# macOS
brew install infer
```

### Usage
```bash
# Analyze compile command
infer run -- make

# Analyze CMake project
infer run -- cmake --build .

# Analyze specific files
infer -- clang++ -c hello.cpp
```

### Key Features
- Null pointer dereference detection
- Resource leak identification
- Memory safety violations
- Concurrency issues
- Facebook-developed analyzer

## 7. CodeSonar

**Category**: Security, quality, compliance
**Integration**: CLI, GUI
**MCP Support**: Via command execution

### Installation
Commercial tool from GrammaTech. Contact for licensing and installation.

### Usage
```bash
# Create project
codesonar create-project myproject

# Analyze with GCC
codesonar analyze myproject gcc myprogram.c

# Analyze with Clang
codesonar analyze myproject clang++ myprogram.cpp
```

### Key Features
- Deep semantic analysis
- Security vulnerability detection
- Standards compliance
- Scalable for large projects
- Detailed visualization

## 8. Klocwork

**Category**: Security, quality, compliance
**Integration**: CLI, IDE plugins, cloud service
**MCP Support**: Via command execution

### Installation
Commercial tool from Perforce. Contact for licensing and installation.

### Usage
```bash
# Analyze project
kwinject make
kwbuildproject --url http://localhost:8080 kwinject.out
kwadmin load myproject mybuildspec.zip
```

### Key Features
- Security vulnerability detection
- Standards compliance (MISRA, CERT)
- Integration with CI/CD
- Cloud and on-premises deployment
- Detailed reporting

## Recommended Tool Combination

For comprehensive C++ static analysis, we recommend using the following combination:

1. **Cppcheck** - Free, comprehensive basic analysis
2. **Clang Static Analyzer** - Integrated with LLVM toolchain
3. **PVS-Studio** - Advanced commercial option with free tier
4. **Infer** - Facebook's powerful open-source analyzer

## CI/CD Integration Example

```yaml
# GitHub Actions example
- name: Install static analysis tools
  run: |
    sudo apt-get update
    sudo apt-get install -y cppcheck clang-tools

- name: Run Cppcheck
  run: |
    cppcheck --enable=all --inconclusive --xml src/ 2> cppcheck-report.xml

- name: Run Clang Static Analyzer
  run: |
    scan-build make

- name: Archive analysis results
  uses: actions/upload-artifact@v3
  if: always()
  with:
    name: static-analysis-reports
    path: |
      cppcheck-report.xml
      ./*/index.html
```

## MCP Integration Approach

For MCP integration, you can execute these tools via command line and parse their output:

```cpp
// Example MCP command execution
#include <cstdlib>
#include <iostream>

int main() {
    int result = std::system("cppcheck --enable=all --xml src/ 2> report.xml");
    if (result == 0) {
        std::cout << "Analysis completed successfully" << std::endl;
    } else {
        std::cout << "Analysis found issues or failed" << std::endl;
    }
    return 0;
}
```

## Configuration Tips

1. **Suppressions**: Use suppression files for third-party code
2. **Performance**: Limit analysis depth for large projects
3. **Incremental**: Use incremental analysis for faster results
4. **Standards**: Enable specific coding standards (MISRA, CERT)
5. **Integration**: Configure different checkers for different build configurations