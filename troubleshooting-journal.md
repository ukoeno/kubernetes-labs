## 2026-07-29 - GitHub Push Permission Error

### Command

git push

### Error

Permission to ukoeno/Kubernetes-labs.git denied to ukoeno-devops.

### Cause

Git Credential Manager was using cached credentials from a different GitHub account.

### Resolution

- Checked remote URL
- Verified Git configuration
- Removed cached GitHub credentials from Windows Credential Manager
- Re-authenticated as ukoeno

### Lesson Learned

Git commit identity is different from GitHub authentication identity.

---

## 2026-07-29 - Minikube Virtualization Error

### Command

minikube start

### Error

Failed to start virtualbox VM.
This computer doesn't have VT-X/AMD-v enabled.

### Cause

Under investigation.

### Resolution

Pending.

### Lesson Learned

Pending.
