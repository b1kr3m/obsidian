📅 **Date**: 13 July 25  
📂 **Category**: Web Security
🔒 **Topic**: Authentication
🏷️ Tags: #websecurity #websecurity/authentication 

---
# Authentication Vulnerabilities
![[../06-Assets/Pasted image 20250713205608.png]]
## What is Authentication?
- Process of verifying identity (who you are).
    
- [ ] Types of authentication:
    - **Something you know** → Passwords, PINs
    - **Something you have** → Tokens, OTP devices
    - **Something you are/do** → Biometrics, keystroke patterns

🔄 **Authentication ≠ Authorization**
- **Authentication:** Confirms identity.
- **Authorization:** Grants permission after identity is verified.

## ⚠️ Why Authentication Fails

1. **Weak brute-force protection**
2. **Logic flaws or insecure implementation**
3. Poor error handling or inconsistent responses
4. Lack of multi-layer security (e.g. no MFA)
## 💥 Impact of Authentication Vulnerabilities

- Account takeover (ATO)
- Unauthorized access to internal/admin panels
- Sensitive data theft
- Abuse of business logic and privilege escalation
- Initial foothold for deeper attacks
---
## 🧱 Common Vulnerabilities
### 🔐 Password-Based Login
#### 🚨 Brute-Force Attacks

- Repeated login attempts using automation and wordlists
- Improved by:
    - Public data (email formats, user leaks)
    - Guessing logic: `Username * Weak Password`
#### 🧠 My Brute-Force Strategy:
```txt
→ Used Intruder with two payload positions: usernames & passwords  
→ X-Forwarded-For header rotation to bypass IP blocks  
→ Observed login response body and status codes for success patterns  
```
#### 🔍 Username Enumeration
- Identify valid usernames via:
    - Status code difference
    - Error message variation
    - Response timing deviation
	
```txt
→ Sent invalid and valid usernames with fixed password  
→ Compared HTTP status, messages, and timing (e.g. 401 vs 200, or delays)  
→ Used Burp's Response Analysis tab to detect subtle differences  
```
### 🛡️ Flawed Brute-Force Protection

- **IP-based Lockout** can be bypassed:
    - By rotating IP headers
    - Logging in with own account between attempts to reset counters
- **Credential Stuffing** isn't stopped by account locking

```txt
→ Inserted my valid credentials every 3 attempts to reset IP ban  
→ Reused creds from real-world leaks (rockyou.txt combos)  
→ Tested if the block was per-user or per-IP  
```

---
### 🚷 Account Locking / Rate Limiting

- Account locking = user-specific brute-force protection
- Rate limiting = blocks users/IPs based on request volume

Weaknesses:
- Predictable account lock thresholds
- Can be abused for Denial of Service (DoS)
- Headers (X-Forwarded-For) may bypass rate limits
#### 🧠 My Bypass Technique:
```text
→ Manipulated headers: X-Forwarded-For and True-Client-IP  
→ Used parallel credentials in one request to beat per-request limit  
→ Focused on password spraying to avoid account lock  
```
### 🌐 HTTP Basic Authentication

- Auth credentials sent in each request:
```sql
Authorization: Basic base64(username:password)
```

Vulnerable due to:
- No brute-force protection
- No CSRF defense
- Credentials exposed if no HTTPS or HSTS

🧠 **My Approach:**
```
→ Decoded Base64 header and tested login with curl  
→ Used hydra and wfuzz for brute-force on protected endpoints  
→ Checked reuse of creds in other endpoints or services  
```

## ✅ Developer Defense Checklist

- 🔒 Enforce **rate limits** and **IP monitoring**
- ❌ Avoid detailed error messages
- 🔁 Rotate session tokens post-login
- 🔐 Implement **MFA**
- 🚨 Use CAPTCHA on repeated login failures
- 📜 Log and alert on suspicious activity
- ⛔ Never allow unlimited login attempts

---
## 🧠 Final Takeaways

- Most real-world ATOs begin with enumeration or weak brute-force defense
- Minor differences in app logic reveal **major vulnerabilities**
- Always test login endpoints both **horizontally** (different accounts) and **vertically** (same account, different methods)

