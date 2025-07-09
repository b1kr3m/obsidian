📅 **Date**: 29 April 25  
📂 **Category**: Web Security
🔒 **Topic**: Race Condition
🏷️ Tags: #websecurity #websecurity/racecondition 

---
# Race Condition
## Limit overrun race conditions
The most well-known type of race condition enables you to exceed some kind of limit imposed by the business logic of the application.

For example, consider an online store that lets you enter a promotional code during checkout to get a one-time discount on your order. To apply this discount, the application may perform the following high-level steps:

1. Check that you haven't already used this code.
2. Apply the discount to the order total.
3. Update the record in the database to reflect the fact that you've now used this code.

## Limit overrun race conditions - Continued

If you later attempt to reuse this code, the initial checks performed at the start of the process should prevent you from doing this:

![[../06-Assets/Pasted image 20250430000250.png]]

Now consider what would happen if a user who has never applied this discount code before tried to apply it twice at almost exactly the same time:

![[../06-Assets/Pasted image 20250430000329.png]]

As you can see, the application transitions through a temporary sub-state; that is, a state that it enters and then exits again before request processing is complete. In this case, the sub-state begins when the server starts processing the first request, and ends when it updates the database to indicate that you've already used this code. This introduces a small race window during which you can repeatedly claim the discount as many times as you like.
## Limit overrun race conditions - Continued

There are many variations of this kind of attack, including:

- Redeeming a gift card multiple times
- Rating a product multiple times
- Withdrawing or transferring cash in excess of your account balance
- Reusing a single CAPTCHA solution
- Bypassing an anti-brute-force rate limit

Limit overruns are a subtype of so-called "time-of-check to time-of-use" (TOCTOU) flaws. Later in this topic, we'll look at some examples of race condition vulnerabilities that don't fall into either of these categories.
## Detecting and exploiting limit overrun race conditions with Burp Repeater

The process of detecting and exploiting limit overrun race conditions is relatively simple. In high-level terms, all you need to do is:

1. Identify a single-use or rate-limited endpoint that has some kind of security impact or other useful purpose.
2. Issue multiple requests to this endpoint in quick succession to see if you can overrun this limit.

The primary challenge is timing the requests so that at least two race windows line up, causing a collision. This window is often just milliseconds and can be even shorter.

Even if you send all of the requests at exactly the same time, in practice there are various uncontrollable and unpredictable external factors that affect when the server processes each request and in which order.
![[../06-Assets/Pasted image 20250430000517.png]]
## Detecting and exploiting limit overrun race conditions with Burp Repeater - Continued

Burp Suite 2023.9 adds powerful new capabilities to Burp Repeater that enable you to easily send a group of parallel requests in a way that greatly reduces the impact of one of these factors, namely network jitter. Burp automatically adjusts the technique it uses to suit the HTTP version supported by the server:

- For HTTP/1, it uses the classic last-byte synchronization technique.
- For HTTP/2, it uses the single-packet attack technique, first demonstrated by PortSwigger Research at Black Hat USA 2023.

The single-packet attack enables you to completely neutralize interference from network jitter by using a single TCP packet to complete 20-30 requests simultaneously.

![[../06-Assets/Pasted image 20250430001337.png]]

Although you can often use just two requests to trigger an exploit, sending a large number of requests like this helps to mitigate internal latency, also known as server-side jitter. This is especially useful during the initial discovery phase. We'll cover this methodology in more detail.

> Lab: Limit overrun race conditions
## Detecting and exploiting limit overrun race conditions with Turbo Intruder

In addition to providing native support for the single-packet attack in Burp Repeater, we've also enhanced the `Turbo Intruder` extension to support this technique. You can download the latest version from the BApp Store.

Turbo Intruder requires some proficiency in Python, but is suited to more complex attacks, such as ones that require multiple retries, staggered request timing, or an extremely large number of requests.

## Detecting and exploiting limit overrun race conditions with Turbo Intruder - Continued

To use the single-packet attack in Turbo Intruder:

