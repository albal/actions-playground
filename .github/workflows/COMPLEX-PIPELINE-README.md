# Complex Build Pipeline Documentation

## Overview
The `complex-build-pipeline.yml` workflow demonstrates a sophisticated CI/CD pipeline using **reusable workflows** for maximum modularity and reusability. The pipeline has been refactored to use separate reusable workflow files for each phase, making it easier to maintain and reuse across different projects.

## Reusable Workflow Architecture

### 🔄 Reusable Workflow Files
The complex pipeline now uses the following reusable workflow files:

- **`setup-reusable.yml`**: Environment setup, dependency caching, and code quality checks
- **`build-reusable.yml`**: Frontend, backend, and database build processes
- **`test-reusable.yml`**: Unit, integration, and end-to-end tests
- **`package-reusable.yml`**: Application packaging and documentation generation
- **`deploy-reusable.yml`**: Deployment to target environments

### 📋 Main Workflow Structure
The main `complex-build-pipeline.yml` now orchestrates the entire pipeline by calling reusable workflows:

```yaml
jobs:
  setup-phase:
    uses: ./.github/workflows/setup-reusable.yml
    
  build-phase:
    uses: ./.github/workflows/build-reusable.yml
    needs: [setup-phase]
    with:
      build-version: ${{ needs.setup-phase.outputs.build-version }}
      
  test-phase:
    uses: ./.github/workflows/test-reusable.yml
    needs: [build-phase]
    with:
      skip-tests: ${{ inputs.skip_tests }}
      
  package-phase:
    uses: ./.github/workflows/package-reusable.yml
    needs: [test-phase, setup-phase]
    with:
      build-version: ${{ needs.setup-phase.outputs.build-version }}
      
  deploy-phase:
    uses: ./.github/workflows/deploy-reusable.yml
    needs: [package-phase]
    with:
      package-version: ${{ needs.package-phase.outputs.package-version }}
      environment: ${{ inputs.environment }}
```

## Workflow Features

### 🎯 Triggers
- **Manual trigger** (`workflow_dispatch`): Allows manual execution with custom inputs
- **Push trigger**: Automatically runs on pushes to main/master branches
- **Pull request trigger**: Runs on PRs to main/master branches

### ⚙️ Input Parameters
- **environment**: Choose between 'staging' or 'production' deployment target
- **skip_tests**: Boolean option to skip the entire test phase for faster builds

### 🏗️ Pipeline Architecture

The pipeline now uses a **reusable workflow architecture** with the following phases:

#### Phase 1: Setup (Reusable Workflow: `setup-reusable.yml`)
- **environment-setup**: Generates build version and cache keys
- **dependency-cache**: Simulates dependency downloading and caching
- **code-quality**: Runs linting and security scans

#### Phase 2: Build (Reusable Workflow: `build-reusable.yml`)
- **frontend-build**: Compiles TypeScript, processes assets, creates bundles
- **backend-build**: Builds services, APIs, and microservices
- **database-build**: Generates and validates migration scripts

#### Phase 3: Test (Reusable Workflow: `test-reusable.yml`)
- **unit-tests**: Frontend, backend, and database unit tests
- **integration-tests**: API and service integration testing
- **e2e-tests**: Browser-based end-to-end testing

#### Phase 4: Package (Reusable Workflow: `package-reusable.yml`)
- **package-application**: Creates deployment packages
- **generate-documentation**: Generates API and deployment docs

#### Phase 5: Deploy (Reusable Workflow: `deploy-reusable.yml`)
- **deploy-package**: Deploys to target environment

### 🔗 Benefits of Reusable Workflows

1. **Modularity**: Each phase is encapsulated in its own reusable workflow
2. **Reusability**: Individual workflows can be used in other projects
3. **Maintainability**: Changes to specific phases don't affect others
4. **Testability**: Each reusable workflow can be tested independently
5. **Composability**: Different combinations of workflows can create new pipelines

### 📊 Key Metrics Simulated
- **Build times**: Realistic sleep durations to simulate actual build times
- **Artifact sizes**: Example package sizes (15.2 MB frontend, 42.1 MB backend)
- **Test counts**: Realistic test numbers (127 frontend, 89 backend, 45 database)
- **Version tracking**: Build version passed through the entire pipeline

## Reusable Workflow Usage Examples

### Using Individual Workflows
Each reusable workflow can be used independently in other projects:

```yaml
# Example: Using just the build phase
jobs:
  build:
    uses: ./.github/workflows/build-reusable.yml
    with:
      build-version: "1.0.0"
```

### Creating Custom Pipelines
You can create new workflows by combining different reusable workflows:

```yaml
# Example: Development pipeline (skip tests)
jobs:
  setup:
    uses: ./.github/workflows/setup-reusable.yml
    
  build:
    uses: ./.github/workflows/build-reusable.yml
    needs: [setup]
    with:
      build-version: ${{ needs.setup.outputs.build-version }}
      
  deploy:
    uses: ./.github/workflows/deploy-reusable.yml
    needs: [build]
    with:
      package-version: ${{ needs.setup.outputs.build-version }}
      environment: "development"
```

## Usage Examples

### Manual Execution
1. Go to Actions tab in GitHub
2. Select "Complex Build Pipeline" 
3. Click "Run workflow"
4. Choose environment and test options
5. Monitor the visual pipeline execution

### Automatic Execution
- Push to main/master branch triggers full pipeline
- Pull requests trigger pipeline with staging deployment only

### Conditional Execution
- Production deployment only occurs on main/master or manual trigger with production environment
- Tests can be skipped using the `skip_tests` input parameter
- Failure notifications only sent if pipeline fails

## Learning Outcomes
This workflow demonstrates:
- ✅ **Reusable workflow architecture** for maximum modularity
- ✅ **Workflow composition** using multiple reusable workflows
- ✅ **Input/output parameter passing** between reusable workflows
- ✅ **Conditional execution** based on inputs and results
- ✅ **Environment-based deployments** with reusable workflows
- ✅ **Dependency management** with `needs` keyword
- ✅ **Realistic build stage simulation** within reusable components

## Performance Characteristics
- **Total execution time**: ~15 minutes (simulated)
- **Modularity**: 5 separate reusable workflow files
- **Reusability**: Each workflow can be used independently
- **Maintainability**: Clear separation of concerns per phase