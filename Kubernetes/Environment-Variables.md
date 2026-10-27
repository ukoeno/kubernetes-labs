# Kubernetes Environment Variables

## Objective

Learn how to provide application configuration to a Kubernetes container using environment variables sourced from a ConfigMap.

## Key Concepts

- **ConfigMap:** A Kubernetes API object that stores non-confidential configuration data.
- **Environment variable:** A name-value pair made available to a process through its execution environment.
- **Secret:** A Kubernetes API object intended for sensitive configuration data, such as passwords and API tokens.

A ConfigMap can supply environment variables to a container through `valueFrom.configMapKeyRef`.

## Lab Files

- `configmap-env-demo.yaml` — Defines a Pod that reads configuration values from the `app-config` ConfigMap.

## Configuration

The `app-config` ConfigMap contains:

```yaml
data:
  APP_COLOR: blue
  APP_ENV: dev
```

The Pod references these keys using `configMapKeyRef`.

## Verification

Command:

```bash
kubectl logs configmap-env-demo
```

Observed output:

```text
APP_COLOR=blue
APP_ENV=dev
```

The output confirms that the container received and printed both environment variable values.

## Important Behaviour

Changing a ConfigMap does not automatically update environment variables already injected into a running container. The container must be recreated to receive the updated values.

A ConfigMap mounted as a volume behaves differently: Kubernetes eventually updates the mounted files, except when mounted using `subPath`. The application must reread the updated file to use the new configuration.

## Key Takeaways

1. ConfigMaps separate non-confidential configuration from container image definitions.
2. Environment variables provide configuration to processes inside containers.
3. Secrets are intended for sensitive data, but appropriate access controls and encryption are still necessary.
4. Always verify configuration from the running workload rather than assuming it was applied correctly.
