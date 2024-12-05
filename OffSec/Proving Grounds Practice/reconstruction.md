# reconstruction

### Nmap Scaning 
```
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -A 192.168.234.103 -p-     

Starting Nmap 7.94 ( https://nmap.org ) at 2024-12-05 17:39 CST
Nmap scan report for 192.168.234.103
Host is up (0.071s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxr-xr-x    2 0        0            4096 Apr 29  2020 WebSOC
|_-rw-r--r--    1 0        0             137 Apr 29  2020 note.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.45.156
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 55:4e:03:dc:c8:92:36:66:49:75:f3:2e:35:15:8e:57 (RSA)
|   256 1b:df:4c:ac:72:93:8f:77:92:05:98:ae:7c:c1:6a:ea (ECDSA)
|_  256 d8:3f:b9:21:e4:8c:34:3a:9b:c7:46:cc:c4:f5:6e:eb (ED25519)
8080/tcp open  http    Werkzeug httpd 1.0.1 (Python 3.6.9)
|_http-title: Blog
|_http-server-header: Werkzeug/1.0.1 Python/3.6.9
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94%E=4%D=12/5%OT=21%CT=1%CU=39222%PV=Y%DS=4%DC=T%G=Y%TM=6751751
OS:B%P=aarch64-unknown-linux-gnu)SEQ(SP=101%GCD=1%ISR=108%TI=Z%CI=I%II=I%TS
OS:=A)SEQ(SP=101%GCD=1%ISR=109%TI=Z%CI=I%II=I%TS=A)SEQ(SP=102%GCD=1%ISR=109
OS:%TI=Z%CI=I%II=I%TS=A)OPS(O1=M578ST11NW7%O2=M578ST11NW7%O3=M578NNT11NW7%O
OS:4=M578ST11NW7%O5=M578ST11NW7%O6=M578ST11)WIN(W1=7120%W2=7120%W3=7120%W4=
OS:7120%W5=7120%W6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M578NNSNW7%CC=Y%Q=)T1(R=
OS:Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A
OS:%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y
OS:%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN
OS:=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 4 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 1720/tcp)
HOP RTT      ADDRESS
1   74.82 ms 192.168.45.1
2   74.89 ms 192.168.45.254
3   74.91 ms 192.168.251.1
4   75.03 ms 192.168.234.103

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 70.37 seconds
```

### ftp 

Nmap 的報告顯示可以使用 Anonymous 的身份登入 FTP，來看看可以撈到什麼檔案。

```
┌──(kali㉿kali)-[~]
└─$ ftp 192.168.234.103

Connected to 192.168.234.103.
220 (vsFTPd 3.0.3)
Name (192.168.234.103:kali): Anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||41338|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Apr 29  2020 WebSOC
-rw-r--r--    1 0        0             137 Apr 29  2020 note.txt
226 Directory send OK.
ftp> cat note.txt
?Invalid command.
ftp> get note.txt
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||56518|)
150 Opening BINARY mode data connection for note.txt (137 bytes).
100% |*****************************************************************************|   137       82.53 KiB/s    00:00 ETA
226 Transfer complete.
137 bytes received in 00:00 (1.83 KiB/s)
ftp> ls
229 Entering Extended Passive Mode (|||48235|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Apr 29  2020 WebSOC
-rw-r--r--    1 0        0             137 Apr 29  2020 note.txt
226 Directory send OK.
ftp> cd WebSOC
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||14479|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0         3086771 Apr 29  2020 1.05.2020.pcap
-rw-r--r--    1 0        0          869677 Apr 29  2020 29.04.2020.pcap
-rw-r--r--    1 0        0        14579662 Apr 29  2020 30.04.2020.pcap
226 Directory send OK.
ftp> get 1.05.2020.pcap
local: 1.05.2020.pcap remote: 1.05.2020.pcap
229 Entering Extended Passive Mode (|||42318|)
150 Opening BINARY mode data connection for 1.05.2020.pcap (3086771 bytes)
100% |********************************************************************
226 Transfer complete.
3086771 bytes received in 00:02 (1.21 MiB/s)
ftp> get 29.04.2020.pcap
local: 29.04.2020.pcap remote: 29.04.2020.pcap
229 Entering Extended Passive Mode (|||64249|)
150 Opening BINARY mode data connection for 29.04.2020.pcap (869677 bytes)
100% |********************************************************************
226 Transfer complete.
869677 bytes received in 00:00 (914.60 KiB/s)
ftp> get 30.04.2020.pcap
local: 30.04.2020.pcap remote: 30.04.2020.pcap
229 Entering Extended Passive Mode (|||59639|)
150 Opening BINARY mode data connection for 30.04.2020.pcap (14579662 byte
100% |********************************************************************
226 Transfer complete.
14579662 bytes received in 00:14 (0.99 MiB/s)
```

四個檔案分別為：
- note.txt
```
┌──(kali㉿kali)-[~]
└─$ cat note.txt 

I've just setup the new WebSOC! This should hopefully help us catch these filthy hackers!


TODO: remove leftover passwords from testing                                           
```
- 1.05.2020.pcap
