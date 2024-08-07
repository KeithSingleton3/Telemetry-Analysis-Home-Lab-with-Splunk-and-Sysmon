<h1>Telemetry Analysis Home Lab with Splunk and Sysmon </h1>

<h2>Description</h2>
In this lab we're going to walk through how to analyze and ingest telemetry from Sysmon logs that are feeding into Splunk. I will be using a Windows 10 VM and a Kali Linux VM inside of Oracle Virtual Box for penetration testing. Configuring this lab will definitely give you hands on experience with networking, analyzing data logs, and so much more! 
<br />


<h2>List of Software Used</h2>

- <b>Splunk</b> 
- <b>Sysmon</b>
- <b>Metasploit</b>
- <b>Oracle Virtual Box</b>
- <b>Nmap</b>

<h2>Environments Used </h2>

- <b>Windows 10</b>
- <b>Kali Linux</b> 

<h2>Program walk-through:</h2>

<p align="center">
Successfully created an internal network for Windows 10 VM and Kali Linux VM called "Intranet"- allowing them to communicate. These VMs do not have internet connectivity and thus are unnattached to my Host system. <br/>
<img src="https://imgur.com/jZ2Yymy.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
Statically assigned IP addresses to both VMs to allow communication. Windows 10 VM was assigned the IP address of 192.168.20.10, Kali Linux VM was assigned the IP address of 192.168.20.11. ipconfig/ifconfig were ran to test if changes were successful. Note: Automatic DHCP was turned off for both VMs.   <br/>
<img src="https://imgur.com/uxW9xrH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/SaXHZdv.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/pFpY8F7.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/uHoaH8B.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
 
<br />
<br />
<br />
Conducted an Nmap scan on my attacker machine of the Windows 10 VM which shows port 3389 open (RDP) : <br/>
<img src="https://imgur.com/pTPTYBY.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
"msfvenom -l payloads" was entered on the Kali Linux command prompt to show a list of available payloads:  <br/>
<img src="https://imgur.com/a30j12u.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/JUN5iNc.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
A Meterpreter Reverse TCP Payload was selected- allowing me to remotely take over the compromised system. The payload is instructed to connect back to the machine based on the lhost (Kali VM) and lport (4444). "lport=4444" specifies that the malware will execute on port 4444. The file format will be an exe format and the file name will be "Resume.pdf.exe"  <br/>
<img src="https://imgur.com/Nyjshxs.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
"mfsconsole" is the command that starts the metasploit command shell- allowing me to listen in on port 4444. <br />
<img src="https://imgur.com/PKTyiwo.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
An http server was created on the Kali Linux VM allowing our Windows machine to download the malware. <br />
<img src="https://imgur.com/qrTb8ZE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
Windows Defender was turned off on the Windows 10 VM to allow malware to execute. <br />
<img src="https://imgur.com/ASJmwqb.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
After successfully downloading the Malware from the Linux web server, a Netstat command was executed on the Windows 10 VM to show active connections. An established connection to 192.168.20.11 (Kali VM) via port 4444 is shown. <br />
<img src="https://imgur.com/cnCgTe2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
On the Kali Linux VM a connection to the Windows 10 VM via meterpreter is established. "net user" was entered into the command prompt from the target machine to get a list of all local user accounts. "ipconfig" was also entered to show the ip address of the compromised machine <br />
<img src="https://imgur.com/CSAzQr6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/AMoQ39P.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
An index labeled "endpoint" was configured inside of the Windows 10 VM to ingest telemetry into Splunk. A query of our attacker's IP address inside of Splunk shows 17 events. <br />
<img src="https://imgur.com/CdlfnNJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
Splunk's "destination_port" field shows 2 entries; Port 3389 (RDP) and Port 4444 which is a port that is commonly used by malware. Further investigation would be needed to determine (1) Should this machine be attempting to connect to our RDP port, (2) Who owns this machine? (3)Is this a legitimate malware attack. <br />
<img src="https://imgur.com/Db9X9gH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Querying a search for our custom malware (Resume.pdf.exe) in Splunk returns several event codes. One of the more critical event codes in Sysmon is Event Code #1- which is the Process Creation Event. <br />
<img src="https://imgur.com/Dy6DQpX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
<br />
With Event Code #1 expanded, we can see that the Parent Process (Resume.pdf.exe) spawned the process of (cmd.exe) with the Process ID of (4564). A further investigation into the Process ID will determine if it’s known to be associated with malware. <br />
<img src="https://imgur.com/lSOZ4gt.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/GixF4WW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://imgur.com/YMjis3x.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
