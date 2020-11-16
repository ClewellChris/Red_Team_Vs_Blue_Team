# Red Team Vs Blue Team
Penetration Test Report
Completed by Chris Clewell 
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

## Blue Team: Log Analysis, Attack Characterization, Mitigation, and Alarms
  As stated above our Victim machine had Filebeat and Metricbeat installed on the system so that we were able to show an analysis of the attack in progress to adequately set alarms and take the appropriate steps to harden the system from future attacks.  Using ELK we were able to analzye HTTP Data, PORT Data, Response Codes, and Traffic Data. From this data analysis we recommeneded the following Alarms and Hardening: 
 
Alarms

 - Notify when there are multiple port scans coming form a singular IP
 - Notify when the anyone accesses the /secret_folder/ on the server 
 - Notify when there are more than 10 (401) response codes on any log in on the server
 - Notify when any IP outside of a proposed white list attempts to access the WebDAV server
 
Hardening
 
 - Removal of the /secret_folder/ directory entirely
 - Not hosting log in instructions on a public facing webpage 
 - Create an IP Whitelist allowing only approved IP's to access the server 
 - Not allowing files to be uploaded from the web interface
 - Creating a lockout policy with progressive delays to prevent Brute Force attacks but not requiring system administrator intervention for every lockout 
 - Filtering any open ports so they are more difficult to discover 
 - Requiring a complicated password policy of at least 16 characters and utilizing punctuation characters 

## Kibana Data Captures 
  Below are screenshots of all the Kibana data collected and used to characterize the attack and create our analysis.  We found that there were 15,353 requests on the /secret_folder/ indicating the Brute Force attack. There were 206 requests on the /webdav folder indicating our ability to connect and upload files. 
  
![Port Scan](/Kibana/Port_Scan.jpg)

![Hidden Directory](/Kibana/request_for_hidden_directory.jpg)

![Brute Force Attack](/Kibana/uncover_brute_force_attack.jpg)

![Reverse Shell](/Kibana/meterpreter.jpg)
    
    
