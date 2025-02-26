📅 **Date**: 23 February 25  
📂 **Category**: TryHackMe 
📝 **Status**: Completed  
🔒 **Room**: Session-Management
🏷️ Tags: #tryhackme #tryhackme/learn

---
## Session Management

### Overview

- [ ] **Session Management**: Tracks user interactions in a stateless HTTP protocol.
- [ ] **Purpose**: Ensures secure user sessions after authentication.
- [ ] **Key Focus**: Securing session creation, tracking, expiry, and termination.

---

### Session Management Lifecycle

#### 1. Session Creation
- [ ] **When**: Often created before authentication (e.g., tracking pre-login actions).
- [ ] **Post-Authentication**: Session value is generated and sent with each request.
- [ ] **Security Concerns**:
    - [ ] **Weak or guessable session values**:
        - [ ] Example: A session ID like `user123` is easily guessable.
        - [ ] Secure alternative: Use a randomly generated UUID like `a1b2c3d4-e5f6-7890-g1h2-i3j4k5l6m7n8`.
    - [ ] **Session fixation**:
        - [ ] Example: An attacker sends a victim a link with a pre-set session ID (`?sessionid=attacker123`). If the app doesn’t regenerate the session ID after login, the attacker can hijack the session.
    - [ ] **Insecure transmission**:
        - [ ] Example: Session data sent over HTTP instead of HTTPS, allowing interception.
		
---

#### 2. Session Tracking

- [ ] **How**: Session value is submitted with each request to identify the user.
- [ ] **Purpose**: Enables authorization checks and user action tracking.
- [ ] **Security Concerns**:
    - [ ] **Authorization bypass**:
        - [ ] **Vertical bypass**: A regular user accesses admin functionality.
            - [ ] Example: A user changes the URL from `/user/dashboard` to `/admin/dashboard` and gains admin access.
        - [ ] **Horizontal bypass**: A user accesses another user’s data.
            - [ ] Example: A user changes the URL from `/profile?id=123` to `/profile?id=456` and views another user’s profile.
    - [ ] **Insufficient logging**:
        - [ ] Example: An app logs only failed login attempts but not successful ones, making it hard to detect session hijacking.

---

#### 3. Session Expiry
- [ ] **Why**: HTTP is stateless; sessions must expire to prevent indefinite access.
- [ ] **Best Practices**:
    - [ ] Set reasonable session lifetimes.
        - [ ] Example: Banking apps may expire sessions after 15 minutes of inactivity, while webmail clients may allow longer sessions.
    - [ ] Terminate sessions if location or behaviour changes.
        - [ ] Example: A session created in the US is used from a different country, triggering termination.

---

#### 4. Session Termination
- [ ] **When**: User logs out or session expires.
- [ ] **Best Practices**:
    - [ ] Invalidate sessions server-side upon logout.
        - [ ] Example: When a user logs out, the server removes the session from its active sessions list.
    - [ ] Terminate all sessions during password resets.
        - [ ] Example: After a password reset, all active sessions for the user are invalidated to prevent unauthorized access.
    - [ ] Use blocklists for token-based sessions.
        - [ ] Example: A JWT token is added to a blocklist after logout to prevent reuse.

---
### IAAA Model in Session Management

#### 1. Identification
- [ ] **What**: User claims an identity (e.g., username or email).
- [ ] **Example**: A user enters `john.doe@example.com` as their username.

#### 2. Authentication
- [ ] **What**: Verify the user’s identity (e.g., password).
- [ ] **Example**: The user provides the correct password for `john.doe@example.com`.
    
#### 3. Authorization
- [ ] **What**: Ensure the user has permission to perform an action.
- [ ] **Example**: A user with the role `student` cannot access the `/admin/dashboard` endpoint.

#### 4. Accountability
- [ ] **What**: Log user actions tied to their session.
- [ ] **Example**: Logging every request with the session ID, user ID, and timestamp for audit purposes.

---
### Session Management Methods
#### 1. Cookie-Based Sessions
- [ ] **How**: Browser stores session cookies (`Set-Cookie` header).
- [ ] **Example**:
    http
    Copy
    Set-Cookie: sessionid=a1b2c3d4; Secure; HttpOnly; SameSite=Strict; Expires=Wed, 21 Oct 2023 07:28:00 GMT
- [ ] **Pros**:
    - [ ] Automatically sent by the browser.
    - [ ] Built-in security features.
- [ ] **Cons**:
    - [ ] Vulnerable to CSRF.
    - [ ] Limited to specific domains.
#### 2. Token-Based Sessions
- [ ] **How**: Tokens (e.g., JWTs) stored in LocalStorage and sent via headers.
- [ ] **Example**:
    http
    Copy
    Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
- [ ] **Pros**:
    - [ ] Works well in decentralized apps.
    - [ ] Blocks CSRF (not automatically sent).
- [ ] **Cons**:
    - [ ] Requires client-side JavaScript for management.
    - [ ] No built-in security protections.

---
### Common Vulnerabilities

#### 1. Session Creation
- [ ] **Weak or predictable session values**:
    - [ ] Example: A session ID like `12345` is easily guessable.
- [ ] **Session fixation**:
    - [ ] Example: An attacker sends a victim a link with a pre-set session ID (`?sessionid=attacker123`).
- [ ] **Insecure transmission**:
    - [ ] Example: Session data sent over HTTP instead of HTTPS.

#### 2. Session Tracking
- [ ] **Authorization bypass**:
    - [ ] Example: A user accesses `/admin/dashboard` without admin privileges.
- [ ] **Insufficient logging**:
    - [ ] Example: Only failed login attempts are logged, not successful ones.

#### 3. Session Expiry
- [ ] **Excessive session lifetimes**:
    - [ ] Example: A session remains active for 30 days, increasing the risk of hijacking.
#### 4. Session Termination
- [ ] **Sessions not invalidated server-side**:
    - [ ] Example: A user logs out, but their session remains active on the server.

---

### Defenses

#### 1. Secure Session Values
- [ ] Use random, non-guessable session values.
    - [ ] Example: Generate session IDs using cryptographic libraries.

#### 2. Authorization Checks

- [ ] Validate user permissions for each action.
    - [ ] Example: Use role-based access control (RBAC) to restrict access.
        
#### 3. Session Expiry
- [ ] Set reasonable session lifetimes.
    - [ ] Example: Expire sessions after 15 minutes of inactivity for banking apps.

#### 4. Session Termination**
- [ ] Invalidate sessions server-side on logout.
    - [ ] Example: Remove the session from the server’s active sessions list.
#### 5. Logging
- [ ] Log all actions tied to sessions.
    - [ ] Example: Log session ID, user ID, timestamp, and action performed.

---

### Exploitation Scenarios

#### 1. Excessive Session Lifetimes
- [ ] Example: A session remains active for 30 days, allowing an attacker to hijack it.
#### 2. Authorization Bypass
- [ ] Example: A student accesses lecturer information by manipulating the URL.

---
### Key Takeaways

- [ ] Secure the entire session lifecycle (creation, tracking, expiry, termination).
- [ ] Use strong session values and enforce proper authorization.
- [ ] Implement logging for accountability.
- [ ] Regularly review and test session management mechanisms.
