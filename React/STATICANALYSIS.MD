# React/JavaScript Static Analysis Tools

This document outlines the best static analysis tools for React/JavaScript that can be used via MCP or CLI.

## 1. ESLint

**Category**: Code quality, style, errors
**Integration**: CLI, IDE plugins
**MCP Support**: Via command execution

### Installation
```bash
npm install eslint --save-dev
# For React projects
npm install eslint-plugin-react eslint-plugin-react-hooks --save-dev
```

### Usage
```bash
# Initialize configuration
npx eslint --init

# Analyze specific files
npx eslint src/

# Fix automatically fixable issues
npx eslint src/ --fix

# Generate report in different formats
npx eslint src/ -f json > report.json
```

### Key Features
- Syntax error detection
- Code quality rules
- React-specific rules
- Auto-fix capabilities
- Plugin architecture

## 2. Prettier

**Category**: Code formatting
**Integration**: CLI, IDE plugins
**MCP Support**: Via command execution

### Installation
```bash
npm install prettier --save-dev
```

### Usage
```bash
# Format files
npx prettier --write src/

# Check formatting without modifying
npx prettier --check src/

# Format specific file types
npx prettier --write "**/*.{js,jsx,ts,tsx,json,css,md}"
```

### Key Features
- Opinionated code formatter
- Consistent styling
- Editor integration
- Configuration options
- Fast execution

## 3. TypeScript Compiler (tsc)

**Category**: Type checking
**Integration**: CLI
**MCP Support**: Via command execution

### Installation
```bash
npm install typescript --save-dev
# For React projects
npm install @types/react @types/react-dom --save-dev
```

### Usage
```bash
# Type check project
npx tsc

# Watch mode
npx tsc --watch

# Emit declaration files
npx tsc --declaration

# No emit mode (check only)
npx tsc --noEmit
```

### Key Features
- Static type checking
- Interface and type support
- Generic types
- Module resolution
- Incremental compilation

## 4. SonarJS

**Category**: Code quality, bug detection
**Integration**: CLI, SonarQube
**MCP Support**: Via command execution

### Installation
Part of SonarQube/SonarCloud or as standalone ESLint plugin:
```bash
npm install eslint-plugin-sonarjs --save-dev
```

### Usage
```bash
# As ESLint plugin
npx eslint src/

# With SonarQube Scanner
sonar-scanner
```

### Key Features
- Bug detection
- Code smell identification
- Cognitive complexity analysis
- Duplicated code detection
- Security hotspot identification

## 5. React-specific ESLint Plugins

### eslint-plugin-react-hooks
```bash
npm install eslint-plugin-react-hooks --save-dev
```

### eslint-plugin-jsx-a11y (Accessibility)
```bash
npm install eslint-plugin-jsx-a11y --save-dev
```

### Usage
```bash
# Configure in .eslintrc.js
module.exports = {
  plugins: ['react-hooks', 'jsx-a11y'],
  rules: {
    'react-hooks/rules-of-hooks': 'error',
    'react-hooks/exhaustive-deps': 'warn',
    'jsx-a11y/alt-text': 'warn',
  }
};
```

### Key Features
- React Hooks rules enforcement
- Accessibility compliance checking
- Best practices validation
- Automatic fix suggestions

## 6. Bundle Analysis Tools

### webpack-bundle-analyzer
```bash
npm install webpack-bundle-analyzer --save-dev
```

### Usage
```bash
# Analyze bundle size
npx webpack-bundle-analyzer build/static/js/main.*.js
```

### Key Features
- Bundle size visualization
- Dependency analysis
- Code splitting optimization
- Duplicate module detection
- Tree-shaking effectiveness

## 7. Jest (Testing as Analysis)

**Category**: Test coverage, code quality
**Integration**: CLI
**MCP Support**: Via command execution

### Installation
```bash
npm install jest @types/jest --save-dev
```

