# SSH Authorized Keys Persistence

## Overview

SSH authorized keys persistence is a technique where an attacker maintains long-term access to a Linux system by adding or modifying public SSH keys in a user’s `authorized_keys` file.

This allows passwordless authentication using SSH key pairs, enabling stealthy and reliable re-entry into a compromised system.

It is one of the most common persistence methods observed in server and cloud environment compromises.

---

## How It Works

SSH uses public-key authentication as an alternative to passwords.

When a user attempts to log in via SSH:

* The server checks the user’s `~/.ssh/authorized_keys` file
* If the client’s private key matches an entry, access is granted
* No password is required

Persistence is achieved by adding an attacker-controlled public key into this file.

---

## Common File Location

### User SSH Directory

```text id="ssh1"
~/.ssh/authorized_keys
```

This file stores all trusted public keys for a user account.

---

### Permissions Requirements

SSH enforces strict permissions:

```text id="ssh2"
~/.ssh/         → 700
authorized_keys → 600
```

Improper permissions may cause SSH authentication to fail.

---

## SSH Authentication Flow

```text id="sshflow"
SSH Client Request
        │
        ▼
SSH Server Receives Connection
        │
        ▼
Checks ~/.ssh/authorized_keys
        │
        ▼
Compares Public Key
        │
        ▼
If Match → Authentication Granted
```

---

## Example Scenario (Key-Based Access Concept)

A hacker generates an SSH key pair:

* Private key (kept locally)
* Public key (added to server)

Generate SSH key pair in his own machine:

```bash
ssh-keygen -t rsa -b 4096
```

This creates:

```text
~/.ssh/id_rsa        (Private key - kept locally)
~/.ssh/id_rsa.pub    (Public key - shared to server)
```

Copy public key to the server:

```bash
cat ~/.ssh/id_rsa.pub | ssh user@server-ip "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

The public key is stored in:

```text
~/.ssh/authorized_keys
```

Once the public key is installed on the server, the hacker can connect like this:

```bash
ssh user@server-ip
```

---

## Related MITRE ATT&CK Technique

**T1098 – Account Manipulation**

---

## Summary

SSH authorized keys persistence is a highly effective technique that leverages built-in Linux authentication mechanisms. By modifying a user’s authorized keys file, attackers can maintain persistent and stealthy access to compromised systems without relying on passwords or additional services.