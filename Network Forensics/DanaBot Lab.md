<h1>Tools</h1>
Wireshark
<br>
VirusTotal
<h1>Scenario</h1>
The SOC team has detected suspicious activity in the network traffic, revealing that a machine has been compromised. Sensitive company information has been stolen. Your task is to use Network Capture (PCAP) files and Threat Intelligence to investigate the incident and determine how the breach occurred.
<h1>Questions</h1>
<h3>Q1.Which IP address was used by the attacker during the initial access?</h3>
Initially, the IP address 10.2.14.101 tries to access the website portfolo.serveirc.com, this is most likely a typo and indicates that the website they are accessing may not be the correct one. The DNS server 10.2.14.1 responds and returns the website for them to access. 
<br><br>
<img width="1920" height="373" alt="image" src="https://github.com/user-attachments/assets/eeb4a866-bc63-4a51-85e2-a9a9b95a370b" />
<br><br>
After the DNS is resolved, a connection is established between 10.2.14.101 and 62.173.142.148 with a three-way handshake (SYN→SYN-ACK→ACK), and data is being sent between the IP addresses, with 10.2.14.101 requesting the login page. 
<br><br>
But the IP address 62.173.142.148 is highly suspicious, searching it on VirusTotal reveals that it has been flagged a few times as malicious, confirming it to be the attacker.
<br><br>
<img width="1702" height="212" alt="image" src="https://github.com/user-attachments/assets/5281e79a-6615-4bd3-a8ff-9433f2be21c6" />
<h4>Answer: 62.173.142.148</h4>
<h3>Q2.What is the name of the malicious file used for initial access?</h3>
Reviewing the network traffic further, we see that 10.2.14.101 had requested the login page /login.php from 62.173.142.148, looking at the packet of the page that 10.2.14.101 received from 62.173.142.148 (HTTP/1.1 200 OK)  may reveal the malicious file that they had gotten.
<br><br>
<img width="1917" height="272" alt="image" src="https://github.com/user-attachments/assets/986a9bb1-d2b8-46b3-9c2c-6061f4e293cd" />
<br><br>
Reviewing the packet data shows that attached to the page was a JavaScript file named allegato_708.js, revealing the name of the malicious file that infected 10.2.14.101.   
<br><br>
<img width="960" height="442" alt="image" src="https://github.com/user-attachments/assets/45aa22f9-10ab-40ab-b258-4fc6326fb4d6" />
<h4>Answer: allegato_708.js</h4>
<h3>Q3.What is the SHA-256 hash of the malicious file used for initial access?</h3>
