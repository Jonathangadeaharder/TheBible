# C++ Development - Human-Centered Workflow

## Key Principle

Focus on effective systems programming team collaboration rather than complex agent coordination.

## Simple Workflow Approach

### 1. System Design
- Define architecture and component interfaces
- Plan memory management and resource usage
- Consider performance and safety requirements

### 2. Implementation Planning
- Break down system into manageable modules
- Assign modules based on team expertise
- Plan integration points and dependencies

### 3. Development
- Follow modern C++ best practices
- Use RAII for resource management
- Conduct regular code reviews and check-ins

### 4. Testing & Integration
- Write unit tests for critical components
- Perform integration testing
- Validate performance and memory usage

## Communication Guidelines

### Daily Standups
- What did you work on yesterday?
- What are you working on today?
- Any blockers or impediments?

### Code Handoffs
- Document APIs and usage examples
- Ensure clear understanding of performance requirements
- Provide build and testing instructions

### Problem Resolution
- Address issues early before they become blockers
- Involve domain experts for complex problems
- Document solutions for future reference

## Tools & Practices

### Recommended Tools
- **Project Management**: Jira, Trello, or Azure DevOps
- **Version Control**: Git with feature branches
- **CI/CD**: GitHub Actions, GitLab CI, or Jenkins
- **Build Systems**: CMake, Bazel, or Meson
- **Communication**: Slack, Teams, or email

### Best Practices
- Prefer stack allocation over heap allocation
- Use smart pointers for dynamic memory
- Follow the Rule of Five for resource management
- Apply const correctness consistently
- Write performance-aware code
- Regular retrospectives to improve processes

## When to Add Complexity

Only consider more complex coordination systems when:
- Team grows beyond 8-10 developers
- Multiple distributed teams are involved
- Safety-critical systems require detailed tracking
- Current simple approach is proven inadequate

For most teams, this human-centered approach will be more effective than complex agent orchestration.