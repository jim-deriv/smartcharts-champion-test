# GitHub Workflows Documentation

This directory contains GitHub Actions workflows and custom actions for the SmartCharts Champion repository. These workflows automate deployment, preview management, and repository operations.

## 📋 Table of Contents

- [Overview](#overview)
- [Workflows](#workflows)
  - [PR Preview Pages](#pr-preview-pages)
  - [Cleanup PR Preview](#cleanup-pr-preview)
- [Custom Actions](#custom-actions)
  - [SmartCharts Checkout](#smartcharts-checkout)
- [Configuration](#configuration)
- [Dependencies](#dependencies)
- [Troubleshooting](#troubleshooting)
- [Security](#security)

## 🔍 Overview

The GitHub Actions setup provides:

- **Automated PR Preview Deployments**: Creates preview sites for pull requests
- **Automatic Cleanup**: Removes preview deployments when PRs are closed
- **Smart Repository Management**: Intelligent checkout with fallback support
- **Security Controls**: Fork safety checks and permission management
- **Status Communication**: Automated comments and status updates

## 🚀 Workflows

### PR Preview Pages

**File**: `.github/workflows/pr-preview-pages.yml`

**Purpose**: Builds and deploys preview sites for pull requests to GitHub Pages with enhanced reliability and performance.

#### Triggers
- `pull_request` events (opened, synchronize)
- Only processes PRs from the same repository (security measure)

#### Enhanced Process Flow
1. **Security & Setup**: Enhanced validation with input sanitization and deployment parameter configuration
2. **Parallel Build Jobs**: Simultaneous Flutter and Node.js builds for improved performance
   - **Flutter Build**: Web application with HTML renderer, extensive caching, and retry logic
   - **Node.js Build**: SmartCharts Champion with webpack, dependency caching, and build verification
3. **Integration & Deployment**: Combines builds, verifies artifacts, deploys with retry mechanisms
4. **Verification**: Health checks and deployment validation with configurable attempts
5. **Notification**: Comprehensive status reporting with build statistics and troubleshooting guidance
6. **Cleanup**: Automatic artifact cleanup and workflow summary generation

#### Enhanced Key Features
- **Parallel Job Architecture**: Flutter and Node.js builds run simultaneously for 40-50% faster execution
- **Multi-Layer Caching**: npm, Flutter dependencies, and build artifact caching
- **Retry Mechanisms**: Configurable retry logic for builds, deployments, and verifications
- **Comprehensive Validation**: Input validation, artifact verification, and deployment health checks
- **Enhanced Security**: Input sanitization, secure SSH key handling with automatic cleanup
- **Build Statistics**: Detailed size reporting, file counts, and performance metrics
- **Robust Error Handling**: Detailed troubleshooting guidance and automatic failure recovery
- **Deployment Verification**: Health checks with configurable timeout and retry attempts

#### Environment Variables
```yaml
FLUTTER_VERSION: '3.24.1'
NODE_VERSION: '18.17.0'
FLUTTER_CHART_REF: 'master'
FLUTTER_WEB_RENDERER: 'html'
NODE_ENV: 'production'
DEPLOYMENT_VERIFICATION_ATTEMPTS: 10
DEPLOYMENT_WAIT_TIME: 60
```

#### Job Architecture
- **security_and_setup**: Input validation and deployment configuration
- **build_flutter**: Parallel Flutter web application build with caching
- **build_nodejs**: Parallel SmartCharts Champion build with optimization
- **deploy**: Integration, deployment, and verification with retry logic
- **cleanup**: Artifact cleanup and workflow summary generation

#### Deployment URLs
- **Preview**: `https://{owner}.github.io/smartcharts-champion/pr-{number}`
- **Main**: `https://{owner}.github.io/smartcharts-champion`

### Cleanup PR Preview

**File**: `.github/workflows/cleanup-pr-preview.yml`

**Purpose**: Automatically removes preview deployments when pull requests are closed with enhanced reliability and comprehensive error handling.

#### Triggers
- `pull_request` events (closed)
- Runs for both merged and closed PRs

#### Enhanced Process Flow
1. **Input Validation**: Comprehensive validation of PR number and essential data
2. **Branch Existence Check**: Remote verification of gh-pages branch without cloning
3. **Optimized Checkout**: Shallow clone with sparse checkout for performance
4. **Git Configuration**: Automated git identity setup for consistent commits
5. **Preview Directory Cleanup**: Safe removal with existence checks and descriptive commits
6. **Empty Directory Cleanup**: Automatic removal of empty parent directories
7. **Push with Retry Logic**: Configurable retry mechanism for network failures
8. **Comprehensive Notification**: Detailed status reporting with cleanup confirmation

#### Enhanced Key Features
- **Comprehensive Input Validation**: PR number format validation and essential data checks
- **Performance Optimizations**: Shallow clone, sparse checkout, and efficient git operations
- **Retry Logic**: Configurable retry mechanisms for network operations with exponential backoff
- **Concurrency Control**: Prevents race conditions with proper locking mechanisms
- **Enhanced Error Handling**: Detailed error reporting with troubleshooting guidance
- **Atomic Operations**: Safe directory removal with rollback capabilities
- **Status Transparency**: Clear communication of cleanup status and reasons for no-action scenarios
- **Manual Cleanup Guidance**: Provides instructions for manual intervention when needed

#### Directory Structure
```
gh-pages/
├── pr-preview/
│   ├── 123/          # PR #123 preview
│   ├── 456/          # PR #456 preview
│   └── ...
└── index.html        # Main site
```

#### Retry Configuration
```yaml
CLEANUP_MAX_RETRIES: 3      # Maximum push retry attempts
CLEANUP_RETRY_DELAY: 2      # Delay between retries (seconds)
```

## 🛠 Custom Actions

### SmartCharts Checkout

**File**: `.github/actions/checkout/action.yml`

**Purpose**: Provides intelligent repository checkout with comprehensive fallback support, extensive validation, and detailed documentation.

#### Enhanced Features
- **Comprehensive Input Validation**: Repository format validation, fetch-depth validation, and parameter consistency checks
- **Advanced Reference Checking**: Timeout-protected ref existence verification with authentication
- **Intelligent Fallback Logic**: Sophisticated primary/fallback decision making with detailed logging
- **Extensive Documentation**: Comprehensive inline documentation with purpose explanations for each step
- **Enhanced Error Handling**: Detailed error messages with troubleshooting guidance
- **Security Improvements**: Input sanitization and validation to prevent malicious usage
- **Performance Optimizations**: Efficient git operations with timeout controls

#### Comprehensive Inputs
| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `repository` | No | `${{ github.repository }}` | Primary repository (validated format: owner/repo) |
| `ref` | No | - | Primary branch/tag/SHA |
| `alternate_repository` | No | - | Fallback repository (validated format: owner/repo) |
| `alternate_ref` | No | - | Fallback branch/tag/SHA |
| `path` | No | - | Checkout path |
| `fetch-depth` | No | `1` | Number of commits to fetch (validated positive integer) |
| `token` | No | `${{ github.token }}` | Authentication token |

#### Enhanced Outputs
| Output | Description |
|--------|-------------|
| `ref_exists` | Whether primary ref exists (true/false/default) |
| `repository_used` | Actual repository checked out |
| `ref_used` | Actual ref checked out |

#### Enhanced Usage Example
```yaml
- name: Checkout with comprehensive fallback
  uses: ./.github/actions/checkout
  with:
    repository: 'primary-org/repo'
    ref: 'feature-branch'
    alternate_repository: 'fallback-org/repo'
    alternate_ref: 'main'
    path: 'source-code'
    fetch-depth: '10'
```

#### Validation Features
- **Repository Format**: Validates owner/repo format for both primary and alternate repositories
- **Fetch Depth**: Ensures positive integer values
- **Parameter Consistency**: Validates that alternate_repository is provided when alternate_ref is specified
- **Timeout Protection**: Prevents hanging on network issues during ref existence checks

## ⚙️ Configuration

### Required Secrets
| Secret | Purpose | Required For |
|--------|---------|--------------|
| `SSH_KEY` | Private repository access | Flutter chart checkout |
| `GITHUB_TOKEN` | API access and deployments | All workflows (auto-provided) |

### Required Permissions
```yaml
permissions:
  contents: write        # Repository content access
  checks: write         # Check run creation
  pull-requests: write  # PR comment posting
  pages: write         # GitHub Pages deployment
```

### Repository Settings
1. **GitHub Pages**: Must be enabled with source set to "Deploy from a branch"
2. **Actions**: Must be enabled with appropriate permissions
3. **SSH Keys**: Private key must be added to repository secrets

## 📦 Dependencies

### Runtime Dependencies
- **Node.js**: 18.17.0 with comprehensive npm caching and retry mechanisms
- **Flutter**: 3.24.1 with stable channel and multi-layer caching
- **Git**: Enhanced repository operations with retry logic and optimizations

### Action Dependencies
- `actions/checkout@v4`: Repository checkout with sparse checkout support
- `actions/setup-node@v4`: Node.js environment with caching
- `subosito/flutter-action@v2`: Flutter environment with caching
- `actions/github-script@v7`: GitHub API interactions
- `actions/cache@v3`: Multi-layer caching for dependencies and build artifacts
- `actions/upload-artifact@v3`: Build artifact management
- `actions/download-artifact@v3`: Artifact retrieval for integration
- `nick-fields/retry@v2`: Retry mechanisms for reliability
- `deriv-com/shared-actions/*`: Custom deployment actions

### Build Dependencies
- **gh-pages**: GitHub Pages deployment with retry logic
- **webpack**: Application bundling with optimization
- **flutter build web**: Web application compilation with HTML renderer
- **Build Verification**: Comprehensive artifact validation and integrity checks

## 🔧 Troubleshooting

### Common Issues

#### Build Failures
**Symptoms**: Workflow fails during build steps
**Enhanced Solutions**:
- **Automatic Retry**: Workflows include 3-attempt retry logic for transient failures
- **Dependency Issues**: Check Node.js/Flutter version compatibility and clear caches
- **Build Verification**: Review comprehensive build logs with grouped sections
- **SSH Key Issues**: Verify SSH_KEY secret format and permissions
- **Parallel Build Conflicts**: Check for resource contention between Flutter and Node.js builds

#### Deployment Failures
**Symptoms**: Build succeeds but deployment fails
**Enhanced Solutions**:
- **Retry Mechanisms**: Deployment includes automatic retry with exponential backoff
- **Verification Checks**: Workflows perform health checks with configurable timeout
- **Permission Issues**: Verify GitHub Pages settings and workflow permissions
- **Branch Protection**: Check for branch protection rules on gh-pages branch
- **Artifact Integration**: Verify build artifact integrity and integration process

#### Preview Not Accessible
**Symptoms**: Deployment succeeds but preview URL returns 404
**Enhanced Solutions**:
- **Automatic Verification**: Workflows include deployment verification with 10 retry attempts
- **Propagation Wait**: Built-in 60-second wait time for GitHub Pages propagation
- **Health Checks**: Automated curl-based verification of deployment accessibility
- **Build Statistics**: Check deployment size and file count in workflow comments
- **Manual Verification**: Use provided troubleshooting URLs in failure comments

#### Fork Security Blocks
**Symptoms**: PRs from forks don't trigger workflows
**Enhanced Solutions**:
- **Enhanced Security**: Comprehensive input validation prevents malicious code execution
- **Clear Communication**: Detailed security messages explain why fork PRs are blocked
- **Manual Approval**: Repository maintainers can manually trigger workflows for trusted forks
- **Branch Strategy**: Contributors should create branches in main repository for automatic builds

#### Cleanup Issues
**Symptoms**: Preview cleanup fails or doesn't run
**Enhanced Solutions**:
- **Retry Logic**: Cleanup includes configurable retry mechanisms for network failures
- **Branch Validation**: Automatic verification of gh-pages branch existence
- **Atomic Operations**: Safe directory removal with rollback capabilities
- **Manual Instructions**: Detailed manual cleanup guidance provided in failure comments
- **Concurrency Control**: Prevents race conditions during cleanup operations

### Debug Information

#### Enhanced Workflow Logs
- **Comprehensive Logging**: All workflows include detailed logging with `::group::` sections and step-by-step progress
- **Build Verification**: Multi-stage verification confirms successful outputs at each phase
- **Error Context**: Error messages include detailed context, troubleshooting steps, and suggested solutions
- **Performance Metrics**: Build size reporting, file counts, and timing information
- **Security Logging**: Input validation results and security check outcomes

#### Enhanced Status Checks
- **Real-time Updates**: Build status reported via GitHub checks API with detailed progress
- **Comprehensive Comments**: PR comments include build statistics, deployment URLs, and verification status
- **Failure Reporting**: Failed builds include workflow run links, error summaries, and next steps
- **Success Metrics**: Successful deployments include size metrics, verification status, and access URLs
- **Cleanup Status**: Cleanup operations provide detailed status and confirmation messages

## 🔒 Security

### Security Measures
1. **Fork Protection**: Only same-repository PRs trigger automatic builds
2. **Permission Scoping**: Minimal required permissions for each workflow
3. **Secret Management**: SSH keys stored securely in repository secrets
4. **Input Validation**: Custom actions validate all inputs
5. **Error Sanitization**: Sensitive information excluded from logs

### Security Best Practices
- Regularly rotate SSH keys
- Monitor workflow runs for suspicious activity
- Keep action dependencies updated
- Review PR changes before approval
- Use branch protection rules for sensitive branches

### Audit Trail
- All deployments are tracked via git commits
- Workflow runs provide complete execution history
- PR comments maintain deployment status records
- GitHub Pages deployment history is preserved

## 📚 Additional Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Flutter Web Deployment](https://docs.flutter.dev/deployment/web)
- [Webpack Configuration](https://webpack.js.org/configuration/)

## 🤝 Contributing

When modifying workflows:
1. Test changes in a fork first
2. Update documentation for any new features
3. Follow existing naming conventions
4. Add appropriate error handling
5. Include status reporting for user feedback

---

*Last updated: February 2025*