### Usage
```bash
# Run tests with coverage
npm test -- --coverage

# Generate coverage report
npm test -- --coverageReporters=json-summary

# Threshold enforcement
npm test -- --coverage --collectCoverageFrom="src/**/*.{js,jsx,ts,tsx}"
```

### Key Features
- Test coverage analysis
- Code quality through testing
- Snapshot testing
- Mocking capabilities
- CI/CD integration

## 8. Deprecation Checkers

### npm-check
```bash
npm install -g npm-check
```

### Usage
```bash
# Check for outdated dependencies
npm-check

# Check with update suggestions
npm-check -u
```

### Key Features
- Outdated dependency detection
- Security vulnerability checking
- Breaking change identification
- Update recommendations
- Interactive update mode

## 9. Security Analysis

### npm audit
```bash
# Built into npm
npm audit
```

### snyk
```bash
npm install -g snyk
snyk test
```

### Key Features
- Vulnerability detection
- License compliance
- Dependency tree analysis
- Fix suggestions
- CI/CD integration

## 10. Performance Analysis

### Lighthouse CI
```bash
npm install -g @lhci/cli
```

### Usage
```bash
# Run Lighthouse analysis
lhci autorun
```

### Key Features
- Performance metrics
- Accessibility scoring
- SEO analysis
- Best practices evaluation
- Progressive Web App compliance

## Recommended Tool Combination

For comprehensive React/JavaScript static analysis, we recommend using the following combination:

1. **ESLint** - Code quality and error detection
2. **Prettier** - Code formatting
3. **TypeScript** - Static type checking (for TypeScript projects)
4. **eslint-plugin-react-hooks** - React Hooks best practices
5. **eslint-plugin-jsx-a11y** - Accessibility compliance
6. **npm audit** - Security vulnerability detection
7. **webpack-bundle-analyzer** - Bundle size optimization

## CI/CD Integration Example

```yaml
# GitHub Actions example
- name: Install dependencies
  run: npm ci

- name: Run ESLint
  run: npx eslint src/ --ext .js,.jsx,.ts,.tsx

- name: Check formatting with Prettier
  run: npx prettier --check src/

- name: Type check with TypeScript
  run: npx tsc --noEmit

- name: Run security audit
  run: npm audit --audit-level moderate

- name: Run tests with coverage
  run: npm test -- --coverage --passWithNoTests

- name: Analyze bundle size
  run: npx webpack-bundle-analyzer build/static/js/*.js --mode static --report dist/report.html
```

## MCP Integration Approach

For MCP integration, you can execute these tools via command line and parse their output:

```javascript
const { exec } = require('child_process');

function runEslint(files) {
  return new Promise((resolve, reject) => {
    exec(`npx eslint ${files} --format json`, (error, stdout, stderr) => {
      if (error && !stdout) {
        reject(error);
      } else {
        try {
          const results = JSON.parse(stdout);
          resolve(results);
        } catch (parseError) {
          reject(parseError);
        }
      }
    });
  });
}

// Example usage in MCP
async function analyzeCode(files) {
  try {
    const eslintResults = await runEslint(files);
    eslintResults.forEach(result => {
      console.log(`File: ${result.filePath}`);
      result.messages.forEach(message => {
        console.log(`  Line ${message.line}: ${message.message} (${message.ruleId})`);
      });
    });
  } catch (error) {
    console.error('Analysis failed:', error);
  }
}
```

## Configuration Tips

1. **Shareable Configurations**: Use eslint-config-airbnb, eslint-config-standard-react
2. **IDE Integration**: Configure real-time feedback in VS Code/WebStorm
3. **Pre-commit Hooks**: Integrate with husky and lint-staged
4. **Performance Optimization**: Use cache and parallel processing
5. **Custom Rules**: Create organization-specific ESLint plugins
6. **Migration Strategy**: Gradually introduce rules with warning levels
7. **Baseline Creation**: Generate ignore files for legacy code issues