1. Ensure that the target supports HTTP/2. The single-packet attack is incompatible with HTTP/1.
2. Set the `engine=Engine.BURP2` and `concurrentConnections=1` configuration options for the request engine.
3. When queueing your requests, group them by assigning them to a named gate using the `gate` argument for the `engine.queue()` method.
4. To send all of the requests in a given group, open the respective gate with the `engine.openGate()` method.
```python
def queueRequests(target, wordlists):
	engine = RequestEngine(endpoint=target.endpoint, 
							concurrentConnections=1, 
							engine=Engine.BURP2 )
# queue 20 requests in gate '1' for i in range(20): engine.queue(target.req, gate='1')
# send all requests in gate '1' in parallel engine.openGate('1')
```

For more details, see the `race-single-packet-attack.py` template provided in Turbo Intruder's default examples directory.

## Hidden multi-step sequences
### 📌 Concept

- A single request may trigger multiple internal steps (sub-states).
- These sub-states are temporary and may be exploitable.
- Goal: exploit the gap **before full processing completes**.

---
### ⚠️ Risk
- Allows actions **before checks like MFA are enforced**.
- Can lead to bypasses or unauthorized access.
---
### 🔓 Example: MFA Bypass:

```python
session['userid'] = user.userid
if user.mfa_enabled:
    session['enforce_mfa'] = True
    # send MFA code
    # redirect to MFA page
```
- `userid` is set → session appears valid.
- `enforce_mfa` not yet set → no MFA check.
- Attacker sends login + sensitive request in parallel to exploit this window.
### 🧪 Exploitation Strategy

1. Identify multi-step flows in a single request.
    
2. Check for temporary access before all checks apply.
    
3. Send race requests:
    
    - One for triggering state
        
    - One for exploiting it
## Methodology

To detect and exploit hidden multi-step sequences, we recommend the following methodology, which is summarized from the whitepaper Smashing the state machine: The true potential of web race conditions by PortSwigger Research.

![[../06-Assets/Pasted image 20250501001921.png]]

## 1 - Predict potential collisions

Cut down unnecessary testing by asking:

- **Is this endpoint security-critical?**
    - Skip endpoints that don’t touch sensitive logic (e.g., authentication, payments).
        
- **Could two requests operate on the _same_ resource?**
    - Example:
        
        - ❌ Two password reset requests for **different** users → no collision.
            
        - ✅ Two requests that edit the **same** record → high collision potential.

![[../06-Assets/Pasted image 20250501001953.png]]
## 2 - Probe for clues
Start by establishing a **baseline behavior**:

- In **Burp Repeater**, use:
    - `Send group in sequence (separate connections)` → benchmark normal behavior.
        
    - `Send group in parallel` → simulate race condition using simultaneous requests.
	
- Or use **Turbo Intruder** for greater precision.
    
👀 Look for:
- Differences in response codes or content.
- **Second-order effects** (e.g., unexpected emails, UI changes, altered server state).
## 3 - Prove the concept
Now that you’ve seen strange behavior:

- **Minimize noise**: Remove unnecessary requests and isolate the cause.
    
- Understand the **underlying logic flaw**, not just surface symptoms.
    
- Treat it as a **structural flaw** (a state machine problem), not a one-off bug.
#### PortSwigger Research
For a more detailed methodology, check out the full whitepaper: Smashing the state machine: The true potential of web race conditions

## Multi-endpoint race conditions
These involve **coordinated requests to multiple endpoints**:

- 💡 **Classic example**:
    
    - Add item to cart → pay → add more items → go directly to confirmation.
        
    - If validation and confirmation happen in a _single request_, it's exploitable.
        
- 🧠 These flaws often stem from **missing or weak state machine validation**.
#### Note
If you're not familiar with this exploit, check out the Insufficient workflow validation lab from our Business logic vulnerabilities topic.

A variation of this vulnerability can occur when payment validation and order confirmation are performed during the processing of a single request. The state machine for the order status might look something like this:
![[../06-Assets/Pasted image 20250501002054.png]]
In this case, you can potentially add more items to your basket during the race window between when the payment is validated and when the order is finally confirmed.
## Aligning multi-endpoint race windows

When testing for multi-endpoint race conditions, you may encounter issues trying to line up the race windows for each request, even if you send them all at exactly the same time using the single-packet technique.
![[../06-Assets/Pasted image 20250501002508.png]]
Why timing often fails:

