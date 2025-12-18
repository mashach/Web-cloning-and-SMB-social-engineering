# Web-cloning-and-SMB-social-engineering
sudo setoolkit

# Navigating to Web Attacks
Select from the menu: 2
Why: We choose "Website Attack Vectors" because we want to create a fake website

# Choosing Attack Type

set:webattack> 3
Credential Harvester: Clones a real website and captures login credentials

Other options: Java Applet, Metasploit Browser, Tabnabbing, Web Jacking, etc.

# Selecting Cloning Method
text
set:webattack> 2
Three Options:

Web Templates: Pre-made fake login pages

Site Cloner: Exact copy of any website (we chose this)

Custom Import: Upload your own HTML file

# Configuration Settings

Enter the IP address for POST back in Harvester/Tabnabbing: 10.6.6.1
Important: This must be your external/public IP if attacking over the internet

Our case: We used local IP 10.6.6.1 for lab environment

Why needed: Tells the fake site where to send captured credentials

# Cloning Target Website
text
Enter the url to clone: http://dvwa.vm
Target: DVWA (Damn Vulnerable Web Application) running locally

Process: SET downloads all website files to create an identical copy

Note: Works with both HTTP and HTTPS sites

 How the Attack Works
The Trap:
Victim visits: User goes to our fake website (looks identical to real site)

Enter credentials: User types username and password

Capture happens: Instead of sending to real server, data goes to our SET server

Redirect: User gets redirected to real site (so they don't suspect anything)

What We Captured:
POSSIBLE USERNAME FIELD FOUND: username=kali
POSSIBLE PASSWORD FIELD FOUND: password=kali
POSSIBLE USERNAME FIELD FOUND: Login=Login
POSSIBLE USERNAME FIELD FOUND: user_token=513ea6844925da369b97d106b849a5de
Credentials: Username kali and password kali

Additional data: Login button text and CSRF token

# SMB-social-engineering
# Becoming Root
## sudo su
Explanation: This command switches the user to root (superuser) to have necessary permissions for scanning and enumeration.

Output: The prompt changes from kali to root.

# Nmap Null Scan (First Scan)

## nmap-sN 172.17.0.2/24
Explanation: This command performs a Null scan (-sN) on the IP range 172.17.0.2/24. A Null scan sends packets without any flags (no SYN, ACK, or FIN) to determine if ports are open, closed, or filtered.

Output:The target 172.17.0.2 (Metasploitable) has port 111 (rpcbind) open|filtered.

The host 172.17.0.1 has port 22 (SSH) open|filtered.

# Nmap Null Scan (Second Scan)
## nmap -sN 172.17.0.0/24
Explanation: This command is similar to the first but on a different network range (172.17.0.0/24). It also uses a Null scan.

Output:The target 172.17.0.2 now shows three open|filtered ports: 25 (SMTP), 111 (rpcbind), and 3306 (MySQL).

The host 172.17.0.1 still shows port 22 (SSH) open|filtered.

# Enum4linux All Enumeration

## enum4linux -A 172.17.0.2
Explanation: This command runs all basic enumeration scripts (-A) on the target 172.17.0.2. Enum4linux is a tool for enumerating information from Windows and Samba systems.

Output:The target is part of the workgroup WORKGROUP.

The server allows null sessions (no username or password).

The domain SID (Security Identifier) is NULL, meaning it's a workgroup, not a domain.

# Enum4linux Share and NBT Stat Enumeration (with typo)

## enum4linux -sN 172.17.0.2
Explanation: The user intended to run share enumeration and NBT stat, but the option -sN is not standard. The tool still attempted share enumeration but encountered an error because of the invalid option or a missing file.

Output:It repeated the workgroup and session check.

Then attempted brute force share enumeration but failed because it couldn't open a share list file (which is expected when using -s without a file?).

# Enum4linux Password Policy Enumeration
## enum4linux -P 172.17.0.2
Explanation: This command enumerates the password policy on the target (-P).

Output:The target has a minimum password length of 5, no password history, and no complexity requirements.

The password policy is relatively weak.
#Enum4linux Standard Enumeration (without options)
## enum4linux 172.17.0.2
Explanation: This runs the standard enumeration (without any options, which is equivalent to -a for all simple enumeration).

Output:It found the workgroup WORKGROUP and the hostname METASPLOITABLE.

It listed all users on the system (like root, msfadmin, user, etc.) and their RIDs (Relative Identifiers).

## It enumerated shares: print$, tmp, opt, IPC$, and ADMIN$.

It mapped the shares and found that the tmp share is accessible and writable.

It also retrieved the password policy and group information.

# SMBClient List Shares
## smbclient -L //172.17.0.2/
Explanation: This command lists the available SMB shares on the target.

Output:It shows the same shares as enum4linux: print$, tmp, opt, IPC$, and ADMIN$.

# Accessing the TMP Share and Uploading a File
## smbclient //172.17.0.2/tmp
Explanation: This command connects to the tmp share on the target.

Output:The connection is successful with anonymous login.

Inside the share, we can run commands.
## We used put virus.txt group_work to upload a file named virus.txt and renamed it to group_work on the target.

The dir command shows the uploaded file and other existing files.

# Conclusion
This exercise demonstrates how to use Nmap and enum4linux to enumerate SMB services and how to use smbclient to interact with SMB shares. The target (Metasploitable) has weak security settings, allowing anonymous access and write permissions on the tmp share.

Attachment snip
[SMB snipnet](https://github.com/user-attachments/assets/4b067135-b627-489b-8020-392e06f0b363)

[SMB snipnet](https://github.com/user-attachments/assets/d0baf2de-8a12-4e44-abff-198a223199d3)

[SMB snipnet](https://github.com/user-attachments/assets/c63892db-34be-4a22-b483-b2f70cdfbd06)

[SMB snipnet](https://github.com/user-attachments/assets/a255b41f-ec53-4e22-bcb8-263639146f4d)

[SMB snipnet](https://github.com/user-attachments/assets/9c778a88-90bd-4248-b108-ef77169509c9)

[setoolkit clone](https://github.com/user-attachments/assets/75d1c391-ac72-4711-aa9c-7408cea10e62)

[SMB snipnet](https://github.com/user-attachments/assets/e8016939-7d9f-4ca7-b081-2d96fffa08ad)

[SMB snipnet](https://github.com/user-attachments/assets/373a6fdd-2585-4ec4-89f7-f405c95ce6c4)

[SMB snipnet](https://github.com/user-attachments/assets/92437847-8528-435b-ac05-9044f422050f)

[SMB snipnet]](https://github.com/user-attachments/assets/6b5eba31-0e48-46df-afdd-79746583ca2a)

[SMB snipnet]](https://github.com/user-attachments/assets/a69c7a74-75a1-403a-989d-757778d6a064)

[SMB snipnet]](https://github.com/user-attachments/assets/eb666da2-1f81-43ec-a61a-27db4d65eb66)

[SMB snipnet](https://github.com/user-attachments/assets/c064f1e6-f911-4ab3-bec5-254c23612781)

[SMB snipnet](https://github.com/user-attachments/assets/4f08ac3e-5ee7-4d6e-9b3e-14f819c08f41)

[setoolkit clone](https://github.com/user-attachments/assets/c4428251-2809-4877-a2b4-2d4b494a06b9)

