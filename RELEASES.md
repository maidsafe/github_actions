# Release Strategy

This document outlines the versioning and release strategy for the Autonomi GitHub Actions.

## Versioning

We follow [Semantic Versioning](https://semver.org/) (SemVer):

- **MAJOR** version when you make incompatible API changes
- **MINOR** version when you add functionality in a backwards compatible manner  
- **PATCH** version when you make backwards compatible bug fixes

### Version Tags

- `v1.0.0` - Specific version tags for exact version pinning
- `v1.0` - Minor version tags (automatically updated)
- `v1` - Major version tags (automatically updated)

## Release Process

### 1. Prepare Release

1. Update version references in documentation
2. Test thoroughly with different configurations
3. Update CHANGELOG.md with new features/fixes
4. Ensure all examples work with the new version

### 2. Create Release

1. Create and push version tags:
   ```bash
   git tag v1.0.0
   git tag -f v1.0  # Move/create minor version tag
   git tag -f v1    # Move/create major version tag
   git push origin v1.0.0
   git push origin v1.0 --force
   git push origin v1 --force
   ```

2. Create GitHub release with release notes

### 3. Update Documentation

Update all documentation references to use the new version.

## Usage Recommendations

### For Production
Use specific version tags for stability:
```yaml
uses: maidsafe/autonomi-actions/spawn-autonomi-network@v1.0.0
```

### For Testing
Use major version tags for latest features:
```yaml
uses: maidsafe/autonomi-actions/spawn-autonomi-network@v1
```

## Breaking Changes

When introducing breaking changes:

1. Increment MAJOR version
2. Document migration path in CHANGELOG.md
3. Provide deprecation warnings when possible
4. Maintain backward compatibility for at least one minor version when feasible

## Branch Strategy

- `main` - Latest stable release
- `develop` - Development branch for next release
- `v1.x` - Maintenance branches for major versions (if needed)

## Testing Before Release

1. Test with multiple Autonomi versions
2. Test different node counts (small, medium, large)
3. Test with and without EVM
4. Test cleanup under various failure scenarios
5. Verify examples work correctly