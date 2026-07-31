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

## Minikube Virtualization Error

Date: 2026-07-29

Command:
minikube start

Error:
Failed to start virtualbox VM.
This computer doesn't have VT-X/AMD-v enabled.

Initial Hypothesis:
Hardware virtualization disabled.

Investigation:
Task Manager → Performance → CPU

Result:
Virtualization = Enabled

Conclusion:
The BIOS virtualization explanation appears incorrect.
Further investigation required.

Pending.

### Lesson Learned

Pending.

### Investigation 3

Command:
VBoxManage list hostinfo

Results:
- Processor supports HW virtualization: yes
- Processor supports nested paging: yes
- Operating system: Windows 11

Conclusion:
VirtualBox can detect hardware virtualization.
The original Minikube message claiming VT-X/AMD-v is disabled appears inaccurate.

### Investigation 5

Command:
systeminfo

Results:
- Hypervisor detected
- Hypervisor Enforced Code Integrity enabled

Conclusion:
Virtualization is active on the system.
Evidence contradicts Minikube's claim that VT-X/AMD-v is disabled.

Current Hypothesis:
The issue is related to VirtualBox interaction with Windows virtualization/security features rather than BIOS settings.
Only 1259 MB of RAM was available at the time of testing.
Resource constraints may be contributing to the startup failure.
