# Offline E2E Testing

This document describes how to run E2E tests in environments without internet connectivity.

## Quick Start

To skip cert-manager installation when network is unavailable:

```bash
export E2E_SKIP_CERT_MANAGER=true
go test ./test/e2e -v
```

## Environment Variables

- `E2E_SKIP_CERT_MANAGER=true` - Skip cert-manager installation completely
- `E2E_OFFLINE=true` - Enable offline mode (alternative to the above)

## Local Manifests

To use local cert-manager manifests instead of downloading from GitHub:

1. Download cert-manager manifests:
   ```bash
   curl -L https://github.com/cert-manager/cert-manager/releases/download/v1.15.1/cert-manager.yaml \
     > test/e2e/manifests/cert-manager.yaml
   ```

2. Run tests normally - they will automatically use local manifests:
   ```bash
   go test ./test/e2e -v
   ```

## Fallback Behavior

The E2E test framework automatically:

1. **First**: Checks for local manifests in:
   - `test/e2e/manifests/cert-manager-{version}.yaml`
   - `test/e2e/manifests/cert-manager.yaml`
   - `manifests/cert-manager-{version}.yaml`
   - `manifests/cert-manager.yaml`

2. **Second**: Tests network connectivity to `github.com:443`

3. **Third**: Falls back to remote download if network is available

4. **Finally**: Skips installation if `E2E_SKIP_CERT_MANAGER=true` or fails with helpful error message

This ensures E2E tests work in both online and offline environments.