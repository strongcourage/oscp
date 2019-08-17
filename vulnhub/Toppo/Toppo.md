# Toppo

## 0. Refs
- Download VM: https://www.vulnhub.com/entry/toppo-1,245/
- Install machine using VMPlayer, then get machine's IP: 172.16.17.129
- https://www.youtube.com/watch?v=dOoPrg9a_sY&t=580s
- https://medium.com/egghunter/toppo-1-vulnhub-walkthrough-c5f05358cf7d

## 1. Enumeration
- Run nmap
```
$ nmap -sV -A 172.16.17.129
Starting Nmap 7.70 ( https://nmap.org ) at 2019-08-17 16:36 EDT
Nmap scan report for 172.16.17.129
Host is up (0.00071s latency).
Not shown: 997 closed ports
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 ec:61:97:9f:4d:cb:75:99:59:d4:c1:c4:d4:3e:d9:dc (DSA)
|   2048 89:99:c4:54:9a:18:66:f7:cd:8e:ab:b6:aa:31:2e:c6 (RSA)
|_  256 39:d9:79:26:60:3d:6c:a2:1e:8b:19:71:c0:e2:5e:5f (ED25519)
80/tcp  open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Clean Blog - Start Bootstrap Theme
111/tcp open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version   port/proto  service
|   100000  2,3,4        111/tcp  rpcbind
|   100000  2,3,4        111/udp  rpcbind
|   100024  1          36696/tcp  status
|_  100024  1          58358/udp  status
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.70%E=4%D=8/17%OT=22%CT=1%CU=34648%PV=Y%DS=2%DC=T%G=Y%TM=5D58655
OS:9%P=x86_64-pc-linux-gnu)SEQ(SP=11%GCD=FA00%ISR=9C%TI=I%CI=I%II=I%SS=S%TS
OS:=U)OPS(O1=M5B4%O2=M5B4%O3=M5B4%O4=M5B4%O5=M5B4%O6=M5B4)WIN(W1=FFFF%W2=FF
OS:FF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FFFF)ECN(R=Y%DF=N%T=41%W=FFFF%O=M5B4%CC=N%
OS:Q=)T1(R=Y%DF=N%T=41%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=N%T=100%W=0%S=Z%A=S%
OS:F=AR%O=%RD=0%Q=)T3(R=Y%DF=N%T=100%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T4(R=Y%DF
OS:=N%T=100%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=N%T=100%W=0%S=Z%A=S+%F=AR%
OS:O=%RD=0%Q=)T6(R=Y%DF=N%T=100%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=N%T=10
OS:0%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=35%IPL=164%UN=0%RIPL=G%RID=G
OS:%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=S%T=2B%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT     ADDRESS
1   0.20 ms 10.0.2.2
2   0.22 ms 172.16.17.129

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.83 seconds
```
- [Zenmap output](zenmap_output.pdf)
```
$ dirb http://172.16.17.129/ /usr/share/dirb/wordlists/common.txt
-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sat Aug 17 16:24:29 2019
URL_BASE: http://172.16.17.129/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://172.16.17.129/ ----
==> DIRECTORY: http://172.16.17.129/admin/                                     
==> DIRECTORY: http://172.16.17.129/css/                                       
==> DIRECTORY: http://172.16.17.129/img/                                       
+ http://172.16.17.129/index.html (CODE:200|SIZE:6437)                         
==> DIRECTORY: http://172.16.17.129/js/                                        
+ http://172.16.17.129/LICENSE (CODE:200|SIZE:1093)                            
==> DIRECTORY: http://172.16.17.129/mail/                                      
==> DIRECTORY: http://172.16.17.129/manual/                                    
+ http://172.16.17.129/server-status (CODE:403|SIZE:301)                       
==> DIRECTORY: http://172.16.17.129/vendor/                                    
                                                                               
---- Entering directory: http://172.16.17.129/admin/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
---- Entering directory: http://172.16.17.129/css/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
---- Entering directory: http://172.16.17.129/img/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
<SNIP>
-----------------
END_TIME: Sat Aug 17 16:33:00 2019
DOWNLOADED: 341288 - FOUND: 76
```
```
$ nikto -h http://172.16.17.129/
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          172.16.17.129
+ Target Hostname:    172.16.17.129
+ Target Port:        80
+ Start Time:         2019-08-17 16:35:08 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.4.10 (Debian)
+ Server leaks inodes via ETags, header found with file /, fields: 0x1925 0x563f5cf714e80 
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.10 appears to be outdated (current is at least Apache/2.4.12). Apache 2.0.65 (final release) and 2.2.29 are also current.
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS 
+ OSVDB-3268: /admin/: Directory indexing found.
+ OSVDB-3092: /admin/: This might be interesting...
+ OSVDB-3268: /img/: Directory indexing found.
+ OSVDB-3092: /img/: This might be interesting...
+ OSVDB-3268: /mail/: Directory indexing found.
+ OSVDB-3092: /mail/: This might be interesting...
+ OSVDB-3092: /manual/: Web server manual found.
+ OSVDB-3268: /manual/images/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7535 requests: 0 error(s) and 15 item(s) reported on remote host
+ End Time:           2019-08-17 16:35:18 (GMT-4) (10 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```
- See http://172.16.17.129/admin/notes.txt:
```
Note to myself :

I need to change my password :/ 12345ted123 is too outdated but the technology isn't my thing i prefer go fishing or watching soccer .
```




