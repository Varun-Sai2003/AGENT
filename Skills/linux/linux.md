---
skill: linux
version_covered: general
depth: comprehensive
last_updated: 2026-03-25
source: web-research
use_when: >
  Use this skill whenever the user asks questions about Linux commands, system administration, kernel architecture, file system permissions, performance optimization, troubleshooting, or bash scripting idioms.
---

# Linux — Expert Reference

> **Quick Summary**: Linux is a powerful, flexible, and scalable open-source monolithic kernel operating system widely used for everything from embedded devices to supercomputers. It is built around a philosophy of modular design, "everything is a file," and small CLI tools that do one thing well.

---

## 1. Overview

Linux is not just an operating system, but an open-source movement and ecosystem. The core (the kernel) bridges hardware and software. A complete "Linux Distribution" bounds the kernel together with the GNU toolchain, a windowing system (like X11 or Wayland), user-space applications, and a package manager. It excels at multi-user environments, networking, servers, high-performance computing, and containerization.

---

## 2. Core Concepts

*   **"Everything is a File"**: Hardware devices (e.g., `/dev/sda`), processes (e.g., `/proc`), network sockets, and directories are all accessible and manipulable as files.
*   **Kernel vs. User Space**: The kernel runs with privileged access to hardware. Applications run in user space and must use System Calls to request hardware operations. 
*   **Small, Focused Tools**: Commands like `ls`, `grep`, and `awk` are designed to do a single job perfectly. By combining them with pipes (`|`), complex operations are possible.
*   **Processes and Signals**: Everything running is a process identified by a PID (Process ID). Processes can be managed and terminated by sending signals (e.g., via `kill`).

### Key Terms

| Term | Definition |
| ---- | ---------- |
| **Shell** | The command-line interpreter (e.g., Bash, Zsh) that takes user input and executes commands. |
| **Root** | The superuser account with unrestricted access to the entire system. |
| **Daemon** | A background process that runs continuously, typically managed by `systemd`. |
| **Package Manager** | Tool used to install, update, or remove software (e.g., `apt`, `dnf`, `pacman`). |
| **NUMA** | Non-Uniform Memory Access; an architecture where memory access time depends on memory location relative to the processor. |

---

## 3. Syntax / API / Command Reference

### File and Directory Operations

```bash
# Display working directory and list contents (including hidden files)
pwd
ls -la

# Create nested directories
mkdir -p /path/to/my/folder

# Recursively delete files and directories forcefully
rm -rf /path/to/folder

# Copy directories recursively
cp -r source/ destination/

# Find files by name in a specific path
find /var/log -name "*.log"

# Search for a pattern inside files recursively
grep -rn "error" /var/log/
```

### System Information and Process Management

```bash
# View active processes and resource usage (real-time)
htop    # Or top

# View disk space in human-readable format
df -h

# Check RAM usage
free -h

# Terminate process by PID or name
kill -9 <PID>
killall -9 <process_name>
```

### Networking

```bash
# Display IP addresses and interfaces
ip a

# Show listening ports and active network connections
netstat -tuln    # Or ss -tuln

# Download a file from the internet
wget https://example.com/file.tar.gz

# Secure copy between hosts
scp file.txt user@192.168.1.10:/tmp/
```

---

## 4. Patterns & Best Practices

### ✅ Do
*   **Use `sudo` instead of `root`:** Run commands as a standard user with elevated privileges only when explicitly needed.
*   **Embrace SSH Keys:** Disable password authentication for SSH (`PasswordAuthentication no` in `sshd_config`) and use key-based authentication.
*   **Use pipes (`|`) and redirection (`>`, `>>`) effectively:** Chains of commands are idiomatic in bash.
*   **Understand package management:** Always use `apt`, `yum`/`dnf`, or `pacman` instead of manually compiling from source, to avoid dependency hell.
*   **Read the docs:** Use `man <command>` or `command --help` to understand available flags.

### ❌ Don't
*   **Don't arbitrarily `chmod 777`:** Setting loose permissions to resolve an access error is a massive security risk. Understand Unix file groups and owners instead.
*   **Don't open unneeded ports:** Use `ufw`, `firewalld`, or `iptables` to block non-essential incoming traffic.
*   **Don't arbitrarily copy-paste commands from the internet:** Understand what the command does before you execute it, especially if it contains `sudo`, `curl | bash`, or `rm`.

---

## 5. Common Pitfalls & Gotchas

