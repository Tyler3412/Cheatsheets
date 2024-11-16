Navigation: [[Pentesting]]

---
# Table of Contents
1. Application Discovery
2. Content Management Systems
3. Containers and Software Dev
4. Infrastructure and Monitoring
5. Customer Service and Configuration Management
6. Common Gateway Interfaces
7. Thick Client
8. Misc.
# Application Discovery
**Eyewitness Scan**
Takes XML output from `nmap` and `Nessus` to create a report with screenshots of web apps using Selenium. It'll categorize, fingerprint, and suggest credentials based on application type.

It can also accept a list of IPs and URLs and be told to pre-pend `https://` and `http://` to the front of each.

Here's the [repo](https://github.com/FortyNorthSecurity/EyeWitness) for eyewitness.
```shell
eyewitness --web -x web_discovery.xml -d <output directory>
```

**Aquatone**
Similar to eyewitness, can take screenshots when provided a `.txt` of hosts or an `nmap` `.xml` file with the `-nmap` flag.

Here's the [repo](https://github.com/shelld3v/aquatone) for aquatone.
```shell
cat web_discovery.xml | ./aquatone -nmap
```



---
Navigation: [[Pentesting]]