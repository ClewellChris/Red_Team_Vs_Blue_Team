# Red Team Vs Blue Team
Slideshow of a mock penetration test on an Azure Lab Environment 
![Penetration Test Environment](/Images/RedVBlueDiagram.jpg)

## Capstone Engagement Assessment, Analysis, and Hardening of a vulnerable system 
Our team was tasked with finding and exploiting vulnerabilities of a public facing web server

Table Of Contents 
- Network Topology 
- Red Team: Security Assessment 
- Blue Team: Log Analysis and Attack Characterization 
- Hardening: Proposed Mitigation and Proposed Alarms 

## Network Topolgy 
All of the work for this penetration test was done in a Azure lab environment and utilized Hyper-V, ELK, Kali Linux, and Chrome Web Browser. This was a Gray Box penetration test as we had knowledge of root level passwords on all machines involved. The Victim machine had Metricbeat and Filebeat installed and forwarded logs the ELK machine so that we could analyze the attack 

Goals 
- Identify the IP address and exposed services of the Victim machine 
- Find hidden files on the Victim machine 
- Brute Force and crack any passwords found 
- Upload a PHP reverse shell to the Victim machine 
- Explore the target system and find a Flag 

Machines 
- Host Machine
  - Windows OS 
  - IPV4: 192.168.1.1
- Attack Machine 
  - Kali Linux 
  - IPV4: 192.168.1.90
- Victim Machine 
  - Linux 
  - IPV4: 192.168.1.105
- ELK Machine 
  - Linux 
  - IPV4: 192.168.1.100
  
## Service Scan 
  Utilizing Nmap to discover all the machines on the system we were able to discover that the Victim machine had both port 80 (Apache HTTP Server 2.4.29) and port 22 wide open.  While Apache 2.4.29 has plenty of medium to low vulnerabilites of its own we were more interested in reported admin vulnerabilities. Our goal being to set up a reverse shell in order to make changes to the system as well as steal files. 

![Nmap Service Scan of Network](/Images/red_team_service_scan.jpg)

![Nmap Scan of Network](/Images/red_team_ping_scan.jpg)

## Red Team: Security Assessment
  Once we were able to discover the server on the system we set to work discovering what other vulnerabilites lied ahead. We utilized Kali Linux, Google Chrome Browser, Crackstation.net, Hydra, and Metasploit in discovering and exploiting all the vulnerabilites. 

Vulnerabilites 
- Directory Traversal 
- Brute Force 
- WebDAV 

We navigated to the server's webpage at http://192.168.1.105/ and while searching around we found that there was a /secret_folder/ hosted on the machine that contained company information.  Adding the /secret_folder/ to the end of the url allowed us to navigate to the page and we were prompted with a log in page to access the information.  It was then discovered that the user "ashton" had access to the folder.  We then set to using Hydra to brute force "ashton's" password to gain access to the folder.  Contained in the folder was information regarding connecting to the WebDAV server and instructions to use "ryans" log in information with a hash provided.  We used www.crackstation.net to crack the hash.  Once we had the password for "ryan" we went about creating a .php payload with MSFVENOM and used our access to the WebDAV server to move our payload on to the machine.  In combination with the /exploit/multi/handler/ exploit and the /php/meterpreter/reverse_tcp/ payload we were able to open a meterpreter shell and have full access to the system.  From there we did some digging to find the "flag.txt" and also to download the "password.dav" file to our Kali Linux machine. 

![Directory Traversal](/Images/secret_folder.jpg)

![Hydra Brute Force](/Images/hydra_password_crack.jpg)

![Access to WebDAV Server](/Images/secretfolder.jpg)

![Crackstation.net](/Images/crackstation.jpg)

![Payload Creation](/Images/payload.jpg)

![WebDAV Connection](/Images/dav_connect.jpg)

![Meterpreter Connection](/Images/connected.jpg)

![Flag](/Images/flag.jpg)

![Password DAV](/Images/password_dav.jpg)