*   **Case Sensitivity:** Linux filesystems are entirely case-sensitive. `File.txt` and `file.txt` are two distinctly different files.
*   **No "C: Drive":** All storage drives are mounted into the unified file tree at specific mount points (e.g., `/mnt/data`), not assigned drive letters.
*   **Accidental Redirection Overwrite:** Standard redirection (`>`) overwrites files. To append, strictly use `>>`. Overwriting system configs accidentally is a common beginner mistake.
*   **"Permission Denied" Errors:** If you receive this, it means your current user lacks rights. Often solved by running `sudo !!` (which runs the last command with root privileges).
*   **Process signals:** Avoid using `kill -9` (`SIGKILL`) unless absolutely necessary, as it bypasses the application's clean-up routines. Prefer `kill -15` (`SIGTERM`).

---

## 6. Advanced Topics

### Kernel Tuning / sysctl
Many kernel parameters can be adjusted dynamically via `/proc/sys` using the `sysctl` command. For example, tweaking network stack buffers for high throughput or adjusting `vm.swappiness` (default 60) to control how aggressively the kernel swaps memory to disk.

### Performance Profiling
*   **eBPF / bpftrace**: Provides programmatic, incredibly low-overhead observability into the kernel without modifying source code or unloading modules.
*   **perf**: Statistical profiler for identifying CPU bottlenecks and tracing events at the hardware level.

### Cgroups (Control Groups)
Modern Linux resource management (often used via `systemd` or Docker). Allows bounding memory, CPU time, and block I/O per process tree to prevent the "noisy neighbor" problem.

---

## 7. Ecosystem & Tooling

| Tool / Concept | Purpose | When to use |
| -------------- | ------- | ----------- |
| **systemd** | Init system and service manager | Managing services, reading logs (`journalctl`), setting timers. |
| **tmux / screen** | Terminal multiplexer | Keeping terminal sessions alive when SSH drops, split-screen CLI. |
| **awk / sed** | Text processing tools | Extracting tabular data, performing inline multi-file text replacements. |
| **rsync** | Fast incremental file transfer | Backing up folders or keeping remote and local directories synchronized. |
| **iptables/nftables**| Packet filtering / Firewall | Configuring system-level network rules and NAT routing. |

---

## 8. Quick Reference Cheat Sheet

*   **Navigation:** `cd -` (go back to previous directory), `cd ~` (go to home dir)
*   **Ownership:** `chown user:group file.txt`
*   **Permissions:** 
    *   Read = 4, Write = 2, Execute = 1. 
    *   `chmod 755 index.sh` (Owner: rwx, Group: rx, Others: rx)
*   **Archiving:** `tar -czvf archive.tar.gz folder/` (compress) / `tar -xzvf archive.tar.gz` (extract)
*   **File Size Check:** `du -sh *` (size of all immediate folders/files)
*   **Background Jobs:** Run `command &` to spawn in background. Use `jobs` to list list, then `fg %1` to bring to foreground.

---

## 9. Worked Examples

### Example 1: Finding the 5 Largest Files in a Directory

```bash
# This pipeline uses 'find' to get all files
# xargs and du measure their sizes
# sort numerically orders them descending
# head grabs the top 5
find /var/log -type f -print0 | xargs -0 du -h | sort -hr | head -n 5
```

### Example 2: Safely Replacing Text in Multiple Files

```bash
# Using 'find' with 'sed' to perform an inline replacement.
# This finds all Python files and changes 'old_function' to 'new_function'.
# The '-i.bak' creates backups of the original files before overwriting.
find . -name "*.py" -exec sed -i.bak 's/old_function/new_function/g' {} +
```

### Example 3: Simple Bash Script with Argument Validation

```bash
#!/bin/bash
# A robust script to show idiomatic bash checking
set -e # Exit immediately if a pipeline returns non-zero

if [ "$#" -ne 1 ]; then
    echo "Usage: $0 <username>" >&2
    exit 1
fi

USER_TO_CHECK="$1"

if id "$USER_TO_CHECK" &>/dev/null; then
    echo "User $USER_TO_CHECK exists."
else
    echo "User $USER_TO_CHECK does not exist."
fi
```

---

## 10. Go Deeper — Resources

*   [Linux Documentation Project](https://tldp.org/)
*   [Brendan Gregg's Linux Performance Tools](https://www.brendangregg.com/linuxperf.html)
*   [Arch Linux Wiki (Excellent general Linux resource)](https://wiki.archlinux.org/)
*   [Explainshell.com (Visualizing shell commands)](https://explainshell.com/)
*   [GNU Coreutils Manual](https://www.gnu.org/software/coreutils/manual/)

---

_Generated by skill-learning. Reload this file to restore expert-level knowledge of linux._
_Source: web-research_
