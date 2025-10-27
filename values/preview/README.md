# Preview Environment

This directory is used for dynamic preview environments that are automatically created for feature branches and pull requests.

## How it works

Preview environments are dynamically created based on branch names or PR numbers. Each preview environment gets its own subdirectory with environment-specific configurations.

## Directory Structure

```
preview/
├── README.md (this file)
├── template/           # Template files for new preview environments
│   ├── kustomization.yaml
│   └── values.yaml
└── [branch-name]/      # Dynamic directories created per branch/PR
    ├── kustomization.yaml
    └── values.yaml
```

## Example Preview Environments

- `preview/feature/user-auth/` - For feature/user-auth branch
- `preview/pr-123/` - For pull request #123
- `preview/hotfix/critical-bug/` - For hotfix/critical-bug branch

## Automation

Preview environments are typically managed by CI/CD pipelines that:

1. Detect new branches or pull requests
2. Create a new subdirectory in `preview/`
3. Generate environment-specific configurations
4. Deploy the preview environment
5. Clean up when the branch/PR is merged or closed

## Configuration

Each preview environment inherits from the base configuration but can override:
- Image tags (usually branch-specific)
- Resource limits (typically smaller than production)
- Environment variables
- Ingress hostnames
- Replica counts

## Cleanup

Preview environments should be automatically cleaned up when:
- The feature branch is merged
- The pull request is closed
- The branch is deleted
- After a configurable TTL (time-to-live)
