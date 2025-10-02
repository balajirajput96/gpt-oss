# Deployment Guide

This guide explains how to deploy and publish the `gpt-oss` package to PyPI.

## Prerequisites

- The repository must be configured with PyPI Trusted Publishing (OIDC)
- GitHub environment named "release" should be configured with appropriate protections
- Required permissions: `contents: read` and `id-token: write`

## Automated Deployment via GitHub Actions

The deployment is fully automated using GitHub Actions. The CI workflow will automatically build and publish to PyPI when:

### 1. Creating a Release

1. Go to the GitHub repository
2. Click on "Releases" → "Create a new release"
3. Create a new tag (e.g., `v0.0.2`)
4. Fill in release title and description
5. Click "Publish release"
6. The CI workflow will automatically trigger and publish to PyPI

### 2. Pushing a Version Tag

```bash
git tag v0.0.2
git push origin v0.0.2
```

The CI workflow will automatically trigger and publish to PyPI.

### 3. Manual Workflow Dispatch

1. Go to the "Actions" tab in GitHub
2. Select the "CI" workflow
3. Click "Run workflow"
4. Select the branch and click "Run workflow"

## Local Build Testing

To test the build locally before deployment:

```bash
# Install build tools
pip install build uv

# Build with Python build
python -m build

# Or build with uv (as used in CI)
uv build

# Check the dist/ folder for artifacts
ls -lh dist/
```

## Build Artifacts

The build process creates two artifacts in the `dist/` directory:
- `gpt_oss-{version}-py3-none-any.whl` - The wheel distribution
- `gpt-oss-{version}.tar.gz` - The source distribution

Note: The `dist/` directory is excluded from git via `.gitignore`.

## Deployment Process Flow

1. Workflow triggers (release/tag/manual)
2. Repository is checked out
3. Python 3.12 is set up
4. Build tools (`pip`, `setuptools`, `wheel`, `build`, `uv`) are installed
5. Package is built using `uv build`
6. Package is published to PyPI using Trusted Publishing (OIDC)
7. Attestations are generated for supply chain security

## Version Management

The package version is defined in `pyproject.toml`:

```toml
[project]
version = "0.0.1"
```

Update this version before creating a new release.

## Troubleshooting

### Build Failures

- Ensure all dependencies are listed in `pyproject.toml`
- Check that the `_build` directory and custom build backend are properly configured
- Test the build locally before pushing tags

### PyPI Publishing Failures

- Verify that Trusted Publishing is configured in PyPI project settings
- Check that the GitHub environment "release" has correct permissions
- Ensure the version number is not already published on PyPI

### Test Failures

The test suite requires access to the harmony encoding vocab files. In CI environments without internet access, some tests may fail. This is expected and does not affect the deployment process.

## Security Considerations

- The workflow uses Trusted Publishing (OIDC) - no API keys are stored
- Attestations are automatically generated for supply chain verification
- The workflow runs in the "release" environment for gated approvals
