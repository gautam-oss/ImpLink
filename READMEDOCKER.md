# WSL → Ubuntu → Docker Engine (Phase 0–2)

## Executive Summary

This document is a **single-source operational reference** covering all **critical commands** used to provision a **production-aligned local development stack** on Windows:

**Windows → WSL2 → Ubuntu LTS → Docker Engine (no Docker Desktop)**

Scope ends at **Phase 2 completion (Docker Engine validated)**.

---

## Phase 0 — WSL Platform (Windows Layer)

### Check WSL Version

```powershell
wsl --version
```

**Purpose:** Validate WSL2 availability and kernel readiness.

---

### List Installed Linux Distributions

```powershell
wsl --list --verbose
```

**Purpose:** Confirm Ubuntu is installed and running under **Version 2**.

---

### Install Ubuntu (If Missing)

```powershell
wsl --install -d Ubuntu
```

**Purpose:** Provision Ubuntu LTS as the Linux execution environment.

---

### Start Ubuntu (Canonical Entry Point)

```powershell
wsl
```

or (explicit):

```powershell
wsl -d Ubuntu
```

**Purpose:** Enter Linux context from Windows.

---

### Start Ubuntu in Linux Home (Recommended)

```powershell
wsl --cd ~
```

**Purpose:** Enforce `/home/<user>` as startup directory.

---

### Shutdown WSL (Apply Config Changes)

```powershell
wsl --shutdown
```

**Purpose:** Fully restart WSL to apply system-level configuration.

---

## Phase 1 — Ubuntu Baseline (Linux Layer)

### Verify Distribution Details

```bash
lsb_release -a
```

**Purpose:** Confirm Ubuntu LTS version and codename.

---

### Update Package Index

```bash
sudo apt update
```

**Purpose:** Synchronize package metadata with upstream repositories.

---

### Upgrade Installed Packages (Optional)

```bash
sudo apt upgrade -y
```

**Purpose:** Align system packages to latest secure versions.

---

### Check Current Working Directory

```bash
pwd
```

**Purpose:** Verify you are operating inside `/home/<user>`.

---

### Exit Ubuntu

```bash
exit
```

**Purpose:** Return to Windows shell.

---

## Phase 1.5 — WSL systemd Enablement (Critical for Docker)

### Edit WSL Configuration

```bash
sudo nano /etc/wsl.conf
```

**Required content:**

```ini
[boot]
systemd=true
```

---

### Apply Configuration

```powershell
wsl --shutdown
wsl --cd ~
```

---

### Verify systemd is PID 1

```bash
ps -p 1 -o comm=
```

**Expected output:**

```text
systemd
```

---

## Phase 2 — Docker Engine Installation (Inside WSL)

### Install Prerequisite Packages

```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
```

**Purpose:** Enable secure third-party package installation.

---

### Create Docker Keyring Directory

```bash
sudo mkdir -p /etc/apt/keyrings
```

---

### Add Docker Official GPG Key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

---

### Register Docker Repository

```bash
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

### Refresh Package Index (Include Docker Repo)

```bash
sudo apt update
```

---

### Install Docker Engine Stack

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

### Add User to Docker Group

```bash
sudo usermod -aG docker $USER
newgrp docker
```

**Purpose:** Enable Docker usage without `sudo`.

---

### Check Docker Version

```bash
docker --version
```

---

### Verify Docker Daemon Status

```bash
sudo systemctl status docker
```

---

### Start Docker (If Needed)

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

---

### Functional Validation (Final Gate)

```bash
docker run hello-world
```

**Success Criteria:**

* Image pulls successfully
* Container runs
* “Hello from Docker!” message appears

---

## Phase 2 — Completion Status

At this point:

* WSL2 is operational
* Ubuntu LTS is stable
* systemd is enabled
* Docker Engine is running
* Docker CLI ↔ daemon communication validated

**Environment is production-aligned and ready for application workloads.**

---

## Next Phases (Out of Scope)

* Phase 3: Python 3.11 + Virtual Environments
* Phase 4: Django / Application Containers
* Phase 5: Docker Compose + Databases

---

**Document Owner:** Gautam Kumar
**Purpose:** Repeatable local-dev baseline
**Status:** Phase 2 — Closed
