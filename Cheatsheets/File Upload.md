Navigation: [[Pentesting]]

---
# Table of Contents
1. Introduction
2. Client Side Validation
3. Server Side Validation
# Introduction
File upload attacks involve uploading a file that the user shouldn't be able to. From here, they're able to run that file through accessing it on the web server, often resulting in RCE. If we don't get RCE, we can introduce vulnerabilities such as `XSS` or `XXE`, cause `DoS`, or overwrite system configuration files.

This vulnerability is caused by improper filtering and validation of uploaded files. It can also be caused by the use of outdated libraries. It's rated as `high` or `critical` in most vulnerability reports.

The most basic version of this vulnerability is arbitrary file upload, where there's no validation filters on uploaded files. We can either upload a web shell or reverse shell, then access that file. 

Note that when sending a web shell, we'll need to use Burp Intruder to fuzz for it using a [word list](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt), or sometimes we might just be able to see it without fuzzing. We'll need to use the correct type of extension for our web app.
# Client Side Validation
# Server Side Validation


---
Navigation: [[Pentesting]]