- **1. Network-level delays**:
    
    - Frontend-to-backend connection lag.
        
    - Protocol overhead (e.g., HTTP/1.1 vs HTTP/2).
        
- **2. Endpoint-specific delays**:
    
    - Some endpoints are inherently slower due to internal logic.

✅ **Mitigation Techniques**:
- **Warm connections** beforehand.
- Time your requests based on **measured delays** from benchmarking.
## Connection Warming:

- Backend delays usually **affect all parallel requests equally**, so race conditions still work.
    
- Use **connection warming** (e.g., sending a harmless `GET /` request before your main request) to smooth out inconsistent response times.
    
- In **Burp Repeater**, use:
    - `Send group in sequence (single connection)`
        
- Still seeing delays? Use **Turbo Intruder** to send warm-up requests first.
## Abusing rate or resource limits:
### Abusing Rate/Resource Limits
- If warming doesn't help, servers may throttle requests sent too fast.
- Trick the server by:
    - Sending many **dummy requests** to trigger delays intentionally.
    - This can make **single-packet attacks viable**.
- ⚠️ Turbo Intruder can add delays but may not work reliably on **high-jitter targets**.

![[../06-Assets/Pasted image 20250709223910.png]]

Web servers often delay the processing of requests if too many are sent too quickly. By sending a large number of dummy requests to intentionally trigger the rate or resource limit, you may be able to cause a suitable server-side delay. This makes the single-packet attack viable even when delayed execution is required.
![[../06-Assets/Pasted image 20250709223843.png]]
## Single-endpoint race condition:
- Send parallel requests to the **same endpoint** with different values.
- Example:
    - Two parallel password resets from the same session (user A and user B).
    - End result: `session['reset-user'] = victim`, but reset token goes to the attacker.
- ⚠️ Needs **precise timing or luck**.
- 💡 **Good targets**: Email confirmation, reset flows — often handled after response is sent.

![[../06-Assets/Pasted image 20250709224010.png]]
#### Note

For this attack to work, the different operations performed by each process must occur in just the right order. It would likely require multiple attempts, or a bit of luck, to achieve the desired outcome.

Email address confirmations, or any email-based operations, are generally a good target for single-endpoint race conditions. Emails are often sent in a background thread after the server issues the HTTP response to the client, making race conditions more likely
## Sessions based lock mechanisms:

- Some frameworks (e.g., PHP) use **request locking per session**.
    
- This makes requests run **sequentially**, masking vulnerabilities.
    
- Bypass by using **different session tokens** for each request.
## Partial construction race conditions:
- pps may create resources in **multiple steps**, leaving them temporarily incomplete.
    
- Exploit this by sending requests during the window where:
    
    - The object exists but key properties (like API keys) are missing.
        
- Use **non-standard parameter formats** to match uninitialized values:
    
    - PHP: `param[]=`, `param[]=foo&param[]=bar`
        
    - Rails: `param[key]` → `{"param": {"key": nil}}`
		```sql
		GET /api/user/info?user=victim&api-key[]= 
		```
⚠️ Similar tricks might work with **passwords**, but you'd need to **match hash digests**.

#### Note

It's possible to cause similar partial construction collisions with a password rather than an API key. However, as passwords are hashed, this means you need to inject a value that makes the hash digest match the uninitialized value.
## Time-sensitive attacks:
- Vulnerabilities can appear even without race conditions if timing is crucial.
    
- Example:
    
    - Reset tokens based on timestamps, not randomness.
        
    - Trigger two resets at **exact same timestamp** → same token for both users.
## How to prevent race condition vulnerabilities:
To secure your app effectively:

- ✅ Avoid mixing data from different storage layers.
    
- ✅ Make sensitive operations **atomic** using transactions (e.g., SQL transactions).
    
- ✅ Use **uniqueness constraints** as backup protection.
    
- ✅ Don’t rely on session storage to protect database logic.
    
- ✅ Keep **session updates consistent** — batch updates are safer than piecemeal.
    
- ✅ Consider **stateless architectures** using encrypted client-side tokens (e.g., JWTs).
    
- ⚠️ Be cautious — [JWT attacks](https://portswigger.net/web-security/jwt) are a known risk.