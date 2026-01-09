# NMap to Metasploitable-2-Write-Up
Using Nmap and Metasploit to scan and target a vulnerable virtual machine (Metasploitable 2) within a controlled home lab environment to practice recon, enumeration and exploitation skills.
<!-- Nmap -->
<img src="https://img.shields.io/badge/-Nmap-4B8BBE?style=for-the-badge&logo=gnometerminal&logoColor=white"/>
  | Educational use only. All activities performed in an isolated TryHackMe lab on virtual machines. 
  No scanning or exploitation was conducted on systems without permission.

- Target: Metasploitable 2 (VirtualBox)
- Attacker Machine: Kali Linux (VirtualBox)
- Network Setup: Host-only Network


Objective:
Using Nmap, a high-level scan identified multiple exposed services, including an outdated FTP service (vsftpd 2.3.4) known to contain a built-in backdoor vulnerability.
The service was successfully exploited using the vsftpd 2.3.4 backdoor module in Metasploit, resulting in unauthenticated remote root access to the system.

## 1. Performing the Nmap Scan
A full service and version detection scan was run against the target:
nmap -A TARGET_IP_ADDRESS

The -A scan allowed me to perform a full system scan of the target and perform 
	• OS detection
	• Version detection
	• Script scanning

After this scan was returned I was able to pinpoint key vulnerabilities;
	• Port 21/tcp (FTP) running vsftpd 2.3.4 
	• Anonymous FTP login allowed
	• SSH, SMTP, MySQL, Tomcat, RPC, SMB, PostgreSQL and more also discovered
	
The FTP service stood out immediately due to its exploitable version.

## 2. Spot vulnerability
I was able to spot - 21/tcp open ftp vsftpd 2.3.4 - in the nmap scan 

This exact version is historically vulnerable:
- Contains a malicious backdoor
- Triggered by logging in with a username ending in :)
- Opens a root shell on TCP port 6200

This then allowed me to search for this exact exploit in metasploit;
#### msfconsole
#### search vsftpd 

Which revealed the full exploit 
#### exploit/unix/ftp/vsftpd_234_backdoor

## 3. Perform Exploit with Metasploit
With this information, I loaded the exploit;
#### use exploit/unix/ftp/vsftpd_234_backdoor
Set the target IP with 
#### set RHOSTS TARGET_IP_ADDRESS
and finally;
#### run

This then dropped me into a command shell session confirming the exploit was successful. 

## 4. Post-Exploitation - Confirming Root Access
Inside the shell session I used; 
#### id
To ensure this returned; 
#### uid=0(root) gid=)(root) 
This output confirms full system compromise. 
Final impact assessment:
- The vulnerability allowed unauthenticated remote code execution.
- The shell ran with root privileges, giving complete control of the system.
An attacker could then do whatever they wish such as; Install malware, add new users, steal or modify data, pivot deeper into the network	
#### Severity: Critical (10.0 CVSS)


