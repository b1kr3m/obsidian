📅 **Date**: 23 February 25  
📂 **Category**: TryHackMe 
📝 **Status**: Completed  
🔒 **Room**: Authentication
🏷️ Tags: #tryhackme #tryhackme/learn 

---
# OAuth 2.0 Key Concepts
## Overview
OAuth 2.0 is an authorization framework that allows third- [ ]party applications to access user data without exposing user credentials. Understanding its core concepts is crucial for identifying vulnerabilities during penetration testing and bug hunting.

---
## Key Components of OAuth 2.0

### 1. **Resource Owner**
   - [ ] The entity (user or system) that owns the protected resources and can grant access to them.
   - [ ] Example: A user who grants a coffee shop app access to their account details.

### 2. **Client**
   - [ ] The application requesting access to the resource owner's data.
   - [ ] Example: The coffee shop's mobile app that requests access to the user's payment information.

### 3. **Authorization Server**
   - [ ] The server that authenticates the resource owner and issues access tokens to the client.
   - [ ] Example: The coffee shop's backend system that verifies user credentials and grants access tokens.

### 4. **Resource Server**
   - [ ] The server hosting the protected resources (e.g., user data) and validating access tokens.
   - [ ] Example: The coffee shop's database storing user account details and order history.

### 5. **Authorization Grant**
   - [ ] A credential representing the resource owner's consent for the client to access their data.
   - [ ] Types: Authorization Code, Implicit, Resource Owner Password Credentials, and Client Credentials.

### 6. **Access Token**
   - [ ] A short-lived credential used by the client to access protected resources on behalf of the resource owner.
   - [ ] Example: A token allowing the coffee shop app to place orders without re-authenticating the user.

### 7. **Refresh Token**
   - [ ] A long-lived credential used to obtain new access tokens without re-authenticating the user.
   - [ ] Example: A token used to refresh the session when the access token expires.

### 8. **Redirect URI**
   - [ ] The URL where the authorization server sends the user after granting or denying access.
   - [ ] Example: The callback URL where the coffee shop app receives the authorization code.

### 9. **Scope**
   - [ ] Defines the level of access the client requests (e.g., read-only, write access).
   - [ ] Example: The coffee shop app requesting access to view order history and make payments.

### 10. **State Parameter**
   - [ ] A CSRF protection mechanism that ensures the authorization response matches the original request.
   - [ ] Example: A unique value sent during the login process to prevent session hijacking.

---
## OAuth 2.0 Grant Types

### 1. **Authorization Code Grant**
   - [ ] Most secure and commonly used flow.
   - [ ] Steps:
     1. User is redirected to the authorization server.
     2. User authenticates and grants access.
     3. Authorization server issues an authorization code.
     4. Client exchanges the code for an access token.
   - [ ] Use Case: Server-side applications (e.g., PHP, Java, .NET).

### 2. **Implicit Grant**
   - [ ] Designed for public clients (e.g., single-page apps).
   - [ ] Steps:
     1. User is redirected to the authorization server.
     2. User authenticates and grants access.
     3. Authorization server issues an access token directly in the URL fragment.
   - [ ] Use Case: Mobile and web apps that cannot securely store secrets.
   - [ ] **Vulnerability**: Tokens are exposed in the URL, making them susceptible to theft.

### 3. **Resource Owner Password Credentials Grant**
   - [ ] Used for highly trusted clients.
   - [ ] Steps:
     1. User provides credentials directly to the client.
     2. Client sends credentials to the authorization server.
     3. Authorization server issues an access token.
   - [ ] Use Case: First-party applications (e.g., official mobile apps).
   - [ ] **Vulnerability**: User credentials are shared with the client, increasing security risks.

### 4. **Client Credentials Grant**
   - [ ] Used for server-to-server communication.
   - [ ] Steps:
     1. Client authenticates with the authorization server using its credentials.
     2. Authorization server issues an access token.
   - [ ] Use Case: BackEnd services and APIs.
---
## Common Vulnerabilities in OAuth 2.0

### 1. **Insecure Redirect URI**
   - [ ] Attackers can hijack tokens if the `redirect_uri` is not properly validated.
   - [ ] Exploit: Manipulate the `redirect_uri` to point to an attacker-controlled domain.
   - [ ] Mitigation: Enforce strict validation of registered redirect URIs.

### 2. **Weak or Missing State Parameter**
   - [ ] Lack of a state parameter makes the flow vulnerable to CSRF attacks.
   - [ ] Exploit: Attackers can trick users into authorizing malicious requests.
   - [ ] Mitigation: Always include and validate the state parameter.

### 3. **Token Leakage**
   - [ ] Tokens exposed in URLs or insecure storage (e.g., localStorage) can be stolen.
   - [ ] Exploit: Use XSS or man-in-the-middle attacks to intercept tokens.
   - [ ] Mitigation: Use secure storage mechanisms (e.g., HTTP-only cookies) and enforce HTTPS.

### 4. **Insufficient Token Expiry**
   - [ ] Long-lived tokens increase the risk of unauthorized access.
   - [ ] Exploit: Attackers can use stolen tokens indefinitely.
   - [ ] Mitigation: Implement short-lived access tokens and refresh tokens.

### 5. **Replay Attacks**
   - [ ] Attackers can reuse intercepted tokens to gain unauthorized access.
   - [ ] Exploit: Capture and replay valid tokens.
   - [ ] Mitigation: Use nonce values and timestamp checks.

---
## OAuth 2.1 Enhancements
OAuth 2.1 addresses several vulnerabilities in OAuth 2.0:
- [ ] **Deprecation of Implicit Grant**: Replaced with Authorization Code Flow with PKCE.
- [ ] **Mandatory State Parameter**: Ensures CSRF protection.
- [ ] **Secure Token Storage**: Recommends against storing tokens in browser storage.
- [ ] **Stricter Redirect URI Validation**: Prevents open redirect vulnerabilities.

---
## Key Takeaways for Bug Hunting
1. **Check Redirect URI Validation**: Ensure the `redirect_uri` is properly validated and matches registered URIs.
2. **Verify State Parameter Usage**: Confirm the state parameter is present and validated.
3. **Inspect Token Storage**: Look for insecure storage practices (e.g., localStorage).
4. **Test Token Expiry**: Verify tokens have short lifespans and refresh tokens are used.
5. **Look for CSRF Vulnerabilities**: Ensure the state parameter is used to prevent CSRF attacks.
6. **Identify Misconfigured Scopes**: Ensure clients request only necessary permissions.

By understanding these concepts and vulnerabilities, you can effectively identify and exploit weaknesses in OAuth 2.0 implementations during penetration testing and bug hunting.