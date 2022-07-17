# Wonderland

## Wonderland-TryHackMe

***

## Info

| Name         | Wonderland                                     |
| ------------ | ---------------------------------------------- |
| Room link    | https://tryhackme.com/room/wonderland          |
| Difficulty   | Medium                                         |
| Created by   | [NinjaJc01](https://tryhackme.com/p/NinjaJc01) |
| solving date | June 9th 2022                                  |

***

|        Room Image        |             Room Background            |
| :----------------------: | :------------------------------------: |
| ![](images/Untitled.png) | ![Background](<images/Untitled 1.png>) |

* Table Of Contents
  * Information Gathering
  * Gaining Access
  * Privilege Escalation
    * from Alice to rabbit
    * from rabbit to hatter
    * from hatter to root
  * Getting the flag
    * root flag
    * user flag

***

## Information Gathering

```bash
export target=10.10.43.237
```

*   namp inital port scan

    ```
    Nmap scan report for 10.10.43.237
    Host is up, received user-set (0.12s latency).
    Scanned at 2022-06-09 12:54:20 EET for 34s
    Not shown: 998 closed ports
    Reason: 998 conn-refused
    PORT   STATE SERVICE REASON  VERSION
    **22**/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
    **80**/tcp open  http    syn-ack Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
    Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

    Read data files from: /usr/bin/../share/nmap
    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 34.24 seconds
    ```

    * we found ports 22 and 80 open let’s navigate to the website while scanning all the ports with `nmap -Pn -vv -p- $target`
*   the website:

    <img src="images/Untitled 2.png" alt="Untitled" data-size="original">
*   look for any interesting information on the site Source

    <img src="images/Untitled 3.png" alt="Untitled" data-size="original">

    * nothing interesting here
* I close it because the internet speed with really bad and Nmap full scan took too much time, but it seems that there is no more open ports
*   Let’s do some fuzzing using `feroxbuster`

    <img src="images/Untitled 4.png" alt="Untitled" data-size="original">
* it seems that the directory is rabbit, like: /r/a/b/b/i/t

![Untitled](<images/Untitled 5.png>)

![Untitled](<images/Untitled 6.png>)

![Untitled](<images/Untitled 7.png>)

![Untitled](<images/Untitled 8.png>)

![Untitled](<images/Untitled 9.png>)

![Untitled](<images/Untitled 10.png>)

*   After checking the sources of these pages, we found this interesting information in `/r/a/b/b/i/t` path

    <img src="images/Untitled 11.png" alt="Untitled" data-size="original">

***

## Gaining Access

*   let’s check if this is a valid credential using ssh (we know that ssh is running from our nmap scan

    <img src="images/Untitled 12.png" alt="Untitled" data-size="original">

    * Great 🙂

***

## Privilege Escalation

### PrivEsc: From Alice to rabbit

*   we can’t view root.txt content

    <img src="images/Untitled 13.png" alt="Untitled" data-size="original">
*   Let’s search for user.txt file first

    ```bash
    alice@wonderland:~$ find / -name user.txt 2>/dev/null
    alice@wonderland:~$ # Nothing found
    ```
* checking our privileges

![Untitled](<images/Untitled 14.png>)

* we can run `/usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py` as rabbit user
* as we can see, we can’t edit the python script, but we can read it

```bash
alice@wonderland:~$ ls -l walrus_and_the_carpenter.py 
-rw-r--r-- 1 root root 3577 May 25  2020 walrus_and_the_carpenter.py
```

<details>

<summary>walrus_and_the_carpenter.py (click me)</summary>

````
    ```
**import random**
poem = """The sun was shining on the sea,
Shining with all his might:
He did his very best to make
The billows smooth and bright —
And this was odd, because it was
The middle of the night.

The moon was shining sulkily,
Because she thought the sun
Had got no business to be there
After the day was done —
"It’s very rude of him," she said,
"To come and spoil the fun!"

The sea was wet as wet could be,
The sands were dry as dry.
You could not see a cloud, because
No cloud was in the sky:
No birds were flying over head —
There were no birds to fly.

The Walrus and the Carpenter
Were walking close at hand;
They wept like anything to see
Such quantities of sand:
"If this were only cleared away,"
They said, "it would be grand!"

"If seven maids with seven mops
Swept it for half a year,
Do you suppose," the Walrus said,
"That they could get it clear?"
"I doubt it," said the Carpenter,
And shed a bitter tear.

"O Oysters, come and walk with us!"
The Walrus did beseech.
"A pleasant walk, a pleasant talk,
Along the briny beach:
We cannot do with more than four,
To give a hand to each."

The eldest Oyster looked at him.
But never a word he said:
The eldest Oyster winked his eye,
And shook his heavy head —
Meaning to say he did not choose
To leave the oyster-bed.

But four young oysters hurried up,
All eager for the treat:
Their coats were brushed, their faces washed,
Their shoes were clean and neat —
And this was odd, because, you know,
They hadn’t any feet.

Four other Oysters followed them,
And yet another four;
And thick and fast they came at last,
And more, and more, and more —
All hopping through the frothy waves,
And scrambling to the shore.

The Walrus and the Carpenter
Walked on a mile or so,
And then they rested on a rock
Conveniently low:
And all the little Oysters stood
And waited in a row.

"The time has come," the Walrus said,
"To talk of many things:
Of shoes — and ships — and sealing-wax —
Of cabbages — and kings —
And why the sea is boiling hot —
And whether pigs have wings."

"But wait a bit," the Oysters cried,
"Before we have our chat;
For some of us are out of breath,
And all of us are fat!"
"No hurry!" said the Carpenter.
They thanked him much for that.

"A loaf of bread," the Walrus said,
"Is what we chiefly need:
Pepper and vinegar besides
Are very good indeed —
Now if you’re ready Oysters dear,
We can begin to feed."

"But not on us!" the Oysters cried,
Turning a little blue,
"After such kindness, that would be
A dismal thing to do!"
"The night is fine," the Walrus said
"Do you admire the view?

"It was so kind of you to come!
And you are very nice!"
The Carpenter said nothing but
"Cut us another slice:
I wish you were not quite so deaf —
I’ve had to ask you twice!"

"It seems a shame," the Walrus said,
"To play them such a trick,
After we’ve brought them out so far,
And made them trot so quick!"
The Carpenter said nothing but
"The butter’s spread too thick!"

"I weep for you," the Walrus said.
"I deeply sympathize."
With sobs and tears he sorted out
Those of the largest size.
Holding his pocket handkerchief
Before his streaming eyes.

"O Oysters," said the Carpenter.
"You’ve had a pleasant run!
Shall we be trotting home again?"
But answer came there none —
And that was scarcely odd, because
They’d eaten every one."""

for i in range(10):
    line = random.choice(poem.split("\n"))
    print("The line was:\t", line)
```
````

</details>

*   in the first line, we can see it imports `random` module, so we can do python module Hijacking by creating `random.py` file in the same directory using `touch random.py` command

    <img src="images/Untitled 15.png" alt="Untitled" data-size="original">
*   run the script to make sure it runs as rabbit

    <img src="images/Untitled 16.png" alt="Untitled" data-size="original">

    * as we can notice the user running the script is rabbit
*   let’s inject our malicious script

    <img src="images/Untitled 17.png" alt="Untitled" data-size="original">

    <img src="images/Untitled 18.png" alt="Untitled" data-size="original">

***

### PrivEsc: From rabbit to hatter

*   discovering rabbit home directory

    <img src="images/Untitled 19.png" alt="Untitled" data-size="original">
* it’s SUID binary file running with root privileges
*   strings command is not installed, so we will copy teaParty to our system (strings: print the sequences of printable characters in files)

    <img src="images/Untitled 20.png" alt="Untitled" data-size="original">
*   transferring teaParty using netcat

    <img src="images/Untitled 21.png" alt="Untitled" data-size="original">
*   teaParty content:

    ```
    ╭─juba@Kubuntu ~ 
    ╰─$ strings teaParty 
    /lib64/ld-linux-x86-64.so.2
    2U~4
    libc.so.6
    setuid
    puts
    getchar
    system
    __cxa_finalize
    setgid
    __libc_start_main
    GLIBC_2.2.5
    _ITM_deregisterTMCloneTable
    __gmon_start__
    _ITM_registerTMCloneTable
    u/UH
    []A\A]A^A_
    Welcome to the tea party!
    The Mad Hatter will be here soon.
    /bin/echo -n 'Probably by ' && **date** --date='next hour' -R
    Ask very nicely, and I will give you some tea while you wait for him
    Segmentation fault (core dumped)
    ;*3$"
    GCC: (Debian 8.3.0-6) 8.3.0
    crtstuff.c
    deregister_tm_clones
    __do_global_dtors_aux
    completed.7325
    __do_global_dtors_aux_fini_array_entry
    frame_dummy
    __frame_dummy_init_array_entry
    teaParty.c
    __FRAME_END__
    __init_array_end
    _DYNAMIC
    __init_array_start
    __GNU_EH_FRAME_HDR
    _GLOBAL_OFFSET_TABLE_
    __libc_csu_fini
    _ITM_deregisterTMCloneTable
    puts@@GLIBC_2.2.5
    _edata
    system@@GLIBC_2.2.5
    __libc_start_main@@GLIBC_2.2.5
    __data_start
    getchar@@GLIBC_2.2.5
    __gmon_start__
    __dso_handle
    _IO_stdin_used
    __libc_csu_init
    __bss_start
    main
    setgid@@GLIBC_2.2.5
    __TMC_END__
    _ITM_registerTMCloneTable
    setuid@@GLIBC_2.2.5
    __cxa_finalize@@GLIBC_2.2.5
    .symtab
    .strtab
    .shstrtab
    .interp
    .note.ABI-tag
    .note.gnu.build-id
    .gnu.hash
    .dynsym
    .dynstr
    .gnu.version
    .gnu.version_r
    .rela.dyn
    .rela.plt
    .init
    .plt.got
    .text
    .fini
    .rodata
    .eh_frame_hdr
    .eh_frame
    .init_array
    .fini_array
    .dynamic
    .got.plt
    .data
    .bss
    .comment
    ```
* we noticed this line `/bin/echo -n 'Probably by ' && **date** --date='next hour' -R` he is calling `date` without specifying the full path, so let’s create an executable date file and add it’s directory to the $PATH env
*   write a script to check our plan

    <img src="images/Untitled 22.png" alt="Untitled" data-size="original">
*   give execution permission for all users

    <img src="images/Untitled 23.png" alt="Untitled" data-size="original">
*   run teaParty

    <img src="images/Untitled 24.png" alt="Untitled" data-size="original">
*   as we can see we are hatter, so let’s inject our malicious script it’s just adding (`/bin/bash`) 😊 to get the shell

    <img src="images/Untitled 25.png" alt="Untitled" data-size="original">

***

### PrivEsc: From hatter to root

*   discovering hatter home directory

    <img src="images/Untitled 26.png" alt="Untitled" data-size="original">
*   we found a password but I don’t know if it’s the root password or hatter’s password, so let’s try it

    <img src="images/Untitled 27.png" alt="Untitled" data-size="original">

    <img src="images/Untitled 28.png" alt="Untitled" data-size="original">

    * it’s hatter’s password
* After some manual enumeration, I prefer to speed the process by using an automation tool like [Linenum](https://github.com/rebootuser/LinEnum)

<details>

<summary>LinEnum Result (click me)</summary>

````
```
[00;31m#########################################################[00m
[00;31m#[00m [00;33mLocal Linux Enumeration & Privilege Escalation Script[00m [00;31m#[00m
[00;31m#########################################################[00m
[00;33m# www.rebootuser.com[00m
[00;33m# version 0.982[00m

[-] Debug Info
[00;33m[+] Thorough tests = Disabled[00m

[00;33mScan started at:
Thu Jun  9 13:02:39 UTC 2022
[00m

[00;33m### SYSTEM ##############################################[00m
[00;31m[-] Kernel information:[00m
Linux wonderland 4.15.0-101-generic #102-Ubuntu SMP Mon May 11 10:07:26 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

[00;31m[-] Kernel information (continued):[00m
Linux version 4.15.0-101-generic (buildd@lgw01-amd64-003) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #102-Ubuntu SMP Mon May 11 10:07:26 UTC 2020

[00;31m[-] Specific release information:[00m
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=18.04
DISTRIB_CODENAME=bionic
DISTRIB_DESCRIPTION="Ubuntu 18.04.4 LTS"
NAME="Ubuntu"
VERSION="18.04.4 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.4 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic

[00;31m[-] Hostname:[00m
wonderland

[00;33m### USER/GROUP ##########################################[00m
[00;31m[-] Current user/group info:[00m
uid=1003(hatter) gid=1003(hatter) groups=1003(hatter)

[00;31m[-] Users that have previously logged onto the system:[00m
Username         Port     From             Latest
tryhackme        pts/0    10.8.6.110       Fri Jun  5 22:28:57 +0000 2020
alice            pts/0    10.8.90.96       Thu Jun  9 11:37:26 +0000 2022
hatter           pts/2    10.8.90.96       Thu Jun  9 12:52:27 +0000 2022

[00;31m[-] Who else is logged on:[00m
 13:02:39 up  2:10,  2 users,  load average: 0.07, 0.03, 0.01
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
alice    pts/0    10.8.90.96       11:37    4:06   0.28s  0.01s sshd: alice [priv]  
hatter   pts/2    10.8.90.96       12:52    7.00s  0.06s  0.00s /bin/bash ./linEnum.sh

[00;31m[-] Group memberships:[00m
uid=0(root) gid=0(root) groups=0(root)
uid=1(daemon) gid=1(daemon) groups=1(daemon)
uid=2(bin) gid=2(bin) groups=2(bin)
uid=3(sys) gid=3(sys) groups=3(sys)
uid=4(sync) gid=65534(nogroup) groups=65534(nogroup)
uid=5(games) gid=60(games) groups=60(games)
uid=6(man) gid=12(man) groups=12(man)
uid=7(lp) gid=7(lp) groups=7(lp)
uid=8(mail) gid=8(mail) groups=8(mail)
uid=9(news) gid=9(news) groups=9(news)
uid=10(uucp) gid=10(uucp) groups=10(uucp)
uid=13(proxy) gid=13(proxy) groups=13(proxy)
uid=33(www-data) gid=33(www-data) groups=33(www-data)
uid=34(backup) gid=34(backup) groups=34(backup)
uid=38(list) gid=38(list) groups=38(list)
uid=39(irc) gid=39(irc) groups=39(irc)
uid=41(gnats) gid=41(gnats) groups=41(gnats)
uid=65534(nobody) gid=65534(nogroup) groups=65534(nogroup)
uid=100(systemd-network) gid=102(systemd-network) groups=102(systemd-network)
uid=101(systemd-resolve) gid=103(systemd-resolve) groups=103(systemd-resolve)
uid=102(syslog) gid=106(syslog) groups=106(syslog),4(adm)
uid=103(messagebus) gid=107(messagebus) groups=107(messagebus)
uid=104(_apt) gid=65534(nogroup) groups=65534(nogroup)
uid=105(lxd) gid=65534(nogroup) groups=65534(nogroup)
uid=106(uuidd) gid=110(uuidd) groups=110(uuidd)
uid=107(dnsmasq) gid=65534(nogroup) groups=65534(nogroup)
uid=108(landscape) gid=112(landscape) groups=112(landscape)
uid=109(pollinate) gid=1(daemon) groups=1(daemon)
uid=110(sshd) gid=65534(nogroup) groups=65534(nogroup)
uid=1000(tryhackme) gid=1000(tryhackme) groups=1000(tryhackme),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
uid=1001(alice) gid=1001(alice) groups=1001(alice)
uid=1003(hatter) gid=1003(hatter) groups=1003(hatter)
uid=1002(rabbit) gid=1002(rabbit) groups=1002(rabbit)

[00;31m[-] It looks like we have some admin users:[00m
uid=102(syslog) gid=106(syslog) groups=106(syslog),4(adm)
uid=1000(tryhackme) gid=1000(tryhackme) groups=1000(tryhackme),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)

[00;31m[-] Contents of /etc/passwd:[00m
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
lxd:x:105:65534::/var/lib/lxd/:/bin/false
uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:109:1::/var/cache/pollinate:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
tryhackme:x:1000:1000:tryhackme:/home/tryhackme:/bin/bash
alice:x:1001:1001:Alice Liddell,,,:/home/alice:/bin/bash
hatter:x:1003:1003:Mad Hatter,,,:/home/hatter:/bin/bash
rabbit:x:1002:1002:White Rabbit,,,:/home/rabbit:/bin/bash

[00;31m[-] Super user account(s):[00m
root

[00;31m[-] Are permissions on /home directories lax:[00m
total 24K
drwxr-xr-x  6 root      root      4.0K May 25  2020 .
drwxr-xr-x 23 root      root      4.0K May 25  2020 ..
drwxr-xr-x  6 alice     alice     4.0K Jun  9 12:08 alice
drwxr-x---  5 hatter    hatter    4.0K Jun  9 13:02 hatter
drwxr-x---  2 rabbit    rabbit    4.0K Jun  9 12:47 rabbit
drwxr-x---  6 tryhackme tryhackme 4.0K May 25  2020 tryhackme

[00;33m### ENVIRONMENTAL #######################################[00m
[00;31m[-] Environment information:[00m
LC_MEASUREMENT=C.UTF-8
SSH_CONNECTION=10.8.90.96 56168 10.10.43.237 22
LESSCLOSE=/usr/bin/lesspipe %s %s
LC_PAPER=C.UTF-8
LC_MONETARY=C.UTF-8
LANG=C.UTF-8
LC_NAME=C.UTF-8
XDG_SESSION_ID=7
USER=hatter
LC_COLLATE=C.UTF-8
PWD=/home/hatter
HOME=/home/hatter
SSH_CLIENT=10.8.90.96 56168 22
LC_ADDRESS=C.UTF-8
LC_NUMERIC=C.UTF-8
SSH_TTY=/dev/pts/2
MAIL=/var/mail/hatter
SHELL=/bin/bash
TERM=xterm-256color
SHLVL=2
LC_TELEPHONE=C.UTF-8
LOGNAME=hatter
XDG_RUNTIME_DIR=/run/user/1003
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
LC_IDENTIFICATION=C.UTF-8
LESSOPEN=| /usr/bin/lesspipe %s
LC_TIME=C.UTF-8
_=/usr/bin/env

[00;31m[-] Path information:[00m
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
drwxr-xr-x 2 root root  4096 May 25  2020 /bin
drwxr-xr-x 2 root root 12288 May 25  2020 /sbin
drwxr-xr-x 2 root root 24576 May 25  2020 /usr/bin
drwxr-xr-x 2 root root  4096 Apr 24  2018 /usr/games
drwxr-xr-x 2 root root  4096 Feb  3  2020 /usr/local/bin
drwxr-xr-x 2 root root  4096 Feb  3  2020 /usr/local/games
drwxr-xr-x 2 root root  4096 Feb  3  2020 /usr/local/sbin
drwxr-xr-x 2 root root  4096 May 25  2020 /usr/sbin

[00;31m[-] Available shells:[00m
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/bin/rbash
/bin/dash
/usr/bin/tmux
/usr/bin/screen

[00;31m[-] Current umask value:[00m
0002
u=rwx,g=rwx,o=rx

[00;31m[-] umask value as specified in /etc/login.defs:[00m
UMASK       022

[00;31m[-] Password and storage information:[00m
PASS_MAX_DAYS   99999
PASS_MIN_DAYS   0
PASS_WARN_AGE   7
ENCRYPT_METHOD SHA512

[00;33m### JOBS/TASKS ##########################################[00m
[00;31m[-] Cron jobs:[00m
-rw-r--r-- 1 root root  722 Nov 16  2017 /etc/crontab

/etc/cron.d:
total 20
drwxr-xr-x  2 root root 4096 Feb  3  2020 .
drwxr-xr-x 91 root root 4096 May 25  2020 ..
-rw-r--r--  1 root root  102 Nov 16  2017 .placeholder
-rw-r--r--  1 root root  589 Jan 14  2020 mdadm
-rw-r--r--  1 root root  191 Feb  3  2020 popularity-contest

/etc/cron.daily:
total 60
drwxr-xr-x  2 root root 4096 May 25  2020 .
drwxr-xr-x 91 root root 4096 May 25  2020 ..
-rw-r--r--  1 root root  102 Nov 16  2017 .placeholder
-rwxr-xr-x  1 root root  376 Nov 20  2017 apport
-rwxr-xr-x  1 root root 1478 Apr 20  2018 apt-compat
-rwxr-xr-x  1 root root  355 Dec 29  2017 bsdmainutils
-rwxr-xr-x  1 root root 1176 Nov  2  2017 dpkg
-rwxr-xr-x  1 root root  372 Aug 21  2017 logrotate
-rwxr-xr-x  1 root root 1065 Apr  7  2018 man-db
-rwxr-xr-x  1 root root  539 Jan 14  2020 mdadm
-rwxr-xr-x  1 root root  538 Mar  1  2018 mlocate
-rwxr-xr-x  1 root root  249 Jan 25  2018 passwd
-rwxr-xr-x  1 root root 3477 Feb 21  2018 popularity-contest
-rwxr-xr-x  1 root root  246 Mar 21  2018 ubuntu-advantage-tools
-rwxr-xr-x  1 root root  214 Nov 12  2018 update-notifier-common

/etc/cron.hourly:
total 12
drwxr-xr-x  2 root root 4096 Feb  3  2020 .
drwxr-xr-x 91 root root 4096 May 25  2020 ..
-rw-r--r--  1 root root  102 Nov 16  2017 .placeholder

/etc/cron.monthly:
total 12
drwxr-xr-x  2 root root 4096 Feb  3  2020 .
drwxr-xr-x 91 root root 4096 May 25  2020 ..
-rw-r--r--  1 root root  102 Nov 16  2017 .placeholder

/etc/cron.weekly:
total 20
drwxr-xr-x  2 root root 4096 Feb  3  2020 .
drwxr-xr-x 91 root root 4096 May 25  2020 ..
-rw-r--r--  1 root root  102 Nov 16  2017 .placeholder
-rwxr-xr-x  1 root root  723 Apr  7  2018 man-db
-rwxr-xr-x  1 root root  211 Nov 12  2018 update-notifier-common

[00;31m[-] Crontab contents:[00m
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#

[00;31m[-] Systemd timers:[00m
NEXT                         LEFT          LAST                         PASSED       UNIT                         ACTIVATES
Thu 2022-06-09 17:24:49 UTC  4h 22min left Thu 2022-06-09 10:54:04 UTC  2h 8min ago  motd-news.timer              motd-news.service
Fri 2022-06-10 01:37:12 UTC  12h left      Thu 2022-06-09 10:54:04 UTC  2h 8min ago  apt-daily.timer              apt-daily.service
Fri 2022-06-10 06:14:11 UTC  17h left      Thu 2022-06-09 10:54:04 UTC  2h 8min ago  apt-daily-upgrade.timer      apt-daily-upgrade.service
Fri 2022-06-10 11:07:50 UTC  22h left      Thu 2022-06-09 11:07:50 UTC  1h 54min ago systemd-tmpfiles-clean.timer systemd-tmpfiles-clean.service
Mon 2022-06-13 00:00:00 UTC  3 days left   Thu 2022-06-09 10:54:04 UTC  2h 8min ago  fstrim.timer                 fstrim.service

5 timers listed.
[2mEnable thorough tests to see inactive timers[00m

[00;33m### NETWORKING  ##########################################[00m
[00;31m[-] Network and IP info:[00m
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 10.10.43.237  netmask 255.255.0.0  broadcast 10.10.255.255
        inet6 fe80::16:ceff:fe4c:901f  prefixlen 64  scopeid 0x20<link>
        ether 02:16:ce:4c:90:1f  txqueuelen 1000  (Ethernet)
        RX packets 207944  bytes 26168259 (26.1 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 193708  bytes 46080553 (46.0 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 222  bytes 18582 (18.5 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 222  bytes 18582 (18.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[00;31m[-] ARP history:[00m
ip-10-10-0-1.eu-west-1.compute.internal (10.10.0.1) at 02:c8:85:b5:5a:aa [ether] on eth0

[00;31m[-] Nameserver(s):[00m
nameserver 127.0.0.53

[00;31m[-] Nameserver(s):[00m
Global
          DNSSEC NTA: 10.in-addr.arpa
                      16.172.in-addr.arpa
                      168.192.in-addr.arpa
                      17.172.in-addr.arpa
                      18.172.in-addr.arpa
                      19.172.in-addr.arpa
                      20.172.in-addr.arpa
                      21.172.in-addr.arpa
                      22.172.in-addr.arpa
                      23.172.in-addr.arpa
                      24.172.in-addr.arpa
                      25.172.in-addr.arpa
                      26.172.in-addr.arpa
                      27.172.in-addr.arpa
                      28.172.in-addr.arpa
                      29.172.in-addr.arpa
                      30.172.in-addr.arpa
                      31.172.in-addr.arpa
                      corp
                      d.f.ip6.arpa
                      home
                      internal
                      intranet
                      lan
                      local
                      private
                      test

Link 2 (eth0)
      Current Scopes: DNS
       LLMNR setting: yes
MulticastDNS setting: no
      DNSSEC setting: no
    DNSSEC supported: no
         DNS Servers: 10.0.0.2
          DNS Domain: eu-west-1.compute.internal

[00;31m[-] Default route:[00m
default         ip-10-10-0-1.eu 0.0.0.0         UG    100    0        0 eth0

[00;31m[-] Listening TCP:[00m
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   

[00;31m[-] Listening UDP:[00m
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 10.10.43.237:68         0.0.0.0:*                           -                   

[00;33m### SERVICES #############################################[00m
[00;31m[-] Running processes:[00m
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.3  1.7 159556  8812 ?        Ss   10:52   0:28 /sbin/init maybe-ubiquity
root         2  0.0  0.0      0     0 ?        S    10:52   0:00 [kthreadd]
root         4  0.0  0.0      0     0 ?        I<   10:52   0:00 [kworker/0:0H]
root         6  0.0  0.0      0     0 ?        I<   10:52   0:00 [mm_percpu_wq]
root         7  0.0  0.0      0     0 ?        S    10:52   0:00 [ksoftirqd/0]
root         8  0.0  0.0      0     0 ?        I    10:52   0:00 [rcu_sched]
root         9  0.0  0.0      0     0 ?        I    10:52   0:00 [rcu_bh]
root        10  0.0  0.0      0     0 ?        S    10:52   0:00 [migration/0]
root        11  0.0  0.0      0     0 ?        S    10:52   0:00 [watchdog/0]
root        12  0.0  0.0      0     0 ?        S    10:52   0:00 [cpuhp/0]
root        13  0.0  0.0      0     0 ?        S    10:52   0:00 [kdevtmpfs]
root        14  0.0  0.0      0     0 ?        I<   10:52   0:00 [netns]
root        15  0.0  0.0      0     0 ?        S    10:52   0:00 [rcu_tasks_kthre]
root        16  0.0  0.0      0     0 ?        S    10:52   0:00 [kauditd]
root        17  0.0  0.0      0     0 ?        S    10:52   0:00 [xenbus]
root        18  0.0  0.0      0     0 ?        S    10:52   0:00 [xenwatch]
root        20  0.0  0.0      0     0 ?        S    10:52   0:00 [khungtaskd]
root        21  0.0  0.0      0     0 ?        S    10:52   0:00 [oom_reaper]
root        22  0.0  0.0      0     0 ?        I<   10:52   0:00 [writeback]
root        23  0.0  0.0      0     0 ?        S    10:52   0:00 [kcompactd0]
root        24  0.0  0.0      0     0 ?        SN   10:52   0:00 [ksmd]
root        25  0.0  0.0      0     0 ?        I<   10:52   0:00 [crypto]
root        26  0.0  0.0      0     0 ?        I<   10:52   0:00 [kintegrityd]
root        27  0.0  0.0      0     0 ?        I<   10:52   0:00 [kblockd]
root        28  0.0  0.0      0     0 ?        I<   10:52   0:00 [ata_sff]
root        29  0.0  0.0      0     0 ?        I<   10:52   0:00 [md]
root        30  0.0  0.0      0     0 ?        I<   10:52   0:00 [edac-poller]
root        31  0.0  0.0      0     0 ?        I<   10:52   0:00 [devfreq_wq]
root        32  0.0  0.0      0     0 ?        I<   10:52   0:00 [watchdogd]
root        35  0.0  0.0      0     0 ?        S    10:52   0:00 [kswapd0]
root        36  0.0  0.0      0     0 ?        I<   10:52   0:00 [kworker/u31:0]
root        37  0.0  0.0      0     0 ?        S    10:52   0:00 [ecryptfs-kthrea]
root        79  0.0  0.0      0     0 ?        I<   10:52   0:00 [kthrotld]
root        80  0.0  0.0      0     0 ?        I<   10:52   0:00 [acpi_thermal_pm]
root        81  0.0  0.0      0     0 ?        S    10:52   0:00 [scsi_eh_0]
root        82  0.0  0.0      0     0 ?        I<   10:52   0:00 [scsi_tmf_0]
root        83  0.0  0.0      0     0 ?        S    10:52   0:00 [scsi_eh_1]
root        84  0.0  0.0      0     0 ?        I<   10:52   0:00 [scsi_tmf_1]
root        90  0.0  0.0      0     0 ?        I<   10:52   0:00 [ipv6_addrconf]
root        99  0.0  0.0      0     0 ?        I<   10:52   0:00 [kstrp]
root       117  0.0  0.0      0     0 ?        I<   10:52   0:00 [charger_manager]
root       156  0.0  0.0      0     0 ?        I<   10:52   0:00 [kworker/0:1H]
root       157  0.0  0.0      0     0 ?        I    10:52   0:00 [kworker/0:2]
root       170  0.0  0.0      0     0 ?        I<   10:52   0:00 [ttm_swap]
root       263  0.0  0.0      0     0 ?        I<   10:52   0:00 [raid5wq]
root       313  0.0  0.0      0     0 ?        S    10:52   0:00 [jbd2/xvda2-8]
root       314  0.0  0.0      0     0 ?        I<   10:52   0:00 [ext4-rsv-conver]
root       373  0.0  4.9 144036 24272 ?        S<s  10:53   0:03 /lib/systemd/systemd-journald
root       396  0.0  0.0      0     0 ?        I<   10:53   0:00 [iscsi_eh]
root       398  0.0  0.0      0     0 ?        I<   10:53   0:00 [ib-comp-wq]
root       399  0.0  0.0      0     0 ?        I<   10:53   0:00 [ib-comp-unb-wq]
root       400  0.0  0.0      0     0 ?        I<   10:53   0:00 [ib_mcast]
root       401  0.0  0.3  97708  1668 ?        Ss   10:53   0:00 /sbin/lvmetad -f
root       402  0.0  0.0      0     0 ?        I<   10:53   0:00 [ib_nl_sa_wq]
root       403  0.0  0.7  45356  3916 ?        Ss   10:53   0:04 /lib/systemd/systemd-udevd
root       405  0.0  0.0      0     0 ?        I<   10:53   0:00 [rdma_cm]
systemd+   485  0.0  0.6 141936  3068 ?        Ssl  10:53   0:00 /lib/systemd/systemd-timesyncd
systemd+   575  0.0  1.0  80060  5044 ?        Ss   10:53   0:00 /lib/systemd/systemd-networkd
systemd+   590  0.0  0.9  70640  4784 ?        Ss   10:53   0:00 /lib/systemd/systemd-resolved
syslog     671  0.0  0.8 263036  4200 ?        Ssl  10:54   0:00 /usr/sbin/rsyslogd -n
daemon     672  0.0  0.4  28332  2124 ?        Ss   10:54   0:00 /usr/sbin/atd -f
root       677  0.0  2.7 169188 13512 ?        Ssl  10:54   0:01 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
root       679  0.0  0.4 637008  2392 ?        Ssl  10:54   0:02 /usr/bin/lxcfs /var/lib/lxcfs/
root       681  0.0  1.2  70584  6036 ?        Ss   10:54   0:00 /lib/systemd/systemd-logind
message+   688  0.0  0.9  50136  4436 ?        Ss   10:54   0:00 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
root       698  0.0  0.5  30104  2852 ?        Ss   10:54   0:00 /usr/sbin/cron -f
root       700  0.0  1.3 286356  6712 ?        Ssl  10:54   0:00 /usr/lib/accountsservice/accounts-daemon
tryhack+   701  0.3  1.6 409720  7856 ?        Ssl  10:54   0:26 /home/tryhackme/server -p 80
root       711  0.0  0.4  14768  2144 ttyS0    Ss+  10:54   0:00 /sbin/agetty -o -p -- \u --keep-baud 115200,38400,9600 ttyS0 vt220
root       715  0.0  0.3  13244  1692 tty1     Ss+  10:54   0:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
root       724  0.0  1.2  72300  6100 ?        Ss   10:54   0:00 /usr/sbin/sshd -D
root       727  0.0  1.3 291388  6656 ?        Ssl  10:54   0:00 /usr/lib/policykit-1/polkitd --no-debug
root       735  0.0  3.0 186032 14868 ?        Ssl  10:54   0:01 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root      1063  0.0  1.4 107984  6992 ?        Ss   11:37   0:00 sshd: alice [priv]
alice     1068  0.0  1.4  76676  7028 ?        Ss   11:37   0:00 /lib/systemd/systemd --user
alice     1070  0.0  0.4 193536  2268 ?        S    11:37   0:00 (sd-pam)
alice     1204  0.0  0.8 108116  4168 ?        S    11:37   0:00 sshd: alice@pts/0
alice     1205  0.0  0.8  21612  4036 pts/0    Ss   11:37   0:00 -bash
root      1304  0.0  0.0      0     0 ?        I    11:48   0:00 [kworker/0:1]
root      1514  0.0  0.7  62324  3792 pts/0    S+   12:08   0:00 sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
rabbit    1515  0.0  1.3  39280  6700 pts/0    S+   12:08   0:00 /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
rabbit    1516  0.0  1.0  21600  4912 pts/1    Ss+  12:08   0:00 /bin/bash
root      1855  0.0  0.0      0     0 ?        I    12:50   0:00 [kworker/u30:1]
root      1877  0.0  1.4 107984  7120 ?        Ss   12:52   0:00 sshd: hatter [priv]
hatter    1879  0.0  1.5  76652  7492 ?        Ss   12:52   0:00 /lib/systemd/systemd --user
hatter    1880  0.0  0.4 193536  2268 ?        S    12:52   0:00 (sd-pam)
hatter    1956  0.0  0.8 108116  4172 ?        S    12:52   0:00 sshd: hatter@pts/2
hatter    1957  0.0  1.0  21576  5196 pts/2    Ss   12:52   0:00 -bash
root      1991  0.0  0.0      0     0 ?        I    12:57   0:00 [kworker/u30:2]
hatter    2487  0.0  0.7  12592  3924 pts/2    S+   13:02   0:00 /bin/bash ./linEnum.sh
hatter    2488  0.1  0.6  12592  2952 pts/2    S+   13:02   0:00 /bin/bash ./linEnum.sh
hatter    2489  0.0  0.1   6284   824 pts/2    S+   13:02   0:00 tee -a
hatter    2677  0.0  0.5  12592  2760 pts/2    S+   13:02   0:00 /bin/bash ./linEnum.sh
hatter    2678  0.0  0.7  38448  3520 pts/2    R+   13:02   0:00 ps aux

[00;31m[-] Process binaries and associated permissions (from above list):[00m
1.1M -rwxr-xr-x 1 root root 1.1M Jun  6  2019 /bin/bash
1.6M -rwxr-xr-x 1 root root 1.6M Apr 20  2020 /lib/systemd/systemd
128K -rwxr-xr-x 1 root root 127K Apr 20  2020 /lib/systemd/systemd-journald
216K -rwxr-xr-x 1 root root 215K Apr 20  2020 /lib/systemd/systemd-logind
1.6M -rwxr-xr-x 1 root root 1.6M Apr 20  2020 /lib/systemd/systemd-networkd
372K -rwxr-xr-x 1 root root 371K Apr 20  2020 /lib/systemd/systemd-resolved
 40K -rwxr-xr-x 1 root root  39K Apr 20  2020 /lib/systemd/systemd-timesyncd
572K -rwxr-xr-x 1 root root 571K Apr 20  2020 /lib/systemd/systemd-udevd
 56K -rwxr-xr-x 1 root root  56K Mar  5  2020 /sbin/agetty
   0 lrwxrwxrwx 1 root root   20 Apr 20  2020 /sbin/init -> /lib/systemd/systemd
 84K -rwxr-xr-x 1 root root  83K Jan 23  2020 /sbin/lvmetad
232K -rwxr-xr-x 1 root root 232K Jun 10  2019 /usr/bin/dbus-daemon
 20K -rwxr-xr-x 1 root root  19K Mar 31  2020 /usr/bin/lxcfs
   0 lrwxrwxrwx 1 root root    9 Oct 25  2018 /usr/bin/python3 -> python3.6
4.4M -rwxr-xr-x 2 root root 4.4M Apr 18  2020 /usr/bin/python3.6
180K -rwxr-xr-x 1 root root 179K Dec 18  2017 /usr/lib/accountsservice/accounts-daemon
 16K -rwxr-xr-x 1 root root  15K Mar 27  2019 /usr/lib/policykit-1/polkitd
 28K -rwxr-xr-x 1 root root  27K Feb 20  2018 /usr/sbin/atd
 48K -rwxr-xr-x 1 root root  47K Nov 16  2017 /usr/sbin/cron
668K -rwxr-xr-x 1 root root 665K Apr 24  2018 /usr/sbin/rsyslogd
772K -rwxr-xr-x 1 root root 769K Mar  4  2019 /usr/sbin/sshd

[00;31m[-] /etc/init.d/ binary permissions:[00m
total 168
drwxr-xr-x  2 root root 4096 May 25  2020 .
drwxr-xr-x 91 root root 4096 May 25  2020 ..
-rwxr-xr-x  1 root root 2269 Apr 22  2017 acpid
-rwxr-xr-x  1 root root 4335 Mar 22  2018 apparmor
-rwxr-xr-x  1 root root 2805 Feb 27  2020 apport
-rwxr-xr-x  1 root root 1071 Aug 21  2015 atd
-rwxr-xr-x  1 root root 1232 Apr 19  2018 console-setup.sh
-rwxr-xr-x  1 root root 3049 Nov 16  2017 cron
-rwxr-xr-x  1 root root  937 Mar 18  2018 cryptdisks
-rwxr-xr-x  1 root root  978 Mar 18  2018 cryptdisks-early
-rwxr-xr-x  1 root root 2813 Nov 15  2017 dbus
-rwxr-xr-x  1 root root 4489 Jun 28  2018 ebtables
-rwxr-xr-x  1 root root  985 Mar 11  2020 grub-common
-rwxr-xr-x  1 root root 3809 Feb 14  2018 hwclock.sh
-rwxr-xr-x  1 root root 2444 Oct 25  2017 irqbalance
-rwxr-xr-x  1 root root 1503 Dec 12  2018 iscsid
-rwxr-xr-x  1 root root 1479 Feb 15  2018 keyboard-setup.sh
-rwxr-xr-x  1 root root 2044 Aug 15  2017 kmod
-rwxr-xr-x  1 root root  695 Dec  3  2017 lvm2
-rwxr-xr-x  1 root root  571 Dec  3  2017 lvm2-lvmetad
-rwxr-xr-x  1 root root  586 Dec  3  2017 lvm2-lvmpolld
-rwxr-xr-x  1 root root 2378 Nov 23  2018 lxcfs
-rwxr-xr-x  1 root root 2240 Nov 23  2018 lxd
-rwxr-xr-x  1 root root 2653 Jan 14  2020 mdadm
-rwxr-xr-x  1 root root 1249 Oct 22  2019 mdadm-waitidle
-rwxr-xr-x  1 root root 2503 Dec 12  2018 open-iscsi
-rwxr-xr-x  1 root root 1846 Dec  9  2019 open-vm-tools
-rwxr-xr-x  1 root root 1366 Apr  4  2019 plymouth
-rwxr-xr-x  1 root root  752 Apr  4  2019 plymouth-log
-rwxr-xr-x  1 root root 1191 Jan 17  2018 procps
-rwxr-xr-x  1 root root 4355 Dec 13  2017 rsync
-rwxr-xr-x  1 root root 2864 Jan 14  2018 rsyslog
-rwxr-xr-x  1 root root 1222 May 21  2017 screen-cleanup
-rwxr-xr-x  1 root root 3837 Jan 25  2018 ssh
-rwxr-xr-x  1 root root 5974 Apr 20  2018 udev
-rwxr-xr-x  1 root root 2083 Aug 15  2017 ufw
-rwxr-xr-x  1 root root 1391 Nov 25  2019 unattended-upgrades
-rwxr-xr-x  1 root root 1306 Jan  8  2020 uuidd

[00;31m[-] /lib/systemd/* config file permissions:[00m
/lib/systemd/:
total 7.3M
drwxr-xr-x 22 root root  36K May 25  2020 system
drwxr-xr-x  2 root root 4.0K May 25  2020 system-generators
drwxr-xr-x  2 root root 4.0K May 25  2020 system-sleep
drwxr-xr-x  2 root root 4.0K May 25  2020 network
drwxr-xr-x  2 root root 4.0K May 25  2020 system-preset
-rw-r--r--  1 root root 2.3M Apr 20  2020 libsystemd-shared-237.so
-rw-r--r--  1 root root  699 Apr 20  2020 resolv.conf
-rwxr-xr-x  1 root root 1.3K Apr 20  2020 set-cpufreq
-rwxr-xr-x  1 root root 1.6M Apr 20  2020 systemd
-rwxr-xr-x  1 root root 6.0K Apr 20  2020 systemd-ac-power
-rwxr-xr-x  1 root root  18K Apr 20  2020 systemd-backlight
-rwxr-xr-x  1 root root  11K Apr 20  2020 systemd-binfmt
-rwxr-xr-x  1 root root  10K Apr 20  2020 systemd-cgroups-agent
-rwxr-xr-x  1 root root  27K Apr 20  2020 systemd-cryptsetup
-rwxr-xr-x  1 root root  15K Apr 20  2020 systemd-dissect
-rwxr-xr-x  1 root root  18K Apr 20  2020 systemd-fsck
-rwxr-xr-x  1 root root  23K Apr 20  2020 systemd-fsckd
-rwxr-xr-x  1 root root  19K Apr 20  2020 systemd-growfs
-rwxr-xr-x  1 root root  10K Apr 20  2020 systemd-hibernate-resume
-rwxr-xr-x  1 root root  23K Apr 20  2020 systemd-hostnamed
-rwxr-xr-x  1 root root  15K Apr 20  2020 systemd-initctl
-rwxr-xr-x  1 root root 127K Apr 20  2020 systemd-journald
-rwxr-xr-x  1 root root  35K Apr 20  2020 systemd-localed
-rwxr-xr-x  1 root root 215K Apr 20  2020 systemd-logind
-rwxr-xr-x  1 root root  10K Apr 20  2020 systemd-makefs
-rwxr-xr-x  1 root root  15K Apr 20  2020 systemd-modules-load
-rwxr-xr-x  1 root root 1.6M Apr 20  2020 systemd-networkd
-rwxr-xr-x  1 root root  19K Apr 20  2020 systemd-networkd-wait-online
-rwxr-xr-x  1 root root  11K Apr 20  2020 systemd-quotacheck
-rwxr-xr-x  1 root root  10K Apr 20  2020 systemd-random-seed
-rwxr-xr-x  1 root root  15K Apr 20  2020 systemd-remount-fs
-rwxr-xr-x  1 root root  10K Apr 20  2020 systemd-reply-password
-rwxr-xr-x  1 root root 371K Apr 20  2020 systemd-resolved
-rwxr-xr-x  1 root root  19K Apr 20  2020 systemd-rfkill
-rwxr-xr-x  1 root root  43K Apr 20  2020 systemd-shutdown
-rwxr-xr-x  1 root root  19K Apr 20  2020 systemd-sleep
-rwxr-xr-x  1 root root  23K Apr 20  2020 systemd-socket-proxyd
-rwxr-xr-x  1 root root  11K Apr 20  2020 systemd-sulogin-shell
-rwxr-xr-x  1 root root  15K Apr 20  2020 systemd-sysctl
-rwxr-xr-x  1 root root  27K Apr 20  2020 systemd-timedated
-rwxr-xr-x  1 root root  39K Apr 20  2020 systemd-timesyncd
-rwxr-xr-x  1 root root 571K Apr 20  2020 systemd-udevd
-rwxr-xr-x  1 root root  15K Apr 20  2020 systemd-update-utmp
-rwxr-xr-x  1 root root  10K Apr 20  2020 systemd-user-sessions
-rwxr-xr-x  1 root root  10K Apr 20  2020 systemd-veritysetup
-rwxr-xr-x  1 root root  10K Apr 20  2020 systemd-volatile-root
-rwxr-xr-x  1 root root 1.3K Apr 20  2020 systemd-sysv-install
drwxr-xr-x  2 root root 4.0K Feb  3  2020 system-shutdown

/lib/systemd/system:
total 972K
drwxr-xr-x 2 root root 4.0K May 25  2020 sockets.target.wants
drwxr-xr-x 2 root root 4.0K May 25  2020 sysinit.target.wants
drwxr-xr-x 2 root root 4.0K May 25  2020 getty.target.wants
drwxr-xr-x 2 root root 4.0K May 25  2020 graphical.target.wants
drwxr-xr-x 2 root root 4.0K May 25  2020 local-fs.target.wants
drwxr-xr-x 2 root root 4.0K May 25  2020 multi-user.target.wants
drwxr-xr-x 2 root root 4.0K May 25  2020 rescue.target.wants
drwxr-xr-x 2 root root 4.0K May 25  2020 timers.target.wants
drwxr-xr-x 2 root root 4.0K May 25  2020 rc-local.service.d
drwxr-xr-x 2 root root 4.0K May 25  2020 user@.service.d
lrwxrwxrwx 1 root root   14 Apr 20  2020 autovt@.service -> getty@.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 bootlogd.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 bootlogs.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 bootmisc.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 checkfs.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 checkroot-bootclean.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 checkroot.service -> /dev/null
-rw-r--r-- 1 root root 1.1K Apr 20  2020 console-getty.service
-rw-r--r-- 1 root root 1.3K Apr 20  2020 container-getty@.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 cryptdisks-early.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 cryptdisks.service -> /dev/null
lrwxrwxrwx 1 root root   13 Apr 20  2020 ctrl-alt-del.target -> reboot.target
lrwxrwxrwx 1 root root   25 Apr 20  2020 dbus-org.freedesktop.hostname1.service -> systemd-hostnamed.service
lrwxrwxrwx 1 root root   23 Apr 20  2020 dbus-org.freedesktop.locale1.service -> systemd-localed.service
lrwxrwxrwx 1 root root   22 Apr 20  2020 dbus-org.freedesktop.login1.service -> systemd-logind.service
lrwxrwxrwx 1 root root   25 Apr 20  2020 dbus-org.freedesktop.timedate1.service -> systemd-timedated.service
-rw-r--r-- 1 root root 1.1K Apr 20  2020 debug-shell.service
lrwxrwxrwx 1 root root   16 Apr 20  2020 default.target -> graphical.target
-rw-r--r-- 1 root root  797 Apr 20  2020 emergency.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 fuse.service -> /dev/null
-rw-r--r-- 1 root root 2.0K Apr 20  2020 getty@.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 halt.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 hostname.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 hwclock.service -> /dev/null
-rw-r--r-- 1 root root  670 Apr 20  2020 initrd-cleanup.service
-rw-r--r-- 1 root root  830 Apr 20  2020 initrd-parse-etc.service
-rw-r--r-- 1 root root  589 Apr 20  2020 initrd-switch-root.service
-rw-r--r-- 1 root root  704 Apr 20  2020 initrd-udevadm-cleanup-db.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 killprocs.service -> /dev/null
-rw-r--r-- 1 root root  717 Apr 20  2020 kmod-static-nodes.service
lrwxrwxrwx 1 root root   28 Apr 20  2020 kmod.service -> systemd-modules-load.service
lrwxrwxrwx 1 root root   28 Apr 20  2020 module-init-tools.service -> systemd-modules-load.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 motd.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 mountall-bootclean.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 mountall.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 mountdevsubfs.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 mountkernfs.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 mountnfs-bootclean.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 mountnfs.service -> /dev/null
lrwxrwxrwx 1 root root   22 Apr 20  2020 procps.service -> systemd-sysctl.service
-rw-r--r-- 1 root root  609 Apr 20  2020 quotaon.service
-rw-r--r-- 1 root root  716 Apr 20  2020 rc-local.service
lrwxrwxrwx 1 root root   16 Apr 20  2020 rc.local.service -> rc-local.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 rc.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 rcS.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 reboot.service -> /dev/null
-rw-r--r-- 1 root root  788 Apr 20  2020 rescue.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 rmnologin.service -> /dev/null
lrwxrwxrwx 1 root root   15 Apr 20  2020 runlevel0.target -> poweroff.target
lrwxrwxrwx 1 root root   13 Apr 20  2020 runlevel1.target -> rescue.target
lrwxrwxrwx 1 root root   17 Apr 20  2020 runlevel2.target -> multi-user.target
lrwxrwxrwx 1 root root   17 Apr 20  2020 runlevel3.target -> multi-user.target
lrwxrwxrwx 1 root root   17 Apr 20  2020 runlevel4.target -> multi-user.target
lrwxrwxrwx 1 root root   16 Apr 20  2020 runlevel5.target -> graphical.target
lrwxrwxrwx 1 root root   13 Apr 20  2020 runlevel6.target -> reboot.target
lrwxrwxrwx 1 root root    9 Apr 20  2020 sendsigs.service -> /dev/null
-rw-r--r-- 1 root root 1.5K Apr 20  2020 serial-getty@.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 single.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 stop-bootlogd-single.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 stop-bootlogd.service -> /dev/null
-rw-r--r-- 1 root root  554 Apr 20  2020 suspend-then-hibernate.target
-rw-r--r-- 1 root root 1.4K Apr 20  2020 system-update-cleanup.service
-rw-r--r-- 1 root root  724 Apr 20  2020 systemd-ask-password-console.service
-rw-r--r-- 1 root root  752 Apr 20  2020 systemd-ask-password-wall.service
-rw-r--r-- 1 root root  752 Apr 20  2020 systemd-backlight@.service
-rw-r--r-- 1 root root  999 Apr 20  2020 systemd-binfmt.service
-rw-r--r-- 1 root root  537 Apr 20  2020 systemd-exit.service
-rw-r--r-- 1 root root  714 Apr 20  2020 systemd-fsck-root.service
-rw-r--r-- 1 root root  715 Apr 20  2020 systemd-fsck@.service
-rw-r--r-- 1 root root  551 Apr 20  2020 systemd-fsckd.service
-rw-r--r-- 1 root root  540 Apr 20  2020 systemd-fsckd.socket
-rw-r--r-- 1 root root  584 Apr 20  2020 systemd-halt.service
-rw-r--r-- 1 root root  671 Apr 20  2020 systemd-hibernate-resume@.service
-rw-r--r-- 1 root root  541 Apr 20  2020 systemd-hibernate.service
-rw-r--r-- 1 root root 1.2K Apr 20  2020 systemd-hostnamed.service
-rw-r--r-- 1 root root  818 Apr 20  2020 systemd-hwdb-update.service
-rw-r--r-- 1 root root  559 Apr 20  2020 systemd-hybrid-sleep.service
-rw-r--r-- 1 root root  551 Apr 20  2020 systemd-initctl.service
-rw-r--r-- 1 root root  771 Apr 20  2020 systemd-journal-flush.service
-rw-r--r-- 1 root root  686 Apr 20  2020 systemd-journald-audit.socket
-rw-r--r-- 1 root root 1.6K Apr 20  2020 systemd-journald.service
-rw-r--r-- 1 root root  597 Apr 20  2020 systemd-kexec.service
-rw-r--r-- 1 root root 1.2K Apr 20  2020 systemd-localed.service
-rw-r--r-- 1 root root 1.5K Apr 20  2020 systemd-logind.service
-rw-r--r-- 1 root root  733 Apr 20  2020 systemd-machine-id-commit.service
-rw-r--r-- 1 root root 1007 Apr 20  2020 systemd-modules-load.service
-rw-r--r-- 1 root root  740 Apr 20  2020 systemd-networkd-wait-online.service
-rw-r--r-- 1 root root 1.9K Apr 20  2020 systemd-networkd.service
-rw-r--r-- 1 root root  593 Apr 20  2020 systemd-poweroff.service
-rw-r--r-- 1 root root  655 Apr 20  2020 systemd-quotacheck.service
-rw-r--r-- 1 root root  792 Apr 20  2020 systemd-random-seed.service
-rw-r--r-- 1 root root  588 Apr 20  2020 systemd-reboot.service
-rw-r--r-- 1 root root  833 Apr 20  2020 systemd-remount-fs.service
-rw-r--r-- 1 root root 1.7K Apr 20  2020 systemd-resolved.service
-rw-r--r-- 1 root root  724 Apr 20  2020 systemd-rfkill.service
-rw-r--r-- 1 root root  573 Apr 20  2020 systemd-suspend-then-hibernate.service
-rw-r--r-- 1 root root  537 Apr 20  2020 systemd-suspend.service
-rw-r--r-- 1 root root  693 Apr 20  2020 systemd-sysctl.service
-rw-r--r-- 1 root root 1.1K Apr 20  2020 systemd-timedated.service
-rw-r--r-- 1 root root 1.4K Apr 20  2020 systemd-timesyncd.service
-rw-r--r-- 1 root root  659 Apr 20  2020 systemd-tmpfiles-clean.service
-rw-r--r-- 1 root root  764 Apr 20  2020 systemd-tmpfiles-setup-dev.service
-rw-r--r-- 1 root root  744 Apr 20  2020 systemd-tmpfiles-setup.service
-rw-r--r-- 1 root root  863 Apr 20  2020 systemd-udev-settle.service
-rw-r--r-- 1 root root  755 Apr 20  2020 systemd-udev-trigger.service
-rw-r--r-- 1 root root 1006 Apr 20  2020 systemd-udevd.service
-rw-r--r-- 1 root root  797 Apr 20  2020 systemd-update-utmp-runlevel.service
-rw-r--r-- 1 root root  794 Apr 20  2020 systemd-update-utmp.service
-rw-r--r-- 1 root root  628 Apr 20  2020 systemd-user-sessions.service
-rw-r--r-- 1 root root  690 Apr 20  2020 systemd-volatile-root.service
lrwxrwxrwx 1 root root   21 Apr 20  2020 udev.service -> systemd-udevd.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 umountfs.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 umountnfs.service -> /dev/null
lrwxrwxrwx 1 root root    9 Apr 20  2020 umountroot.service -> /dev/null
lrwxrwxrwx 1 root root   27 Apr 20  2020 urandom.service -> systemd-random-seed.service
-rw-r--r-- 1 root root  593 Apr 20  2020 user@.service
lrwxrwxrwx 1 root root    9 Apr 20  2020 x11-common.service -> /dev/null
-rw-r--r-- 1 root root  362 Apr 20  2020 ondemand.service
-rw-r--r-- 1 root root  342 Apr 18  2020 getty-static.service
-rw-r--r-- 1 root root  311 Mar 31  2020 lxcfs.service
-rw-r--r-- 1 root root  498 Mar 25  2020 open-vm-tools.service
-rw-r--r-- 1 root root  127 Mar  5  2020 fstrim.service
-rw-r--r-- 1 root root  205 Mar  5  2020 fstrim.timer
-rw-r--r-- 1 root root  189 Mar  5  2020 uuidd.service
-rw-r--r-- 1 root root  126 Mar  5  2020 uuidd.socket
-rw-r--r-- 1 root root  372 Feb 17  2020 unattended-upgrades.service
lrwxrwxrwx 1 root root    9 Feb  3  2020 screen-cleanup.service -> /dev/null
drwxr-xr-x 2 root root 4.0K Feb  3  2020 halt.target.wants
drwxr-xr-x 2 root root 4.0K Feb  3  2020 initrd-switch-root.target.wants
drwxr-xr-x 2 root root 4.0K Feb  3  2020 kexec.target.wants
drwxr-xr-x 2 root root 4.0K Feb  3  2020 poweroff.target.wants
drwxr-xr-x 2 root root 4.0K Feb  3  2020 reboot.target.wants
lrwxrwxrwx 1 root root    9 Jan 31  2020 sudo.service -> /dev/null
-rw-r--r-- 1 root root  383 Jan 23  2020 blk-availability.service
-rw-r--r-- 1 root root  341 Jan 23  2020 dm-event.service
-rw-r--r-- 1 root root  248 Jan 23  2020 dm-event.socket
-rw-r--r-- 1 root root  345 Jan 23  2020 lvm2-lvmetad.service
-rw-r--r-- 1 root root  215 Jan 23  2020 lvm2-lvmetad.socket
-rw-r--r-- 1 root root  300 Jan 23  2020 lvm2-lvmpolld.service
-rw-r--r-- 1 root root  213 Jan 23  2020 lvm2-lvmpolld.socket
-rw-r--r-- 1 root root  693 Jan 23  2020 lvm2-monitor.service
-rw-r--r-- 1 root root  403 Jan 23  2020 lvm2-pvscan@.service
lrwxrwxrwx 1 root root    9 Jan 23  2020 lvm2.service -> /dev/null
-rw-r--r-- 1 root root  418 Jan 15  2020 cloud-config.service
-rw-r--r-- 1 root root  482 Jan 15  2020 cloud-final.service
-rw-r--r-- 1 root root  580 Jan 15  2020 cloud-init-local.service
-rw-r--r-- 1 root root  642 Jan 15  2020 cloud-init.service
-rw-r--r-- 1 root root  536 Jan 14  2020 cloud-config.target
-rw-r--r-- 1 root root  256 Jan 14  2020 cloud-init.target
-rw-r--r-- 1 root root  481 Jan 14  2020 mdadm-grow-continue@.service
-rw-r--r-- 1 root root  210 Jan 14  2020 mdadm-last-resort@.service
-rw-r--r-- 1 root root  179 Jan 14  2020 mdadm-last-resort@.timer
-rw-r--r-- 1 root root  670 Jan 14  2020 mdadm-shutdown.service
lrwxrwxrwx 1 root root    9 Jan 14  2020 mdadm-waitidle.service -> /dev/null
lrwxrwxrwx 1 root root    9 Jan 14  2020 mdadm.service -> /dev/null
-rw-r--r-- 1 root root 1.1K Jan 14  2020 mdmon@.service
-rw-r--r-- 1 root root  388 Jan 14  2020 mdmonitor.service
-rw-r--r-- 1 root root  408 Dec  9  2019 vgauth.service
-rw-r--r-- 1 root root  463 Nov 26  2019 iscsid.service
-rw-r--r-- 1 root root  175 Nov 26  2019 iscsid.socket
-rw-r--r-- 1 root root  987 Nov 26  2019 open-iscsi.service
-rw-r--r-- 1 root root  212 Nov 11  2019 apport-autoreport.path
-rw-r--r-- 1 root root  242 Nov 11  2019 apport-autoreport.service
-rw-r--r-- 1 root root  246 Nov 11  2019 apport-forward.socket
-rw-r--r-- 1 root root  142 Nov 11  2019 apport-forward@.service
-rw-r--r-- 1 root root  173 Sep 27  2019 motd-news.service
-rw-r--r-- 1 root root  161 Sep 27  2019 motd-news.timer
-rw-r--r-- 1 root root  238 Sep  3  2019 apt-daily-upgrade.service
-rw-r--r-- 1 root root  184 Sep  3  2019 apt-daily-upgrade.timer
-rw-r--r-- 1 root root  326 Sep  3  2019 apt-daily.service
-rw-r--r-- 1 root root  156 Sep  3  2019 apt-daily.timer
-rw-r--r-- 1 root root  254 Aug 15  2019 thermald.service
-rw-r--r-- 1 root root  505 Jun 10  2019 dbus.service
-rw-r--r-- 1 root root  106 Jun 10  2019 dbus.socket
-rw-r--r-- 1 root root  312 Apr 23  2019 console-setup.service
-rw-r--r-- 1 root root  287 Apr 23  2019 keyboard-setup.service
-rw-r--r-- 1 root root  330 Apr 23  2019 setvtrgb.service
-rw-r--r-- 1 root root  250 Apr  9  2019 ureadahead-stop.service
-rw-r--r-- 1 root root  242 Apr  9  2019 ureadahead-stop.timer
-rw-r--r-- 1 root root  404 Apr  9  2019 ureadahead.service
-rw-r--r-- 1 root root  412 Apr  4  2019 plymouth-halt.service
-rw-r--r-- 1 root root  426 Apr  4  2019 plymouth-kexec.service
lrwxrwxrwx 1 root root   27 Apr  4  2019 plymouth-log.service -> plymouth-read-write.service
-rw-r--r-- 1 root root  421 Apr  4  2019 plymouth-poweroff.service
-rw-r--r-- 1 root root  200 Apr  4  2019 plymouth-quit-wait.service
-rw-r--r-- 1 root root  194 Apr  4  2019 plymouth-quit.service
-rw-r--r-- 1 root root  244 Apr  4  2019 plymouth-read-write.service
-rw-r--r-- 1 root root  416 Apr  4  2019 plymouth-reboot.service
-rw-r--r-- 1 root root  532 Apr  4  2019 plymouth-start.service
-rw-r--r-- 1 root root  291 Apr  4  2019 plymouth-switch-root.service
lrwxrwxrwx 1 root root   21 Apr  4  2019 plymouth.service -> plymouth-quit.service
-rw-r--r-- 1 root root  490 Apr  4  2019 systemd-ask-password-plymouth.path
-rw-r--r-- 1 root root  467 Apr  4  2019 systemd-ask-password-plymouth.service
-rw-r--r-- 1 root root  368 Jan  9  2019 irqbalance.service
-rw-r--r-- 1 root root  605 Nov 23  2018 lxd.service
-rw-r--r-- 1 root root  320 Nov 23  2018 lxd-containers.service
-rw-r--r-- 1 root root  197 Nov 23  2018 lxd.socket
-rw-r--r-- 1 root root  618 Oct 15  2018 friendly-recovery.service
-rw-r--r-- 1 root root  172 Oct 15  2018 friendly-recovery.target
-rw-r--r-- 1 root root  258 Oct 15  2018 networkd-dispatcher.service
-rw-r--r-- 1 root root  456 Jun 28  2018 ebtables.service
-rw-r--r-- 1 root root  309 May 30  2018 pollinate.service
-rw-r--r-- 1 root root  290 Apr 24  2018 rsyslog.service
drwxr-xr-x 2 root root 4.0K Apr 20  2018 runlevel1.target.wants
drwxr-xr-x 2 root root 4.0K Apr 20  2018 runlevel2.target.wants
drwxr-xr-x 2 root root 4.0K Apr 20  2018 runlevel3.target.wants
drwxr-xr-x 2 root root 4.0K Apr 20  2018 runlevel4.target.wants
drwxr-xr-x 2 root root 4.0K Apr 20  2018 runlevel5.target.wants
-rw-r--r-- 1 root root  175 Mar 27  2018 polkit.service
-rw-r--r-- 1 root root  544 Mar 22  2018 apparmor.service
-rw-r--r-- 1 root root  169 Feb 20  2018 atd.service
-rw-r--r-- 1 root root  919 Jan 28  2018 basic.target
-rw-r--r-- 1 root root  419 Jan 28  2018 bluetooth.target
-rw-r--r-- 1 root root  465 Jan 28  2018 cryptsetup-pre.target
-rw-r--r-- 1 root root  412 Jan 28  2018 cryptsetup.target
-rw-r--r-- 1 root root  750 Jan 28  2018 dev-hugepages.mount
-rw-r--r-- 1 root root  665 Jan 28  2018 dev-mqueue.mount
-rw-r--r-- 1 root root  471 Jan 28  2018 emergency.target
-rw-r--r-- 1 root root  541 Jan 28  2018 exit.target
-rw-r--r-- 1 root root  480 Jan 28  2018 final.target
-rw-r--r-- 1 root root  506 Jan 28  2018 getty-pre.target
-rw-r--r-- 1 root root  500 Jan 28  2018 getty.target
-rw-r--r-- 1 root root  598 Jan 28  2018 graphical.target
-rw-r--r-- 1 root root  527 Jan 28  2018 halt.target
-rw-r--r-- 1 root root  509 Jan 28  2018 hibernate.target
-rw-r--r-- 1 root root  530 Jan 28  2018 hybrid-sleep.target
-rw-r--r-- 1 root root  593 Jan 28  2018 initrd-fs.target
-rw-r--r-- 1 root root  561 Jan 28  2018 initrd-root-device.target
-rw-r--r-- 1 root root  566 Jan 28  2018 initrd-root-fs.target
-rw-r--r-- 1 root root  754 Jan 28  2018 initrd-switch-root.target
-rw-r--r-- 1 root root  763 Jan 28  2018 initrd.target
-rw-r--r-- 1 root root  541 Jan 28  2018 kexec.target
-rw-r--r-- 1 root root  435 Jan 28  2018 local-fs-pre.target
-rw-r--r-- 1 root root  547 Jan 28  2018 local-fs.target
-rw-r--r-- 1 root root  445 Jan 28  2018 machine.slice
-rw-r--r-- 1 root root  532 Jan 28  2018 multi-user.target
-rw-r--r-- 1 root root  505 Jan 28  2018 network-online.target
-rw-r--r-- 1 root root  502 Jan 28  2018 network-pre.target
-rw-r--r-- 1 root root  521 Jan 28  2018 network.target
-rw-r--r-- 1 root root  554 Jan 28  2018 nss-lookup.target
-rw-r--r-- 1 root root  513 Jan 28  2018 nss-user-lookup.target
-rw-r--r-- 1 root root  394 Jan 28  2018 paths.target
-rw-r--r-- 1 root root  592 Jan 28  2018 poweroff.target
-rw-r--r-- 1 root root  417 Jan 28  2018 printer.target
-rw-r--r-- 1 root root  745 Jan 28  2018 proc-sys-fs-binfmt_misc.automount
-rw-r--r-- 1 root root  655 Jan 28  2018 proc-sys-fs-binfmt_misc.mount
-rw-r--r-- 1 root root  583 Jan 28  2018 reboot.target
-rw-r--r-- 1 root root  549 Jan 28  2018 remote-cryptsetup.target
-rw-r--r-- 1 root root  436 Jan 28  2018 remote-fs-pre.target
-rw-r--r-- 1 root root  522 Jan 28  2018 remote-fs.target
-rw-r--r-- 1 root root  492 Jan 28  2018 rescue.target
-rw-r--r-- 1 root root  540 Jan 28  2018 rpcbind.target
-rw-r--r-- 1 root root  442 Jan 28  2018 shutdown.target
-rw-r--r-- 1 root root  402 Jan 28  2018 sigpwr.target
-rw-r--r-- 1 root root  460 Jan 28  2018 sleep.target
-rw-r--r-- 1 root root  449 Jan 28  2018 slices.target
-rw-r--r-- 1 root root  420 Jan 28  2018 smartcard.target
-rw-r--r-- 1 root root  396 Jan 28  2018 sockets.target
-rw-r--r-- 1 root root  420 Jan 28  2018 sound.target
-rw-r--r-- 1 root root  503 Jan 28  2018 suspend.target
-rw-r--r-- 1 root root  393 Jan 28  2018 swap.target
-rw-r--r-- 1 root root  795 Jan 28  2018 sys-fs-fuse-connections.mount
-rw-r--r-- 1 root root  767 Jan 28  2018 sys-kernel-config.mount
-rw-r--r-- 1 root root  710 Jan 28  2018 sys-kernel-debug.mount
-rw-r--r-- 1 root root  558 Jan 28  2018 sysinit.target
-rw-r--r-- 1 root root 1.4K Jan 28  2018 syslog.socket
-rw-r--r-- 1 root root  592 Jan 28  2018 system-update.target
-rw-r--r-- 1 root root  445 Jan 28  2018 system.slice
-rw-r--r-- 1 root root  704 Jan 28  2018 systemd-ask-password-console.path
-rw-r--r-- 1 root root  632 Jan 28  2018 systemd-ask-password-wall.path
-rw-r--r-- 1 root root  564 Jan 28  2018 systemd-initctl.socket
-rw-r--r-- 1 root root 1.2K Jan 28  2018 systemd-journald-dev-log.socket
-rw-r--r-- 1 root root  882 Jan 28  2018 systemd-journald.socket
-rw-r--r-- 1 root root  631 Jan 28  2018 systemd-networkd.socket
-rw-r--r-- 1 root root  657 Jan 28  2018 systemd-rfkill.socket
-rw-r--r-- 1 root root  490 Jan 28  2018 systemd-tmpfiles-clean.timer
-rw-r--r-- 1 root root  635 Jan 28  2018 systemd-udevd-control.socket
-rw-r--r-- 1 root root  610 Jan 28  2018 systemd-udevd-kernel.socket
-rw-r--r-- 1 root root  435 Jan 28  2018 time-sync.target
-rw-r--r-- 1 root root  445 Jan 28  2018 timers.target
-rw-r--r-- 1 root root  457 Jan 28  2018 umount.target
-rw-r--r-- 1 root root  432 Jan 28  2018 user.slice
-rw-r--r-- 1 root root  493 Jan 25  2018 ssh.service
-rw-r--r-- 1 root root  244 Jan 25  2018 ssh@.service
-rw-r--r-- 1 root root  216 Jan 16  2018 ssh.socket
-rw-r--r-- 1 root root  741 Dec 18  2017 accounts-daemon.service
-rw-r--r-- 1 root root  251 Nov 16  2017 cron.service
-rw-r--r-- 1 root root  266 Aug 15  2017 ufw.service
-rw-r--r-- 1 root root  115 Apr 22  2017 acpid.path
-rw-r--r-- 1 root root  234 Apr 22  2017 acpid.service
-rw-r--r-- 1 root root  115 Apr 22  2017 acpid.socket
-rw-r--r-- 1 root root  188 Feb 24  2014 rsync.service

/lib/systemd/system/sockets.target.wants:
total 0
lrwxrwxrwx 1 root root 25 Apr 20  2020 systemd-initctl.socket -> ../systemd-initctl.socket
lrwxrwxrwx 1 root root 32 Apr 20  2020 systemd-journald-audit.socket -> ../systemd-journald-audit.socket
lrwxrwxrwx 1 root root 34 Apr 20  2020 systemd-journald-dev-log.socket -> ../systemd-journald-dev-log.socket
lrwxrwxrwx 1 root root 26 Apr 20  2020 systemd-journald.socket -> ../systemd-journald.socket
lrwxrwxrwx 1 root root 31 Apr 20  2020 systemd-udevd-control.socket -> ../systemd-udevd-control.socket
lrwxrwxrwx 1 root root 30 Apr 20  2020 systemd-udevd-kernel.socket -> ../systemd-udevd-kernel.socket
lrwxrwxrwx 1 root root 14 Jun 10  2019 dbus.socket -> ../dbus.socket

/lib/systemd/system/sysinit.target.wants:
total 0
lrwxrwxrwx 1 root root 20 Apr 20  2020 cryptsetup.target -> ../cryptsetup.target
lrwxrwxrwx 1 root root 22 Apr 20  2020 dev-hugepages.mount -> ../dev-hugepages.mount
lrwxrwxrwx 1 root root 19 Apr 20  2020 dev-mqueue.mount -> ../dev-mqueue.mount
lrwxrwxrwx 1 root root 28 Apr 20  2020 kmod-static-nodes.service -> ../kmod-static-nodes.service
lrwxrwxrwx 1 root root 36 Apr 20  2020 proc-sys-fs-binfmt_misc.automount -> ../proc-sys-fs-binfmt_misc.automount
lrwxrwxrwx 1 root root 32 Apr 20  2020 sys-fs-fuse-connections.mount -> ../sys-fs-fuse-connections.mount
lrwxrwxrwx 1 root root 26 Apr 20  2020 sys-kernel-config.mount -> ../sys-kernel-config.mount
lrwxrwxrwx 1 root root 25 Apr 20  2020 sys-kernel-debug.mount -> ../sys-kernel-debug.mount
lrwxrwxrwx 1 root root 36 Apr 20  2020 systemd-ask-password-console.path -> ../systemd-ask-password-console.path
lrwxrwxrwx 1 root root 25 Apr 20  2020 systemd-binfmt.service -> ../systemd-binfmt.service
lrwxrwxrwx 1 root root 30 Apr 20  2020 systemd-hwdb-update.service -> ../systemd-hwdb-update.service
lrwxrwxrwx 1 root root 32 Apr 20  2020 systemd-journal-flush.service -> ../systemd-journal-flush.service
lrwxrwxrwx 1 root root 27 Apr 20  2020 systemd-journald.service -> ../systemd-journald.service
lrwxrwxrwx 1 root root 36 Apr 20  2020 systemd-machine-id-commit.service -> ../systemd-machine-id-commit.service
lrwxrwxrwx 1 root root 31 Apr 20  2020 systemd-modules-load.service -> ../systemd-modules-load.service
lrwxrwxrwx 1 root root 30 Apr 20  2020 systemd-random-seed.service -> ../systemd-random-seed.service
lrwxrwxrwx 1 root root 25 Apr 20  2020 systemd-sysctl.service -> ../systemd-sysctl.service
lrwxrwxrwx 1 root root 37 Apr 20  2020 systemd-tmpfiles-setup-dev.service -> ../systemd-tmpfiles-setup-dev.service
lrwxrwxrwx 1 root root 33 Apr 20  2020 systemd-tmpfiles-setup.service -> ../systemd-tmpfiles-setup.service
lrwxrwxrwx 1 root root 31 Apr 20  2020 systemd-udev-trigger.service -> ../systemd-udev-trigger.service
lrwxrwxrwx 1 root root 24 Apr 20  2020 systemd-udevd.service -> ../systemd-udevd.service
lrwxrwxrwx 1 root root 30 Apr 20  2020 systemd-update-utmp.service -> ../systemd-update-utmp.service
lrwxrwxrwx 1 root root 30 Apr  4  2019 plymouth-read-write.service -> ../plymouth-read-write.service
lrwxrwxrwx 1 root root 25 Apr  4  2019 plymouth-start.service -> ../plymouth-start.service

/lib/systemd/system/getty.target.wants:
total 0
lrwxrwxrwx 1 root root 23 Apr 20  2020 getty-static.service -> ../getty-static.service

/lib/systemd/system/graphical.target.wants:
total 0
lrwxrwxrwx 1 root root 39 Apr 20  2020 systemd-update-utmp-runlevel.service -> ../systemd-update-utmp-runlevel.service

/lib/systemd/system/local-fs.target.wants:
total 0
lrwxrwxrwx 1 root root 29 Apr 20  2020 systemd-remount-fs.service -> ../systemd-remount-fs.service

/lib/systemd/system/multi-user.target.wants:
total 0
lrwxrwxrwx 1 root root 15 Apr 20  2020 getty.target -> ../getty.target
lrwxrwxrwx 1 root root 33 Apr 20  2020 systemd-ask-password-wall.path -> ../systemd-ask-password-wall.path
lrwxrwxrwx 1 root root 25 Apr 20  2020 systemd-logind.service -> ../systemd-logind.service
lrwxrwxrwx 1 root root 39 Apr 20  2020 systemd-update-utmp-runlevel.service -> ../systemd-update-utmp-runlevel.service
lrwxrwxrwx 1 root root 32 Apr 20  2020 systemd-user-sessions.service -> ../systemd-user-sessions.service
lrwxrwxrwx 1 root root 15 Jun 10  2019 dbus.service -> ../dbus.service
lrwxrwxrwx 1 root root 29 Apr  4  2019 plymouth-quit-wait.service -> ../plymouth-quit-wait.service
lrwxrwxrwx 1 root root 24 Apr  4  2019 plymouth-quit.service -> ../plymouth-quit.service

/lib/systemd/system/rescue.target.wants:
total 0
lrwxrwxrwx 1 root root 39 Apr 20  2020 systemd-update-utmp-runlevel.service -> ../systemd-update-utmp-runlevel.service

/lib/systemd/system/timers.target.wants:
total 0
lrwxrwxrwx 1 root root 31 Apr 20  2020 systemd-tmpfiles-clean.timer -> ../systemd-tmpfiles-clean.timer

/lib/systemd/system/rc-local.service.d:
total 4.0K
-rw-r--r-- 1 root root 290 Apr 18  2020 debian.conf

/lib/systemd/system/user@.service.d:
total 4.0K
-rw-r--r-- 1 root root 125 Apr 18  2020 timeout.conf

/lib/systemd/system/halt.target.wants:
total 0
lrwxrwxrwx 1 root root 24 Apr  4  2019 plymouth-halt.service -> ../plymouth-halt.service

/lib/systemd/system/initrd-switch-root.target.wants:
total 0
lrwxrwxrwx 1 root root 25 Apr  4  2019 plymouth-start.service -> ../plymouth-start.service
lrwxrwxrwx 1 root root 31 Apr  4  2019 plymouth-switch-root.service -> ../plymouth-switch-root.service

/lib/systemd/system/kexec.target.wants:
total 0
lrwxrwxrwx 1 root root 25 Apr  4  2019 plymouth-kexec.service -> ../plymouth-kexec.service

/lib/systemd/system/poweroff.target.wants:
total 0
lrwxrwxrwx 1 root root 28 Apr  4  2019 plymouth-poweroff.service -> ../plymouth-poweroff.service

/lib/systemd/system/reboot.target.wants:
total 0
lrwxrwxrwx 1 root root 26 Apr  4  2019 plymouth-reboot.service -> ../plymouth-reboot.service

/lib/systemd/system/runlevel1.target.wants:
total 0

/lib/systemd/system/runlevel2.target.wants:
total 0

/lib/systemd/system/runlevel3.target.wants:
total 0

/lib/systemd/system/runlevel4.target.wants:
total 0

/lib/systemd/system/runlevel5.target.wants:
total 0

/lib/systemd/system-generators:
total 220K
lrwxrwxrwx 1 root root   22 May  4  2020 netplan -> ../../netplan/generate
-rwxr-xr-x 1 root root  23K Apr 20  2020 systemd-cryptsetup-generator
-rwxr-xr-x 1 root root  10K Apr 20  2020 systemd-debug-generator
-rwxr-xr-x 1 root root  31K Apr 20  2020 systemd-fstab-generator
-rwxr-xr-x 1 root root  14K Apr 20  2020 systemd-getty-generator
-rwxr-xr-x 1 root root  26K Apr 20  2020 systemd-gpt-auto-generator
-rwxr-xr-x 1 root root  10K Apr 20  2020 systemd-hibernate-resume-generator
-rwxr-xr-x 1 root root  10K Apr 20  2020 systemd-rc-local-generator
-rwxr-xr-x 1 root root  10K Apr 20  2020 systemd-system-update-generator
-rwxr-xr-x 1 root root  31K Apr 20  2020 systemd-sysv-generator
-rwxr-xr-x 1 root root  14K Apr 20  2020 systemd-veritysetup-generator
-rwxr-xr-x 1 root root  11K Jan 23  2020 lvm2-activation-generator
-rwxr-xr-x 1 root root 4.9K Jan 15  2020 cloud-init-generator
-rwxr-xr-x 1 root root  286 Jun 21  2019 friendly-recovery

/lib/systemd/system-sleep:
total 8.0K
-rwxr-xr-x 1 root root 219 Feb 17  2020 unattended-upgrades
-rwxr-xr-x 1 root root  92 Feb 22  2018 hdparm

/lib/systemd/network:
total 16K
-rw-r--r-- 1 root root 645 Jan 28  2018 80-container-host0.network
-rw-r--r-- 1 root root 718 Jan 28  2018 80-container-ve.network
-rw-r--r-- 1 root root 704 Jan 28  2018 80-container-vz.network
-rw-r--r-- 1 root root 412 Jan 28  2018 99-default.link

/lib/systemd/system-preset:
total 4.0K
-rw-r--r-- 1 root root 951 Jan 28  2018 90-systemd.preset

/lib/systemd/system-shutdown:
total 4.0K
-rwxr-xr-x 1 root root 160 Jan 14  2020 mdadm.shutdown

[00;33m### SOFTWARE #############################################[00m
[00;31m[-] Sudo version:[00m
Sudo version 1.8.21p2

[00;33m### INTERESTING FILES ####################################[00m
[00;31m[-] Useful file locations:[00m
/bin/nc
/bin/netcat
/usr/bin/wget
/usr/bin/curl

[00;31m[-] Can we read/write sensitive files:[00m
-rw-r--r-- 1 root root 1741 May 25  2020 /etc/passwd
-rw-r--r-- 1 root root 765 May 25  2020 /etc/group
-rw-r--r-- 1 root root 581 Apr  9  2018 /etc/profile
-rw-r----- 1 root shadow 1314 May 25  2020 /etc/shadow

[00;31m[-] SUID files:[00m
-rwsr-xr-- 1 root messagebus 42992 Jun 10  2019 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
-rwsr-xr-x 1 root root 14328 Mar 27  2019 /usr/lib/policykit-1/polkit-agent-helper-1
-rwsr-xr-x 1 root root 436552 Mar  4  2019 /usr/lib/openssh/ssh-keysign
-rwsr-xr-x 1 root root 100760 Nov 23  2018 /usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
-rwsr-xr-x 1 root root 10232 Mar 28  2017 /usr/lib/eject/dmcrypt-get-device
-rwsr-xr-x 1 root root 44528 Mar 22  2019 /usr/bin/chsh
-rwsr-xr-x 1 root root 37136 Mar 22  2019 /usr/bin/newuidmap
-rwsr-xr-x 1 root root 18448 Jun 28  2019 /usr/bin/traceroute6.iputils
-rwsr-xr-x 1 root root 76496 Mar 22  2019 /usr/bin/chfn
-rwsr-xr-x 1 root root 59640 Mar 22  2019 /usr/bin/passwd
-rwsr-xr-x 1 root root 75824 Mar 22  2019 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 40344 Mar 22  2019 /usr/bin/newgrp
-rwsr-sr-x 1 daemon daemon 51464 Feb 20  2018 /usr/bin/at
-rwsr-xr-x 1 root root 37136 Mar 22  2019 /usr/bin/newgidmap
-rwsr-xr-x 1 root root 22520 Mar 27  2019 /usr/bin/pkexec
-rwsr-xr-x 1 root root 149080 Jan 31  2020 /usr/bin/sudo
-rwsr-xr-x 1 root root 30800 Aug 11  2016 /bin/fusermount
-rwsr-xr-x 1 root root 26696 Mar  5  2020 /bin/umount
-rwsr-xr-x 1 root root 64424 Jun 28  2019 /bin/ping
-rwsr-xr-x 1 root root 43088 Mar  5  2020 /bin/mount
-rwsr-xr-x 1 root root 44664 Mar 22  2019 /bin/su

[00;31m[-] SGID files:[00m
-rwxr-sr-x 1 root shadow 34816 Feb 27  2019 /sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 34816 Feb 27  2019 /sbin/pam_extrausers_chkpwd
-rwxr-sr-x 1 root utmp 10232 Mar 11  2016 /usr/lib/x86_64-linux-gnu/utempter/utempter
-rwxr-sr-x 1 root crontab 39352 Nov 16  2017 /usr/bin/crontab
-rwxr-sr-x 1 root tty 14328 Jan 17  2018 /usr/bin/bsd-write
-rwxr-sr-x 1 root shadow 22808 Mar 22  2019 /usr/bin/expiry
-rwxr-sr-x 1 root shadow 71816 Mar 22  2019 /usr/bin/chage
-rwsr-sr-x 1 daemon daemon 51464 Feb 20  2018 /usr/bin/at
-rwxr-sr-x 1 root mlocate 43088 Mar  1  2018 /usr/bin/mlocate
-rwxr-sr-x 1 root ssh 362640 Mar  4  2019 /usr/bin/ssh-agent
-rwxr-sr-x 1 root tty 30800 Mar  5  2020 /usr/bin/wall

[00;31m[+] Files with POSIX capabilities set:[00m
/usr/bin/perl5.26.1 = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/perl = **cap_setuid+ep**

[-] Can't search *.conf files as no keyword was entered

[-] Can't search *.php files as no keyword was entered

[-] Can't search *.log files as no keyword was entered

[-] Can't search *.ini files as no keyword was entered

[00;31m[-] All *.conf files in /etc (recursive 1 level):[00m
-rw-r--r-- 1 root root 191 Feb  7  2018 /etc/libaudit.conf
-rw-r--r-- 1 root root 513 Feb  3  2020 /etc/nsswitch.conf
-rw-r--r-- 1 root root 6920 Sep 20  2018 /etc/overlayroot.conf
-rw-r--r-- 1 root root 350 Feb  3  2020 /etc/popularity-contest.conf
-rw-r--r-- 1 root root 280 Jun 20  2014 /etc/fuse.conf
-rw-r--r-- 1 root root 3028 Feb  3  2020 /etc/adduser.conf
-rw-r--r-- 1 root root 703 Aug 21  2017 /etc/logrotate.conf
-rw-r--r-- 1 root root 144 May 25  2020 /etc/kernel-img.conf
-rw-r--r-- 1 root root 14867 Oct 13  2016 /etc/ltrace.conf
-rw-r--r-- 1 root root 92 Apr  9  2018 /etc/host.conf
-rw-r--r-- 1 root root 604 Aug 13  2017 /etc/deluser.conf
-rw-r--r-- 1 root root 812 Mar 24  2018 /etc/mke2fs.conf
-rw-r--r-- 1 root root 1358 Jan 30  2018 /etc/rsyslog.conf
-rw-r--r-- 1 root root 2584 Feb  1  2018 /etc/gai.conf
-rw-r--r-- 1 root root 2969 Feb 28  2018 /etc/debconf.conf
-rw-r--r-- 1 root root 138 Feb 14  2020 /etc/sos.conf
-rw-r--r-- 1 root root 552 Apr  4  2018 /etc/pam.conf
-rw-r--r-- 1 root root 4861 Feb 22  2018 /etc/hdparm.conf
-rw-r--r-- 1 root root 34 Jan 27  2016 /etc/ld.so.conf
-rw-r--r-- 1 root root 403 Mar  1  2018 /etc/updatedb.conf
-rw-r--r-- 1 root root 1260 Feb 26  2018 /etc/ucf.conf
-rw-r--r-- 1 root root 5898 Feb  3  2020 /etc/ca-certificates.conf
-rw-r--r-- 1 root root 2683 Jan 17  2018 /etc/sysctl.conf

[00;31m[-] Current user's history files:[00m
lrwxrwxrwx 1 root root 9 May 25  2020 /home/hatter/.bash_history -> /dev/null

[00;31m[-] Location and contents (if accessible) of .bash_history file(s):[00m
/home/alice/.bash_history
/home/hatter/.bash_history

[00;31m[-] Any interesting mail in /var/mail:[00m
total 8
drwxrwsr-x  2 root mail 4096 Feb  3  2020 .
drwxr-xr-x 12 root root 4096 May 25  2020 ..

[00;33m### SCAN COMPLETE ####################################[00m
```
````

</details>

* we noticed this line ‘`/usr/bin/perl = **cap_setuid+ep`’\*\* it means that `/usr/bin/perl` can set the user ID, so we can use perl to set it to zero (root)
* if you are not familiar with perl, you can see this exploit in [GTFoBins](https://gtfobins.github.io/gtfobins/perl/#capabilities)

![Untitled](<images/Untitled 29.png>)

* great, we are root (Notice the prompt is #)

***

## Getting the flags

### root flag

![Untitled](<images/Untitled 30.png>)

***

### user flag

![Untitled](<images/Untitled 31.png>)

***

### I hope you enjoyed the write-up

* [Linkedin](https://www.linkedin.com/in/juba0x00/)
* [Twitter](https://twitter.com/juba0x00/)
* [TryHackMe](https://tryhackme.com/p/Juba0x430x55)
