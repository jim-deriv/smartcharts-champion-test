# GitHub Actions

This directory contains reusable GitHub Actions for the SmartCharts Champion project.

## Actions Overview

### 1. Security Validation (`security-validation/`)
**Purpose**: Validates PR source and sets deployment parameters

**Inputs**:
- `pr_number`: Pull request number
- `event_name`: GitHub event name
- `head_repo_full_name`: Head repository full name
- `base_repo_full_name`: Base repository full name
- `repository_owner`: Repository owner

**Outputs**:
- `is_safe`: Whether the PR is safe to build
- `branch_name`: Deployment branch name
- `deploy_url`: Deployment URL
- `is_preview`: Whether this is a preview deployment
- `build_base_path`: Build base path for deployment

### 2. Flutter Build (`flutter-build/`)
**Purpose**: Builds Flutter web application with caching and retry logic

**Inputs**:
- `flutter_version`: Flutter version (default: "3.24.1")
- `flutter_chart_ref`: Flutter chart repository reference (default: "master")
- `web_renderer`: Flutter web renderer (default: "html")
- `ssh_key`: SSH key for private repository access
- `github_token`: GitHub token for repository access
- `merge_commit_sha`: Merge commit SHA to checkout

**Outputs**:
- `build_size`: Size of the Flutter build
- `artifact_name`: Name of the uploaded artifact

### 3. Node.js Build (`nodejs-build/`)
**Purpose**: Builds SmartCharts Champion application with caching and retry logic

**Inputs**:
- `node_version`: Node.js version (default: "18.17.0")
- `node_env`: Node environment (default: "production")
- `merge_commit_sha`: Merge commit SHA to checkout

**Outputs**:
- `build_size`: Size of the Node.js build
- `artifact_name`: Name of the uploaded artifact

### 4. Deploy Preview (`deploy-preview/`)
**Purpose**: Integrates builds and deploys to GitHub Pages with verification

**Inputs**:
- `node_version`: Node.js version (default: "18.17.0")
- `merge_commit_sha`: Merge commit SHA to checkout
- `branch_name`: Deployment branch name
- `deploy_url`: Deployment URL
- `is_preview`: Whether this is a preview deployment
- `github_token`: GitHub token for deployment
- `deployment_verification_attempts`: Number of verification attempts (default: "10")
- `deployment_wait_time`: Time to wait before verification (default: "60")

**Outputs**:
- `deployment_verified`: Whether deployment was verified successfully
- `total_size`: Total deployment size

### 5. Preview Notifications (`preview-notifications/`)
**Purpose**: Posts preview deployment notifications and build statistics

**Inputs**:
- `github_token`: GitHub token for posting comments
- `pr_number`: Pull request number
- `pr_title`: Pull request title
- `head_sha`: Head commit SHA
- `deploy_url`: Deployment URL
- `deployment_verified`: Whether deployment was verified
- `total_size`: Total deployment size (optional)
- `flutter_build_size`: Flutter build size (optional)
- `nodejs_build_size`: Node.js build size (optional)
- `workflow_status`: Overall workflow status (success/failure/started)
- `workflow_run_id`: GitHub workflow run ID
- `repository_url`: Repository URL

### 6. SmartCharts Checkout (`checkout/`)
**Purpose**: Provides intelligent repository checkout with fallback support

**Features**:
- Primary/Fallback Logic: Attempts primary repository/ref, falls back if needed
- Existence Checking: Verifies refs exist before checkout
- Flexible Configuration: Supports various checkout scenarios
- Status Reporting: Outputs which repository/ref was actually used

## Usage

These actions are used in the main PR preview workflow (`pr-preview-pages.yml`) to provide:

1. **Parallel Execution**: Flutter and Node.js builds run simultaneously
2. **Reusability**: Actions can be used in other workflows
3. **Maintainability**: Each action has a single, clear responsibility
4. **Error Handling**: Comprehensive error handling and retry mechanisms
5. **Security**: Input validation and secure operations

## Benefits

- **Performance**: Parallel builds reduce overall workflow time
- **Modularity**: Each action can be tested and maintained independently
- **Consistency**: Standardized error handling and logging across all actions
- **Flexibility**: Actions can be easily reused in different workflows
- **Debugging**: Isolated failures are easier to troubleshoot

## Dependencies

- **Node.js**: Version 18.17.0 with npm caching
- **Flutter**: Version 3.24.1 with stable channel
- **SSH Key**: Configured in repository secrets for private repository access
- **GitHub Token**: With appropriate permissions for deployment and comments
