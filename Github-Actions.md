# GitHub Actions - Complete Guide

## Table of Contents
- [GitHub Actions Fundamentals](#github-actions-fundamentals)
  - [What is CI/CD?](#what-is-cicd)
  - [What is GitHub Actions?](#what-is-github-actions)
  - [GitHub Actions Architecture](#github-actions-architecture)
  - [YAML Syntax Basics](#yaml-syntax-basics)
  - [Creating Your First Pipeline](#creating-your-first-pipeline)
  - [Debugging Pipeline Failures](#debugging-pipeline-failures)
- [Understanding Workflows](#understanding-workflows)
- [Workflow Triggers Explained](#workflow-triggers-explained)
- [Trigger Types](#trigger-types)
- [Common Usage Patterns](#common-usage-patterns)
- [Best Practices](#best-practices)

---

## GitHub Actions Fundamentals

### What is CI/CD?

#### Continuous Integration (CI)

**Continuous Integration** is a development practice where developers frequently merge their code changes into a central repository. Each merge triggers an automated build and testing process.

**Key Benefits of CI:**
- **Early Bug Detection**: Issues are identified immediately when code is pushed
- **Reduced Integration Problems**: Smaller, frequent changes are easier to integrate
- **Improved Code Quality**: Automated testing ensures code meets quality standards
- **Faster Development**: Developers get quick feedback on their changes
- **Team Collaboration**: Everyone works with the latest code version

**Example CI Workflow:**
1. Developer writes code and commits changes
2. Code is pushed to GitHub repository
3. Automated tests run immediately
4. Build process verifies code compiles successfully
5. Developer receives notification of success or failure

#### Continuous Deployment/Delivery (CD)

**Continuous Deployment** automatically deploys every change that passes all tests to production.

**Continuous Delivery** automatically deploys to staging, but requires manual approval for production.

**Key Benefits of CD:**
- **Faster Time to Market**: Features reach users quickly
- **Reduced Deployment Risk**: Smaller, frequent deployments are less risky
- **Automated Process**: Eliminates manual deployment errors
- **Reliable Releases**: Consistent, repeatable deployment process
- **Quick Rollback**: Easy to revert if issues occur

#### Why CI/CD Matters

**Without CI/CD:**
- Manual testing is time-consuming and error-prone
- Integration conflicts occur when merging large changes
- Deployment is slow and risky
- Bugs are discovered late in the process
- Teams work with outdated code

**With CI/CD:**
- Automated testing catches issues immediately
- Small, frequent integrations prevent conflicts
- Deployment is fast and reliable
- Bugs are found and fixed quickly
- Teams always work with latest code

---

### What is GitHub Actions?

**GitHub Actions** is a powerful automation platform built directly into GitHub that enables you to automate your software development workflows.

#### Key Features

1. **Built-in to GitHub**: No external services needed
2. **Event-Driven**: Responds to repository events automatically
3. **Flexible**: Supports any programming language or platform
4. **Marketplace**: Thousands of pre-built actions available
5. **Free Tier**: 2,000 minutes/month for private repositories (unlimited for public)
6. **Hosted Runners**: GitHub provides the infrastructure
7. **Self-Hosted Runners**: Use your own infrastructure if needed

#### What Can You Automate?

- **Testing**: Run unit tests, integration tests, end-to-end tests
- **Building**: Compile code, build Docker images
- **Deployment**: Deploy to cloud platforms (AWS, Azure, GCP)
- **Code Quality**: Run linters, security scans, code coverage
- **Documentation**: Generate and publish documentation
- **Notifications**: Send alerts to Slack, email, etc.
- **Issue Management**: Automatically label, assign, or close issues
- **Release Management**: Create releases, generate changelogs

---

### GitHub Actions Architecture

Understanding the architecture is crucial to effectively using GitHub Actions.

#### Component Hierarchy

```
Repository
  └── .github/workflows/
       ├── workflow1.yml
       └── workflow2.yml
            ├── Trigger/Event (when to run)
            └── Jobs (what to do)
                 ├── Job 1
                 │    ├── runs-on: ubuntu-latest (runner)
                 │    └── Steps
                 │         ├── Step 1 (checkout code)
                 │         ├── Step 2 (setup environment)
                 │         └── Step 3 (run tests)
                 │
                 └── Job 2
                      ├── runs-on: windows-latest
                      └── Steps
                           └── ...
```

#### 1. Workflows

**What is a Workflow?**
A workflow is an automated process defined in a YAML file that contains one or more jobs.

**Workflow Characteristics:**
- Stored in `.github/workflows/` directory
- Defined in YAML format
- Can have multiple workflows per repository
- Each workflow has a unique name
- Triggered by specific events

**Example Workflow Structure:**
```yaml
name: CI Pipeline                    # Workflow name
on: [push, pull_request]            # When to run
jobs:                               # What to do
  test:                             # Job name
    runs-on: ubuntu-latest          # Where to run
    steps:                          # How to do it
      - name: Checkout code
        uses: actions/checkout@v4
```

#### 2. Events (Triggers)

**What is an Event?**
An event is something that happens in or to your repository that triggers a workflow.

**Common Events:**
- `push`: Code is pushed to repository
- `pull_request`: Pull request is opened or updated
- `schedule`: Time-based (cron schedule)
- `workflow_dispatch`: Manual trigger
- `release`: Release is published
- `issues`: Issue is created or modified

**Example:**
```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
```

#### 3. Jobs

**What is a Job?**
A job is a set of steps that execute on the same runner. Multiple jobs in a workflow run in parallel by default.

**Job Characteristics:**
- Runs on a fresh virtual environment (runner)
- Can run in parallel or sequentially
- Can depend on other jobs
- Can have conditions for execution

**Example:**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Build application
        run: npm run build
  
  test:
    needs: build              # Runs after build job
    runs-on: ubuntu-latest
    steps:
      - name: Run tests
        run: npm test
```

#### 4. Steps

**What is a Step?**
A step is an individual task within a job. Steps run sequentially within a job.

**Types of Steps:**
- **Run Commands**: Execute shell commands
- **Use Actions**: Use pre-built actions from marketplace

**Example:**
```yaml
steps:
  # Using a pre-built action
  - name: Checkout code
    uses: actions/checkout@v4
  
  # Running shell commands
  - name: Install dependencies
    run: npm install
  
  - name: Run tests
    run: npm test
```

#### 5. Runners

**What is a Runner?**
A runner is a server that executes your workflows. It's a virtual machine that runs your jobs.

**GitHub-Hosted Runners:**
GitHub provides free hosted runners with pre-installed software.

**Available Operating Systems:**
- `ubuntu-latest` (Ubuntu Linux)
- `windows-latest` (Windows Server)
- `macos-latest` (macOS)

**Pre-installed Software:**
- Programming languages (Node.js, Python, Java, etc.)
- Package managers (npm, pip, Maven, etc.)
- Build tools (Docker, Git, etc.)
- Databases (PostgreSQL, MySQL, etc.)

**Example:**
```yaml
jobs:
  test-linux:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running on Linux"
  
  test-windows:
    runs-on: windows-latest
    steps:
      - run: echo "Running on Windows"
  
  test-mac:
    runs-on: macos-latest
    steps:
      - run: echo "Running on macOS"
```

**Self-Hosted Runners:**
You can also use your own infrastructure:
- More control over environment
- Access to specific hardware
- Reduced costs for heavy usage
- Custom software configurations

#### 6. Actions

**What is an Action?**
An action is a reusable unit of code that performs a specific task.

**Types of Actions:**
- **JavaScript Actions**: Run on any platform
- **Docker Actions**: Run in a Docker container
- **Composite Actions**: Combine multiple steps

**Using Actions from Marketplace:**
```yaml
steps:
  # Official GitHub action
  - uses: actions/checkout@v4
  
  # Setup Node.js
  - uses: actions/setup-node@v4
    with:
      node-version: '20'
  
  # Third-party action
  - uses: aws-actions/configure-aws-credentials@v4
    with:
      aws-region: us-east-1
```

**Creating Custom Actions:**
You can create your own actions and share them or keep them private.

#### Architecture Visualization

```
┌─────────────────────────────────────────────────────┐
│                   GitHub Repository                  │
│  ┌───────────────────────────────────────────────┐  │
│  │         .github/workflows/ci.yml              │  │
│  └───────────────────────────────────────────────┘  │
└───────────────────────┬─────────────────────────────┘
                        │
                        ▼ (Event: push)
┌─────────────────────────────────────────────────────┐
│              GitHub Actions Service                  │
│  • Receives event                                    │
│  • Reads workflow file                              │
│  • Schedules jobs                                   │
└───────────┬─────────────────────────┬───────────────┘
            │                         │
            ▼                         ▼
    ┌───────────────┐         ┌───────────────┐
    │   Runner 1    │         │   Runner 2    │
    │  (ubuntu)     │         │  (windows)    │
    │               │         │               │
    │  Job: build   │         │  Job: test    │
    │  ├─ Step 1    │         │  ├─ Step 1    │
    │  ├─ Step 2    │         │  ├─ Step 2    │
    │  └─ Step 3    │         │  └─ Step 3    │
    └───────────────┘         └───────────────┘
            │                         │
            └──────────┬──────────────┘
                       ▼
            ┌──────────────────┐
            │  Results/Logs    │
            │  Status: Success │
            └──────────────────┘
```

---

### YAML Syntax Basics

GitHub Actions workflows are written in **YAML** (YAML Ain't Markup Language), a human-readable data serialization format.

#### Why YAML?

- **Human-readable**: Easy to read and write
- **Structured**: Clear hierarchy using indentation
- **Widely used**: Standard for configuration files
- **Simple**: Minimal syntax rules

#### YAML Fundamentals

**1. Key-Value Pairs**
```yaml
name: My Workflow
version: 1.0
```

**2. Indentation (Spaces, NOT Tabs)**
```yaml
jobs:
  build:              # 2 spaces
    runs-on: ubuntu-latest    # 4 spaces
    steps:            # 4 spaces
      - name: Test    # 6 spaces (dash + space counts)
```

**3. Lists/Arrays**
```yaml
# Using dashes
branches:
  - main
  - develop
  - staging

# Inline format
branches: [main, develop, staging]
```

**4. Strings**
```yaml
# No quotes needed for simple strings
name: My Workflow

# Quotes for special characters
message: "Hello, World!"

# Multi-line strings
description: |
  This is a long description
  that spans multiple lines
  and preserves line breaks.

# Multi-line folded (joins lines)
description: >
  This is a long description
  that will be joined into
  a single line.
```

**5. Booleans**
```yaml
enabled: true
disabled: false
```

**6. Numbers**
```yaml
timeout: 30
retries: 3
```

**7. Nested Structures**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Build
        run: npm run build
```

**8. Comments**
```yaml
# This is a comment
name: My Workflow  # Inline comment

# Multi-line comment
# can be written
# like this
```

#### Common YAML Mistakes

**1. Mixing Tabs and Spaces**
```yaml
# Wrong (uses tabs)
jobs:
→→build:              # Tab
    runs-on: ubuntu-latest

# Correct (uses spaces)
jobs:
  build:              # 2 spaces
    runs-on: ubuntu-latest
```

**2. Incorrect Indentation**
```yaml
# Wrong
jobs:
build:                # Missing indentation
  runs-on: ubuntu-latest

# Correct
jobs:
  build:
    runs-on: ubuntu-latest
```

**3. Missing Colon**
```yaml
# Wrong
name My Workflow

# Correct
name: My Workflow
```

**4. Unquoted Special Characters**
```yaml
# Wrong
message: Hello: World    # Colon confuses parser

# Correct
message: "Hello: World"
```

#### GitHub Actions Specific YAML

**Complete Example:**
```yaml
name: CI Pipeline                        # Workflow name

on:                                      # Trigger events
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:                                     # Environment variables
  NODE_VERSION: '20'
  CI: true

jobs:                                    # Jobs section
  test:                                  # Job ID
    name: Run Tests                      # Job display name
    runs-on: ubuntu-latest              # Runner
    
    steps:                              # Steps section
      - name: Checkout code             # Step name
        uses: actions/checkout@v4       # Using an action
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:                           # Action inputs
          node-version: ${{ env.NODE_VERSION }}
      
      - name: Install dependencies
        run: npm install                # Running commands
      
      - name: Run tests
        run: npm test
        env:                            # Step-level env vars
          DATABASE_URL: test-db
      
      - name: Multi-line commands
        run: |                          # Multi-line command
          echo "Building application"
          npm run build
          echo "Build complete"

  deploy:
    name: Deploy Application
    needs: test                         # Job dependency
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' # Conditional execution
    
    steps:
      - name: Deploy
        run: echo "Deploying..."
```

---

### Creating Your First Pipeline

Let's create a simple but complete CI/CD pipeline step by step.

#### Step 1: Create Repository Structure

Your repository needs a specific folder structure:

```
my-project/
├── .github/
│   └── workflows/
│       └── ci.yml           # Your workflow file
├── src/
│   └── app.js
├── tests/
│   └── app.test.js
└── package.json
```

#### Step 2: Create Your First Workflow

Create `.github/workflows/hello-world.yml`:

```yaml
name: Hello World Workflow

# This workflow runs when code is pushed
on:
  push:
    branches:
      - main

# Define what the workflow does
jobs:
  say-hello:
    name: Say Hello
    runs-on: ubuntu-latest
    
    steps:
      # Step 1: Print a message
      - name: Print greeting
        run: echo "Hello, GitHub Actions!"
      
      # Step 2: Print date and time
      - name: Print current date
        run: date
      
      # Step 3: Show system information
      - name: Show system info
        run: |
          echo "Operating System: $(uname -s)"
          echo "Hostname: $(hostname)"
          echo "User: $(whoami)"
```

#### Step 3: Create a Real CI Pipeline

Create `.github/workflows/ci.yml`:

```yaml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  # Job 1: Checkout and Test
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    
    steps:
      # Checkout code from repository
      - name: Checkout code
        uses: actions/checkout@v4
      
      # Setup Node.js environment
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      # Cache dependencies for faster builds
      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      
      # Install project dependencies
      - name: Install dependencies
        run: npm ci
      
      # Run linter
      - name: Run linter
        run: npm run lint
      
      # Run tests
      - name: Run tests
        run: npm test
      
      # Generate code coverage
      - name: Generate coverage report
        run: npm run test:coverage
      
      # Upload coverage report
      - name: Upload coverage
        uses: actions/upload-artifact@v3
        with:
          name: coverage-report
          path: coverage/
  
  # Job 2: Build Application
  build:
    name: Build Application
    needs: test                    # Runs only if test job succeeds
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build application
        run: npm run build
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist/
  
  # Job 3: Deploy (only on main branch)
  deploy:
    name: Deploy to Staging
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    
    steps:
      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-files
          path: dist/
      
      - name: Deploy to staging
        run: |
          echo "Deploying to staging environment..."
          # Add your deployment commands here
          echo "Deployment complete!"
```

#### Step 4: Commit and Push

```bash
# Add the workflow file
git add .github/workflows/ci.yml

# Commit the change
git commit -m "Add CI pipeline"

# Push to GitHub
git push origin main
```

#### Step 5: View Your Workflow Running

1. Go to your repository on GitHub
2. Click the **Actions** tab
3. You'll see your workflow running
4. Click on the workflow run to see details
5. Click on job names to see step-by-step logs

#### Understanding the Workflow Run

**Workflow Run Page Shows:**
- Overall status (In progress, Success, Failed)
- Which jobs ran
- How long each job took
- Who triggered the workflow
- Which commit triggered it

**Job Details Show:**
- Each step that ran
- Console output for each step
- Timing information
- Success/failure status

---

### Debugging Pipeline Failures

Debugging is a critical skill when working with CI/CD pipelines. Here's how to effectively troubleshoot issues.

#### Common Failure Types

**1. Syntax Errors**
**2. Dependency Issues**
**3. Test Failures**
**4. Environment Problems**
**5. Permission Issues**
**6. Timeout Issues**

#### Debugging Strategy

**Step 1: Identify Where It Failed**

Check the Actions tab and look for:
- ❌ Red X marks indicate failure
- ⏱️ Yellow indicates cancelled or skipped
- ✅ Green indicates success

**Step 2: Read the Error Message**

Click on the failed job and step to see error output:

```
Error: Cannot find module 'express'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:)
    at Function.Module._load (internal/modules/cjs/loader.js:)
```

#### Common Issues and Solutions

**1. Syntax Errors in YAML**

**Symptom:**
```
Workflow validation error: Invalid workflow file
```

**Solution:**
- Check indentation (use spaces, not tabs)
- Validate YAML syntax using online validators
- Ensure all required fields are present

**Example Fix:**
```yaml
# Wrong (missing colon)
name My Workflow

# Correct
name: My Workflow
```

**2. Missing Dependencies**

**Symptom:**
```
Error: Cannot find module 'package-name'
```

**Solution:**
```yaml
steps:
  - name: Install dependencies
    run: npm ci              # Use 'ci' instead of 'install'
```

**3. Test Failures**

**Symptom:**
```
FAIL  tests/app.test.js
  ✕ should return 200 (45 ms)
```

**Solution:**
- Run tests locally first
- Check for environment-specific issues
- Review test output carefully

```yaml
- name: Run tests with verbose output
  run: npm test -- --verbose
```

**4. Permission Errors**

**Symptom:**
```
Permission denied: ./deploy.sh
```

**Solution:**
```yaml
- name: Make script executable
  run: chmod +x ./deploy.sh

- name: Run deployment script
  run: ./deploy.sh
```

**5. Environment Variables Not Set**

**Symptom:**
```
Error: DATABASE_URL is not defined
```

**Solution:**
```yaml
steps:
  - name: Run with environment variables
    run: npm test
    env:
      DATABASE_URL: ${{ secrets.DATABASE_URL }}
      NODE_ENV: test
```

**6. Timeout Issues**

**Symptom:**
```
The job running on runner has exceeded the maximum execution time of 360 minutes.
```

**Solution:**
```yaml
jobs:
  long-running-job:
    runs-on: ubuntu-latest
    timeout-minutes: 60        # Set appropriate timeout
    steps:
      - name: Long task
        run: npm run long-task
        timeout-minutes: 30    # Step-level timeout
```

#### Debugging Techniques

**1. Enable Debug Logging**

Add these secrets to your repository:
- `ACTIONS_STEP_DEBUG`: Set to `true`
- `ACTIONS_RUNNER_DEBUG`: Set to `true`

This provides verbose logging for troubleshooting.

**2. Add Debug Steps**

```yaml
steps:
  - name: Debug - Print environment
    run: |
      echo "Node version: $(node --version)"
      echo "NPM version: $(npm --version)"
      echo "Working directory: $(pwd)"
      echo "Directory contents:"
      ls -la
  
  - name: Debug - Print environment variables
    run: env | sort
  
  - name: Your actual step
    run: npm test
```

**3. Use Conditional Debugging**

```yaml
steps:
  - name: Run tests
    run: npm test
  
  - name: Debug on failure
    if: failure()
    run: |
      echo "Test failed! Debugging info:"
      cat logs/test.log
      echo "Environment:"
      env
```

**4. Test Locally with Act**

Use [act](https://github.com/nektos/act) to run GitHub Actions locally:

```bash
# Install act
# On macOS: brew install act
# On Windows: choco install act-cli

# Run workflows locally
act push                    # Simulate push event
act pull_request            # Simulate PR event
act -l                      # List available jobs
```

**5. Isolate the Problem**

Create a minimal workflow to test specific functionality:

```yaml
name: Debug Workflow

on: workflow_dispatch

jobs:
  debug:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Test specific command
        run: |
          # Only test the problematic command
          npm run build
```

**6. Check Workflow Artifacts**

Upload files for inspection:

```yaml
- name: Save logs
  if: always()
  uses: actions/upload-artifact@v3
  with:
    name: debug-logs
    path: |
      logs/
      *.log
```

#### Best Practices for Debugging

1. **Start Simple**: Create minimal reproduction case
2. **Check Locally First**: Run commands locally before adding to workflow
3. **Read Error Messages**: Error messages usually point to the issue
4. **Use Incremental Changes**: Add one change at a time
5. **Check Documentation**: Review action documentation for correct usage
6. **Compare with Working Examples**: Look at similar workflows that work
7. **Use Debugging Steps**: Add echo statements to track progress

#### Common Error Patterns

**Pattern 1: "Command not found"**
```
Solution: Install the required tool or use correct command name
```

**Pattern 2: "File not found"**
```
Solution: Ensure checkout step runs first, check file paths
```

**Pattern 3: "Exit code 1"**
```
Solution: The command failed, check the command output
```

**Pattern 4: "Secret not found"**
```
Solution: Add the secret in repository settings
```

#### Getting Help

When you're stuck:
1. **GitHub Community Forum**: [community.github.com](https://github.community)
2. **GitHub Actions Documentation**: [docs.github.com/actions](https://docs.github.com/actions)
3. **Stack Overflow**: Search or ask questions with `github-actions` tag
4. **GitHub Status**: Check if GitHub Actions is having issues: [status.github.com](https://status.github.com)

---

## Understanding Workflows

Now that you understand the fundamentals, let's dive deeper into workflow structure and configuration.

### Workflow File Location

All workflow files must be stored in the `.github/workflows/` directory:

```
my-repository/
└── .github/
    └── workflows/
        ├── ci.yml
        ├── deploy.yml
        └── test.yml
```

### Workflow Components

```yaml
name: Workflow Name                # Human-readable name

on: [trigger_event]                # When to run this workflow

jobs:                              # What to do
  job-name:
    runs-on: ubuntu-latest         # Where to run
    steps:
      - name: Step name
        run: echo "Hello World"    # Commands to execute
```

---

## Workflow Triggers Explained

Triggers define **when** your workflow will execute. GitHub Actions supports multiple trigger types to accommodate different automation needs.

### Trigger Syntax

The `on:` keyword in your workflow file specifies the trigger conditions:

```yaml
on: event_name
```

or for multiple events:

```yaml
on: [event1, event2]
```

---

## Trigger Types

### 1. Manual Triggers

**Purpose**: Allow users to manually start a workflow from the GitHub interface.

**Use Cases**:
- Deploying to production after manual approval
- Running maintenance tasks on demand
- Testing workflows without code changes

**Basic Syntax**:
```yaml
on:
  workflow_dispatch:
```

**With Input Parameters**:
```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment for deployment'
        required: true
        default: 'staging'
        type: choice
        options:
          - development
          - staging
          - production
      
      debug_mode:
        description: 'Enable debug logging'
        required: false
        default: false
        type: boolean
      
      version:
        description: 'Version number to deploy'
        required: true
        type: string
```

**How to Use**: Navigate to Actions tab → Select workflow → Click "Run workflow" button

---

### 2. Push Events

**Purpose**: Trigger workflows when code is pushed to the repository.

**Use Cases**:
- Running tests when code changes
- Building application on commits
- Deploying to environments automatically

**Basic Syntax**:
```yaml
on:
  push:
```

**With Branch Filters**:
```yaml
on:
  push:
    branches:
      - main           # Only main branch
      - develop        # Only develop branch
      - 'release/**'   # Any branch starting with release/
```

**With Path Filters**:
```yaml
on:
  push:
    paths:
      - 'src/**'              # Only when files in src/ change
      - '**.js'               # Only when JavaScript files change
      - 'package.json'        # Only when package.json changes
```

**With Path Exclusions**:
```yaml
on:
  push:
    paths-ignore:
      - 'docs/**'             # Ignore documentation changes
      - '**.md'               # Ignore markdown files
      - '.gitignore'          # Ignore .gitignore changes
```

**With Tag Triggers**:
```yaml
on:
  push:
    tags:
      - 'v*'                  # Triggers on version tags (v1.0, v2.0)
      - 'release-*'           # Triggers on release tags
```

---

### 3. Pull Request Events

**Purpose**: Trigger workflows when pull requests are opened, updated, or modified.

**Use Cases**:
- Running automated tests on pull requests
- Checking code quality and linting
- Validating that changes don't break the build

**Basic Syntax**:
```yaml
on:
  pull_request:
```

**With Branch Filters**:
```yaml
on:
  pull_request:
    branches:
      - main              # Only PRs targeting main branch
      - develop           # Only PRs targeting develop branch
```

**With Activity Types**:
```yaml
on:
  pull_request:
    types:
      - opened            # When PR is first created
      - synchronize       # When new commits are pushed to PR
      - reopened          # When a closed PR is reopened
      - ready_for_review  # When PR is marked ready for review
      - closed            # When PR is closed or merged
```

**With Path Filters**:
```yaml
on:
  pull_request:
    paths:
      - 'src/**'          # Only when source code changes
      - 'tests/**'        # Only when tests change
```

---

### 4. Scheduled Events (Cron Jobs)

**Purpose**: Run workflows at specific times or intervals automatically.

**Use Cases**:
- Daily database backups
- Weekly security scans
- Monthly report generation
- Regular health checks

**Syntax**:
```yaml
on:
  schedule:
    - cron: '0 0 * * *'    # Cron expression
```

**Cron Expression Format**:
```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6) (Sunday to Saturday)
│ │ │ │ │
* * * * *
```

**Common Patterns**:

| Pattern | Description | Example |
|---------|-------------|---------|
| `0 0 * * *` | Daily at midnight UTC | Backup databases |
| `0 */4 * * *` | Every 4 hours | Check system status |
| `0 9 * * 1-5` | Weekdays at 9 AM | Send daily reports |
| `0 0 * * 0` | Every Sunday at midnight | Weekly cleanup |
| `0 0 1 * *` | First day of each month | Monthly billing |
| `*/15 * * * *` | Every 15 minutes | Health monitoring |
| `0 8,12,16 * * *` | At 8 AM, 12 PM, 4 PM | Multiple times daily |

**Important Notes**:
- All times are in UTC
- Scheduled workflows may be delayed during high GitHub usage
- Minimum interval is every 5 minutes
- Scheduled workflows don't run on forked repositories by default

**Example**:
```yaml
on:
  schedule:
    - cron: '0 2 * * *'    # Daily at 2 AM UTC
    - cron: '0 14 * * 5'   # Every Friday at 2 PM UTC
```

---

### 5. Repository Events

**Purpose**: Trigger workflows based on specific repository activities.

#### Issue Events

**Use Cases**:
- Automatically label new issues
- Assign issues to team members
- Notify teams on Slack/Discord

```yaml
on:
  issues:
    types:
      - opened            # When a new issue is created
      - edited            # When issue title/body is edited
      - closed            # When issue is closed
      - reopened          # When issue is reopened
      - labeled           # When label is added
      - unlabeled         # When label is removed
      - assigned          # When someone is assigned
```

#### Release Events

**Use Cases**:
- Deploy to production when release is published
- Generate release notes
- Notify users of new versions

```yaml
on:
  release:
    types:
      - published         # When release is published
      - created           # When release is created (draft or published)
      - released          # When release is made public
      - prereleased       # When release is marked as pre-release
```

#### Fork Events

**Use Cases**:
- Track repository popularity
- Thank users for forking

```yaml
on:
  fork:                   # Triggered when repository is forked
```

#### Watch Events (Stars)

**Use Cases**:
- Track when someone stars your repo
- Send thank you messages

```yaml
on:
  watch:
    types:
      - started           # When someone stars the repository
```

#### Other Repository Events

```yaml
on:
  create:                 # Branch or tag is created
  delete:                 # Branch or tag is deleted
  public:                 # Repository changes from private to public
  gollum:                 # Wiki page is created or updated
```

---

### 6. Webhook Events

**Purpose**: Trigger workflows from external systems via HTTP requests.

**Use Cases**:
- Integration with external CI/CD systems
- Triggering workflows from other applications
- Custom automation workflows

```yaml
on:
  repository_dispatch:
    types:
      - custom-event      # Custom event type you define
      - deploy-request
      - data-sync
```

**How to Trigger**:

Using cURL:
```bash
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/dispatches \
  -d '{"event_type":"custom-event","client_payload":{"key":"value"}}'
```

**Accessing Payload Data**:
```yaml
jobs:
  handle-event:
    runs-on: ubuntu-latest
    steps:
      - name: Echo custom data
        run: echo "${{ github.event.client_payload.key }}"
```

---

### 7. Workflow Call (Reusable Workflows)

**Purpose**: Create reusable workflows that can be called by other workflows.

**Use Cases**:
- Centralizing common CI/CD logic
- Sharing workflows across multiple repositories
- Creating workflow templates

**Reusable Workflow** (`.github/workflows/reusable.yml`):
```yaml
on:
  workflow_call:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        type: string
      
      config-path:
        description: 'Path to configuration file'
        required: false
        type: string
        default: './config.yml'
    
    secrets:
      token:
        description: 'API token'
        required: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to ${{ inputs.environment }}
        run: echo "Deploying using config at ${{ inputs.config-path }}"
```

**Calling the Reusable Workflow**:
```yaml
on:
  push:
    branches:
      - main

jobs:
  call-workflow:
    uses: ./.github/workflows/reusable.yml
    with:
      environment: 'production'
      config-path: './prod-config.yml'
    secrets:
      token: ${{ secrets.API_TOKEN }}
```

---

### 8. Multiple Triggers Combined

**Purpose**: Run the same workflow on multiple different events.

**Use Cases**:
- CI/CD pipeline that runs on push and PR
- Workflows that can be triggered manually or automatically
- Comprehensive testing strategies

**Example 1: CI/CD Pipeline**
```yaml
on:
  push:
    branches:
      - main
      - develop
  
  pull_request:
    branches:
      - main
  
  workflow_dispatch:
```

**Example 2: Deployment with Options**
```yaml
on:
  push:
    branches:
      - main
    tags:
      - 'v*'
  
  workflow_dispatch:
    inputs:
      environment:
        required: true
        type: choice
        options:
          - staging
          - production
  
  schedule:
    - cron: '0 0 * * 0'    # Weekly health check
```

**Example 3: Comprehensive Testing**
```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'src/**'
      - 'tests/**'
  
  pull_request:
    types:
      - opened
      - synchronize
      - ready_for_review
  
  schedule:
    - cron: '0 0 * * *'    # Nightly regression tests
  
  workflow_dispatch:        # Manual test execution
```

---

### 9. Path and File Filters

**Purpose**: Trigger workflows only when specific files or directories change.

**Use Cases**:
- Run frontend tests only when frontend code changes
- Build Docker images only when Dockerfile changes
- Deploy documentation only when docs are updated

**Include Specific Paths**:
```yaml
on:
  push:
    paths:
      - 'src/**'                    # All files in src directory
      - 'tests/**'                  # All files in tests directory
      - '**.js'                     # All JavaScript files anywhere
      - '**.ts'                     # All TypeScript files anywhere
      - 'package.json'              # Specific file
      - 'package-lock.json'
```

**Exclude Specific Paths**:
```yaml
on:
  push:
    paths-ignore:
      - 'docs/**'                   # Ignore documentation
      - '**.md'                     # Ignore all markdown files
      - '.github/**'                # Ignore GitHub config changes
      - '.gitignore'
```

**Combined Include/Exclude**:
```yaml
on:
  push:
    paths:
      - 'src/**'                    # Include source code
    paths-ignore:
      - 'src/test/**'               # But exclude test files
      - '**.md'                     # And markdown files
```

**Glob Pattern Examples**:

| Pattern | Matches | Example |
|---------|---------|---------|
| `*` | Any characters except `/` | `*.js` matches `file.js` |
| `**` | Any characters including `/` | `src/**` matches all in src |
| `?` | Single character | `file?.js` matches `file1.js` |
| `[abc]` | Characters in brackets | `file[123].js` matches `file1.js` |
| `[!abc]` | Not these characters | `file[!0-9].js` matches `fileA.js` |

---

## Common Usage Patterns

### Pattern 1: CI/CD Pipeline

**Scenario**: Automatically test code on every push and pull request.

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main
      - develop
  
  pull_request:
    branches:
      - main
    types:
      - opened
      - synchronize

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: npm test
  
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build application
        run: npm run build
```

---

### Pattern 2: Deployment Workflow

**Scenario**: Deploy to different environments based on branch or manual trigger.

```yaml
name: Deploy Application

on:
  push:
    branches:
      - main          # Auto-deploy production
      - staging       # Auto-deploy staging
  
  workflow_dispatch:  # Manual deployment
    inputs:
      environment:
        description: 'Environment to deploy to'
        required: true
        type: choice
        options:
          - development
          - staging
          - production

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Determine environment
        id: env
        run: |
          if [ "${{ github.event_name }}" = "workflow_dispatch" ]; then
            echo "environment=${{ inputs.environment }}" >> $GITHUB_OUTPUT
          elif [ "${{ github.ref }}" = "refs/heads/main" ]; then
            echo "environment=production" >> $GITHUB_OUTPUT
          else
            echo "environment=staging" >> $GITHUB_OUTPUT
          fi
      
      - name: Deploy to ${{ steps.env.outputs.environment }}
        run: echo "Deploying to ${{ steps.env.outputs.environment }}"
```

---

### Pattern 3: Scheduled Maintenance

**Scenario**: Regular maintenance tasks that run automatically.

```yaml
name: Maintenance Tasks

on:
  schedule:
    - cron: '0 2 * * *'       # Daily at 2 AM UTC
    - cron: '0 0 * * 0'       # Weekly on Sunday at midnight
  
  workflow_dispatch:          # Allow manual execution

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Clean up old artifacts
        run: echo "Cleaning up..."
  
  backup:
    if: github.event.schedule == '0 0 * * 0'  # Only on Sunday
    runs-on: ubuntu-latest
    steps:
      - name: Weekly backup
        run: echo "Creating backup..."
  
  health-check:
    runs-on: ubuntu-latest
    steps:
      - name: System health check
        run: echo "Checking system health..."
```

---

### Pattern 4: Monorepo Workflows

**Scenario**: Different workflows for different parts of a monorepo.

```yaml
name: Frontend CI

on:
  push:
    paths:
      - 'frontend/**'
      - 'package.json'
  
  pull_request:
    paths:
      - 'frontend/**'

jobs:
  test-frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Test frontend
        run: npm run test:frontend
```

```yaml
name: Backend CI

on:
  push:
    paths:
      - 'backend/**'
      - 'go.mod'
  
  pull_request:
    paths:
      - 'backend/**'

jobs:
  test-backend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Test backend
        run: go test ./...
```

---

### Pattern 5: Release Automation

**Scenario**: Automatically build and deploy when a release is created.

```yaml
name: Release Workflow

on:
  release:
    types:
      - published
  
  push:
    tags:
      - 'v*'

jobs:
  build-release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Get version
        id: version
        run: echo "version=${GITHUB_REF#refs/tags/}" >> $GITHUB_OUTPUT
      
      - name: Build release artifacts
        run: |
          echo "Building version ${{ steps.version.outputs.version }}"
          npm run build
      
      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: release-${{ steps.version.outputs.version }}
          path: dist/
  
  deploy-production:
    needs: build-release
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: echo "Deploying release to production"
```

---

## Best Practices

### 1. Naming Conventions

**Use descriptive workflow names:**
```yaml
# Good
name: CI - Test and Build

# Bad
name: Workflow1
```

### 2. Minimize Unnecessary Runs

**Use path filters to avoid unnecessary workflow runs:**
```yaml
on:
  push:
    paths-ignore:
      - 'docs/**'
      - '**.md'
      - '.github/workflows/**'  # Don't trigger on workflow changes
```

### 3. Security Best Practices

**Don't trigger workflows on fork PRs for sensitive operations:**
```yaml
on:
  pull_request:
    branches:
      - main

jobs:
  deploy:
    # Only run on PRs from the main repo, not forks
    if: github.event.pull_request.head.repo.full_name == github.repository
    runs-on: ubuntu-latest
    steps:
      - name: Deploy
        run: echo "Deploying..."
```

### 4. Combine Manual and Automatic Triggers

**Allow flexibility by supporting both:**
```yaml
on:
  push:
    branches:
      - main
  workflow_dispatch:  # Allows manual execution when needed
```

### 5. Use Conditional Execution

**Run specific jobs only when needed:**
```yaml
jobs:
  deploy:
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy
        run: echo "Deploying to production"
```

### 6. Schedule Considerations

**Be mindful of execution timing:**
- Use off-peak hours for heavy tasks
- Stagger multiple scheduled workflows
- Remember all times are UTC

```yaml
on:
  schedule:
    - cron: '0 2 * * *'  # 2 AM UTC = Off-peak for many regions
```

### 7. Documentation

**Comment complex trigger logic:**
```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'src/**'      # Only run when source code changes
      - '!src/test/**'  # But ignore test files
  
  # Allow manual deployment for hotfixes
  workflow_dispatch:
    inputs:
      reason:
        description: 'Reason for manual deployment'
        required: true
```

---

## Additional Resources

### Official Documentation
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Workflow Syntax](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)
- [Events that trigger workflows](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

### Tools and Utilities
- [Crontab Guru](https://crontab.guru/) - Cron expression helper
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) - Pre-built actions
- [Act](https://github.com/nektos/act) - Run GitHub Actions locally

---

## Summary

GitHub Actions provides flexible workflow automation through various trigger types:

| Trigger Type | Best For | Frequency |
|--------------|----------|-----------|
| `push` | CI/CD, automatic deployment | On every push |
| `pull_request` | Code review checks, testing | On PR activity |
| `schedule` | Maintenance, backups, reports | Time-based |
| `workflow_dispatch` | Manual operations, hotfixes | On-demand |
| `release` | Production deployments | On release |
| `issues` | Issue management, automation | On issue activity |
| `repository_dispatch` | External integrations | API triggered |
| `workflow_call` | Reusable workflows | Called by other workflows |

Choose the appropriate trigger(s) based on your automation needs, and combine them when necessary for comprehensive coverage.

---

## 🧑‍💻 Author
**Md. Sarowar Alam**  
Lead DevOps Engineer, Hogarth Worldwide  
📧 Email: sarowar@hotmail.com  
🔗 LinkedIn: [linkedin.com/in/sarowar](https://www.linkedin.com/in/sarowar/)
