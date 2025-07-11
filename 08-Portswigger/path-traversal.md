📅 **Date**: 11 July 25  
📂 **Category**: Web Security
🔒 **Topic**: Path Traversal
🏷️ Tags: #websecurity #websecurity/path0traversal

---
# Path Traversal

**Source**: https://portswigger.net/web-security/file-path-traversal

## Overview

Path traversal vulnerabilities enable attackers to read arbitrary files on the server running an application. In some cases, attackers can also write to arbitrary files, potentially leading to full server compromise.

## What Can Be Accessed

- **Application code and data**
- **Credentials for back-end systems**
- **Sensitive operating system files**
- **Configuration files**

## Basic Attack Mechanism

### Example Vulnerable Application

```html
<img src="/loadImage?filename=218.png">
```

**File Path Construction:**

- Base directory: `/var/www/images/`
- User input: `218.png`
- Final path: `/var/www/images/218.png`

### Basic Attack Payload

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

**Result:** Application reads `/etc/passwd` instead of intended image file

### Directory Traversal Sequences

- **Unix/Linux**: `../` (step up one directory)
- **Windows**: `../` and `..\` (both valid)

## Common Bypass Techniques

### 1. Absolute Path Bypass

**When**: Application blocks directory traversal sequences **Technique**: Use absolute paths from filesystem root

```
filename=/etc/passwd
```

### 2. Nested Traversal Sequences

**When**: Application strips traversal sequences non-recursively **Technique**: Use nested sequences that revert after stripping

```
....//
....\/
```

### 3. URL Encoding Bypass

**When**: Web servers strip traversal sequences before passing to application **Techniques**:

- Single URL encoding: `%2e%2e%2f`
- Double URL encoding: `%252e%252e%252f`
- Non-standard encodings: `..%c0%af` or `..%ef%bc%8f`

### 4. Required Base Folder Bypass

**When**: Application requires filename to start with expected base folder **Technique**: Include required base folder followed by traversal

```
filename=/var/www/images/../../../etc/passwd
```

### 5. File Extension Validation Bypass

**When**: Application requires specific file extension **Technique**: Use null byte to terminate path before extension

```
filename=../../../etc/passwd%00.png
```

## Lab Practice Summary

| Lab Level    | Technique            | Description                 |
| ------------ | -------------------- | --------------------------- |
| Apprentice   | Simple case          | Basic `../` traversal       |
| Practitioner | Absolute path        | Bypass with `/etc/passwd`   |
| Practitioner | Nested sequences     | Use `....//` patterns       |
| Practitioner | URL encoding         | Double encoding bypass      |
| Practitioner | Path validation      | Required base folder bypass |
| Practitioner | Extension validation | Null byte bypass            |

## Prevention Strategies

### Primary Defense

**Avoid passing user-supplied input to filesystem APIs altogether**

### Two-Layer Defense Approach

#### Layer 1: Input Validation

- **Whitelist approach**: Compare input against permitted values
- **Content validation**: Verify input contains only permitted characters (alphanumeric)

#### Layer 2: Canonical Path Verification

- Append input to base directory
- Use platform filesystem API to canonicalize path
- Verify canonicalized path starts with expected base directory

### Java Prevention Example

```java
File file = new File(BASE_DIRECTORY, userInput);
if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) {
    // process file
}
```

## Key Takeaways

1. **Path traversal** can lead to sensitive file disclosure and potential full server compromise
2. **Multiple bypass techniques** exist for common defensive measures
3. **Defense in depth** is crucial - use both input validation and canonical path verification
4. **Burp Suite Professional** includes predefined payload lists for testing
5. **Platform differences** exist (Unix vs Windows traversal sequences)

## Tools & Resources

- **Burp Intruder**: Predefined payload list "Fuzzing - path traversal"
- **Manual testing**: Various encoding techniques
- **Target files**: `/etc/passwd` (Unix), `\windows\win.ini` (Windows)

---