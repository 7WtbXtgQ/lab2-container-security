# Lab 2 – Container Security Hardening

## About the lab
In this lab I worked with a simple Python application and focused on improving its container security. The starting point was a vulnerable Docker image, and the goal was to harden it and verify the improvements using security tools.

---

## What I did

### 1. Analyzed the vulnerable container
The original container had several security issues:
- Running as root
- Using a larger base image
- No healthcheck
- No clear security configurations

---

### 2. Hardened the container
I created a new Dockerfile with improvements:

- Switched to a smaller base image (`python:3.12-slim-bookworm`)
- Created and used a non-root user
- Set correct ownership on files
- Installed dependencies with `--no-cache-dir`
- Added a healthcheck

These changes reduce the attack surface and follow best practices.

---

## Security scanning (Trivy)

### Before hardening
The vulnerable image had a large number of vulnerabilities:

- **Total: 1215**
- **High: 1067**
- **Critical: 148**

This shows how insecure a default or unoptimized container can be.

### After hardening
After applying improvements:

- **Total: 0**
- **High: 0**
- **Critical: 0**

This is a significant improvement and shows that using a minimal base image and better practices can drastically reduce vulnerabilities.

See:
- `scan-before.txt`
- `scan-after.txt`

---

## Policies (Kubernetes / Gatekeeper)

### Vulnerable pod
The vulnerable pod was completely denied due to multiple policy violations, including:

- Running as privileged container
- No CPU/memory limits
- Missing liveness/readiness probes
- Not running as non-root
- Using `latest` tag
- Using default service account
- Not using read-only root filesystem
- Not dropping capabilities

This shows how many security issues exist in a default setup.

---

### Hardened pod
The hardened pod passed most checks:

- **Allowed with 1 warning**

Remaining issue:
- Using default service account (`no-default-sa`)

This means the container is mostly compliant but can still be improved further.

---

## Screenshots
Screenshots are included showing:

- Trivy scan (before hardening)
- Trivy scan (after hardening)
- Policy denial for vulnerable pod
- Policy result for hardened pod

Located in:
```
screenshots/
```

---

## Reflection

This lab showed how big of a difference basic security improvements can make. The vulnerable container had over 1000 vulnerabilities, while the hardened version had none in the same severity range.

Using a smaller base image and avoiding unnecessary packages made a big impact. Running as a non-root user is also a simple but important change.

Working with Trivy made it easier to understand vulnerabilities in practice, instead of just theory.

The policy part was also useful. It showed that even if something works, it can still be considered insecure. The vulnerable pod was denied completely, which clearly shows how many best practices were missing.

One thing I noticed is that even after hardening, there can still be small issues left, like using the default service account. This shows that security is something you improve step by step.

---

## Conclusion

This lab demonstrates how to take a vulnerable container and improve it using basic security practices. The results show a clear reduction in vulnerabilities and better compliance with security policies.
