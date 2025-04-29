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

## Lab: Limit overrun race conditions:
