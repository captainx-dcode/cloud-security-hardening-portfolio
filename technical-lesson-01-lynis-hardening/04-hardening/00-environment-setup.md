# 00 - Environment Setup

**Lesson:** Technical Lesson 1 - Securing Cloud Services Using Lynis
**Date:** 2026-09-21
**Objective:** Prepare an isolated Ubuntu 22.04 assessment environment containing an Apache web server, and install the Lynis auditing tool.

---

## 1. Pull the assessment image

```bash
sudo docker pull nwendlo1/c4technicallesson
```

- Image: `nwendlo1/c4technicallesson:latest`
- Digest: `sha256:afbc710512f72168b7a928d59149bad3bdd1a22c013ed3e186ab597483496c42`
- Recording the digest makes the exact image version reproducible.

## 2. Start the container

```bash
sudo docker run -it --name lynis-lab -p 8080:80 nwendlo1/c4technicallesson
```

| Flag | Purpose |
|---|---|
| `-it` | Interactive terminal |
| `--name lynis-lab` | Predictable container name for later `docker cp` / `docker start` |
| `-p 8080:80` | Maps host port 8080 to Apache on container port 80 |

To re-enter the container later:

```bash
sudo docker start -ai lynis-lab
```

## 3. Verify the environment

| Check | Command | Result |
|---|---|---|
| Operating system | `cat /etc/os-release` | Ubuntu 22.04.4 LTS (Jammy Jellyfish) |
| Current user | `whoami` | `Neo` (non-root) |
| Hostname | `hostname` | `f76cb5b89009` (container ID) |
| Web server | `apache2 -v` | Apache/2.4.52 (Ubuntu), built 2024-07-17 |
| Apache running | `ps aux \| grep apache` | Master process as `root`, workers as `www-data` |
| Lynis | `lynis --version` | Not found initially |

**Why this matters:** the OS version confirms the CIS Ubuntu Linux 22.04 LTS Benchmark is the correct reference, and Apache 2.4 confirms the CIS Apache HTTP Server Benchmark applies.

## 4. Privilege check

```bash
sudo -l
```

Result: `(ALL) NOPASSWD: ALL`

**Observation:** the `Neo` account has unrestricted, passwordless root access. This is acceptable in a disposable training container but would be a finding on a production server.

- CIS Controls v8, Safeguard 5.4 (IG1): restrict administrator privileges to dedicated administrator accounts.
- Cloud relevance: equivalent to over-permissive IAM roles. In the cloud, use least-privilege roles and just-in-time elevation.

## 5. Install Lynis

Lynis was not present (`find / -iname "*lynis*"` returned nothing), so it was installed from the Ubuntu repository:

```bash
sudo apt update
sudo apt install lynis -y
lynis --version
```

Result: **Lynis 3.0.7**

### Attempt to use the latest upstream release

```bash
git clone https://github.com/CISOfy/lynis.git
cd lynis && ./lynis --version
```

Result: Lynis refused to run from the cloned directory:

```
Fatal error: permissions of file /home/Neo/lynis/db/languages/en are not strict enough.
Access to 'other' should be denied or read-only.
```

**Explanation:** Lynis checks its own files for safe permissions before running, so that a low-privilege user cannot tamper with an audit tool that runs as root. The clone was created with a permissive default umask, which left "other" write access on some files. This is the same class of weakness Lynis later flags in the system umask (AUTH-9328).

**Decision:** the baseline uses the packaged Lynis 3.0.7 so results are consistent and reproducible. The upstream version can be revisited after tightening permissions.

## 6. Evidence

The terminal output for each step is shown in the code blocks and tables above. The scan evidence begins in the next document.

## Next

[01 - Baseline Lynis Audit](./01-baseline-lynis-audit.md)