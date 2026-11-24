# Week 4 – Lab 3: Harden CampusVault's Image

## 1. Compare v1 and v2

| Measurement | v1 | v2 |
|---|---|---|
| Size | Larger | Smaller |
| Layers | More build layers | Multi-stage build |
| User | root (UID 0) | UID 10001 |
| pip / curl / git | More tools available | Fewer unnecessary tools |
| HIGH + CRITICAL | More vulnerabilities expected | Fewer vulnerabilities expected |
| Rebuild time | Slower after code changes | Faster after code changes |

**Answer:**

The biggest size improvement came from the multi-stage build and copying only necessary files. The biggest security improvement came from running the application as a non-root user (UID 10001).

## 2. Which build was faster after the code change?

**Result:** The v1 build should be faster.

**Explanation:** Docker reuses cached dependency layers because the requirements file did not change.

The important line is:

`COPY requirements.txt .`

It comes before:

`RUN /venv/bin/pip install --no-cache-dir -r requirements.txt`

This prevents reinstalling dependencies when only application code changes.

## 3. Why did RUN rm fake-config.txt not remove the secret?

Docker stores every instruction in a separate image layer. Removing the file in a later layer does not erase it from the previous layer. The team must immediately revoke an exposed real key, generate a new one, and check for misuse.

**Expected result:** The fake password can still be recovered from the image layers.

`FAKE_DB_PASSWORD=not-a-real-secret-123`

## 4. Why did we scan a saved .tar?

We scanned a saved `.tar` file to avoid giving Trivy access to the Docker socket. The Docker socket provides powerful access to the Docker daemon. Scanning the saved image is safer because Trivy can inspect it without direct Docker socket access.

## 5. HIGH vulnerability

The exact result depends on the Trivy scan.

- CVE: Check your Trivy output.
- Package: Check your Trivy output.
- Installed version: Check your Trivy output.
- Fixed version: Check your Trivy output.

**Answer:** If a fixed version exists, update the affected package to that version, rebuild the Docker image, and scan it again.

## 6. Why did D1 fail and D2 work?

**D1 result:** Failed – Read-only file system.

**D2 result:** Worked – File created successfully in `/tmp`.

We want D1 to fail to protect application files from modification. We want D2 to work because temporary files must be allowed in `/tmp`.

## 7. What happens with shell-form CMD?

With shell-form CMD, `/bin/sh` becomes PID 1 instead of Uvicorn. The shell may not forward the SIGTERM signal correctly to the application. Docker may wait until the timeout and then send SIGKILL, making shutdown slower.

Exec-form CMD is better because Uvicorn receives the termination signal directly.