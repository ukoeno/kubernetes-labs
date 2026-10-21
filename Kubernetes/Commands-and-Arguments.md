# Kubernetes Commands and Arguments

## 1. Objective

Understand how Kubernetes controls the process that runs inside a container using `command` and `args`, and how these fields relate to the Docker image's `ENTRYPOINT` and `CMD`.

## 2. Key Concepts

- **`command`** overrides the container image's `ENTRYPOINT`.
- **`args`** overrides the container image's `CMD`.
- If neither field is specified, Kubernetes uses the defaults configured in the image.
- If only `args` is specified, the image's `ENTRYPOINT` is retained.
- If only `command` is specified, the image's default `CMD` is not automatically appended.

| Kubernetes field | Corresponding image setting |
|---|---|
| `command` | `ENTRYPOINT` |
| `args` | `CMD` |

## 3. Lab 1: Run a Command That Finishes

### Pod manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: command-args-demo
spec:
  restartPolicy: Never
  containers:
    - name: demo
      image: busybox:1.36
      command: ["echo"]
      args: ["Kubernetes commands and arguments"]
```

### Commands used

```bash
kubectl apply -f command-args-demo.yaml
kubectl get pod command-args-demo
kubectl logs command-args-demo
```

### Expected output

```text
Kubernetes commands and arguments
```

The Pod reached the `Completed` status with zero restarts.

### Explanation

Kubernetes ran `echo` with the supplied text as its argument. The process printed the message and exited successfully. Because the process had finished and the Pod's restart policy was `Never`, the Pod became `Completed`.

**Key lesson:** `Completed` is a normal successful outcome for a Pod whose work has finished.

## 4. Lab 2: Run a Command That Keeps Executing

### Pod manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: command-args-sleep
spec:
  restartPolicy: Never
  containers:
    - name: sleeper
      image: busybox:1.36
      command: ["sleep"]
      args: ["300"]
```

### Commands used

```bash
kubectl apply -f command-args-sleep.yaml
kubectl get pod command-args-sleep
kubectl get pod command-args-sleep -o yaml
```

### Observed result

The Pod was `Running`, with `1/1` containers ready and zero restarts.

### Explanation

Kubernetes ran `sleep` with `300` as its argument. The process remained active while waiting for the specified duration. When it exits, the Pod should transition to `Completed`, provided it is not deleted first and the process exits successfully.

The YAML output can be used to inspect the configured `command` and `args` fields.

## 5. Interview Example

Suppose a container image defines:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

A Kubernetes Pod specifies:

```yaml
args: ["worker.py"]
```

**What command runs?**

```text
python worker.py
```

**Why?**

The Kubernetes `args` field replaces the image's default `CMD` (`app.py`). The image's `ENTRYPOINT` (`python`) remains in effect.

## 6. Cleanup

Remove the test Pods after completing the lab:

```bash
kubectl delete pod command-args-demo command-args-sleep
```

## 7. Key Takeaways

- `command` overrides the image's `ENTRYPOINT`.
- `args` overrides the image's `CMD`.
- A process that finishes successfully can leave its Pod in `Completed` status.
- A long-running process can keep a Pod in `Running` status.
- Understanding the relationship between image defaults and Pod overrides helps troubleshoot container startup and execution issues.

