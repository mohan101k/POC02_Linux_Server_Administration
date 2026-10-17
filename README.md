# POC 02 — Linux Server Administration Lab

## Overview

This hands-on lab uses an Amazon EC2 instance running **Amazon Linux 2023** to practise basic Linux server administration. The work included identifying the operating system and kernel, exploring files and directories, managing the Nginx service with `systemctl`, editing the web server's HTML page, and checking the result in a browser.

The purpose of this POC is to become comfortable with the Linux terminal and understand how common server-administration tasks are performed on a cloud virtual machine.

## Objectives

- Identify the Linux distribution and kernel running on the EC2 instance.
- Explore the filesystem and list directory contents.
- Understand how to manage a Linux service using `systemctl`.
- Start the Nginx web server.
- Edit the HTML file served by Nginx.
- Verify the change from a browser and troubleshoot basic issues.

## Environment

| Item | Details |
|---|---|
| Cloud platform | Amazon Web Services (AWS) |
| Compute | Amazon EC2 instance |
| Operating system | Amazon Linux 2023 |
| Web server | Nginx |
| Service manager | systemd (`systemctl`) |
| Access method | EC2 Instance Connect / SSH, depending on the session |

> Replace any environment details with the values from your own lab if they differ. Do not publish AWS account IDs, SSH private keys, credentials, or other secrets.

## Architecture / workflow

```text
Administrator
     |
     | EC2 Instance Connect or SSH
     v
Amazon EC2 — Amazon Linux 2023
     |
     | systemctl manages the service
     v
Nginx web server
     |
     | serves the document-root HTML file
     v
Browser — http://<EC2-PUBLIC-IP>
```

The EC2 instance is the Linux machine. Nginx is a separate service running on that machine; it is **not** running inside Docker in this lab workflow. Docker/container concepts were explored separately in POC 01.

## Practical steps and commands

### 1. Identify the operating system

```bash
cat /etc/os-release
```

**What it does:** Displays distribution information, such as the operating-system name and version. The lab showed Amazon Linux 2023.

### 2. Check kernel and system information

```bash
uname -a
```

**What it does:** Prints the kernel name, host information, kernel release, architecture, and related system details.

### 3. Explore the current directory

```bash
pwd
ls -la
ls -la | head -5
```

**What each command does:**
- `pwd` prints the current working directory.
- `ls -la` lists directory contents, including hidden entries, with detailed information.
- `|` sends the first command's output to the next command.
- `head -5` displays the first five lines of that output.

**Learning note:** A relative path such as `./file.txt` refers to a file from the current directory. If a command returns “No such file or directory,” check `pwd`, the spelling, the path, and whether the file exists.

### 4. Start the Nginx service

```bash
sudo systemctl start nginx
```

**What it does:** Requests systemd to start the Nginx service. `sudo` runs the command with elevated privileges; `systemctl` manages systemd services.

To inspect the service state when troubleshooting, use:

```bash
sudo systemctl status nginx
```

**Expected check:** Look for `active (running)` if Nginx started successfully. If the command reports an error, read the status output before trying more changes.

### 5. Edit the Nginx HTML page

For the default Amazon Linux Nginx package layout used in this lab, the usual document-root file is `/usr/share/nginx/html/index.html`. Confirm the location on your own instance before editing.

```bash
sudo vi /usr/share/nginx/html/index.html
```

Basic `vi` workflow:
1. Press `i` to enter insert mode.
2. Edit the HTML.
3. Press `Esc` to leave insert mode.
4. Type `:wq` and press `Enter` to save and exit.

To display the file content without editing it:

```bash
cat /usr/share/nginx/html/index.html
```

### 6. Verify the web page

Open the instance's current public IPv4 address in a browser:

```text
http://<EC2-PUBLIC-IP>
```

Use the current public IP shown in the EC2 console; public IPs can change when an instance is stopped and started unless an Elastic IP is used.

The instance's Security Group must allow inbound HTTP traffic on TCP port `80` from the intended source. Nginx must also be running and listening on the expected port.

The updated HTML was checked in the browser during the lab. For a static HTML file, Nginx usually serves the changed file without needing a service restart. Restarting the service is not normally required solely because the HTML changed.

## Command quick reference

| Command | Meaning |
|---|---|
| `cat /etc/os-release` | Show Linux distribution and version details |
| `uname -a` | Show kernel and system information |
| `pwd` | Print the current working directory |
| `ls -la` | List files, hidden entries, and details |
| `ls -la \| head -5` | Show the first five lines of the detailed listing |
| `sudo systemctl start nginx` | Start the Nginx service |
| `sudo systemctl status nginx` | Check the Nginx service status |
| `sudo vi /usr/share/nginx/html/index.html` | Edit the HTML served by Nginx, if this is the active document root |
| `cat /usr/share/nginx/html/index.html` | Display the HTML file content |

## Troubleshooting checklist

### Nginx page does not load

1. Confirm that you are using the instance's current public IP address.
2. Check that the EC2 instance is running.
3. Confirm the Security Group allows inbound TCP port `80` from the intended source.
4. Check Nginx service state:

   ```bash
   sudo systemctl status nginx
   ```

5. If the service is not running, inspect the status output and then try starting it:

   ```bash
   sudo systemctl start nginx
   ```

### The default Nginx page still appears

1. Confirm that you edited the correct `index.html` file.
2. Display the file content:

   ```bash
   cat /usr/share/nginx/html/index.html
   ```

3. Refresh the browser. If the page appears unchanged, verify the active document root and rule out browser caching.

### “No such file or directory” appears

- Run `pwd` to check the current directory.
- Check the spelling and full path.
- Use `ls -la` to inspect the current directory.
- For absolute paths, confirm that the file really exists at that location.

## What I learned

- How to identify an Amazon Linux system and view kernel information.
- How to inspect directories and understand command output.
- How Linux services are controlled through systemd and `systemctl`.
- How Nginx serves an HTML file from its document root.
- How to make a simple content change and validate it from a browser.
- How to start troubleshooting by checking paths, service status, IP addresses, and security rules.

## Scope and limitations

This was a foundational Linux administration lab, not a production-ready web architecture. It used a single EC2 instance and a manually edited static HTML page. Automated configuration management, CI/CD, HTTPS, monitoring/alerting, and high availability were not implemented as part of this POC.

## Suggested next improvements

- Practise Linux users, groups, permissions, and ownership.
- Inspect service logs with `journalctl` when a service fails.
- Learn process, disk, and memory troubleshooting commands.
- Configure HTTPS and monitoring in a separate, documented POC.
- Automate repeatable setup after understanding the manual steps.

## Evidence to add to this repository

Consider adding screenshots (with account IDs or other sensitive details hidden) showing:

- Amazon Linux version output.
- Nginx service status.
- The updated HTML file.
- The custom page in the browser.

## Interview summary

> I practised basic Linux server administration on an Amazon Linux 2023 EC2 instance. I checked the OS and kernel information, explored the filesystem, managed the Nginx service with `systemctl`, edited its HTML document, and verified the page from a browser. This exercise helped me connect Linux commands, service management, web-server document roots, and basic network troubleshooting.
