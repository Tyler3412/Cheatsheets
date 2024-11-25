Navigation: [[Pentesting]]

---
```shell
# Directories
ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ

# Extensions
ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/indexFUZZ

# Pages
ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ.php

# Recursion
ffuf -w wordlist.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v

```

Useful [wordlists](https://github.com/danielmiessler/SecLists)
```shell
/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
/SecLists/Discovery/Web-Content/web-extensions.txt
/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```

---
Navigation: [[Pentesting]]