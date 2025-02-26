
📅 **Date**: 23 February 25  
📂 **Category**: TryHackMe 
📝 **Status**: Completed  
🔒 **Room**: JWT Security
🏷️ Tags: #tryhackme #tryhackme/learn

---
## JWT Security

#### Overview
- [ ] **JWT (JSON Web Token)**: A compact, URL-safe token format used for securely transmitting information between parties as a JSON object.
- [ ] **Purpose**: Commonly used for authentication and authorization in APIs and stateless applications.
- [ ] **Key Focus**: Securing JWT creation, transmission, and validation to prevent common vulnerabilities.

---
### JWT Structure

#### Components of a JWT
- [ ] **Header**:
    - [ ] Contains metadata about the token (e.g., algorithm used for signing).
    - [ ] Example:
```json
	{
	  "alg": "HS256",
	  "typ": "JWT"
	}
```  
- [ ] **Payload**:
    - [ ] Contains claims (e.g., user information, permissions).
    - [ ] Example:
```json
        {
          "username": "user",
          "admin": 0,
          "exp": 1698765432
        }
```
- [ ] **Signature**:
    - [ ] Ensures the token's integrity by signing the header and payload with a secret or private key.
    - [ ] Example: `HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)`

---
### Common JWT Vulnerabilities

#### 1. **Sensitive Information Disclosure
- [ ] **Issue**: Sensitive data (e.g., passwords, internal IPs) exposed in the payload.
- [ ] **Example**:
```json
    {
      "username": "user",
      "password": "password123",
      "admin": 0
    }
```
- [ ] **Fix**: Avoid storing sensitive data in the payload. Use server-side storage instead.

---
#### 2. No Signature Verification
- [ ] **Issue**: Server fails to verify the JWT signature, allowing forged tokens.
- [ ] **Example**:
    - [ ] Original JWT: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MH0.abc123`
    - [ ] Forged JWT: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InVzZXIiLCJhZG1pbiI6MX0.abc123`
- [ ] **Fix**: Always verify the signature using the correct secret or public key.

---
#### 3. Algorithm Downgrade to "None"
- [ ] **Issue**: Attacker changes the `alg` field to `none`, bypassing signature verification.
- [ ] **Example**:
    - [ ] Original Header:
	```json
	        {
	          "alg": "HS256",
	          "typ": "JWT"
	        }
	```
    - [ ] Modified Header:
```json
        {
          "alg": "none",
          "typ": "JWT"
        }
```
- [ ] **Fix**: Reject tokens with the `none` algorithm.

---
#### 4. Weak Symmetric Secrets
- [ ] **Issue**: Weak secrets used for signing JWTs can be brute-forced.
- [ ] **Example**:
    - [ ] Weak Secret: `secret123`
    - [ ] Strong Secret: `a1b2c3d4-e5f6-7890-g1h2-i3j4k5l6m7n8`
- [ ] **Fix**: Use strong, random secrets for signing JWTs.

---
#### 5. Signature Algorithm Confusion
- [ ] **Issue**: Attacker downgrades from asymmetric (e.g., RS256) to symmetric (e.g., HS256) and uses the public key as the secret.
- [ ] **Example**:
    - [ ] Original Header:
	```json
	        {
	          "alg": "RS256",
	          "typ": "JWT"
	        }
	```
    - [ ] Modified Header:
```json
        {
          "alg": "HS256",
          "typ": "JWT"
        }
```
- [ ] **Fix**: Use separate keys for symmetric and asymmetric algorithms.

---
#### 6. Token Lifetime Issues
- [ ] **Issue**: Tokens with no expiration (`exp` claim) or excessively long lifetimes.
- [ ] **Example**:
```json
    {
      "username": "user",
      "admin": 0
    }
```
- [ ] **Fix**: Always set an expiration time (`exp` claim) for JWTs.

---
### Practical Exploitation Scenarios

#### 1. Sensitive Information Disclosure
- [ ] **Steps**:
    1. Authenticate to the API and retrieve the JWT.
    2. Decode the JWT payload to extract sensitive information.
- [ ] **Example**:
    - [ ] Decoded Payload:
```json
        
        {
          "username": "user",
          "password": "password123",
          "admin": 0
        }
```

---
#### 2. No Signature Verification
- [ ] **Steps**:
    1. Authenticate to the API and retrieve the JWT.
    2. Modify the payload (e.g., set `admin` to `1`).
    3. Submit the forged JWT without re-signing.
- [ ] **Example**:
    - [ ] Forged Payload:
```json
        {
          "username": "user",
          "admin": 1
        }
```

---
#### 3. Algorithm Downgrade to "None"
- [ ] **Steps**:
    1. Authenticate to the API and retrieve the JWT.
    2. Change the `alg` field to `none`.
    3. Submit the modified JWT.
- [ ] **Example**:
    - [ ] Modified Header:
```json
        {
          "alg": "none",
          "typ": "JWT"
        }
```

---
#### 4. Weak Symmetric Secrets
- [ ] **Steps**:
    1. Retrieve the JWT.
    2. Use tools like Hashcat to bruteforce the secret.
    3. Forge a new JWT with the cracked secret.
- [ ] **Example**:
    - [ ] Hashcat Command:
```bash
        hashcat -m 16500 jwt.txt jwt.secrets.list
```

---
#### 5. Signature Algorithm Confusion
- [ ] **Steps**:
    1. Retrieve the JWT and public key.
    2. Downgrade the algorithm to `HS256`.
    3. Use the public key as the secret to sign the JWT.
- [ ] **Example**:
    - [ ] Python Script:
```python
        import jwt
        
        public_key = "ADD_KEY_HERE"
        payload = {"username": "user", "admin": 1}
        token = jwt.encode(payload, public_key, algorithm="HS256")
        print(token)
```
        
---
### Defenses
#### 1. Secure Payloads
- [ ] Avoid storing sensitive data in the payload.
- [ ] Example:
```json
    {
      "username": "user",
      "admin": 0,
      "exp": 1698765432
    }
```
#### 2. Signature Verification
- [ ] Always verify the JWT signature using the correct secret or public key.
- [ ] Example:
    payload = jwt.decode(token, secret, algorithms=["HS256"])
#### 3. Reject "None" Algorithm
- [ ] Reject JWTs with the `none` algorithm.
- [ ] Example:
```python
    if header["alg"] == "none":
        raise Exception("Invalid algorithm")
```

#### 4. Use Strong Secrets
- [ ] Use strong, random secrets for signing JWTs.
- [ ] Example:
```python
    secret = "a1b2c3d4-e5f6-7890-g1h2-i3j4k5l6m7n8"
```
    

#### 5. Set Token Expiration
- [ ] Always set an expiration time (`exp` claim) for JWTs.
- [ ] Example:
``` python
payload = {
"username": "user",
"admin": 0,
"exp": datetime.datetime.now() + datetime.timedelta(minutes=5)
}
   ``` 

---
### Key Takeaways
- [ ] Secure the entire JWT lifecycle (creation, transmission, validation).
- [ ] Avoid exposing sensitive data in the payload.
- [ ] Always verify the JWT signature and reject invalid tokens.
- [ ] Use strong secrets and set appropriate token lifetimes.
