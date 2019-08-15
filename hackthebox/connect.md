#### How to connect and access to HackTheBox's machines
- Tuts: 
  + Click on Labs/Access on the left: https://www.hackthebox.eu/home/htb/access
  + https://www.youtube.com/watch?v=Rq3pFKCocjI
- Install openvpn:
```
$ sudo apt install openvpn
```
- (Click on Regenerate) Download file Connection Pack `strongcourage.ovpn` and run:
```
$ openvpn --config strongcourage.ovpn
Thu Aug 15 11:15:10 2019 OpenVPN 2.4.7 x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Feb 20 2019
Thu Aug 15 11:15:10 2019 library versions: OpenSSL 1.1.1a  20 Nov 2018, LZO 2.10
Thu Aug 15 11:15:10 2019 Outgoing Control Channel Authentication: Using 256 bit message hash 'SHA256' for HMAC authentication
Thu Aug 15 11:15:10 2019 Incoming Control Channel Authentication: Using 256 bit message hash 'SHA256' for HMAC authentication
Thu Aug 15 11:15:10 2019 TCP/UDP: Preserving recently used remote address: [AF_INET]51.79.40.240:1337
Thu Aug 15 11:15:10 2019 Socket Buffers: R=[212992->212992] S=[212992->212992]
Thu Aug 15 11:15:10 2019 UDP link local: (not bound)
Thu Aug 15 11:15:10 2019 UDP link remote: [AF_INET]51.79.40.240:1337
Thu Aug 15 11:15:10 2019 TLS: Initial packet from [AF_INET]51.79.40.240:1337, sid=6dde69b1 e6e6a138
Thu Aug 15 11:15:10 2019 VERIFY OK: depth=1, C=UK, ST=City, L=London, O=HackTheBox, CN=HackTheBox CA, name=htb, emailAddress=info@hackthebox.eu
Thu Aug 15 11:15:10 2019 VERIFY KU OK
Thu Aug 15 11:15:10 2019 Validating certificate extended key usage
Thu Aug 15 11:15:10 2019 ++ Certificate has EKU (str) TLS Web Server Authentication, expects TLS Web Server Authentication
Thu Aug 15 11:15:10 2019 VERIFY EKU OK
Thu Aug 15 11:15:10 2019 VERIFY OK: depth=0, C=UK, ST=City, L=London, O=HackTheBox, CN=htb, name=htb, emailAddress=info@hackthebox.eu
Thu Aug 15 11:15:10 2019 Control Channel: TLSv1.2, cipher TLSv1.2 ECDHE-RSA-AES256-GCM-SHA384, 2048 bit RSA
Thu Aug 15 11:15:10 2019 [htb] Peer Connection Initiated with [AF_INET]51.79.40.240:1337
Thu Aug 15 11:15:11 2019 SENT CONTROL [htb]: 'PUSH_REQUEST' (status=1)
Thu Aug 15 11:15:11 2019 PUSH: Received control message: 'PUSH_REPLY,route 10.10.10.0 255.255.255.0,route-ipv6 dead:beef::/64,tun-ipv6,route-gateway 10.10.14.1,topology subnet,ping 10,ping-restart 120,ifconfig-ipv6 dead:beef:2::107f/64 dead:beef:2::1,ifconfig 10.10.14.129 255.255.254.0,peer-id 1,cipher AES-256-GCM'
Thu Aug 15 11:15:11 2019 OPTIONS IMPORT: timers and/or timeouts modified
Thu Aug 15 11:15:11 2019 OPTIONS IMPORT: --ifconfig/up options modified
Thu Aug 15 11:15:11 2019 OPTIONS IMPORT: route options modified
Thu Aug 15 11:15:11 2019 OPTIONS IMPORT: route-related options modified
Thu Aug 15 11:15:11 2019 OPTIONS IMPORT: peer-id set
Thu Aug 15 11:15:11 2019 OPTIONS IMPORT: adjusting link_mtu to 1625
Thu Aug 15 11:15:11 2019 OPTIONS IMPORT: data channel crypto options modified
Thu Aug 15 11:15:11 2019 Data Channel: using negotiated cipher 'AES-256-GCM'
Thu Aug 15 11:15:11 2019 Outgoing Data Channel: Cipher 'AES-256-GCM' initialized with 256 bit key
Thu Aug 15 11:15:11 2019 Incoming Data Channel: Cipher 'AES-256-GCM' initialized with 256 bit key
Thu Aug 15 11:15:11 2019 ROUTE_GATEWAY 10.0.2.2/255.255.255.0 IFACE=eth0 HWADDR=08:00:27:90:0b:0c
Thu Aug 15 11:15:11 2019 GDG6: remote_host_ipv6=n/a
Thu Aug 15 11:15:11 2019 ROUTE6_GATEWAY fe80::1a90:d8ff:fece:a2ec IFACE=eth0
Thu Aug 15 11:15:11 2019 TUN/TAP device tun0 opened
Thu Aug 15 11:15:11 2019 TUN/TAP TX queue length set to 100
Thu Aug 15 11:15:11 2019 /sbin/ip link set dev tun0 up mtu 1500
Thu Aug 15 11:15:12 2019 /sbin/ip addr add dev tun0 10.10.14.129/23 broadcast 10.10.15.255
Thu Aug 15 11:15:12 2019 /sbin/ip -6 addr add dead:beef:2::107f/64 dev tun0
Thu Aug 15 11:15:12 2019 /sbin/ip route add 10.10.10.0/24 via 10.10.14.1
Thu Aug 15 11:15:12 2019 add_route_ipv6(dead:beef::/64 -> dead:beef:2::1 metric -1) dev tun0
Thu Aug 15 11:15:12 2019 /sbin/ip -6 route add dead:beef::/64 dev tun0
Thu Aug 15 11:15:12 2019 WARNING: this configuration may cache passwords in memory -- use the auth-nocache option to prevent this
Thu Aug 15 10:19:28 2019 Initialization Sequence Completed
```
- Reload the site and see the HTB Lab Access Details:
```
Server 	edge-us-free-1.hackthebox.eu
Port 	1337
Server status 	
Connected 	
HTB Network IPv4 	10.10.14.129
HTB Network IPv6 	dead:beef:2::107f
Traffic 	0.01 MB 0.01 MB
```
- Note that free users can only ping the Active Machines in green (not Retired Machines) in Labs/Machines/All.
```
$ ping 10.10.10.134
PING 10.10.10.134 (10.10.10.134) 56(84) bytes of data.
64 bytes from 10.10.10.134: icmp_seq=1 ttl=127 time=170 ms
64 bytes from 10.10.10.134: icmp_seq=2 ttl=127 time=177 ms
64 bytes from 10.10.10.134: icmp_seq=3 ttl=127 time=130 ms
64 bytes from 10.10.10.134: icmp_seq=4 ttl=127 time=102 ms
64 bytes from 10.10.10.134: icmp_seq=5 ttl=127 time=102 ms
64 bytes from 10.10.10.134: icmp_seq=6 ttl=127 time=176 ms
64 bytes from 10.10.10.134: icmp_seq=7 ttl=127 time=138 ms
^C
--- 10.10.10.134 ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 16ms
rtt min/avg/max/mdev = 101.928/142.160/176.606/30.453 ms
```
- Run nmap on Bastion machine:
```
$ nmap -sV -A 10.10.10.134
Starting Nmap 7.70 ( https://nmap.org ) at 2019-08-15 11:23 EDT
Nmap scan report for 10.10.10.134
Host is up (0.14s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE      VERSION
22/tcp  open  ssh          OpenSSH for_Windows_7.9 (protocol 2.0)
| ssh-hostkey: 
|   2048 3a:56:ae:75:3c:78:0e:c8:56:4d:cb:1c:22:bf:45:8a (RSA)
|   256 cc:2e:56:ab:19:97:d5:bb:03:fb:82:cd:63:da:68:01 (ECDSA)
|_  256 93:5f:5d:aa:ca:9f:53:e7:f2:82:e6:64:a8:a3:a0:18 (ED25519)
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.70%E=4%D=8/15%OT=22%CT=1%CU=31117%PV=Y%DS=2%DC=T%G=Y%TM=5D55794
OS:4%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=2%ISR=108%TI=I%CI=RD%II=I%SS=S%TS
OS:=A)SEQ(SP=105%GCD=1%ISR=108%TI=I%CI=RD%II=I%TS=A)OPS(O1=M54DNW8ST11%O2=M
OS:54DNW8ST11%O3=M54DNW8NNT11%O4=M54DNW8ST11%O5=M54DNW8ST11%O6=M54DST11)WIN
OS:(W1=2000%W2=2000%W3=2000%W4=2000%W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=200
OS:0%O=M54DNW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=
OS:Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%
OS:RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0
OS:%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7
OS:(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=
OS:0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 2 hops
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -39m26s, deviation: 1h09m14s, median: 31s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Bastion
|   NetBIOS computer name: BASTION\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2019-08-15T17:25:17+02:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2019-08-15 11:25:16
|_  start_date: 2019-08-15 10:48:28

TRACEROUTE (using port 1025/tcp)
HOP RTT       ADDRESS
1   102.43 ms 10.10.14.1
2   124.71 ms 10.10.10.134

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 67.22 seconds
```




